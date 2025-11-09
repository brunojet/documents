# Propostas para o Estado Futuro (To-Be)

Esta pasta contém as propostas técnicas do novo sistema a ser implementado.

## 📋 Documentação Disponível

### Responsabilidades e Jornadas
- ✅ `01-raci-matrix-responsabilidades.md` - Matriz RACI definindo responsabilidades entre Portal Certificação (PC), Portal Relacionamento (PR) e Loja de Aplicativos
- ✅ `to-be.md` - Jornadas completas e diagramas de sequência mostrando interação entre os 3 sistemas

### APIs da Loja de Aplicativos
- ✅ `02-endpoints-store.md` - Especificação completa da API REST do backend da Loja
  - Endpoints com chaves compostas (aplicativo + configuracao_terminal + estagio)
  - Estados: perfis (revisão/produção) e versões (piloto/produção)
  - Operações de catálogo, busca e gerenciamento
  - Semântica REST adequada para operações complexas

### Arquitetura Futura
- ✅ **Separação de responsabilidades**: Portal Certificação (PC) vs Portal Relacionamento (PR) vs Loja de Aplicativos
- ✅ **Desacoplamento**: Certificação técnica (PC) separada de perfil comercial (PR/Loja)
- ✅ **Especialização**: Cada sistema com papel bem definido conforme matriz RACI
- ✅ **Estados bem definidos**: Perfis (pendente → revisão → produção) e Versões (pendente → piloto → produção)
- [ ] Estratégia de dados e persistência
- [ ] Padrões de escalabilidade e performance

## 🎯 O Que É Proposto Para Entrega

### ⚙️ Configuração e Identificação de Terminais
- **Valor de negócio**: Identificação automática de terminal por número lógico
- **Proposta de implementação**: `GET /configuracoes-terminal/numero-logico/{numero}` retorna configuração + estágio
- **Benefício**: Sem necessidade de configuração manual no app

### 📋 Cadastro Simplificado de Aplicativos  
- **Valor de negócio**: Entrada rápida de aplicativos no sistema
- **Proposta de implementação**: `POST /aplicativos` + `POST /aplicativos/{id}/configuracoes`
- **Benefício**: Base para perfis comerciais sem complexidade de certificação

### 📄 Perfis Comerciais Flexíveis
- **Valor de negócio**: Mudança de informações comerciais SEM recertificação técnica
- **Proposta de implementação**: Ciclo pendente → revisão → produção → arquivado
- **Benefício**: Time-to-market reduzido para updates de vitrine

### 📦 Versões Técnicas Independentes
- **Valor de negócio**: Testes graduais antes de produção
- **Proposta de implementação**: Ciclo pendente → piloto → produção → arquivado  
- **Benefício**: Redução de risco com validação em piloto

### 📱 Busca e Descoberta Avançada
- **Valor de negócio**: Usuários encontram apps relevantes rapidamente
- **Proposta de implementação**: `POST /catalogo-aplicativos/busca/{id}/{estagio}` com filtros combinados
- **Benefício**: Experiência superior vs ServiceNow atual

## 🎯 Propósito da Proposta
Definir claramente o novo ecossistema proposto:
- **Sistema focado**: Loja de aplicativos SEM responsabilidades de certificação (delegadas para PC)
- **Arquitetura limpa**: Separação clara entre PC (certificação) vs PR (comercial) vs Loja (vitrine)  
- **Configuração flexível**: Suporte a múltiplos modelos de terminal e estágios (piloto/produção)
- **Performance otimizada**: APIs da Loja desenhadas para experiência mobile eficiente
- **Estados independentes**: Perfis comerciais e versões técnicas com ciclos próprios

## � Fundamentos Técnicos
- **Chave composta**: `(aplicativo + configuracao_terminal + estagio)` para precisão contextual
- **Processamento assíncrono**: Downsizing automático + deduplicação por hash
- **REST semântico**: POST /busca para filtros complexos, GET para recursos específicos  
- **Performance**: Separação listagem básica vs detalhes completos
- **Visibilidade controlada**: Apps só visíveis para terminais compatíveis
- **Visibilidade controlada**: Apps só visíveis para terminais compatíveis
- **REST semântico**: Uso correto de métodos HTTP para diferentes operações

## 🚫 O Que NÃO Será Responsabilidade da Loja
- ❌ **Certificação técnica**: Responsabilidade do Portal de Certificação (PC)
- ❌ **Aprovações comerciais**: Responsabilidade do Portal de Relacionamento (PR)  
- ❌ **Distribuição B2B**: Responsabilidade do LDM
- ❌ **Apps privados**: Só apps destinados à loja pública (conforme matriz RACI)

## ✅ Benefícios Entregues vs As-Is
- **🔥 Sem recertificação**: Mudança de perfil comercial (PR→Loja) não requer nova certificação (PC)
- **⚡ Performance**: APIs da Loja otimizadas para mobile com dados contextualizados por terminal
- **🎯 Responsabilidades claras**: Cada sistema (PC/PR/Loja) faz apenas o que deve conforme RACI
- **🔄 Flexibilidade**: Perfis (revisão→produção) e versões (piloto→produção) independentes
- **📱 Experiência**: Busca avançada e descoberta de aplicativos melhoradas vs ServiceNow

## 🔧 Status Técnico
- **API Design**: ✅ Completo e validado (chaves compostas, REST semântico)
- **Jornadas de Negócio**: ✅ Mapeadas com separação de responsabilidades
- **Endpoints Specification**: ✅ Documentação completa com exemplos
- **Database Design**: 🔄 Em definição (suporte a chaves compostas)
- **Frontend Integration**: 📋 Especificação pronta para implementação
- **Security Model**: 📋 A ser definido conforme padrões da empresa

---
*Estado: Propostas técnicas aguardando validação e aprovação para desenvolvimento do backend da loja.*