---
title: 'Azure Active Directory Domain Services: Risolvere i problemi correlati agli avvisi | Microsoft Docs'
description: Risolvere i problemi correlati agli avvisi di Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: e4b8f31fe3eb79f9b38ae01af598290582a2cde3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - Risolvere i problemi correlati agli avvisi
Questo articolo fornisce istruzioni per la risoluzione dei problemi correlati agli avvisi che si possono ricevere nel dominio gestito.


Seguire la procedura di risoluzione dei problemi corrispondente all'ID o al messaggio nell'avviso.

| **ID di avviso** | **Messaggio di avviso** | **Risoluzione** |
| --- | --- | :--- |
| AADDS001 | *LDAP sicuro su Internet è abilitato per il dominio gestito. L'accesso alla porta 636 non è tuttavia bloccato tramite un gruppo di sicurezza di rete. È quindi possibile che gli account utente sul dominio gestito siano esposti ad attacchi di forza bruta alle password.* | [Incorrect secure LDAP configuration](active-directory-ds-troubleshoot-ldaps.md) (Errata configurazione di LDAP sicuro) |
| AADDS100 | *È possibile che la directory di Azure AD associata al dominio gestito sia stata eliminata. Il dominio gestito non si trova più in una configurazione supportata. Microsoft non può monitorare, applicare patch e sincronizzare il dominio gestito.* | [Directory mancante](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services non può essere abilitato per una directory di Azure AD B2C.* | [Azure AD B2C è in esecuzione in questa directory](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Un'entità servizio necessaria per il funzionamento corretto di Azure AD Domain Services è stata eliminata dal tenant di Azure AD. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.* | [Missing Service Principal](active-directory-ds-troubleshoot-service-principals.md) (Entità servizio mancante) |
| AADDS103 | *L'intervallo di indirizzi IP per la rete virtuale in cui è stato abilitato Azure AD Domain Services si trova in un intervallo di indirizzi IP pubblici. È necessario abilitare Azure AD Domain Services in una rete virtuale con un intervallo di indirizzi IP privati. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.* | [Indirizzo incluso in un intervallo di IP pubblici](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft non riesce a raggiungere i controller di dominio per questo dominio gestito. È possibile che questo problema si verifichi se un gruppo di sicurezza di rete configurato sulla rete virtuale impedisce l'accesso al dominio gestito oppure se è presente una route definita dall'utente che blocca il traffico in ingresso da Internet.* | [Network Error](active-directory-ds-troubleshoot-nsg.md) (Errore di rete) |
| AADDS105 | *The service principal with the application ID “d87dcbc6-a371-462e-88e3-28ad15ec4e64” was deleted, and Microsoft was able to recreate it (L'entità servizio con ID applicazione "d87dcbc6-a371-462e-88e3-28ad15ec4e64" è stata eliminata e Microsoft è stata in grado di ricrearla). Questa entità servizio gestisce un'altra entità servizio e un'applicazione che vengono usate per la sincronizzazione delle password. L'entità servizio e l'applicazione gestite non sono autorizzate in base alla nuova entità servizio creata e diventeranno obsolete alla scadenza del certificato di sincronizzazione. Questo significa che la nuova entità servizio creata non sarà in grado di aggiornare le applicazioni gestite precedenti con effetti sulla sincronizzazione degli oggetti da Azure Active Directory.* | [L'applicazione di sincronizzazione delle password non è aggiornata](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | *The managed domain was last synchronized with Azure AD on [date]. Users may be unable to sign-in on the managed domain or group memberships may not be in sync with Azure AD. (Il dominio gestito è stato sincronizzato l'ultima volta con Azure AD il [data]. È possibile che gli utenti non siano in grado di accedere al dominio gestito o che le appartenenze a gruppi non siano sincronizzate con Azure AD.)* | [Sincronizzazione non eseguita da qualche tempo](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *The managed domain was last backed up on [date].* (L'ultimo backup del dominio gestito è stato eseguito in data [data].) | [Backup non eseguito da qualche tempo](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *The secure LDAP certificate for the managed domain will expire on XX.* (Il certificato LDAP sicuro per il dominio gestito scadrà in data XX.) | [Certificato LDAP sicuro in scadenza](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *The managed domain is suspended because the Azure subscription associated with the domain is not active.* (Il dominio gestito è sospeso perché la sottoscrizione di Azure associata al dominio non è attiva.) | [Sospensione per sottoscrizione disabilitata](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *The managed domain is suspended due to an invalid configuration. The service has been unable to manage, patch, or update the domain controllers for your managed domain for a long time.* (Il dominio gestito è sospeso a causa di una configurazione non valida. Il servizio non è riuscito a gestire, applicare patch o aggiornare i controller di dominio per un periodo prolungato.) | [Sospensione per configurazione non valida](#aadds504-suspension-due-to-an-invalid-configuration) |


## <a name="aadds100-missing-directory"></a>AADDS100: Directory mancante
**Messaggio di avviso:**

*È possibile che la directory di Azure AD associata al dominio gestito sia stata eliminata. Il dominio gestito non si trova più in una configurazione supportata. Microsoft non può monitorare, applicare patch e sincronizzare il dominio gestito.*

**Risoluzione:**

Questo errore è generalmente dovuto a un errato spostamento della sottoscrizione di Azure in una nuova directory di Azure AD e all'eliminazione della precedente directory di Azure AD quando è ancora associata ad Azure AD Domain Services.

Questo errore è irreversibile. Per risolvere questo problema, è necessario [eliminare il dominio gestito esistente](active-directory-ds-disable-aadds.md) e ricrearlo nella nuova directory. [Per richieste di supporto](active-directory-ds-contact-us.md) in caso di problemi durante l'operazione di eliminazione, contattare il team del prodotto Azure Active Directory Domain Services.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C è in esecuzione in questa directory
**Messaggio di avviso:**

*Azure AD Domain Services non può essere abilitato per una directory di Azure AD B2C.*

**Risoluzione:**

>[!NOTE]
>Per continuare a usare Azure AD Domain Services, è necessario ricreare l'istanza di Azure AD Domain Services in una directory che non sia di Azure AD B2C.

Per ripristinare il servizio, seguire questa procedura:

1. [Eliminare il dominio gestito](active-directory-ds-disable-aadds.md) dalla directory di Azure AD esistente.
2. Creare una nuova directory che non sia una directory di Azure AD B2C.
3. Seguire le istruzioni fornite nella [Guida introduttiva](active-directory-ds-getting-started.md) per ricreare un dominio gestito.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Indirizzo incluso in un intervallo di IP pubblici

**Messaggio di avviso:**

*L'intervallo di indirizzi IP per la rete virtuale in cui è stato abilitato Azure AD Domain Services si trova in un intervallo di indirizzi IP pubblici. È necessario abilitare Azure AD Domain Services in una rete virtuale con un intervallo di indirizzi IP privati. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.*

**Risoluzione:**

> [!NOTE]
> Per risolvere questo problema, è necessario eliminare il dominio gestito esistente e ricrearlo in una rete virtuale con un intervallo di indirizzi IP privati. Questo processo comporta un'interruzione del servizio.

Prima di iniziare, leggere la sezione relativa allo **spazio di indirizzi IP v4 privati** in [questo articolo](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

All'interno della rete virtuale, i computer possono inviare richieste a risorse di Azure che sono nello stesso intervallo di indirizzi IP di quelle configurate per la subnet. Tuttavia, poiché la rete virtuale è configurata per questo intervallo, tali richieste vengono indirizzate all'interno della rete virtuale e non raggiungono le risorse Web desiderate. Questa configurazione può causare errori imprevisti in Azure AD Domain Services.

**Se il proprio intervallo di indirizzi IP Internet è configurato anche nella rete virtuale, questo avviso può essere ignorato. Con questa configurazione, tuttavia, Azure AD Domain Services non può rispettare le condizioni del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] poiché possono verificarsi errori imprevisti.**


1. [Eliminare il dominio gestito](active-directory-ds-disable-aadds.md) dalla directory.
2. Correggere l'intervallo di indirizzi IP della subnet:
  1. Passare alla [pagina Reti virtuali nel portale di Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Selezionare la rete virtuale che si prevede di usare per Azure AD Domain Services.
  3. Fare clic su **Spazio di indirizzi** in Impostazioni.
  4. Eseguire l'aggiornamento facendo clic sull'intervallo di indirizzi esistente e modificandolo oppure aggiungendo un altro intervallo di indirizzi. Verificare che il nuovo intervallo di indirizzi sia incluso in un intervallo di indirizzi IP privati. Salvare le modifiche.
  5. Fare clic su **Subnet** nel menu di spostamento a sinistra.
  6. Fare clic sulla subnet che si vuole modificare nella tabella.
  7. Aggiornare l'intervallo di indirizzi e salvare le modifiche.
3. Seguire le istruzioni fornite nella [Guida introduttiva all'uso di Azure AD Domain Services](active-directory-ds-getting-started.md) per ricreare il dominio gestito. Selezionare una rete virtuale con un intervallo di indirizzi IP privati.
4. Per aggiungere le macchine virtuali al nuovo dominio, seguire [queste istruzioni](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Per verificare che il problema segnalato dall'avviso sia stato risolto, controllare l'integrità del dominio dopo due ore.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Sincronizzazione non eseguita da qualche tempo

**Messaggio di avviso:**

*The managed domain was last synchronized with Azure AD on [date]. Users may be unable to sign-in on the managed domain or group memberships may not be in sync with Azure AD. (Il dominio gestito è stato sincronizzato l'ultima volta con Azure AD in data [data]. È possibile che gli utenti non siano in grado di accedere al dominio gestito o che le appartenenze a gruppi non siano sincronizzate con Azure AD.)*

**Risoluzione:**

[Verificare l'integrità del dominio](active-directory-ds-check-health.md) per controllare la presenza di avvisi che potrebbero indicare problemi nella configurazione del dominio gestito. In alcuni casi, problemi di configurazione possono impedire a Microsoft di sincronizzare il dominio gestito. Se si è in grado di risolvere i problemi correlati agli avvisi, attendere due ore e quindi controllare se la sincronizzazione è stata completata.


## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Backup non eseguito da qualche tempo

**Messaggio di avviso:**

*The managed domain was last backed up on [date].* (L'ultimo backup del dominio gestito è stato eseguito in data [data].)

**Risoluzione:**

[Verificare l'integrità del dominio](active-directory-ds-check-health.md) per controllare la presenza di avvisi che potrebbero indicare problemi nella configurazione del dominio gestito. In alcuni casi, problemi di configurazione possono impedire a Microsoft di sincronizzare il dominio gestito. Se si è in grado di risolvere i problemi correlati agli avvisi, attendere due ore e quindi controllare se la sincronizzazione è stata completata.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Sospensione per sottoscrizione disabilitata

**Messaggio di avviso:**

*The managed domain is suspended because the Azure subscription associated with the domain is not active.* (Il dominio gestito è sospeso perché la sottoscrizione di Azure associata al dominio non è attiva.)

**Risoluzione:**

Per ripristinare il servizio, [rinnovare la sottoscrizione di Azure](https://docs.microsoft.com/en-us/azure/billing/billing-subscription-become-disable) associata al dominio gestito.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Sospensione per configurazione non valida

**Messaggio di avviso:**

*The managed domain is suspended due to an invalid configuration. The service has been unable to manage, patch, or update the domain controllers for your managed domain for a long time.* (Il dominio gestito è sospeso a causa di una configurazione non valida. Il servizio non è riuscito a gestire, applicare patch o aggiornare i controller di dominio per un periodo prolungato.)

**Risoluzione:**

[Verificare l'integrità del dominio](active-directory-ds-check-health.md) per controllare la presenza di avvisi che potrebbero indicare problemi nella configurazione del dominio gestito. Se si è in grado di risolvere i problemi correlati agli avvisi, procedere. In seguito, contattare il supporto per riabilitare la sottoscrizione.

## <a name="contact-us"></a>Contatti
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
