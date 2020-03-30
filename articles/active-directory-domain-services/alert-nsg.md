---
title: Risolvere gli avvisi dei gruppi di sicurezza di rete in Azure AD DS Documenti Microsoft
description: Informazioni su come risolvere i problemi e risolvere gli avvisi di configurazione dei gruppi di sicurezza di rete per Servizi di dominio Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257994"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Problemi noti: Avvisi di configurazione di rete in Servizi di dominio Azure Active DirectoryKnown issues: Network configuration alerts in Azure Active Directory Domain Services

Per consentire ad applicazioni e servizi di comunicare correttamente con Servizi di dominio Azure Active Directory, è necessario che siano aperte porte di rete specifiche per consentire il flusso del traffico. In Azure è possibile controllare il flusso di traffico usando i gruppi di sicurezza di rete. Lo stato di integrità di un dominio gestito di Servizi di dominio Active Directory di Azure mostra un avviso se le regole del gruppo di sicurezza di rete necessarie non sono obbligatorie.

Questo articolo consente di comprendere e risolvere gli avvisi comuni per i problemi di configurazione dei gruppi di sicurezza di rete.

## <a name="alert-aadds104-network-error"></a>Avviso AADDS104: errore di rete

### <a name="alert-message"></a>Messaggio di avviso

*Microsoft non è in grado di raggiungere i controller di dominio per questo dominio gestito. Ciò può verificarsi se un gruppo di sicurezza di rete (NSG) configurato nella rete virtuale blocca l'accesso al dominio gestito. Un altro possibile motivo è se esiste un percorso definito dall'utente che blocca il traffico in ingresso da Internet.*

Le regole dei gruppi di sicurezza di rete non valide sono la causa più comune di errori di rete per Servizi di dominio Active Directory di Azure.Invalid network security group rules are the most common cause of network errors for Azure AD DS. Il gruppo di sicurezza di rete per la rete virtuale deve consentire l'accesso a porte e protocolli specifici. Se queste porte sono bloccate, la piattaforma Azure non può monitorare o aggiornare il dominio gestito. Viene influenzata anche la sincronizzazione tra la directory di Azure AD e il dominio gestito di Servizi di dominio Active Directory di Azure.The Azure AD directory and Azure AD DS managed domain is also impacted. Assicurarsi di mantenere aperte le porte predefinite per evitare interruzioni del servizio.

## <a name="default-security-rules"></a>Regole di sicurezza predefinite

Le regole di sicurezza predefinite in ingresso e in uscita seguenti vengono applicate al gruppo di sicurezza di rete per un dominio gestito di Servizi di dominio Active Directory di Azure.The following default inbound and outbound security rules are applied to the network security group for an Azure AD DS managed domain. Queste regole mantengono sicura Servizi di dominio Active Directory di Azure e consentono alla piattaforma Azure di monitorare, gestire e aggiornare il dominio gestito. È inoltre possibile disporre di una regola aggiuntiva che consente il traffico in ingresso se si [configura LDAP sicuro][configure-ldaps].

### <a name="inbound-security-rules"></a>Regole di sicurezza in ingresso

| Priorità | Nome | Porta | Protocollo | Source (Sorgente) | Destination | Azione |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Qualsiasi | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Qualsiasi | Allow |
| 301      | AllowPSRemoting (AllowPSRemoting) | 5986| TCP | AzureActiveDirectoryDomainServices | Qualsiasi | Allow |
| 65000    | AllVnetInBound | Qualsiasi | Qualsiasi | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Qualsiasi | Qualsiasi | AzureLoadBalancer | Qualsiasi | Allow |
| 65500    | DenyAllInBound | Qualsiasi | Qualsiasi | Qualsiasi | Qualsiasi | Nega |

### <a name="outbound-security-rules"></a>Regole di sicurezza in uscita

| Priorità | Nome | Porta | Protocollo | Source (Sorgente) | Destination | Azione |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Qualsiasi | Qualsiasi | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | Qualsiasi | Qualsiasi |  Qualsiasi | Internet | Allow |
| 65500    | DenyAllOutBound | Qualsiasi | Qualsiasi | Qualsiasi | Qualsiasi | Nega |

>[!NOTE]
> Servizi di dominio Active Directory di Azure richiede un accesso in uscita senza restrizioni dalla rete virtuale. Non è consigliabile creare regole aggiuntive che limitano l'accesso in uscita per la rete virtuale.

## <a name="verify-and-edit-existing-security-rules"></a>Verificare e modificare le regole di sicurezza esistenti

Per verificare le regole di sicurezza esistenti e assicurarsi che le porte predefinite siano aperte, completare la procedura seguente:

1. Nel portale di Azure cercare e selezionare Gruppi di **sicurezza di rete**.
1. Scegliere il gruppo di sicurezza di rete associato al dominio gestito, ad esempio *AADDS-contoso.com-NSG*.
1. Nella pagina **Panoramica** vengono visualizzate le regole di sicurezza in ingresso e in uscita esistenti.

    Esaminare le regole in ingresso e in uscita e confrontarle con l'elenco delle regole richieste nella sezione precedente. Se necessario, selezionare ed eliminare eventuali regole personalizzate che bloccano il traffico richiesto. Se manca una delle regole necessarie, aggiungere una regola nella sezione successiva.

    Dopo aver aggiunto o eliminato regole per consentire il traffico richiesto, l'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

### <a name="add-a-security-rule"></a>Aggiungere una regola di sicurezza

La procedura seguente illustra come aggiungere una regola di sicurezza mancante:

1. Nel portale di Azure cercare e selezionare Gruppi di **sicurezza di rete**.
1. Scegliere il gruppo di sicurezza di rete associato al dominio gestito, ad esempio *AADDS-contoso.com-NSG*.
1. In **Impostazioni** nel riquadro sinistro fare clic su Regole di *sicurezza in ingresso* o Regole di sicurezza in *uscita* a seconda della regola da aggiungere.
1. Selezionare **Aggiungi**, quindi creare la regola richiesta in base alla porta, al protocollo, alla direzione e così via. Quando si è pronti, selezionare **OK**.

L'aggiunta e la visualizzazione della regola di sicurezza nell'elenco richiede alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
