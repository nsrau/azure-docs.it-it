---
title: Risolvere gli avvisi LDAP sicuri in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come risolvere i problemi e risolvere gli avvisi comuni con LDAP sicuro per Azure Active Directory Domain Services.
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
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "71258042"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Problemi noti: LDAP sicuro avvisi in Azure Active Directory Domain Services

Le applicazioni e i servizi che usano LDAP (Lightweight Directory Access Protocol) per la comunicazione con Azure Active Directory Domain Services (Azure AD DS) possono essere [configurati per l'uso di LDAP sicuro](tutorial-configure-ldaps.md). Per il corretto funzionamento di LDAP sicuro, è necessario aprire un certificato appropriato e le porte di rete necessarie.

Questo articolo aiuta a comprendere e risolvere gli avvisi comuni con accesso LDAP sicuro in Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: configurazione di rete LDAP sicuro

### <a name="alert-message"></a>Messaggio di avviso

*LDAP sicuro su Internet è abilitato per il dominio gestito. Tuttavia, l'accesso alla porta 636 non è bloccato usando un gruppo di sicurezza di rete. Questo può esporre gli account utente nel dominio gestito agli attacchi di forza bruta per le password.*

### <a name="resolution"></a>Risoluzione

Quando si Abilita LDAP sicuro, è consigliabile creare regole aggiuntive che limitino l'accesso LDAPs in ingresso a indirizzi IP specifici. Queste regole proteggono il dominio gestito Azure AD DS da attacchi di forza bruta. Per aggiornare il gruppo di sicurezza di rete per limitare l'accesso alla porta TCP 636 per LDAP sicuro, attenersi alla procedura seguente:

1. Nella portale di Azure cercare e selezionare **gruppi di sicurezza di rete**.
1. Scegliere il gruppo di sicurezza di rete associato al dominio gestito, ad esempio *AADDS-contoso.com-NSG*, quindi selezionare **regole di sicurezza in ingresso** .
1. **+ Aggiungere** una regola per la porta TCP 636. Se necessario, selezionare **Avanzate** nella finestra per creare una regola.
1. Per l' **origine**scegliere *indirizzi IP* dal menu a discesa. Immettere gli indirizzi IP di origine a cui si vuole concedere l'accesso per il traffico LDAP sicuro.
1. Scegliere *any* come **destinazione**, quindi immettere *636* per gli **intervalli di porte di destinazione**.
1. Impostare il **protocollo** come *TCP* e l' **azione** da *consentire*.
1. Specificare la priorità per la regola, quindi immettere un nome, ad esempio *RestrictLDAPS*.
1. Quando si è pronti, selezionare **Aggiungi** per creare la regola.

L'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

> [!TIP]
> La porta TCP 636 non è l'unica regola necessaria per la corretta esecuzione di Azure AD DS. Per altre informazioni, vedere i [gruppi di sicurezza di rete Azure AD DS e le porte obbligatorie](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Scadenza del certificato LDAP sicuro

### <a name="alert-message"></a>Messaggio di avviso

*The secure LDAP certificate for the managed domain will expire on [date]]* (Il certificato LDAP sicuro per il dominio gestito scadrà in [data]]).

### <a name="resolution"></a>Risoluzione

Creare un certificato LDAP sicuro sostitutivo attenendosi alla procedura per [creare un certificato per l'LDAP sicuro](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Applicare il certificato sostitutivo a Azure AD DS e distribuire il certificato a tutti i client che si connettono usando LDAP sicuro.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano ancora problemi, [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
