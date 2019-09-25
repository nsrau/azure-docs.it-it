---
title: Avvisi comuni e risoluzioni in Azure AD Domain Services | Microsoft Docs '
description: Informazioni su come risolvere gli avvisi comuni generati come parte dello stato di integrità per Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 3286d3e786fc5b0e7a772f5b0e3caa3acf38671e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257939"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Problemi noti: Avvisi comuni e risoluzioni in Azure Active Directory Domain Services

Come parte centrale dell'identità e dell'autenticazione per le applicazioni, Azure Active Directory Domain Services (Azure AD DS) a volte presenta dei problemi. Se si verificano problemi, sono disponibili alcuni avvisi comuni e i passaggi di risoluzione dei problemi associati che consentono di eseguire di nuovo le operazioni. In qualsiasi momento, è anche possibile [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

Questo articolo fornisce informazioni sulla risoluzione dei problemi per gli avvisi comuni in Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Directory mancante

### <a name="alert-message"></a>Messaggio di avviso

*È possibile che la directory di Azure AD associata al dominio gestito sia stata eliminata. Il dominio gestito non si trova più in una configurazione supportata. Microsoft non può monitorare, applicare patch e sincronizzare il dominio gestito.*

### <a name="resolution"></a>Risoluzione

Questo errore si verifica in genere quando una sottoscrizione di Azure viene spostata in una nuova directory di Azure AD e la vecchia directory Azure AD associata a Azure AD DS viene eliminata.

Questo errore è irreversibile. Per risolvere l'avviso, [eliminare il dominio gestito Azure AD DS esistente](delete-aadds.md) e ricrearlo nella nuova directory. In caso di problemi durante l'eliminazione del dominio gestito Azure AD DS, [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C è in esecuzione in questa directory
 
### <a name="alert-message"></a>Messaggio di avviso

*Azure AD Domain Services non può essere abilitato per una directory di Azure AD B2C.*

### <a name="resolution"></a>Risoluzione

Azure AD DS si sincronizza automaticamente con una directory Azure AD. Se la directory Azure AD è configurata per B2C, Azure AD DS non può essere distribuita e sincronizzata.

Per usare Azure AD DS, è necessario ricreare il dominio gestito in una directory non Azure AD B2C usando la procedura seguente:

1. [Eliminare il dominio gestito Azure AD DS](delete-aadds.md) dalla directory Azure ad esistente.
1. Creare una nuova directory di Azure AD che non sia una directory Azure AD B2C.
1. [Creare una sostituzione Azure ad dominio gestito DS](tutorial-create-instance.md).

L'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Indirizzo incluso in un intervallo di IP pubblici

### <a name="alert-message"></a>Messaggio di avviso

*L'intervallo di indirizzi IP per la rete virtuale in cui è stato abilitato Azure AD Domain Services si trova in un intervallo di indirizzi IP pubblici. È necessario abilitare Azure AD Domain Services in una rete virtuale con un intervallo di indirizzi IP privati. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.*

### <a name="resolution"></a>Risoluzione

Prima di iniziare, verificare di avere compreso gli [spazi di indirizzi IP v4 privati](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

All'interno di una rete virtuale, le macchine virtuali possono eseguire richieste alle risorse di Azure nello stesso intervallo di indirizzi IP configurato per la subnet. Se si configura un intervallo di indirizzi IP pubblici per una subnet, le richieste indirizzate all'interno di una rete virtuale potrebbero non raggiungere le risorse Web desiderate. Questa configurazione può causare errori imprevedibili con Azure AD DS.

> [!NOTE]
> Se si è proprietari dell'intervallo di indirizzi IP in Internet configurato nella rete virtuale, questo avviso può essere ignorato. Tuttavia, non Azure AD Domain Services possibile eseguire il commit nel [contratto di contratto](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)con questa configurazione perché può causare errori imprevedibili.

Per risolvere questo avviso, eliminare il dominio gestito di Azure AD DS esistente e ricrearlo in una rete virtuale con un intervallo di indirizzi IP privati. Questo processo è problematico perché il dominio gestito di Azure AD DS non è disponibile e tutte le risorse personalizzate create come le ou o gli account del servizio vengono perse.

1. [Eliminare il dominio gestito Azure AD DS](delete-aadds.md) dalla directory.
1. Per aggiornare l'intervallo di indirizzi IP della rete virtuale, cercare e selezionare *rete virtuale* nella portale di Azure. Selezionare la rete virtuale per Azure AD DS per cui è impostato un intervallo di indirizzi IP pubblici non corretto.
1. In **Impostazioni**selezionare *spazio indirizzi*.
1. Aggiornare l'intervallo di indirizzi scegliendo l'intervallo di indirizzi esistente e modificarlo oppure aggiungendo un intervallo di indirizzi aggiuntivo. Verificare che il nuovo intervallo di indirizzi IP si trovi in un intervallo di indirizzi IP privati. Quando è pronto, **salvare** le modifiche.
1. Selezionare **subnet** nella parte sinistra della finestra di navigazione.
1. Scegliere la subnet che si vuole modificare o creare una subnet aggiuntiva.
1. Aggiornare o specificare un intervallo di indirizzi IP privati, quindi **salvare** le modifiche.
1. [Creare una sostituzione Azure ad dominio gestito DS](tutorial-create-instance.md). Assicurarsi di selezionare una subnet di rete virtuale aggiornata con un intervallo di indirizzi IP privati.

L'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: La sottoscrizione di Azure non è stata trovata

### <a name="alert-message"></a>Messaggio di avviso

*La sottoscrizione di Azure associata al dominio gestito è stata eliminata.  Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva.*

### <a name="resolution"></a>Risoluzione

Azure AD DS richiede una sottoscrizione attiva e non può essere spostata in una sottoscrizione diversa. Se la sottoscrizione di Azure a cui è stato associato il dominio gestito di Azure AD DS è stata eliminata, è necessario ricreare una sottoscrizione di Azure e un dominio gestito di Azure AD DS.

1. [Creare una sottoscrizione di Azure](../billing/billing-create-subscription.md).
1. [Eliminare il dominio gestito Azure AD DS](delete-aadds.md) dalla directory Azure ad esistente.
1. [Creare una sostituzione Azure ad dominio gestito DS](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: La sottoscrizione di Azure è disabilitata

### <a name="alert-message"></a>Messaggio di avviso

*La sottoscrizione di Azure associata al dominio gestito non è attiva.  Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva.*

### <a name="resolution"></a>Risoluzione

Azure AD DS richiede una sottoscrizione attiva. Se la sottoscrizione di Azure a cui è associato il dominio gestito di Azure AD DS non è attiva, è necessario rinnovarla per riattivare la sottoscrizione.

1. [Rinnovare la sottoscrizione di Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Una volta rinnovata la sottoscrizione, una notifica Azure AD DS consente di riabilitare il dominio gestito.

Quando il dominio gestito viene nuovamente abilitato, l'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Directory della sottoscrizione spostate

### <a name="alert-message"></a>Messaggio di avviso

*La sottoscrizione usata da Azure AD Domain Services è stata spostata in un'altra directory. Per il funzionamento corretto di Azure AD Domain Services è necessaria una sottoscrizione attiva nella stessa directory.*

### <a name="resolution"></a>Risoluzione

Azure AD DS richiede una sottoscrizione attiva e non può essere spostata in una sottoscrizione diversa. Se la sottoscrizione di Azure a cui è stato associato il dominio gestito di Azure AD DS viene spostata, riportare la sottoscrizione alla directory precedente oppure [eliminare il dominio gestito](delete-aadds.md) dalla directory esistente e [creare una sostituzione Azure ad gestita da DS dominio nella sottoscrizione selezionata](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Non è possibile trovare le risorse per il dominio gestito

### <a name="alert-message"></a>Messaggio di avviso

*Una risorsa usata per il dominio gestito è stata eliminata. Questa risorsa è necessaria per il funzionamento corretto di Azure AD Domain Services.*

### <a name="resolution"></a>Risoluzione

Azure AD DS crea risorse specifiche per funzionare correttamente, ad esempio indirizzi IP pubblici, nic e un servizio di bilanciamento del carico. Se una di queste risorse viene eliminata, il dominio gestito si trova in uno stato non supportato e impedisce la gestione del dominio. Per altre informazioni su queste risorse, vedere [risorse di rete usate da Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Questo avviso viene generato quando una di queste risorse obbligatorie viene eliminata. Se la risorsa è stata eliminata meno di 4 ore fa, è possibile che la piattaforma Azure ricrei automaticamente la risorsa eliminata. La procedura seguente illustra come verificare lo stato di integrità e il timestamp per l'eliminazione di risorse:

1. Nella portale di Azure cercare e selezionare **servizi di dominio**. Scegliere il dominio gestito di Azure AD DS, ad esempio *contoso.com*.
1. Nella finestra di spostamento a sinistra selezionare **stato**.
1. Nella pagina stato selezionare l'avviso con l'ID *AADDS109*.
1. L'avviso ha un timestamp per la prima volta che è stato trovato. Se il timestamp è inferiore a 4 ore fa, la piattaforma Azure potrebbe riuscire a ricreare automaticamente la risorsa e risolvere l'avviso.

    Se l'avviso supera le 4 ore precedenti, il dominio gestito Azure AD DS si trova in uno stato irreversibile. [Eliminare il dominio gestito Azure AD DS](delete-aadds.md) , quindi [creare un dominio gestito sostitutivo](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: La subnet associata al dominio gestito è piena

### <a name="alert-message"></a>Messaggio di avviso

*La subnet selezionata per la distribuzione di Azure AD Domain Services è piena e non ha spazio sufficiente per il controller di dominio aggiuntivo che deve essere creato.*

### <a name="resolution"></a>Risoluzione

La subnet della rete virtuale per Azure AD DS necessita di indirizzi IP sufficienti per le risorse create automaticamente. Questo spazio di indirizzi IP include la necessità di creare risorse sostitutive se è presente un evento di manutenzione. Per ridurre al minimo il rischio di esaurire gli indirizzi IP disponibili, non distribuire risorse aggiuntive, ad esempio le macchine virtuali, nella stessa subnet della rete virtuale di Azure AD DS.

Questo errore è irreversibile. Per risolvere l'avviso, [eliminare il dominio gestito Azure AD DS esistente](delete-aadds.md) e ricrearlo. In caso di problemi durante l'eliminazione del dominio gestito Azure AD DS, [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Entità servizio non autorizzata

### <a name="alert-message"></a>Messaggio di avviso

*Un'entità servizio usata da Azure AD Domain Services per gestire il dominio non è autorizzata a gestire risorse nella sottoscrizione di Azure. L'entità servizio deve ottenere le autorizzazioni per gestire il dominio gestito.*

### <a name="resolution"></a>Risoluzione

Alcune entità servizio generate automaticamente vengono usate per gestire e creare risorse per un dominio gestito Azure AD DS. Se le autorizzazioni di accesso per una di queste entità servizio sono state modificate, non è possibile gestire correttamente le risorse. Nei passaggi seguenti viene illustrato come comprendere e quindi concedere le autorizzazioni di accesso a un'entità servizio:

1. Informazioni sul [controllo degli accessi in base al ruolo e su come concedere l'accesso alle applicazioni nel portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
2. Verificare l'accesso dell'entità servizio con ID *abba844e-BC0E-44b0-947A-dc74e5d09022* e concedere l'accesso negato a una data precedente.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Non sono presenti indirizzi IP sufficienti nel dominio gestito

### <a name="alert-message"></a>Messaggio di avviso

*È stato rilevato che la subnet della rete virtuale in questo dominio potrebbe non avere indirizzi IP sufficienti. Azure AD Domain Services necessita di almeno due indirizzi IP disponibili entro la subnet in cui è abilitato. È consigliabile avere almeno da 3 a 5 indirizzi IP disponibili entro la subnet. È possibile che questo problema si verifichi se altre macchine virtuali sono distribuite nella subnet, esaurendo il numero di indirizzi IP, o se è presente una limitazione sul numero di indirizzi IP disponibili nella subnet.*

### <a name="resolution"></a>Risoluzione

La subnet della rete virtuale per Azure AD DS necessita di indirizzi IP sufficienti per le risorse create automaticamente. Questo spazio di indirizzi IP include la necessità di creare risorse sostitutive se è presente un evento di manutenzione. Per ridurre al minimo il rischio di esaurire gli indirizzi IP disponibili, non distribuire risorse aggiuntive, ad esempio le macchine virtuali, nella stessa subnet della rete virtuale di Azure AD DS.

Per risolvere questo avviso, eliminare il dominio gestito di Azure AD DS esistente e ricrearlo in una rete virtuale con un intervallo di indirizzi IP sufficientemente grande. Questo processo è problematico perché il dominio gestito di Azure AD DS non è disponibile e tutte le risorse personalizzate create come le ou o gli account del servizio vengono perse.

1. [Eliminare il dominio gestito Azure AD DS](delete-aadds.md) dalla directory.
1. Per aggiornare l'intervallo di indirizzi IP della rete virtuale, cercare e selezionare *rete virtuale* nella portale di Azure. Selezionare la rete virtuale per Azure AD DS che l'intervallo di indirizzi IP ridotto.
1. In **Impostazioni**selezionare *spazio indirizzi*.
1. Aggiornare l'intervallo di indirizzi scegliendo l'intervallo di indirizzi esistente e modificarlo oppure aggiungendo un intervallo di indirizzi aggiuntivo. Verificare che il nuovo intervallo di indirizzi IP sia sufficientemente grande per l'intervallo di subnet Azure AD DS. Quando è pronto, **salvare** le modifiche.
1. Selezionare **subnet** nella parte sinistra della finestra di navigazione.
1. Scegliere la subnet che si vuole modificare o creare una subnet aggiuntiva.
1. Aggiornare o specificare un intervallo di indirizzi IP sufficientemente grande, quindi **salvare** le modifiche.
1. [Creare una sostituzione Azure ad dominio gestito DS](tutorial-create-instance.md). Assicurarsi di selezionare una subnet di rete virtuale aggiornata con un intervallo di indirizzi IP sufficientemente grande.

L'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Le risorse non sono recuperabili

### <a name="alert-message"></a>Messaggio di avviso

*Le risorse usate da Azure AD Domain Services sono state rilevate con uno stato imprevisto e non possono essere ripristinate.*

### <a name="resolution"></a>Risoluzione

Questo errore è irreversibile. Per risolvere l'avviso, [eliminare il dominio gestito Azure AD DS esistente](delete-aadds.md) e ricrearlo. In caso di problemi durante l'eliminazione del dominio gestito Azure AD DS, [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Subnet non valida

### <a name="alert-message"></a>Messaggio di avviso

*La subnet selezionata per la distribuzione di Azure AD Domain Services non è valida e non può essere usata.*

### <a name="resolution"></a>Risoluzione

Questo errore è irreversibile. Per risolvere l'avviso, [eliminare il dominio gestito Azure AD DS esistente](delete-aadds.md) e ricrearlo. In caso di problemi durante l'eliminazione del dominio gestito Azure AD DS, [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Le risorse sono bloccate

### <a name="alert-message"></a>Messaggio di avviso

*Non è possibile eseguire operazioni in una o più risorse di rete usate dal dominio gestito perché l'ambito di destinazione è stato bloccato.*

### <a name="resolution"></a>Risoluzione

I blocchi delle risorse possono essere applicati alle risorse e ai gruppi di risorse di Azure per impedire la modifica o l'eliminazione. Poiché Azure AD DS è un servizio gestito, la piattaforma Azure necessita della possibilità di apportare modifiche alla configurazione. Se un blocco di risorsa viene applicato ad alcuni componenti Azure AD DS, la piattaforma Azure non può eseguire le attività di gestione.

Per verificare i blocchi delle risorse nei componenti Azure AD DS e rimuoverli, seguire questa procedura:

1. Per ogni componente di rete Azure AD DS nel gruppo di risorse, ad esempio rete virtuale, NIC o indirizzo IP pubblico, controllare i registri operazioni nel portale di Azure. Questi log operazioni devono indicare il motivo per cui un'operazione non riesce e il punto in cui viene applicato un blocco di risorsa.
1. Selezionare la risorsa in cui viene applicato un blocco, quindi in **blocchi**selezionare e rimuovere i blocchi.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Le risorse non sono utilizzabili

### <a name="alert-message"></a>Messaggio di avviso

*Non è possibile eseguire operazioni in una o più risorse di rete usate dal dominio gestito a causa di restrizioni dei criteri.*

### <a name="resolution"></a>Risoluzione

I criteri vengono applicati alle risorse e ai gruppi di risorse di Azure per controllare quali azioni di configurazione sono consentite. Poiché Azure AD DS è un servizio gestito, la piattaforma Azure necessita della possibilità di apportare modifiche alla configurazione. Se un criterio viene applicato ad alcuni componenti Azure AD DS, la piattaforma Azure potrebbe non essere in grado di eseguire le attività di gestione.

Per verificare i criteri applicati nei componenti Azure AD DS e aggiornarli, attenersi alla procedura seguente:

1. Per ogni componente di rete Azure AD DS nel gruppo di risorse, ad esempio rete virtuale, NIC o indirizzo IP pubblico, controllare i registri operazioni nel portale di Azure. Questi log operazioni devono indicare il motivo per cui un'operazione non riesce e la posizione in cui viene applicato un criterio restrittivo.
1. Selezionare la risorsa in cui applicare un criterio, quindi in **criteri**selezionare e modificare il criterio in modo che sia meno restrittivo.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Sincronizzazione non eseguita da qualche tempo

### <a name="alert-message"></a>Messaggio di avviso

*Il dominio gestito è stato sincronizzato l'ultima volta con Azure AD in data [data]. È possibile che gli utenti non riescano ad accedere al dominio gestito o che le appartenenze a gruppi non siano sincronizzate con Azure AD.*

### <a name="resolution"></a>Risoluzione

[Controllare lo stato Azure AD DS](check-health.md) per eventuali avvisi che indicano problemi nella configurazione del dominio gestito. Problemi con la configurazione di rete possono bloccare la sincronizzazione da Azure AD. Se è possibile risolvere gli avvisi che indicano un problema di configurazione, attendere due ore e verificare se la sincronizzazione è stata completata.

I motivi comuni seguenti causano l'arresto della sincronizzazione in un Azure AD domini gestiti di DS:

* La connettività di rete necessaria è bloccata. Per altre informazioni su come controllare la rete virtuale di Azure per individuare eventuali problemi, vedere risolvere i problemi relativi ai [gruppi di sicurezza di rete](alert-nsg.md) e ai requisiti di [rete per Azure ad Domain Services](network-considerations.md).
*  La sincronizzazione password non è stata configurata o completata al momento della distribuzione del dominio gestito Azure AD DS. È possibile configurare la sincronizzazione delle password per [gli utenti solo cloud](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) o per [gli utenti ibridi da](tutorial-configure-password-hash-sync.md)locale.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Backup non eseguito da qualche tempo

### <a name="alert-message"></a>Messaggio di avviso

*L'ultimo backup del dominio gestito è stato eseguito in data [data].*

### <a name="resolution"></a>Risoluzione

[Controllare lo stato Azure AD DS](check-health.md) per eventuali avvisi che indicano problemi nella configurazione del dominio gestito. Problemi con la configurazione di rete possono impedire alla piattaforma Azure di eseguire correttamente i backup. Se è possibile risolvere gli avvisi che indicano un problema di configurazione, attendere due ore e verificare se la sincronizzazione è stata completata.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Sospensione per sottoscrizione disabilitata

### <a name="alert-message"></a>Messaggio di avviso

*Il dominio gestito è stato sospeso perché la sottoscrizione di Azure associata al dominio non è attiva.*

### <a name="resolution"></a>Risoluzione

> [!WARNING]
> Se un dominio gestito di Azure AD DS viene sospeso per un periodo di tempo prolungato, sussiste il rischio di eliminarlo. Risolvere il motivo della sospensione il più rapidamente possibile. Per ulteriori informazioni, vedere [comprendere gli stati sospesi per Azure AD DS](suspension.md).

Azure AD DS richiede una sottoscrizione attiva. Se la sottoscrizione di Azure a cui è associato il dominio gestito di Azure AD DS non è attiva, è necessario rinnovarla per riattivare la sottoscrizione.

1. [Rinnovare la sottoscrizione di Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Una volta rinnovata la sottoscrizione, una notifica Azure AD DS consente di riabilitare il dominio gestito.

Quando il dominio gestito viene nuovamente abilitato, l'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Sospensione per configurazione non valida

### <a name="alert-message"></a>Messaggio di avviso

*Il dominio gestito è stato sospeso a causa di una configurazione non valida. Il servizio non è riuscito a gestire, applicare patch o aggiornare i controller di dominio per il dominio gestito per molto tempo.*

### <a name="resolution"></a>Risoluzione

> [!WARNING]
> Se un dominio gestito di Azure AD DS viene sospeso per un periodo di tempo prolungato, sussiste il rischio di eliminarlo. Risolvere il motivo della sospensione il più rapidamente possibile. Per ulteriori informazioni, vedere [comprendere gli stati sospesi per Azure AD DS](suspension.md).

[Controllare lo stato Azure AD DS](check-health.md) per eventuali avvisi che indicano problemi nella configurazione del dominio gestito. Se è possibile risolvere gli avvisi che indicano un problema di configurazione, attendere due ore e verificare se la sincronizzazione è stata completata. Quando si è pronti, [aprire una richiesta di supporto tecnico di Azure][azure-support] per abilitare nuovamente il dominio gestito di Azure AD DS.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano ancora problemi, [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
