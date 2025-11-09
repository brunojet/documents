# Estado Atual (As-Is)

Esta pasta contém toda a documentação relacionada ao sistema atual em produção.

## 📋 Inventário Disponível

### Arquitetura Atual
- ✅ `as-is.md` - Diagrama completo da arquitetura legacy (ServiceNow)
- ✅ `as-is.md` - Fluxo de dados de certificação existente
- ✅ `as-is.md` - Integrações em uso (ServiceNow, Assinador, LDM)
- ✅ `as-is.md` - Limitações técnicas identificadas detalhadamente

### APIs Existentes
- ✅ `as-is.md` - Documentação das APIs ServiceNow em uso
- ✅ `as-is.md` - Contratos de dados atuais (requisição de certificação)
- ✅ `as-is.md` - Dependências externas (Assinador de aplicativos)

### Processos de Negócio
- ✅ `as-is.md` - Mapeamento completo da jornada única de certificação
- ✅ `as-is.md` - Pontos de dor identificados (repreenchimento, bifurcação)
- ✅ `as-is.md` - Casos de uso existentes (loja pública vs privada)

### Interface do Cliente
- ✅ `as-is.md` - Funcionalidades atuais da loja no terminal
- ✅ `as-is.md` - Experiência do usuário documentada
- ✅ `as-is.md` - Limitações da interface ServiceNow

## 🎯 Propósito
Servir como baseline completa para compreender:
- ✅ O que funciona bem atualmente (interface da loja, busca, gestão de apps)
- ✅ Quais limitações precisam ser superadas (jornada monolítica, recertificação)
- ✅ Como os usuários interagem com o sistema hoje (3 abas, menu hamburger)
- ✅ Dependências que devem ser mantidas durante a migração (ServiceNow crítico)

## 📊 Principais Achados Documentados
- **Problema Central**: Jornada única mistura certificação técnica + perfil comercial
- **Concorrência Interna**: ServiceNow vs LDM para distribuição
- **Retrabalho**: Mesmo aplicativo recertificado para mudança simples (privada→pública) e atualização do perfil
- **Inconsistência**: Formulário manual gera perfis diferentes por terminal
- **Interface Melhorada**: Evolução de WebView ServiceNow (experiência ruim) para app nativo Android + APIs ServiceNow (estado atual)

## 🎯 Features Suportadas no Estado Atual

### 📱 Interface da Loja no Terminal
- **3 Abas Principais**: Navegação horizontal simples
  - 🌟 **Destaques**: Carrossel de banners + apps relacionados
  - 🔍 **Apps**: Busca e catálogo de aplicativos
  - 📦 **Meus Apps**: Gestão de apps instalados

### 🔍 Busca e Descoberta
- **Busca por nome**: Busca parcial por string
- **Filtros disponíveis**: 
  - Ramos e subramos (categorização)
  - Avaliação média (rating)
  - Região de atuação (segmentação geográfica)

### 📦 Gestão de Aplicativos
- **Instalação**: Download e instalação de apps
- **Atualização**: Update para versões mais recentes  
- **Remoção**: Desinstalação de aplicativos
- **Lista de instalados**: Visualização de apps do terminal

### ⚙️ Recursos Auxiliares
- **Menu hambúrguer**: Acesso a funcionalidades secundárias
  - 🔔 Notificações da loja
  - 📄 Termos de uso
  - ❓ FAQ / Ajuda ao usuário

### 🔧 Arquitetura Backend (ServiceNow)
- **Jornada única**: Certificação + publicação em um só processo
- **Dois tipos de loja**: Pública (ServiceNow) vs Privada (LDM)
- **Uso indevido da loja**: Sistema usado como "puxadinho" para certificação de apps privados (que nem deveriam passar pela loja)
- **Papel de LDM indevido**: As-is assumiu responsabilidades de distribuição que não deveria ter
- **Assinatura digital**: Integração com serviço de assinatura de APKs
- **Testes manuais**: Validação em dispositivos físicos de certificação

### ⚠️ Problemas Arquiteturais Identificados
- **Loja usada para certificação**: Apps privados passam pela loja só para serem certificados (não para distribuição)
- **Responsabilidades misturadas**: Sistema de loja fazendo papel de LDM (distribuição B2B)
- **Fluxo bifurcado**: Apps públicos (loja completa) vs privados (só certificação)

---
*✅ Análise completa do estado atual finalizada - `as-is.md` contém documentação detalhada*