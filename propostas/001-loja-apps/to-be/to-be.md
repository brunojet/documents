## Contexto ToBe

Vamos documentar a exposição da API de uma loja de aplicativos para dispositivos POS Android. Para melhorar o contexto, criaremos diagramas de sequência apresentando as jornadas pretendidas. Vamos começar descrevendo os atores e sistemas envolvidos:

### Atores
- **Parceiro**: Usuário de software de empresa parceira que pretende disponibilizar aplicativos integrados ao nosso aplicativo de pagamento.
- **Time relacionamento**: Responsável por gerenciar o relacionamento comercial com parceiros, validar aspectos contratuais e aprovar cadastros de aplicativos e perfis.
- **Time certificação**: Responsável por validar e certificar tecnicamente os aplicativos submetidos pelos parceiros.
- **Time loja**: Responsável por gerenciar a loja de aplicativos.

## Sistemas
- **Portal de relacionamento**: Sistema onde os parceiros gerenciam o relacionamento comercial, cadastram aplicativos e criam perfis de aplicativo para a loja. Interage com a loja de aplicativos e LDM.
- **Portal de certificação**: Sistema onde os parceiros submetem seus aplicativos para certificação. Interage com a loja de aplicativos e LDM.
- **Assinador de aplicativos**: Sistema externo responsável por assinar digitalmente os APKs submetidos para certificação.
- **Loja de aplicativos**: Sistema que exibe metadados dos aplicativos certificados para comerciantes individuais, funcionando como vitrine para seleção e download de aplicações.
- **Sistema LDM**: Sistema responsável pela distribuição e entrega dos aplicativos certificados para os dispositivos POS Android.
- **Dispositivos Piloto**: Grupo específico de dispositivos POS Android utilizados para testes controlados antes da liberação em produção.
- **Dispositivos Produção**: Todos os dispositivos POS Android elegíveis onde os aplicativos são instalados para uso pelos usuários finais.
- **Dispositivo POS Android**: Dispositivo onde os aplicativos certificados são instalados e utilizados pelos usuários finais.

## Jornadas

### Configuração do ambiente - cadastro de configurações técnicas

Configuração inicial das configurações técnicas, onde o time de certificação cadastra os modelos de terminais suportados, tipos de integração e configurações de terminais no Portal de certificação. Essas configurações serão utilizadas pelos parceiros durante o cadastro de aplicativos e certificações técnicas através de consultas diretas ao Portal de Certificação.

```mermaid
sequenceDiagram
        autonumber
        actor t_cert as Time certificação
        participant p_cert as Portal de certificação
        participant p_store as Loja de aplicativos
        t_cert->>p_cert: cadastra modelos de terminais suportados
        t_cert->>p_cert: cadastra tipos de integração suportados
        t_cert->>p_cert: cadastra configurações de terminal
        note over t_cert,p_cert: modelo de terminal + tipo de integração
        loop Cadastro de prefixos de número lógico
            t_cert->>p_cert: cadastra prefixo de número lógico para configuração
            note over t_cert,p_cert: prefixo (ex: "AA", "BB") + id configuração terminal
        end
        alt Sincronização com loja (para aplicativos com suporte à loja)
            loop Configurações de terminal com prefixos
                p_cert->>p_store: cadastra configuração de terminal
                note over p_cert,p_store: modelo, tipo integração, prefixo número lógico
                p_store->>p_cert: retorna id configuração de terminal
                loop Prefixos da configuração
                    p_cert->>p_store: associa prefixo à configuração
                    note over p_cert,p_store: prefixo + id configuração
                    p_store->>p_cert: confirma associação
                end
            end
        end
```

### Configuração do ambiente - cadastro de filtros

Configuração específica para a loja de aplicativos, onde o time da loja cadastra os tipos de filtro de aplicativo e filtros para categorização e organização dos aplicativos na loja self-service.

```mermaid
sequenceDiagram
        autonumber
        actor t_store as Time loja
        participant p_store as Loja de aplicativos
        t_store->>p_store: cadastra tipos de filtro de aplicativo
        t_store->>p_store: cadastra filtros de aplicativo
        note over t_store,p_store: específico para categorização na loja self-service

```

### Cadastro de aplicativo

Viabiliza o cadastro de um aplicativo por um parceiro com dois fluxos distintos: cadastro obrigatório no LDM para distribuição direta (grandes redes/B2B) e cadastro opcional na Loja para disponibilização via self-service (comerciantes individuais). O parceiro seleciona configurações de terminais (fornecidas pelo Portal de Certificação) e filtros de categorização (fornecidos pela Loja) apenas se optar pela loja. Após validação pelo time de relacionamento, o aplicativo é sempre registrado no LDM e, condicionalmente, na Loja conforme escolha do parceiro.

```mermaid
sequenceDiagram
        autonumber
        actor partner as Parceiro
        participant p_rel as Portal de relacionamento
        actor t_rel as Time relacionamento
        participant p_cert as Portal de certificação
        participant p_store as Loja de aplicativos
        participant ldm as Sistema LDM

        partner->>p_rel: inicia cadastro de aplicativo
        p_rel->>p_cert: recupera configurações de terminais disponíveis
        p_cert->>p_rel: retorna configurações de terminais
        p_rel->>partner: exibe opções de configuração
        partner->>partner: seleciona uma ou mais configurações de terminal
        alt Parceiro quer loja
            note over p_rel,partner: necessário para comerciantes individuais/self-service
            p_rel->>p_store: recupera lista de tipos e filtros de aplicativo
            note over p_rel,p_store: filtros de categorização definidos pelo time da loja
            p_rel->>partner: exibe opções de configuração
            partner->>partner: seleciona uma ou mais filtros de aplicativo
        end
        partner->>p_rel: submete cadastro de aplicativo para validação
        p_rel->>t_rel: notifica novo cadastro de aplicativo
        note over p_rel,t_rel: validação de aspectos comerciais, contratuais e dados básicos
        t_rel->>p_rel: valida e aprova cadastro de aplicativo
        p_rel->>ldm: cadastra aplicativo
        note over p_rel,ldm: obrigatório para todas as distribuições (B2B direto e self-service)
        ldm->>p_rel: retorna cadastro de aplicativo
        alt Parceiro quer loja
            note over p_rel,p_store: opcional - só para disponibilização via self-service
            p_rel->>p_store: cadastra aplicativo na loja
            p_store->>p_rel: retorna id cadastro de aplicativo
            loop Configurações de aplicativo
                p_rel->>p_store: cadastra configurações de aplicativo
                note over p_rel,p_store: aplicativo + configuração de terminal + nome pacote android
                p_store->>p_rel: retorna id configuração aplicativo
            end
        end
```

### Cadastro de perfil de aplicativo

Permite ao parceiro enriquecer o perfil de um aplicativo já cadastrado com suporte à loja, adicionando metadados para exibição como ícones, screenshots, descrições detalhadas, changelogs e outras informações visuais e descritivas que serão apresentadas aos usuários finais. O processo inclui validação editorial pelo time de relacionamento, otimização de storage através de deduplicação baseada em hash dos arquivos, processamento assíncrono das imagens para diferentes modelos de terminais, e controle manual de publicação para flexibilidade futura de validações adicionais.

```mermaid
sequenceDiagram
        autonumber
        actor partner as Parceiro
        participant p_rel as Portal de relacionamento
        actor t_rel as Time relacionamento
        participant p_store as Loja de aplicativos

        partner->>p_rel: solicita lista de aplicativos cadastrados
        p_rel->>p_rel: recupera aplicativos locais do parceiro
        note over p_rel: filtro: somente aplicativos com suporte à loja
        p_rel->>partner: exibe aplicativos disponíveis para edição de perfil
        partner->>partner: seleciona aplicativo para editar
        p_rel->>p_store: recupera perfil atual do aplicativo
        p_rel->>partner: exibe perfil atual para edição
        partner->>partner: edita perfil de aplicativo
        partner->>p_rel: submete metadados do perfil
        note over partner,p_rel: nome, descrição, changelogs, categoria
        partner->>p_rel: faz upload de ícones
        partner->>p_rel: faz upload de screenshots
        p_rel->>t_rel: notifica atualização de perfil de aplicativo
        t_rel->>p_rel: valida e aprova perfil de aplicativo
        note over t_rel,p_rel: validação editorial no Portal: qualidade de imagens, adequação de conteúdo, informações de contato
        p_rel->>p_store: atualiza metadados do perfil de aplicativo
        note over p_rel,p_store: dados textuais + metadados das imagens (nome, hash, tamanho)
        p_store->>p_rel: confirma atualização dos metadados
        note over p_store,p_rel: resposta inclui status: completo ou lista de URLs pré-assinadas para uploads pendentes
        alt Possui pendência de upload
            p_store->>p_store: cadastro de perfil de aplicativo incompleto (estágio pendente)
            loop Itens pendentes de upload
                p_rel->>p_store: faz upload dos itens pendentes
            end
            note over p_rel,p_store: deduplicação por hash - só arquivos não existentes na Loja
        end
        p_store->>p_store: cadastro de perfil de aplicativo completo (estágio revisao)
        note over p_store: processamento assíncrono concluído - downsizing para todos os modelos de terminais
        p_store->>p_rel: confirma perfil de aplicativo completo
        p_rel->>p_store: solicita publicação do perfil de aplicativo (estágio producao)
        note over p_rel: ação manual do Portal - flexibilidade para futuras validações
        p_store->>p_store: Atualiza perfil de aplicativo no catálogo de aplicativos
        p_store->>p_rel: confirma publicação do perfil de aplicativo
        p_rel->>partner: confirma atualização do perfil de aplicativo
```

### Certificação técnica

Permite ao parceiro submeter executáveis (APKs) para certificação técnica de aplicativos já cadastrados. A certificação é feita por configuração específica (aplicativo + modelo de terminal), onde cada configuração passa por validação técnica independente. O processo inclui upload do executável, validação de integridade, assinatura digital via sistema externo, instalação no POS de certificação via LDM, e testes funcionais em dispositivos produtivos pelo time de certificação.

**Registro automático na loja**: Para aplicativos que possuem suporte à loja (self-service), após a aprovação da certificação técnica, a versão do aplicativo é automaticamente registrada no sistema da loja com todos os metadados necessários (APK, tamanho, referências MDM). Esta versão fica no estado "certificado" - registrada mas não ativada - permitindo que seja posteriormente promovida através dos fluxos de gestão de perfil da loja.

```mermaid
sequenceDiagram
        autonumber
        actor partner as Parceiro
        actor t_cert as Time certificação
        participant p_cert as Portal de certificação
        participant p_rel as Portal de relacionamento
        participant assinador as Assinador de aplicativos
        participant ldm as Sistema LDM
        participant pos_cert as POS Certificação

        partner->>p_cert: solicita lista de aplicativos cadastrados
        p_cert->>p_rel: recupera aplicativos do parceiro
        p_rel->>p_cert: retorna lista de aplicativos
        p_cert->>partner: exibe aplicativos disponíveis para certificação
        partner->>partner: seleciona aplicativo para certificar
        p_cert->>p_cert: recupera configurações do aplicativo (dados técnicos locais)
        p_cert->>partner: exibe configurações disponíveis para certificação
        partner->>partner: seleciona configuração específica (aplicativo + terminal)
        partner->>p_cert: faz upload do executável (APK)
        note over partner,p_cert: arquivo APK + metadados técnicos
        p_cert->>p_cert: valida formato e integridade do arquivo
        p_cert->>assinador: submete APK para assinatura digital
        assinador->>p_cert: retorna APK assinado
        p_cert->>ldm: instala aplicativo assinado
        ldm->>pos_cert: disponibiliza aplicativo no POS de certificação
        pos_cert->>ldm: confirma instalação no dispositivo
        ldm->>p_cert: confirma disponibilização para certificação
        p_cert->>t_cert: notifica aplicativo disponível para certificação
        note over t_cert,pos_cert: certificação em dispositivos produtivos
        t_cert->>pos_cert: executa testes funcionais, compatibilidade, segurança
        t_cert->>p_cert: aprova certificação técnica
        alt Aplicativo com suporte à loja
            note over p_cert,p_store: registro de versão técnica para self-service
            p_cert->>p_store: cadastra versão aplicativo (metadados APK, tamanho, referência MDM)
            note over p_cert,p_store: versão registrada sem ativação - estado "certificado"
            p_store->>p_cert: confirma registro da versão
        end
        p_cert->>partner: confirma certificação concluída
```

### Distribuição em piloto (b2b)

Permite ao parceiro criar campanhas de distribuição para aplicativos já certificados, configurando grupos de dispositivos para pilotagem. O processo inclui seleção da versão certificada, definição do grupo piloto, distribuição automática via LDM, execução do piloto com número mínimo de terminais, validação dos resultados e confirmação da conclusão do piloto.

```mermaid
sequenceDiagram
        autonumber
        actor partner as Parceiro
        participant p_rel as Portal de relacionamento
        participant p_cert as Portal de certificação
        participant ldm as Sistema LDM
        participant pos_piloto as Dispositivos Piloto

        partner->>p_rel: solicita lista de aplicativos certificados
        p_rel->>p_cert: recupera aplicativos certificados do parceiro
        p_cert->>p_rel: retorna lista com versões certificadas
        p_rel->>partner: exibe aplicativos disponíveis para distribuição
        partner->>partner: seleciona aplicativo e versão certificada
        partner->>p_rel: cria nova campanha de distribuição
        partner->>p_rel: define grupo de dispositivos para piloto
        note over partner,p_rel: seleção de terminais específicos para teste
        partner->>p_rel: configura parâmetros do piloto
        note over partner,p_rel: número mínimo de terminais, critérios de sucesso
        partner->>p_rel: submete campanha para execução
        p_rel->>ldm: distribui versão aplicativo para dispositivos do piloto no ldm
        ldm->>pos_piloto: distribui aplicativo para dispositivos do piloto
        note over p_rel,pos_piloto: distribuição para terminais específicos do piloto
        p_rel->>p_cert: atualiza aplicativo para estágio "piloto"
        ldm->>p_cert: reporta downloads e atualizações dos dispositivos do piloto
        p_cert->>p_cert: consolida métricas do piloto (downloads + atualizações)
        p_cert->>p_rel: sinaliza início do piloto com métricas
        p_rel->>partner: notifica início do piloto
        partner->>p_rel: avalia resultados do piloto
        partner->>p_rel: confirma piloto concluído
        p_rel->>p_cert: atualiza aplicativo para estágio "piloto_aprovado"
        p_rel->>partner: confirma piloto aprovado
```

### Ativação em produção (b2b)

Permite ao parceiro ativar aplicativos que já passaram pelo piloto com sucesso, liberando-os para distribuição em todos os dispositivos elegíveis em produção. O processo inclui seleção de versões com piloto aprovado, aprovação conjunta do time de relacionamento, e distribuição automática via LDM para o ambiente produtivo.

```mermaid
sequenceDiagram
        autonumber
        actor partner as Parceiro
        actor t_rel as Time relacionamento
        participant p_rel as Portal de relacionamento
        participant p_cert as Portal de certificação
        participant ldm as Sistema LDM
        participant pos_prod as Dispositivos Produção

        partner->>p_rel: solicita lista de aplicativos com piloto aprovado
        p_rel->>p_cert: recupera aplicativos em estágio "piloto_aprovado"
        p_cert->>p_rel: retorna lista com versões aprovadas no piloto
        p_rel->>partner: exibe aplicativos disponíveis para produção
        partner->>partner: seleciona versão para ativar em produção
        partner->>p_rel: solicita aprovação para produção
        p_rel->>t_rel: notifica solicitação de liberação para produção
        t_rel->>p_rel: avalia campanha e aprova para produção
        partner->>p_rel: confirma liberação para produção
        p_rel->>ldm: distribui versão aplicativo para dispositivos de produção no ldm
        ldm->>pos_prod: distribui aplicativo para dispositivos de produção
        note over p_rel,pos_prod: distribuição para todos dispositivos elegíveis
        p_rel->>p_cert: atualiza aplicativo para estágio "producao"
        p_rel->>partner: notifica aplicativo disponível em produção
```

### Distribuição em piloto (self-service)

Permite ao parceiro criar campanhas de distribuição para aplicativos já certificados na loja self-service, configurando disponibilização controlada para comerciantes selecionados. O processo é híbrido: comerciantes piloto sem o aplicativo verão a nova versão na loja substituindo a atual, enquanto comerciantes piloto que já possuem o aplicativo instalado receberão a atualização automaticamente via LDM.

```mermaid
sequenceDiagram
        autonumber
        actor partner as Parceiro
        participant p_rel as Portal de relacionamento
        participant p_cert as Portal de certificação
        participant p_store as Loja de aplicativos
        participant ldm as Sistema LDM
        participant pos_prod as Dispositivos Produção (com app)

        partner->>p_rel: solicita lista de aplicativos certificados para loja
        p_rel->>p_cert: recupera aplicativos certificados com suporte à loja
        p_cert->>p_rel: retorna lista com versões certificadas
        p_rel->>partner: exibe aplicativos disponíveis para distribuição na loja
        partner->>partner: seleciona aplicativo e versão certificada
        partner->>p_rel: cria nova campanha de distribuição self-service
        partner->>p_rel: define grupo de comerciantes para piloto
        note over partner,p_rel: seleção de comerciantes específicos para teste
        partner->>p_rel: configura parâmetros do piloto
        note over partner,p_rel: número mínimo de downloads, critérios de sucesso
        partner->>p_rel: submete campanha para execução
        p_rel->>ldm: distribui versão aplicativo para dispositivos do piloto no ldm
        ldm->>pos_prod: distribui aplicativo para dispositivos do piloto com app
        note over p_rel,pos_prod: atualização automática para quem já tem instalado
        p_rel->>p_store: configura versão aplicativo para piloto
        note over p_rel,p_store: aplicativo fica visível para dispositivos do piloto
        p_rel->>p_cert: atualiza aplicativo para estágio "piloto_loja"
        ldm->>p_cert: reporta downloads e atualizações dos dispositivos do piloto
        p_cert->>p_cert: consolida métricas do piloto (downloads + atualizações)
        p_cert->>p_rel: sinaliza início do piloto com métricas
        p_rel->>partner: notifica início do piloto
        partner->>p_rel: avalia resultados do piloto
        partner->>p_rel: confirma piloto concluído
        p_rel->>p_cert: atualiza aplicativo para estágio "piloto_aprovado"
        p_rel->>partner: confirma piloto aprovado
```

### Ativação em produção (self-service)

Permite ao parceiro ativar aplicativos que já passaram pelo piloto na loja com sucesso, liberando-os para todos os comerciantes. O processo é híbrido: a nova versão substitui a atual na loja para todos os comerciantes sem o aplicativo, enquanto comerciantes que já possuem o aplicativo instalado recebem a atualização automaticamente via LDM.

```mermaid
sequenceDiagram
        autonumber
        actor partner as Parceiro
        actor t_rel as Time relacionamento
        participant p_rel as Portal de relacionamento
        participant p_cert as Portal de certificação
        participant p_store as Loja de aplicativos
        participant ldm as Sistema LDM
        participant pos_prod as Dispositivos Produção (com app)

        partner->>p_rel: solicita lista de aplicativos com piloto loja aprovado
        p_rel->>p_cert: recupera aplicativos em estágio "piloto_aprovado"
        p_cert->>p_rel: retorna lista com versões aprovadas no piloto
        p_rel->>partner: exibe aplicativos disponíveis para produção na loja
        partner->>partner: seleciona versão para ativar na loja
        partner->>p_rel: solicita aprovação para produção na loja
        p_rel->>t_rel: notifica solicitação de liberação para loja
        t_rel->>p_rel: avalia campanha e aprova para loja
        partner->>p_rel: confirma liberação para loja
        p_rel->>ldm: distribui versão aplicativo para dispositivos de produção no ldm
        ldm->>pos_prod: distribui aplicativo para dispositivos de produção com app
        note over p_rel,pos_prod: atualização automática para quem já tem instalado
        p_rel->>p_store: configura versão aplicativo para produção na loja
        note over p_rel,p_store: aplicativo fica visível para dispositivos de produção
        p_rel->>p_cert: atualiza aplicativo para estágio "producao"
        p_rel->>partner: notifica aplicativo disponível na loja
```

### Gestão de terminais em produção

Permite ao parceiro gerenciar a distribuição de aplicativos já em produção para novos terminais, seja para atender novos clientes ou expandir a base de terminais de clientes existentes. O processo inclui seleção da versão produtiva, definição de novos terminais para associação, remoção de terminais quando necessário, e distribuição direta via LDM sem necessidade de pilotagem.

```mermaid
sequenceDiagram
        autonumber
        actor partner as Parceiro
        participant p_rel as Portal de relacionamento
        participant p_cert as Portal de certificação
        participant ldm as Sistema LDM
        participant pos_novos as Novos Dispositivos
        participant pos_existentes as Dispositivos Existentes

        partner->>p_rel: solicita lista de aplicativos em produção
        p_rel->>p_cert: consulta aplicativos produtivos do parceiro
        p_cert->>p_rel: retorna aplicativos em produção
        p_rel->>partner: exibe versões disponíveis em produção
        partner->>partner: seleciona versão produtiva para gestão
        p_rel->>ldm: recupera terminais associados à versão
        ldm->>p_rel: retorna lista de terminais atuais
        p_rel->>partner: exibe terminais associados e disponíveis
        alt Adicionar novos terminais
            partner->>p_rel: seleciona novos terminais para associar
            p_rel->>ldm: associa novos terminais à versão produtiva
            ldm->>pos_novos: distribui aplicativo para novos terminais
            pos_novos->>ldm: confirma instalação nos novos dispositivos
            ldm->>p_rel: confirma associação dos novos terminais
            p_rel->>p_cert: atualiza registro de distribuição
        end
        alt Remover terminais existentes
            partner->>p_rel: seleciona terminais para remover
            p_rel->>ldm: remove associação de terminais
            ldm->>pos_existentes: desinstala aplicativo dos terminais removidos
            pos_existentes->>ldm: confirma remoção dos dispositivos
            ldm->>p_rel: confirma remoção dos terminais
            p_rel->>p_cert: atualiza registro de distribuição
        end
        p_rel->>partner: confirma gestão de terminais concluída
```
