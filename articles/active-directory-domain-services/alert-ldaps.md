---
title: Risolvere gli avvisi LDAP sicuri in Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come risolvere i problemi relativi e risolvere gli avvisi comuni con LDAP sicuro per Servizi di dominio Azure Active Directory.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258042"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Problemi noti: Avvisi LDAP sicuri in Servizi di dominio Azure Active DirectoryKnown issues: Secure LDAP alerts in Azure Active Directory Domain Services

Le applicazioni e i servizi che usano LDAP (Lightweight Directory Access Protocol) per comunicare con Servizi di dominio Azure Active Directory (Azure AD DS) possono essere [configurati per l'uso](tutorial-configure-ldaps.md)di LDAP sicuro. Affinché LDAP protetto funzioni correttamente, è necessario che siano aperti un certificato appropriato e le porte di rete necessarie.

Questo articolo consente di comprendere e risolvere gli avvisi comuni con accesso LDAP sicuro in Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Configurazione di rete LDAP sicura

### <a name="alert-message"></a>Messaggio di avviso

*LDAP sicuro su Internet è abilitato per il dominio gestito. Tuttavia, l'accesso alla porta 636 non è bloccato utilizzando un gruppo di sicurezza di rete. Ciò potrebbe esporre gli account utente nel dominio gestito ad attacchi di forza bruta password.*

### <a name="resolution"></a>Risoluzione

Quando si abilita LDAP sicuro, si consiglia di creare regole aggiuntive che limitano l'accesso LDAPS in ingresso a indirizzi IP specifici. Queste regole proteggono il dominio gestito di Servizi di dominio Active Directory di Azure dagli attacchi di forza bruta. La procedura seguente illustra come aggiornare il gruppo di sicurezza di rete in modo da limitare l'accesso alla porta TCP 636 per LDAP protetto:

1. Nel portale di Azure cercare e selezionare Gruppi di **sicurezza di rete**.
1. Scegliere il gruppo di sicurezza di rete associato al dominio gestito, ad esempio *AADDS-contoso.com-NSG*, quindi selezionare Regole di **sicurezza in ingresso**
1. **Aggiungere** una regola per la porta TCP 636. Se necessario, selezionare **Avanzate** nella finestra per creare una regola.
1. Per **Origine**, scegliere *Indirizzi IP* dal menu a discesa. Immettere gli indirizzi IP di origine a cui si desidera concedere l'accesso per il traffico LDAP protetto.
1. Scegliere *Qualsiasi* come **Destinazione**, quindi immettere *636* per **Intervalli di porte**di destinazione .
1. Impostare il **protocollo** come *TCP* e **l'azione** *da consentire*.
1. Specificare la priorità per la regola, quindi immettere un nome, ad esempio *RestrictLDAPS*.
1. Quando si è pronti, selezionare **Aggiungi** per creare la regola.

L'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

> [!TIP]
> La porta TCP 636 non è l'unica regola necessaria per il corretto funzionamento di Servizi di dominio Active Directory di Azure.TCP port 636 is't the only rule needed for Azure AD DS to run smoothly. Per altre informazioni, vedere I gruppi di sicurezza di Rete di Servizi di dominio Active Directory di [Azure e le porte necessarie](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Scadenza del certificato LDAP sicuro

### <a name="alert-message"></a>Messaggio di avviso

*Il certificato LDAP sicuro per il dominio gestito scadrà in [data]*.

### <a name="resolution"></a>Risoluzione

Creare un certificato LDAP sicuro sostitutivo seguendo la procedura per [creare un certificato per LDAP sicuro.](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap) Applicare il certificato sostitutivo ad Azure AD DS e distribuire il certificato a tutti i client che si connettono tramite LDAP sicuro.

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
