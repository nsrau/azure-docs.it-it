---
title: 'Azure Active Directory Domain Services: Risolvere i problemi correlati agli avvisi | Microsoft Docs'
description: Risolvere i problemi correlati agli avvisi di Azure AD Domain Services
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: ergreenl
ms.openlocfilehash: b2e0edf3588f3b1db5f4b6641019be1ded9cb50e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - Risolvere i problemi correlati agli avvisi
Questo articolo fornisce istruzioni per la risoluzione dei problemi correlati agli avvisi che si possono ricevere nel dominio gestito.


Seguire la procedura di risoluzione dei problemi corrispondente all'ID di avviso o al messaggio ricevuto.

| **ID di avviso** | **Messaggio di avviso** | **Risoluzione** |
| --- | --- | :--- |
| AADDS001 | *LDAP sicuro su Internet è abilitato per il dominio gestito. L'accesso alla porta 636 non è tuttavia bloccato tramite un gruppo di sicurezza di rete. È quindi possibile che gli account utente sul dominio gestito siano esposti ad attacchi di forza bruta alle password.* | [Incorrect secure LDAP configuration](active-directory-ds-troubleshoot-ldaps.md) (Errata configurazione di LDAP sicuro) |
| AADDS100 | *È possibile che la directory di Azure AD associata al dominio gestito sia stata eliminata. Il dominio gestito non si trova più in una configurazione supportata. Microsoft non può monitorare, applicare patch e sincronizzare il dominio gestito.* | [Directory mancante](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services non può essere abilitato per una directory di Azure AD B2C.* | [Azure AD B2C è in esecuzione in questa directory](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Un'entità servizio necessaria per il funzionamento corretto di Azure AD Domain Services è stata eliminata dal tenant di Azure AD. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.* | [Missing Service Principal](active-directory-ds-troubleshoot-service-principals.md) (Entità servizio mancante) |
| AADDS103 | *L'intervallo di indirizzi IP per la rete virtuale in cui è stato abilitato Azure AD Domain Services si trova in un intervallo di indirizzi IP pubblici. È necessario abilitare Azure AD Domain Services in una rete virtuale con un intervallo di indirizzi IP privati. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.* | [Indirizzo incluso in un intervallo di IP pubblici](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft non riesce a raggiungere i controller di dominio per questo dominio gestito. È possibile che questo problema si verifichi se un gruppo di sicurezza di rete configurato sulla rete virtuale impedisce l'accesso al dominio gestito oppure se è presente una route definita dall'utente che blocca il traffico in ingresso da Internet.* | [Network Error](active-directory-ds-troubleshoot-nsg.md) (Errore di rete) |

## <a name="aadds100-missing-directory"></a>AADDS100: Directory mancante
**Messaggio di avviso:**

*È possibile che la directory di Azure AD associata al dominio gestito sia stata eliminata. Il dominio gestito non si trova più in una configurazione supportata. Microsoft non può monitorare, applicare patch e sincronizzare il dominio gestito.*

**Correzione:**

Questo errore è generalmente dovuto a un errato spostamento della sottoscrizione di Azure in una nuova directory di Azure AD e all'eliminazione della precedente directory di Azure AD quando è ancora associata ad Azure AD Domain Services.

Questo errore è irreversibile. Per risolvere questo problema, è necessario [eliminare il dominio gestito esistente](active-directory-ds-disable-aadds.md) e ricrearlo nella nuova directory. [Per richieste di supporto](active-directory-ds-contact-us.md) in caso di problemi durante l'operazione di eliminazione, contattare il team del prodotto Azure Active Directory Domain Services.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C è in esecuzione in questa directory
**Messaggio di avviso:**

*Azure AD Domain Services non può essere abilitato per una directory di Azure AD B2C.*

**Correzione:**

>[!NOTE]
>Per continuare a usare Azure AD Domain Services, è necessario ricreare l'istanza di Azure AD Domain Services in una directory che non sia di Azure AD B2C.

Per ripristinare il servizio, seguire questa procedura:

1. [Eliminare il dominio gestito](active-directory-ds-disable-aadds.md) dalla directory di Azure AD esistente.
2. Creare una nuova directory che non sia una directory di Azure AD B2C.
3. Seguire le istruzioni fornite nella [Guida introduttiva](active-directory-ds-getting-started.md) per ricreare un dominio gestito.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Indirizzo incluso in un intervallo di IP pubblici

**Messaggio di avviso:**

*L'intervallo di indirizzi IP per la rete virtuale in cui è stato abilitato Azure AD Domain Services si trova in un intervallo di indirizzi IP pubblici. È necessario abilitare Azure AD Domain Services in una rete virtuale con un intervallo di indirizzi IP privati. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.*

**Correzione:**

> [!NOTE]
> Per risolvere questo problema, è necessario eliminare il dominio gestito esistente e ricrearlo in una rete virtuale con un intervallo di indirizzi IP privati. Questo processo comporta un'interruzione del servizio.

Prima di iniziare, leggere la sezione relativa allo **spazio di indirizzi IP v4 privati** in [questo articolo](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

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
8. Controllare l'integrità del dominio dopo due ore per assicurarsi che la procedura sia stata eseguita correttamente.


## <a name="contact-us"></a>Contatti
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
