# Endpoints da Loja de Aplicativos

Este documento lista todos os endpoints que a **Loja de Aplicativos** fornece como API para outros sistemas que conversam com ela, identificados a partir dos diagramas de sequência das jornadas de negócio.

## Configuração

### Gestão de Configurações de Terminal
- `POST /configuracoes-terminal` - Cadastra configuração de terminal (modelo de terminal, tipo de integração)
- `POST /configuracoes-terminal/{idConfiguracao}/mascaras` - Associa prefixo de número lógico à configuração
- `GET /configuracoes-terminal/numero-logico/{numeroLogico}` - Consulta configuração de terminal por número lógico (aceita prefixo ou número completo)
  - **Response**: Retorna dados da configuração incluindo estágio atual (producao, piloto)

### Gestão de Filtros
- `POST /tipos-filtro` - Cadastra tipos de filtro de aplicativo
- `POST /tipos-filtro/${idTipoFiltro}/filtros` - Cadastra filtros para um tipo de filtro específico

## Gestão de Aplicativos

### Cadastro e Consulta
- `POST /aplicativos` - Cadastra aplicativo na loja (recebe: referência aplicativo ldm, nome do aplicativo)
- `POST /aplicativos/{idAplicativo}/configuracoes` - Cadastra configurações de aplicativo (aplicativo, configuração de terminal, nome pacote android)

### Gestão de perfis de aplicativo
- `POST /aplicativos/{id}/perfis` - Cadastra novo perfil de aplicativo e retorna pendencias de upload caso existam (URLs pre-assinadas)
- `GET /aplicativos/{id}/perfis/estagio/{estagio}` - Recupera perfil para estágio específico
- `GET /aplicativos/{id}/perfis/{idPerfil}` - Recupera perfil de aplicativo por ID
- `PATCH /aplicativos/{id}/perfis/{idPerfil}/promover-producao` - Atualiza estágio do perfil de aplicativo para produção

### Gestão de versões de aplicativo
- `POST /configuracoes-aplicativo/{idAplicativo}/{idConfiguracao}/versoes` - Cadastra nova versão de aplicativo para configuração específica (recebe: referencia versão aplicativo LDM, tamanho do aplicativo, outros metadados)
- `GET /configuracoes-aplicativo/{idAplicativo}/{idConfiguracao}/versoes/estagio/{estagio}` - Recupera versão de aplicativo por estágio para configuração específica
- `GET /configuracoes-aplicativo/{idAplicativo}/{idConfiguracao}/versoes/{idVersao}` - Recupera versão de aplicativo por ID para configuração específica
- `PATCH /configuracoes-aplicativo/{idAplicativo}/{idConfiguracao}/versoes/{idVersao}/promover-piloto` - Atualiza o estado da versão do aplicativo para piloto na configuração específica
- `PATCH /configuracoes-aplicativo/{idAplicativo}/{idConfiguracao}/versoes/{idVersao}/promover-producao` - Atualiza o estado da versão do aplicativo para produção na configuração específica

## Catálogo de Aplicativos

### Busca e Listagem
- `POST /catalogo-aplicativos/busca/{idConfiguracao}/{estagio}` - Busca aplicativos com filtros avançados
  - **Path params**: 
    - `idConfiguracao` - ID da configuração de terminal
    - `estagio` - producao, piloto
  - **Query params** (opcionais): 
    - `limit`, `offset` (paginação)
    - `ordenacao` - nome_asc, nome_desc, rating_asc, rating_desc, data_asc, data_desc
  - **Body** (opcional): Filtros específicos por tipo
    ```json
    {
      "busca_textual": "whatsapp",
      "filtros_categoria": ["financeiro", "produtividade"],
      "filtros_desenvolvedor": ["Banco XYZ", "FinTech ABC"],
      "filtros_tamanho": {
        "min_mb": 1,
        "max_mb": 50
      },
      "filtros_rating": {
        "min": 4.0
      }
    }
    ```
  - **Response**: Lista com dados básicos (id, nome, ícone, descrição resumida, categoria, versão atual)
  - **Uso**: Busca flexível - body vazio = listar todos, com filtros = busca específica

### Detalhes Completos
- `GET /catalogo-aplicativos/{idAplicativo}/{idConfiguracao}/{estagio}` - Recupera detalhes completos usando chave composta
  - **Path params**: 
    - `idAplicativo` - ID único do aplicativo
    - `idConfiguracao` - ID da configuração de terminal
    - `estagio` - producao, piloto
  - **Response**: Metadados completos do perfil e versão específicos para a configuração e estágio
    - Perfil: dados específicos para a configuração de terminal
    - Versão: compatível com o modelo de terminal
    - Arquivos: processados para o modelo específico (downsizing)
---

## Notas Técnicas

### Estados dos Perfis de Aplicativo
- `pendente` - Cadastro de perfil de aplicativo incompleto
- `revisao` - Cadastro de perfil de aplicativo completo (processamento assíncrono concluído)
- `producao` - Perfil de aplicativo publicado no catálogo
- `arquivado` - Perfil de aplicativo substituído por nova versão no estágio correspondente

### Características Específicas
- **Deduplicação de arquivos**: Upload por hash - só arquivos não existentes na Loja
- **Processamento assíncrono**: Downsizing para todos os modelos de terminais
- **Visibilidade controlada**: Aplicativos ficam visíveis apenas para dispositivos específicos conforme configuração

### Design dos Endpoints de Catálogo
- **Chave composta**: `(aplicativo + configuracao_terminal + estagio)` → referencia perfil e versão específicos
- **Estágio via path**: Parte da URL para representar corretamente a chave composta
- **Filtragem por configuração**: Cada terminal vê apenas apps compatíveis com seu modelo e estágio
- **Estágios separados**: Produção vs Piloto podem ter perfis/versões diferentes
- **Performance**: Endpoint de busca retorna apenas dados essenciais para renderização da lista
- **Dados completos**: Endpoint de detalhes carrega metadados completos sob demanda
- **Processamento específico**: Arquivos já processados para o modelo de terminal (downsizing)
- **Paginação**: Suporte a limit/offset para listas grandes

### Fluxo do App Terminal
1. **Identificação**: `GET /configuracoes-terminal/numero-logico/{numeroLogico}` → obtém `idConfiguracao` e `estagio` na resposta
2. **Listagem básica**: `POST /catalogo-aplicativos/busca/{id}/{estagio}` (body vazio) → lista todos
3. **Busca avançada**: `POST /catalogo-aplicativos/busca/{id}/{estagio}` (com filtros no body) → lista filtrada  
4. **Detalhes**: `GET /catalogo-aplicativos/{idApp}/{idConfiguracao}/{estagio}` → metadados específicos

### Exemplos de Uso

#### Resposta da Configuração
```json
GET /configuracoes-terminal/numero-logico/001234567

{
  "id": 5,
  "modelo": "A920",
  "tipo_integracao": "pos",
  "estagio": "piloto"
}
```

#### Listagem Básica (sem filtros)
```http
POST /catalogo-aplicativos/busca/5/piloto?limit=20&offset=0
Content-Type: application/json

{}
```

#### Busca Avançada
```http
POST /catalogo-aplicativos/busca/5/piloto?limit=20&ordenacao=rating_desc
Content-Type: application/json

{
  "busca_textual": "pix",
  "filtros_categoria": ["financeiro", "produtividade"],
  "filtros_desenvolvedor": ["Banco XYZ"],
  "filtros_tamanho": {
    "max_mb": 50
  },
  "filtros_rating": {
    "min": 4.0
  }
}
```