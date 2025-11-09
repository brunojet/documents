# ADR-001: Backend Especializado para Loja de Aplicativos

## Status
**Proposto** - Aguardando aprovação

## Contexto

### Situação Atual
O sistema atual (ServiceNow) apresenta uma **jornada monolítica** que mistura:
- Certificação técnica de aplicativos
- Perfil comercial (vitrine)
- Distribuição para terminais
- Aprovações de negócio

### Problemas Identificados
1. **Retrabalho desnecessário**: Mudança de perfil comercial força nova certificação técnica completa
2. **Jornada única confusa**: Mesmo processo para aspectos técnicos e comerciais distintos
3. **Concorrência interna**: ServiceNow vs LDM para distribuição B2B
4. **Interface limitada**: Experiência não otimizada para descoberta de aplicativos
5. **Inconsistência**: Mesmo aplicativo com perfis diferentes por modelo de terminal

### Motivação para Mudança
- **Separação de responsabilidades**: Certificação ≠ Vitrine ≠ Distribuição
- **Time-to-market**: Mudanças comerciais sem recertificação
- **Experiência do usuário**: Busca avançada e descoberta otimizada
- **Arquitetura evolutiva**: Base técnica para expansão futura

## Decisão

### Proposta: Backend Especializado da Loja
Criar um sistema especializado focado **exclusivamente** em:
- **Vitrine de aplicativos**: Catálogo, busca e descoberta
- **Perfis comerciais**: Metadados para apresentação
- **APIs otimizadas**: Performance mobile e experiência superior

### Escopo Delimitado
**✅ Responsabilidades do Backend da Loja:**
- Gestão de perfis comerciais (pendente → revisão → produção → arquivado)
- Gestão de versões técnicas (pendente → piloto → produção → arquivado)  
- API de busca avançada com filtros combinados
- Configuração de terminais e identificação automática
- Catálogo contextualizado por modelo de terminal

**❌ NÃO será responsabilidade do Backend da Loja:**
- Certificação técnica (Portal Certificação)
- Aprovações comerciais (Portal Relacionamento)  
- Distribuição B2B (LDM)
- Workflow de aprovação
- Apps privados (só apps destinados à loja pública)

### Arquitetura Proposta

#### Separação de Sistemas
- **Portal Certificação (PC)**: Certificação técnica
- **Portal Relacionamento (PR)**: Aprovações comerciais
- **Backend da Loja**: Vitrine e catálogo especializado

#### Características Técnicas
- **Chave composta**: `(aplicativo + configuracao_terminal + estagio)` para precisão contextual
- **Estados independentes**: Perfis e versões com ciclos de vida separados
- **Processamento assíncrono**: Downsizing automático por modelo de terminal
- **REST semântico**: POST /busca para operações complexas de filtro
- **Deduplicação inteligente**: Upload por hash para otimização

## Estratégia de Implementação

### Migração Incremental Proposta
**Rejeição da abordagem big-bang** por:
- Alto risco de migração
- Redundância sem valor imediato
- ROI negativo

### Abordagem de Feature Flags
**Duas dimensões da migração:**

1. **Alimentação**: ServiceNow → Backend da Loja via orquestrador
2. **Consumo**: Clientes → Backend da Loja via feature flags

### Fases Propostas

#### Fase 1: Portal Dev (Recuperação de Perfis)
- **Escopo**: Portal Dev consome backend para buscar perfis
- **Valor**: Observabilidade real e validação de APIs
- ⚠️ **ALERTA**: Alta complexidade para valor limitado (duplicação ServiceNow)

#### Fase 2: Clientes Finais (App Terminais)
- **Escopo**: Apps nos terminais consomem backend diretamente  
- **Valor**: Performance superior e funcionalidades avançadas
- **Controle**: Feature flags granulares por aplicativo/terminal

## Consequências

### Benefícios Esperados
- **🔥 Sem recertificação**: Mudanças de perfil comercial independentes de certificação técnica
- **⚡ Performance**: APIs otimizadas para mobile com dados contextualizados
- **🎯 Responsabilidades claras**: Cada sistema faz apenas o que deve (conforme matriz RACI)
- **🔄 Flexibilidade**: Estados independentes (revisão/produção para perfis, piloto/produção para versões)
- **📱 Experiência**: Busca avançada vs interface básica atual

### Riscos e Mitigações
- **Risco**: Complexidade de integração entre 3 sistemas
  - **Mitigação**: Matriz RACI clara e APIs bem definidas
- **Risco**: Fase 1 com baixo ROI
  - **Mitigação**: Reavaliação da necessidade da Fase 1
- **Risco**: Backend implementa apenas fração das funcionalidades
  - **Mitigação**: Comunicação clara de que é ponto de partida evolutivo

### Impactos
- **Time de desenvolvimento**: Novo sistema para desenvolver e manter
- **Integrações**: Necessidade de orquestração ServiceNow → Backend
- **Evolução**: Time de negócios precisará definir próximos incrementos
- **Governance**: Matriz RACI entre PC/PR/Loja deve ser respeitada

## Contexto Evolutivo

### Ponto de Partida, Não Destino
- **Escopo atual**: Apenas fração das funcionalidades ServiceNow
- **Expectativa**: Time de negócios evolui funcionalidades a partir desta base
- **Decisões futuras**: Cada nova feature avaliada por valor vs esforço
- **Base sólida**: Fundação técnica adequada para expansão

### Roadmap Futuro (Indicativo)
- Funcionalidades avançadas de recomendação
- Analytics de uso e descoberta
- Personalização por perfil de usuário
- Integrações com sistemas externos

## Referências
- **AS-IS**: Análise completa em `as-is/as-is.md`
- **Especificações técnicas**: APIs detalhadas em `to-be/02-endpoints-store.md`
- **Matriz RACI**: Responsabilidades em `to-be/01-raci-matrix-responsabilidades.md`
- **Estratégia de migração**: Detalhes em `transicao/02-estrategia-migracao-feature-flags.md`

---
**Data**: Novembro 2025  
**Autor**: Time de Arquitetura  
**Revisão**: Aguardando validação das propostas  
**Próximo ADR**: Decisões de implementação técnica (quando aprovado)