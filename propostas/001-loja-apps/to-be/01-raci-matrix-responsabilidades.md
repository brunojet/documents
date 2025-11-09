# Matriz RACI - Ecossistema de Aplicativos

## Definições RACI

- **R (Responsible)**: Quem executa a atividade
- **A (Accountable)**: Quem responde pelo resultado final (apenas 1 por atividade)
- **C (Consulted)**: Quem deve ser consultado (input bidirecional)
- **I (Informed)**: Quem deve ser informado (input unidirecional)

## Atores

### Sistemas
- **PC**: Portal Certification
- **PR**: Portal Relationship  
- **Store**: Loja de Aplicativos
- **LDM**: Life Device Management

### Pessoas
- **Parceiro**: Desenvolvedor/Empresa parceira
- **T.Rel**: Time de Relacionamento
- **T.Cert**: Time de Certificação

---

## 1. Gestão de Aplicativos

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Cadastrar aplicativo | R | I | - | I | A | I | - |
| Validar informações comerciais | C | A | - | - | R | - | - |
| Aprovar registro de aplicativo | I | A | - | - | R | I | - |
| Consultar aplicativos cadastrados | R | C | - | - | A | I | - |

## 2. Gestão de Configurações Técnicas

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Criar configuração técnica | C | - | R | A | I | - | - |
| Validar compatibilidade de terminais | - | - | R | A | I | - | - |
| Aprovar configuração técnica | I | - | A | R | I | - | - |
| Gerar prefixos lógicos de terminais | - | - | R | A | - | - | I |
| Consultar configurações disponíveis | R | C | C | A | I | - | - |

## 3. Gestão de Perfis Comerciais

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Criar perfil de aplicativo | R | C | - | I | A | I | - |
| Validar metadados comerciais | C | R | - | - | A | I | - |
| Definir categorização na loja | C | R | - | - | I | A | - |
| Promover perfil (pendente→revisao) | R | - | - | - | A | I | - |
| Aprovar perfil (revisao→producao) | I | A | - | - | R | I | - |
| Arquivar perfil | R | A | - | - | R | I | - |
| Consultar perfis por estado | R | C | - | - | A | I | - |

## 4. Gestão de Versões Técnicas

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Submeter versão para certificação | R | I | I | A | I | - | - |
| Executar testes de certificação | - | - | R | A | I | - | - |
| Validar compatibilidade técnica | - | - | R | A | I | - | - |
| Aprovar certificação técnica | I | - | A | R | I | - | - |
| Reprovar certificação (com feedback) | I | - | A | R | I | - | - |
| Realizar downsizing de APK | - | - | R | A | I | R | - |
| Registrar versão na loja (self-service) | - | - | R | C | I | A | - |
| Consultar status de certificação | R | C | C | A | I | I | - |

## 5. Distribuição Piloto

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Solicitar distribuição piloto | R | C | - | I | A | I | - |
| Validar elegibilidade para piloto | C | R | - | I | A | I | - |
| Aprovar distribuição piloto | I | A | - | - | R | I | - |
| Distribuir para dispositivos piloto | - | I | - | I | I | R | A |
| Notificar conclusão de piloto | I | I | - | - | A | R | I |

## 6. Distribuição Produção (B2B)

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Solicitar ativação produção | R | C | - | - | A | I | - |
| Validar campanha comercial | C | A | - | - | R | - | - |
| Aprovar liberação produção | I | A | - | - | R | I | - |
| Distribuir para dispositivos produção | - | I | - | - | I | R | A |
| Notificar disponibilidade produção | I | I | - | - | A | R | I |

## 7. Distribuição Produção (Self-Service)

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Ativar perfil para self-service | R | - | - | - | I | A | - |
| Disponibilizar na vitrine loja | - | - | - | - | I | A | - |
| Instalar aplicativo via loja | R | - | - | - | - | A | I |
| Notificar instalação concluída | I | - | - | - | - | A | I |

## 8. Gestão de Atualizações

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Submeter nova versão | R | I | I | A | I | - | - |
| Certificar nova versão | - | - | R | A | I | - | - |
| Validar impacto da atualização | C | R | C | I | A | I | - |
| Aprovar estratégia atualização | I | A | - | - | R | I | - |
| Executar atualização automática | - | - | - | - | I | R | A |
| Permitir atualização manual | - | - | - | - | I | A | I |

## 9. Consultas e Relatórios

| Atividade | Parceiro | T.Rel | T.Cert | PC | PR | Store | LDM |
|-----------|----------|-------|--------|----|----|-------|-----|
| Consultar aplicativos por categoria | R | - | - | - | I | A | - |
| Consultar aplicativos por terminal | R | C | - | A | I | R | - |
| Gerar relatórios de distribuição | C | A | C | I | R | I | I |
| Consultar histórico certificações | R | C | A | R | I | I | - |
| Monitorar status instalações | C | A | - | - | R | I | R |

---

## Resumo de Responsabilidades por Sistema

### Portal Certification (PC)
- **Accountable**: Configurações técnicas, certificações, prefixos lógicos
- **Responsible**: Testes técnicos, validações de compatibilidade, downsizing
- **Consulted**: Estratégias de atualização, relatórios técnicos

### Portal Relationship (PR)  
- **Accountable**: Aplicativos, perfis comerciais, aprovações de negócio
- **Responsible**: Validações comerciais, aprovações de campanha
- **Informed**: Todas as atividades técnicas

### Store (Loja de Aplicativos)
- **Accountable**: Categorização, distribuição, instalações self-service
- **Responsible**: Vitrine, consultas por categoria, notificações de instalação
- **Informed**: Todas as atividades de gestão

### LDM (Life Device Management)
- **Accountable**: Distribuição física para dispositivos
- **Responsible**: Instalação/atualização nos terminais
- **Informed**: Aprovações e configurações técnicas

---

## Resumo de Responsabilidades por Ator

### Parceiro
- **Responsible**: Todas as solicitações e consultas próprias
- **Consulted**: Validações técnicas e comerciais que impactam seus aplicativos

### Time Relacionamento  
- **Accountable**: Aprovações comerciais e estratégicas
- **Responsible**: Validações de campanha e impacto de negócio

### Time Certificação
- **Accountable**: Aprovações técnicas e certificações
- **Responsible**: Execução de testes e validações técnicas