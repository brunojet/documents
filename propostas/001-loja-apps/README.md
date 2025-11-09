# Documentação do Projeto Store - Estrutura Organizacional

Este projeto está estruturado em três grandes áreas para melhor organização e compreensão:

## 📁 as-is/
Documentação do estado atual do sistema. Contém análise completa do sistema legado ServiceNow em produção.

**Conteúdo:**
- `as-is.md` - Análise detalhada da arquitetura atual, limitações e problemas identificados
- `README.md` - Resumo executivo dos achados e features atuais
- Jornada única de certificação (problema central)
- Interface atual da loja no terminal
- Limitações técnicas e de negócio documentadas

## 📁 to-be/
Propostas para o estado futuro desejado. Contém especificações do novo ecossistema com separação PC/PR/Loja.

**Conteúdo:**
- `01-raci-matrix-responsabilidades.md` - Proposta de matriz RACI definindo responsabilidades entre Portal Certificação, Portal Relacionamento e Loja
- `02-endpoints-store.md` - Proposta de especificação da API REST do backend da Loja
- `to-be.md` - Propostas de jornadas futuras com separação de responsabilidades
- `README.md` - Resumo das propostas de entrega
- Foco: Backend da Loja como **base inicial proposta** para evolução futura

## 📁 transicao/
Propostas estratégicas de migração inteligente. Contém estratégias propostas de alimentação e consumo do novo backend.

**Conteúdo:**
- `01-proposta-inicial-transicao.md` - Proposta de como ServiceNow alimenta o backend da Loja via orquestrador
- `02-estrategia-migracao-feature-flags.md` - Proposta de como clientes consomem o backend via feature flags
- `README.md` - Consolidação das propostas estratégicas
- Proposta central: **Migração incremental** vs big-bang rejeitado
- Contexto: Backend como fração das funcionalidades (ponto de partida evolutivo proposto)

## 🎯 Objetivo
Esta estrutura facilita:
- **Stakeholders** - Visão clara do que existe (as-is), o que será construído (to-be) e como migrar (transicao)
- **Desenvolvedores** - Separação clara entre análise atual, especificações futuras e estratégia de implementação
- **Gestores** - Foco nos aspectos estratégicos de migração incremental com feature flags
- **Auditoria** - Rastro completo das decisões arquiteturais e rejeição de abordagens big-bang
- **Time de Negócios** - Contexto claro sobre backend como base inicial para evolução da jornada da loja

## 🚨 Propostas e Decisões em Análise
- **❌ Big-bang rejeitado**: Alto risco, sem valor imediato
- **📋 Proposta: Migração incremental**: Feature flags e fases controladas (em avaliação)
- **⚠️ Escopo limitado**: Backend implementa apenas fração do ServiceNow (vitrine, catálogo, busca)
- **🚀 Proposta evolutiva**: Base técnica para expansão futura conforme demanda de negócio

---
*Última atualização: Novembro 2025*
*Status: Propostas em análise para projeto Store - aguardando validação e aprovação*