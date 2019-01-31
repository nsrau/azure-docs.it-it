---
title: 'Azure Active Directory Domain Services: Risolvere i problemi correlati agli avvisi | Microsoft Docs'
description: Risolvere i problemi correlati agli avvisi di Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: 494acc55001bd3180d345801ef8d62800dd5e837
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174033"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - Risolvere i problemi correlati agli avvisi
Questo articolo fornisce istruzioni per la risoluzione dei problemi correlati agli avvisi che si possono ricevere nel dominio gestito.


Seguire la procedura di risoluzione dei problemi corrispondente all'ID o al messaggio nell'avviso.

| **ID di avviso** | **Messaggio di avviso** | **Risoluzione** |
| --- | --- | :--- |
| AADDS001 | *LDAP sicuro su Internet è abilitato per il dominio gestito. L'accesso alla porta 636 non è tuttavia bloccato tramite un gruppo di sicurezza di rete. È quindi possibile che gli account utente sul dominio gestito siano esposti ad attacchi di forza bruta alle password.* | [Non è stato possibile configurare LDAP sicuro](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *È possibile che la directory di Azure AD associata al dominio gestito sia stata eliminata. Il dominio gestito non si trova più in una configurazione supportata. Microsoft non può monitorare, applicare patch e sincronizzare il dominio gestito.* | [Directory mancante](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services non può essere abilitato per una directory di Azure AD B2C.* | [Azure AD B2C è in esecuzione in questa directory](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Un'entità servizio necessaria per il funzionamento corretto di Azure AD Domain Services è stata eliminata dal tenant di Azure AD. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.* | [L'entità servizio non è presente](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *L'intervallo di indirizzi IP per la rete virtuale in cui è stato abilitato Azure AD Domain Services si trova in un intervallo di indirizzi IP pubblici. È necessario abilitare Azure AD Domain Services in una rete virtuale con un intervallo di indirizzi IP privati. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.* | [Indirizzo incluso in un intervallo di IP pubblici](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft non riesce a raggiungere i controller di dominio per questo dominio gestito. È possibile che questo problema si verifichi se un gruppo di sicurezza di rete configurato sulla rete virtuale impedisce l'accesso al dominio gestito oppure se è presente una route definita dall'utente che blocca il traffico in ingresso da Internet.* | [Errore di rete](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *L'entità servizio con ID applicazione "d87dcbc6-a371-462e-88e3-28ad15ec4e64" è stata eliminata e quindi ricreata. La nuova creazione ha generato autorizzazioni non coerenti nelle risorse di Azure AD Domain Services necessarie per la gestione del dominio gestito. È possibile che la sincronizzazione di password nel dominio gestito sia interessata da questo problema.* | [L'applicazione di sincronizzazione delle password non è aggiornata](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *La sottoscrizione di Azure associata al dominio gestito è stata eliminata.  Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva.* | [La sottoscrizione di Azure non è stata trovata](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *La sottoscrizione di Azure associata al dominio gestito non è attiva.  Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva.* | [La sottoscrizione di Azure è disabilitata](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *La sottoscrizione usata da Azure AD Domain Services è stata spostata in un'altra directory. Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva nella stessa directory.* | [Directory della sottoscrizione spostate](#aadds108-subscription-moved-directories) |
| AADDS109 | *Una risorsa usata per il dominio gestito è stata eliminata. Questa risorsa è necessaria per il funzionamento corretto di Azure AD Domain Services.* | [Una risorsa è stata eliminata](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *La subnet selezionata per la distribuzione di Azure AD Domain Services è piena e non ha spazio sufficiente per il controller di dominio aggiuntivo che deve essere creato.* | [La subnet è piena](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Un'entità servizio usata da Azure AD Domain Services per gestire il dominio non è autorizzata a gestire risorse nella sottoscrizione di Azure. L'entità servizio deve ottenere le autorizzazioni per gestire il dominio gestito. * | [Entità servizio non autorizzata](#aadds111-service-principal-unauthorized) |
| AADDS112 | *È stato rilevato che la subnet della rete virtuale in questo dominio potrebbe non avere indirizzi IP sufficienti. Azure AD Domain Services necessita di almeno due indirizzi IP disponibili entro la subnet in cui è abilitato. È consigliabile avere almeno da 3 a 5 indirizzi IP disponibili entro la subnet. È possibile che questo problema si verifichi se altre macchine virtuali sono distribuite nella subnet, esaurendo il numero di indirizzi IP, o se è presente una limitazione sul numero di indirizzi IP disponibili nella subnet.* | [Gli indirizzi IP non sono sufficienti](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Le risorse usate da Azure AD Domain Services sono state rilevate con uno stato imprevisto e non possono essere ripristinate.* | [Le risorse non sono recuperabili](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *La subnet selezionata per la distribuzione di Azure AD Domain Services non è valida e non può essere usata. * | [Subnet non valida](#aadds114-subnet-invalid) |
| AADDS115 | *Non è possibile eseguire operazioni in una o più risorse di rete usate dal dominio gestito perché l'ambito di destinazione è stato bloccato.* | [Le risorse sono bloccate](#aadds115-resources-are-locked) |
| AADDS116 | *Non è possibile eseguire operazioni in una o più risorse di rete usate dal dominio gestito a causa di restrizioni dei criteri.* | [Le risorse non sono utilizzabili](#aadds116-resources-are-unusable) |
| AADDS500 | *Il dominio gestito è stato sincronizzato l'ultima volta con Azure AD in data [data]. È possibile che gli utenti non riescano ad accedere al dominio gestito o che le appartenenze a gruppi non siano sincronizzate con Azure AD.* | [Sincronizzazione non eseguita da qualche tempo](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *L'ultimo backup del dominio gestito è stato eseguito in data [data].* | [Backup non eseguito da qualche tempo](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Il certificato LDAP sicuro per il dominio gestito scadrà il [data]*. | [Certificato LDAP sicuro in scadenza](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Il dominio gestito è stato sospeso perché la sottoscrizione di Azure associata al dominio non è attiva.* | [Sospensione per sottoscrizione disabilitata](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Il dominio gestito è stato sospeso a causa di una configurazione non valida. Il servizio non è riuscito a gestire, applicare patch o aggiornare i controller di dominio per il dominio gestito per molto tempo.* | [Sospensione per configurazione non valida](#aadds504-suspension-due-to-an-invalid-configuration) |



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

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: La sottoscrizione di Azure non è stata trovata

**Messaggio di avviso:**

*La sottoscrizione di Azure associata al dominio gestito è stata eliminata.  Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva.*

**Risoluzione:**

Azure AD Domain Services richiede una sottoscrizione per funzionare correttamente e non può essere spostato in un'altra sottoscrizione. Poiché la sottoscrizione di Azure a cui era associato il dominio gestito è stata eliminata, sarà necessario creare una nuova sottoscrizione di Azure e Azure AD Domain Services.

1. Crea una sottoscrizione di Azure
2. [Eliminare il dominio gestito](active-directory-ds-disable-aadds.md) dalla directory di Azure AD esistente.
3. Seguire le istruzioni fornite nella [Guida introduttiva](active-directory-ds-getting-started.md) per ricreare un dominio gestito.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: La sottoscrizione di Azure è disabilitata

**Messaggio di avviso:**

*La sottoscrizione di Azure associata al dominio gestito non è attiva.  Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva.*

**Risoluzione:**


1. [Rinnovare la sottoscrizione di Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Dopo il rinnovo della sottoscrizione, Azure AD Domain Services riceverà una notifica da Azure per riabilitare il dominio gestito.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Directory della sottoscrizione spostate

**Messaggio di avviso:**

*La sottoscrizione usata da Azure AD Domain Services è stata spostata in un'altra directory. Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva nella stessa directory.*

**Risoluzione:**

È possibile spostare la sottoscrizione associata ad Azure AD Domain Services nella directory precedente oppure è necessario [eliminare il dominio gestito](active-directory-ds-disable-aadds.md) dalla directory esistente e ricrearlo nella directory scelta (con una nuova sottoscrizione o modificando la directory in cui si trova l'istanza di Azure AD Domain Services).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Non è possibile trovare le risorse per il dominio gestito

**Messaggio di avviso:**

*Una risorsa usata per il dominio gestito è stata eliminata. Questa risorsa è necessaria per il funzionamento corretto di Azure AD Domain Services.*

**Risoluzione:**

Per funzionare correttamente Azure AD Domain Services crea risorse specifiche durante la distribuzione, inclusi indirizzi IP pubblici, interfacce di rete e un servizio di bilanciamento del carico. Se una qualsiasi delle risorse specificate viene eliminata, il dominio risulterà in uno stato non supportato e non potrà essere gestito. Questo avviso viene visualizzato quando un utente che è in grado di modificare le risorse di Azure AD Domain Services elimina una risorsa necessaria. I passaggi seguenti illustrano come ripristinare il dominio gestito.

1.  Accedere alla pagina relativa allo stato di integrità di Azure AD Domain Services
  1.    Passare alla [pagina di Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) nel portale di Azure.
  2.    Nel riquadro di spostamento a sinistra fare clic su **Integrità**.
2.  Verificare se l'avviso è stato restituito da meno di 4 ore
  1.    Nella pagina relativa allo stato di integrità fare clic sull'avviso con l'ID **AADDS109**
  2.    L'avviso avrà un timestamp relativo al momento in cui è stato rilevato per la prima volta. Se il timestamp è stato registrato da meno di 4 ore, è possibile che Azure AD Domain Services sia in grado di ricreare la risorsa eliminata.
3.  Se invece l'avviso è successivo, il dominio gestito non è più ripristinabile. È necessario eliminarlo e ricreare un'istanza di Azure AD Domain Services.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: La subnet associata al dominio gestito è piena

**Messaggio di avviso:**

*La subnet selezionata per la distribuzione di Azure AD Domain Services è piena e non ha spazio sufficiente per il controller di dominio aggiuntivo che deve essere creato.*

**Risoluzione:**

Questo errore è irreversibile. Per risolvere questo problema, è necessario [eliminare il dominio gestito esistente](active-directory-ds-disable-aadds.md) e [crearne uno nuovo](active-directory-ds-getting-started.md).

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Entità servizio non autorizzata

**Messaggio di avviso:**

*Un'entità servizio usata da Azure AD Domain Services per gestire il dominio non è autorizzata a gestire risorse nella sottoscrizione di Azure. L'entità servizio deve ottenere le autorizzazioni per gestire il dominio gestito.*

**Risoluzione:**

Le entità servizio devono accedere per poter gestire e creare le risorse nel dominio gestito. Un utente ha negato l'accesso all'entità servizio che ora non riesce a gestire le risorse. Seguire i passaggi per concedere l'accesso all'entità servizio.

1. Vedere [Controllo degli accessi in base al ruolo e come concedere l'accesso alle applicazioni nel portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Esaminare l'accesso dell'entità servizio con l'ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` e concedere l'accesso negato in precedenza.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Non sono presenti indirizzi IP sufficienti nel dominio gestito

**Messaggio di avviso:**

*È stato rilevato che la subnet della rete virtuale in questo dominio potrebbe non avere indirizzi IP sufficienti. Azure AD Domain Services necessita di almeno due indirizzi IP disponibili entro la subnet in cui è abilitato. È consigliabile avere almeno da 3 a 5 indirizzi IP disponibili entro la subnet. È possibile che questo problema si verifichi se altre macchine virtuali sono distribuite nella subnet, esaurendo il numero di indirizzi IP, o se è presente una limitazione sul numero di indirizzi IP disponibili nella subnet.*

**Risoluzione:**

1. [Eliminare il dominio gestito](#active-directory-ds-disable-aadds.md) dal tenant.
2. Correggere l'intervallo di indirizzi IP della subnet:
  1. Passare alla [pagina Reti virtuali nel portale di Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Selezionare la rete virtuale che si prevede di usare per Azure AD Domain Services.
  3. Fare clic su **Spazio di indirizzi** in Impostazioni.
  4. Eseguire l'aggiornamento facendo clic sull'intervallo di indirizzi esistente e modificandolo oppure aggiungendo un altro intervallo di indirizzi. Salvare le modifiche.
  5. Fare clic su **Subnet** nel menu di spostamento a sinistra.
  6. Fare clic sulla subnet che si vuole modificare nella tabella.
  7. Aggiornare l'intervallo di indirizzi e salvare le modifiche.
3. Seguire le istruzioni fornite nella [Guida introduttiva all'uso di Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) per ricreare il dominio gestito. Selezionare una rete virtuale con un intervallo di indirizzi IP privati.
4. Per aggiungere le macchine virtuali al nuovo dominio, seguire [queste istruzioni](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Controllare l'integrità del dominio dopo due ore per assicurarsi che la procedura sia stata eseguita correttamente.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Le risorse non sono recuperabili

**Messaggio di avviso:**

*Le risorse usate da Azure AD Domain Services sono state rilevate con uno stato imprevisto e non possono essere ripristinate.*

**Risoluzione:**

Questo errore è irreversibile. Per risolvere questo problema, è necessario [eliminare il dominio gestito esistente](active-directory-ds-disable-aadds.md) e [crearne uno nuovo](active-directory-ds-getting-started.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Subnet non valida

**Messaggio di avviso:**

*La subnet selezionata per la distribuzione di Azure AD Domain Services non è valida e non può essere usata.*

**Risoluzione:**

Questo errore è irreversibile. Per risolvere questo problema, è necessario [eliminare il dominio gestito esistente](active-directory-ds-disable-aadds.md) e [crearne uno nuovo](active-directory-ds-getting-started.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Le risorse sono bloccate

**Messaggio di avviso:**

*Non è possibile eseguire operazioni in una o più risorse di rete usate dal dominio gestito perché l'ambito di destinazione è stato bloccato.*

**Risoluzione:**

1.  Esaminare i log delle operazioni di Resource Manager sulle risorse di rete per ottenere informazioni sugli eventuali blocchi che impediscono le modifiche.
2.  Rimuovere i blocchi sulle risorse in modo che l'entità servizio di Azure AD Domain Services possa operare su tali risorse.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Le risorse non sono utilizzabili

**Messaggio di avviso:**

*Non è possibile eseguire operazioni in una o più risorse di rete usate dal dominio gestito a causa di restrizioni dei criteri.*

**Risoluzione:**

1.  Esaminare i log delle operazioni di Resource Manager sulle risorse di rete per il dominio gestito.
2.  Ridurre le limitazioni dei criteri sulle risorse in modo che l'entità servizio di Azure AD Domain Services possa operare su tali risorse.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Sincronizzazione non eseguita da qualche tempo

**Messaggio di avviso:**

*Il dominio gestito è stato sincronizzato l'ultima volta con Azure AD in data [data]. È possibile che gli utenti non riescano ad accedere al dominio gestito o che le appartenenze a gruppi non siano sincronizzate con Azure AD.*

**Risoluzione:**

[Verificare l'integrità del dominio](active-directory-ds-check-health.md) per controllare la presenza di avvisi che potrebbero indicare problemi nella configurazione del dominio gestito. In alcuni casi, problemi di configurazione possono impedire a Microsoft di sincronizzare il dominio gestito. Se si è in grado di risolvere i problemi correlati agli avvisi, attendere due ore e quindi controllare se la sincronizzazione è stata completata.

Ecco alcuni motivi comuni per cui la sincronizzazione viene arrestata nei domini gestiti:
- La connessione di rete è bloccata nel dominio gestito. Per altre informazioni sul controllo dei problemi di rete, leggere la procedura per [Risolvere i problemi dei gruppi di sicurezza di rete](active-directory-ds-troubleshoot-nsg.md) e i [Requisiti di rete per Azure AD Domain Services](active-directory-ds-networking.md).
-  La sincronizzazione delle password non è stata mai configurata o completata. Per configurare la sincronizzazione delle password, leggere [questo articolo](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Backup non eseguito da qualche tempo

**Messaggio di avviso:**

*L'ultimo backup del dominio gestito è stato eseguito in data [data].*

**Risoluzione:**

[Verificare l'integrità del dominio](active-directory-ds-check-health.md) per controllare la presenza di avvisi che potrebbero indicare problemi nella configurazione del dominio gestito. In alcuni casi, problemi di configurazione possono impedire a Microsoft di eseguire un backup del dominio gestito. Se è possibile risolvere i problemi correlati agli avvisi, attendere due ore e quindi controllare se il backup è stato completato.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Sospensione per sottoscrizione disabilitata

**Messaggio di avviso:**

*Il dominio gestito è stato sospeso perché la sottoscrizione di Azure associata al dominio non è attiva.*

**Risoluzione:**

> [!WARNING]
> Se il dominio gestito è sospeso per un lungo periodo di tempo, rischia di essere eliminato. È consigliabile risolvere la sospensione nel minor tempo possibile. Per altre informazioni, leggere [questo articolo](active-directory-ds-suspension.md).

Per ripristinare il servizio, [rinnovare la sottoscrizione di Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) associata al dominio gestito.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Sospensione per configurazione non valida

**Messaggio di avviso:**

*Il dominio gestito è stato sospeso a causa di una configurazione non valida. Il servizio non è riuscito a gestire, applicare patch o aggiornare i controller di dominio per il dominio gestito per molto tempo.*

**Risoluzione:**

> [!WARNING]
> Se il dominio gestito è sospeso per un lungo periodo di tempo, rischia di essere eliminato. È consigliabile risolvere la sospensione nel minor tempo possibile. Per altre informazioni, leggere [questo articolo](active-directory-ds-suspension.md).

[Verificare l'integrità del dominio](active-directory-ds-check-health.md) per controllare la presenza di avvisi che potrebbero indicare problemi nella configurazione del dominio gestito. Se si è in grado di risolvere i problemi correlati agli avvisi, procedere. In seguito, contattare il supporto per riabilitare la sottoscrizione.


## <a name="contact-us"></a>Contatti
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
