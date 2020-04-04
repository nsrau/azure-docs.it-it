---
title: Avvisi e risoluzioni comuni in Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come risolvere gli avvisi comuni generati come parte dello stato di integrità per Servizi di dominio Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: ed791ea10c072308c16baac9fa469a46b19ec648
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654507"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Problemi noti: avvisi e risoluzioni comuni in Servizi di dominio Azure Active DirectoryKnown issues: Common alerts and resolutions in Azure Active Directory Domain Services

Come parte centrale dell'identità e dell'autenticazione per le applicazioni, Servizi di dominio Azure Active Directory (Azure AD DS) talvolta presenta problemi. Se si verificano problemi, sono disponibili alcuni avvisi comuni e procedure di risoluzione dei problemi associate che consentono di eseguire di nuovo le operazioni. In qualsiasi momento, è anche possibile aprire una richiesta di supporto di [Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

Questo articolo fornisce informazioni sulla risoluzione dei problemi per gli avvisi comuni in Azure AD DS.This article provides troubleshooting information for common alerts in Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Directory mancante

### <a name="alert-message"></a>Messaggio di avviso

*La directory di Azure AD associata al dominio gestito potrebbe essere stata eliminata. Il dominio gestito non è più in una configurazione supportata. Microsoft non può monitorare, gestire, applicare patch e sincronizzare il dominio gestito.*

### <a name="resolution"></a>Risoluzione

Questo errore si verifica in genere quando una sottoscrizione di Azure viene spostata in una nuova directory di Azure AD e la directory di Azure AD precedente associata a Servizi di dominio Active Directory di Azure viene eliminata.

Questo errore è irreversibile. Per risolvere l'avviso, eliminare il dominio gestito di [Servizi di dominio Active Directory di Azure esistente](delete-aadds.md) e ricrearlo nella nuova directory. In caso di problemi durante l'eliminazione del dominio gestito di Servizi di dominio Active Directory di Azure, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C è in esecuzione in questa directory

### <a name="alert-message"></a>Messaggio di avviso

*Azure AD Domain Services non può essere abilitato per una directory di Azure AD B2C.*

### <a name="resolution"></a>Risoluzione

Servizi di dominio Active Directory di Azure viene sincronizzato automaticamente con una directory di Azure AD. Se la directory di Azure AD è configurata per B2C, Azure AD DS non può essere distribuito e sincronizzato.

Per usare Azure AD DS, è necessario ricreare il dominio gestito in una directory B2C non di Azure AD attenendosi alla procedura seguente:To use Azure AD DS, you must recreate your managed domain in a non-Azure AD B2C directory using the following steps:

1. [Eliminare il dominio gestito](delete-aadds.md) di Servizi di dominio Active Directory di Azure dalla directory di Azure AD esistente.
1. Creare una nuova directory di Azure AD che non sia una directory B2C di Azure AD.
1. Creare un dominio gestito di [Servizi di dominio Active Directory di Azure sostitutivo.](tutorial-create-instance.md)

L'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Indirizzo incluso in un intervallo di IP pubblici

### <a name="alert-message"></a>Messaggio di avviso

*L'intervallo di indirizzi IP per la rete virtuale in cui è stato abilitato Servizi di dominio Azure AD è in un intervallo di indirizzi IP pubblici. Servizi di dominio Azure AD deve essere abilitato in una rete virtuale con un intervallo di indirizzi IP privati. Questa configurazione influisce sulla capacità di Microsoft di monitorare, gestire, applicare patch e sincronizzare il dominio gestito.*

### <a name="resolution"></a>Risoluzione

Prima di iniziare, assicurarsi di aver [compreso gli spazi di indirizzi IP v4 privati.](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)

All'interno di una rete virtuale, le macchine virtuali possono effettuare richieste alle risorse di Azure nello stesso intervallo di indirizzi IP configurato per la subnet. Se si configura un intervallo di indirizzi IP pubblici per una subnet, le richieste instradate all'interno di una rete virtuale potrebbero non raggiungere le risorse Web desiderate. Questa configurazione può causare errori imprevedibili con Servizi di dominio Active Directory di Azure.This configuration can lead to unpredictable errors with Azure AD DS.

> [!NOTE]
> Se il proprio intervallo di indirizzi IP Internet è configurato anche nella rete virtuale, questo avviso può essere ignorato. Tuttavia, Servizi di dominio Azure AD non può eseguire il commit nel provider di servizi [sLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] con questa configurazione poiché può causare errori imprevisti.

Per risolvere questo avviso, eliminare il dominio gestito di Servizi di dominio Active Directory esistente e ricrearlo in una rete virtuale con un intervallo di indirizzi IP privati. Questo processo è problematico in quanto il dominio gestito di Servizi di dominio Active Directory di Azure non è disponibile e tutte le risorse personalizzate create come le unità ora o gli account del servizio vengono persi.

1. [Eliminare il dominio gestito](delete-aadds.md) di Servizi di dominio Active Directory di Azure dalla directory.
1. Per aggiornare l'intervallo di indirizzi IP della rete virtuale, cercare e selezionare Rete virtuale nel portale di Azure.To update the virtual network IP address range, search for and select *Virtual network* in the Azure portal. Selezionare la rete virtuale per Servizi di dominio Active Directory di Azure che in modo non corretto dispone di un intervallo di indirizzi IP pubblici impostato.
1. In **Impostazioni**selezionare *Spazio indirizzi*.
1. Aggiornare l'intervallo di indirizzi scegliendo l'intervallo di indirizzi esistente e modificandolo oppure aggiungendo un intervallo di indirizzi aggiuntivo. Assicurarsi che il nuovo intervallo di indirizzi IP sia compreso in un intervallo IP privato. Quando sei pronto, **Salva** le modifiche.
1. Selezionare **Subnet** nel riquadro di spostamento a sinistra.
1. Scegliere la subnet che si desidera modificare o creare una subnet aggiuntiva.
1. Aggiornare o specificare un intervallo di indirizzi IP privati, quindi **salvare** le modifiche.
1. Creare un dominio gestito di [Servizi di dominio Active Directory di Azure sostitutivo.](tutorial-create-instance.md) Assicurarsi di selezionare la subnet di rete virtuale aggiornata con un intervallo di indirizzi IP privati.

L'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: La sottoscrizione di Azure non è stata trovata

### <a name="alert-message"></a>Messaggio di avviso

*La sottoscrizione di Azure associata al dominio gestito è stata eliminata.  Servizi di dominio Azure AD richiede una sottoscrizione attiva per continuare a funzionare correttamente.*

### <a name="resolution"></a>Risoluzione

Servizi di dominio Active Directory di Azure richiede una sottoscrizione attiva e non può essere spostato in una sottoscrizione diversa. Se la sottoscrizione di Azure a cui è stato associato il dominio gestito di Servizi di dominio Active Directory di Azure viene eliminata, è necessario ricreare una sottoscrizione di Azure azure e un dominio gestito di Servizi di dominio Active Directory di Azure.If the Azure subscription that the Azure AD DS managed domain was associated with is deleted, you must recreate an Azure subscription and Azure AD DS managed domain.

1. Creare una [sottoscrizione di Azure.](../cost-management-billing/manage/create-subscription.md)
1. [Eliminare il dominio gestito](delete-aadds.md) di Servizi di dominio Active Directory di Azure dalla directory di Azure AD esistente.
1. Creare un dominio gestito di [Servizi di dominio Active Directory di Azure sostitutivo.](tutorial-create-instance.md)

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: La sottoscrizione di Azure è disabilitata

### <a name="alert-message"></a>Messaggio di avviso

*La sottoscrizione di Azure associata al dominio gestito non è attiva.  Servizi di dominio Azure AD richiede una sottoscrizione attiva per continuare a funzionare correttamente.*

### <a name="resolution"></a>Risoluzione

Servizi di dominio Active Directory richiede una sottoscrizione attiva. Se la sottoscrizione di Azure a cui è stato associato il dominio gestito di Servizi di dominio Active Directory non è attiva, è necessario rinnovarla per riattivare la sottoscrizione.

1. [Rinnovare la sottoscrizione di Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Una volta rinnovata la sottoscrizione, una notifica di Servizi di dominio Active Directory di Azure consente di riabilitare il dominio gestito.

Quando il dominio gestito viene abilitato di nuovo, l'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Directory della sottoscrizione spostate

### <a name="alert-message"></a>Messaggio di avviso

*La sottoscrizione usata da Servizi di dominio Azure AD è stata spostata in un'altra directory. Servizi di dominio Azure AD deve avere una sottoscrizione attiva nella stessa directory per funzionare correttamente.*

### <a name="resolution"></a>Risoluzione

Servizi di dominio Active Directory di Azure richiede una sottoscrizione attiva e non può essere spostato in una sottoscrizione diversa. Se la sottoscrizione di Azure a cui è stato associato il dominio gestito di Servizi di dominio Active Directory di Azure AD viene spostata, spostare nuovamente la sottoscrizione nella directory precedente oppure [eliminare il dominio gestito](delete-aadds.md) dalla directory esistente e creare un dominio gestito di Servizi di dominio Active Directory di Azure [sostitutivo nella sottoscrizione scelta.](tutorial-create-instance.md)

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Non è possibile trovare risorse per il dominio gestito

### <a name="alert-message"></a>Messaggio di avviso

*Una risorsa utilizzata per il dominio gestito è stata eliminata. Questa risorsa è necessaria per il corretto funzionamento di Servizi di dominio Azure AD.*

### <a name="resolution"></a>Risoluzione

Servizi di dominio Active Directory di Azure crea risorse aggiuntive per funzionare correttamente, ad esempio indirizzi IP pubblici, interfacce di rete virtuale e un servizio di bilanciamento del carico. Se una di queste risorse viene eliminata, il dominio gestito è in uno stato non supportato e impedisce la gestione del dominio. Per altre informazioni su queste risorse, vedere Risorse di rete usate da Servizi di dominio Active Directory di Azure.For more information on these resources, see [Network resources used by Azure AD DS.](network-considerations.md#network-resources-used-by-azure-ad-ds)

Questo avviso viene generato quando viene eliminata una di queste risorse necessarie. Se la risorsa è stata eliminata meno di 4 ore fa, è possibile che la piattaforma Azure possa ricreare automaticamente la risorsa eliminata. I passaggi seguenti illustrano come controllare lo stato di integrità e il timestamp per l'eliminazione delle risorse:The following steps outline how to check the health status and timestamp for resource deletion:

1. Nel portale di Azure cercare e selezionare **Servizi di dominio**. Scegliere il dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio *aaddscontoso.com*.
1. Nel riquadro di spostamento sinistro selezionare **Integrità**.
1. Nella pagina di integrità selezionare l'avviso con ID *AADDS109*.
1. L'avviso ha un timestamp per la prima volta che è stato trovato. Se il timestamp è inferiore a 4 ore fa, la piattaforma Azure potrebbe essere in grado di ricreare automaticamente la risorsa e risolvere l'avviso da sola.

    Se l'avviso ha più di 4 ore, il dominio gestito di Servizi di dominio Active Directory di Azure si trova in uno stato irreversibile. [Eliminare il dominio gestito](delete-aadds.md) di Servizi di dominio Active Directory di Azure e quindi [creare un dominio gestito sostitutivo.](tutorial-create-instance.md)

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: La subnet associata al dominio gestito è piena

### <a name="alert-message"></a>Messaggio di avviso

*La subnet selezionata per la distribuzione di Azure AD Domain Services è piena e non ha spazio sufficiente per il controller di dominio aggiuntivo che deve essere creato.*

### <a name="resolution"></a>Risoluzione

La subnet della rete virtuale per Servizi di dominio Active Directory di Azure richiede indirizzi IP sufficienti per le risorse create automaticamente. Questo spazio di indirizzi IP include la necessità di creare risorse sostitutive se è presente un evento di manutenzione. Per ridurre al minimo il rischio di esaurire gli indirizzi IP disponibili, non distribuire risorse aggiuntive, ad esempio le proprie macchine virtuali, nella stessa subnet di rete virtuale di Azure AD DS.

Questo errore è irreversibile. Per risolvere l'avviso, eliminare il dominio gestito di [Servizi di dominio Active Directory di Azure esistente](delete-aadds.md) e ricrearlo. In caso di problemi durante l'eliminazione del dominio gestito di Servizi di dominio Active Directory di Azure, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Entità servizio non autorizzata

### <a name="alert-message"></a>Messaggio di avviso

*Un'entità servizio usata da Servizi di dominio Azure AD per servire il dominio non è autorizzata a gestire le risorse nella sottoscrizione di Azure.A service principal that Azure AD Domain Services uses to service your domain is not authorized to manage resources on the Azure subscription. L'entità servizio deve ottenere le autorizzazioni per servire il dominio gestito.*

### <a name="resolution"></a>Risoluzione

Alcune entità servizio generate automaticamente vengono usate per gestire e creare risorse per un dominio gestito di Azure AD DS.Some automatically generated service principals are used to manage and create resources for an Azure AD DS managed domain. Se le autorizzazioni di accesso per una di queste entità servizio vengono modificate, il dominio non è in grado di gestire correttamente le risorse. I passaggi seguenti illustrano come comprendere e quindi concedere le autorizzazioni di accesso a un'entità servizio:The following steps show you how to understand and then grant access permissions to a service principal:

1. Informazioni sul controllo degli [accessi in base al ruolo e su come concedere l'accesso alle applicazioni nel portale](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)di Azure.
2. Esaminare l'accesso che l'entità servizio con l'ID *abba844e-bc0e-44b0-947a-dc74e5d09022* ha e concedere l'accesso negato in una data precedente.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Gli indirizzi IP disponibili nel dominio gestito non sono sufficienti

### <a name="alert-message"></a>Messaggio di avviso

*È stato identificato che la subnet della rete virtuale in questo dominio potrebbe non avere indirizzi IP sufficienti. Servizi di dominio Azure AD richiede almeno due indirizzi IP disponibili all'interno della subnet in cui è abilitato. È consigliabile avere almeno 3-5 indirizzi IP di riserva all'interno della subnet. Ciò può verificarsi se altre macchine virtuali sono distribuite all'interno della subnet, esaurendo così il numero di indirizzi IP disponibili o se esiste una restrizione sul numero di indirizzi IP disponibili nella subnet.*

### <a name="resolution"></a>Risoluzione

La subnet della rete virtuale per Servizi di dominio Active Directory di Azure richiede indirizzi IP sufficienti per le risorse create automaticamente. Questo spazio di indirizzi IP include la necessità di creare risorse sostitutive se è presente un evento di manutenzione. Per ridurre al minimo il rischio di esaurire gli indirizzi IP disponibili, non distribuire risorse aggiuntive, ad esempio le proprie macchine virtuali, nella stessa subnet di rete virtuale di Azure AD DS.

Per risolvere questo avviso, eliminare il dominio gestito di Servizi di dominio Active Directory di Azure esistente e ricrearlo in una rete virtuale con un intervallo di indirizzi IP sufficientemente grande. Questo processo è problematico in quanto il dominio gestito di Servizi di dominio Active Directory di Azure non è disponibile e tutte le risorse personalizzate create come le unità ora o gli account del servizio vengono persi.

1. [Eliminare il dominio gestito](delete-aadds.md) di Servizi di dominio Active Directory di Azure dalla directory.
1. Per aggiornare l'intervallo di indirizzi IP della rete virtuale, cercare e selezionare Rete virtuale nel portale di Azure.To update the virtual network IP address range, search for and select *Virtual network* in the Azure portal. Selezionare la rete virtuale per Servizi di dominio Active Directory di Azure con l'intervallo di indirizzi IP ridotto.
1. In **Impostazioni**selezionare *Spazio indirizzi*.
1. Aggiornare l'intervallo di indirizzi scegliendo l'intervallo di indirizzi esistente e modificandolo oppure aggiungendo un intervallo di indirizzi aggiuntivo. Assicurarsi che il nuovo intervallo di indirizzi IP sia sufficientemente grande per l'intervallo di subnet di Azure AD DS. Quando sei pronto, **Salva** le modifiche.
1. Selezionare **Subnet** nel riquadro di spostamento a sinistra.
1. Scegliere la subnet che si desidera modificare o creare una subnet aggiuntiva.
1. Aggiornare o specificare un intervallo di indirizzi IP sufficientemente grande, quindi **salvare** le modifiche.
1. Creare un dominio gestito di [Servizi di dominio Active Directory di Azure sostitutivo.](tutorial-create-instance.md) Assicurarsi di selezionare la subnet di rete virtuale aggiornata con un intervallo di indirizzi IP sufficientemente ampio.

L'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Le risorse non sono recuperabili

### <a name="alert-message"></a>Messaggio di avviso

*Le risorse usate da Azure AD Domain Services sono state rilevate con uno stato imprevisto e non possono essere ripristinate.*

### <a name="resolution"></a>Risoluzione

Questo errore è irreversibile. Per risolvere l'avviso, eliminare il dominio gestito di [Servizi di dominio Active Directory di Azure esistente](delete-aadds.md) e ricrearlo. In caso di problemi durante l'eliminazione del dominio gestito di Servizi di dominio Active Directory di Azure, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Subnet non valida

### <a name="alert-message"></a>Messaggio di avviso

*La subnet selezionata per la distribuzione di Servizi di dominio Azure AD non è valida e non può essere usata.*

### <a name="resolution"></a>Risoluzione

Questo errore è irreversibile. Per risolvere l'avviso, eliminare il dominio gestito di [Servizi di dominio Active Directory di Azure esistente](delete-aadds.md) e ricrearlo. In caso di problemi durante l'eliminazione del dominio gestito di Servizi di dominio Active Directory di Azure, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Le risorse sono bloccate

### <a name="alert-message"></a>Messaggio di avviso

*Non è possibile eseguire operazioni in una o più risorse di rete usate dal dominio gestito perché l'ambito di destinazione è stato bloccato.*

### <a name="resolution"></a>Risoluzione

I blocchi delle risorse possono essere applicati alle risorse di Azure per impedire modifiche o eliminazioni. Poiché Servizi di dominio Active Directory di Azure è un servizio gestito, la piattaforma Azure deve avere la possibilità di apportare modifiche alla configurazione. Se viene applicato un blocco delle risorse in alcuni componenti di Servizi di dominio Active Directory di Azure, la piattaforma Azure non può eseguire le attività di gestione.

Per verificare la presenza di blocchi delle risorse nei componenti di Servizi di dominio Active Directory di Azure e rimuoverli, completare i passaggi seguenti:To check for resource locks on the Azure AD DS components and remove them, complete the following steps:

1. Per ogni componente di rete di Servizi di dominio Active Directory di Azure nel gruppo di risorse, ad esempio rete virtuale, interfaccia di rete o indirizzo IP pubblico, controllare i log delle operazioni nel portale di Azure.For each of the Azure AD DS network components in your resource group, such as virtual network, network interface, or public IP address, check the operation logs in the Azure portal. Questi registri delle operazioni devono indicare il motivo per cui un'operazione non riesce e dove viene applicato un blocco delle risorse.
1. Selezionare la risorsa a cui viene applicato un blocco, quindi in **Blocchi**selezionare e rimuovere i blocchi.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Le risorse non sono utilizzabili

### <a name="alert-message"></a>Messaggio di avviso

*Non è possibile eseguire operazioni in una o più risorse di rete usate dal dominio gestito a causa di restrizioni dei criteri.*

### <a name="resolution"></a>Risoluzione

I criteri vengono applicati alle risorse di Azure e ai gruppi di risorse che controllano le azioni di configurazione consentite. Poiché Servizi di dominio Active Directory di Azure è un servizio gestito, la piattaforma Azure deve avere la possibilità di apportare modifiche alla configurazione. Se un criterio viene applicato in alcuni componenti di Servizi di dominio Active Directory di Azure, la piattaforma Azure potrebbe non essere in grado di eseguire le attività di gestione.

Per verificare i criteri applicati nei componenti di Servizi di dominio Active Directory di Azure e aggiornarli, completare i passaggi seguenti:To check for applied policies on the Azure AD DS components and update them, complete the following steps:

1. Per ogni componente di rete di Servizi di dominio Active Directory di Azure nel gruppo di risorse, ad esempio rete virtuale, scheda di interfaccia di rete o indirizzo IP pubblico, controllare i log delle operazioni nel portale di Azure.For each of the Azure AD DS network components in your resource group, such as virtual network, NIC, or public IP address, check the operation logs in the Azure portal. Questi registri delle operazioni devono indicare il motivo per cui un'operazione non riesce e dove viene applicato un criterio restrittivo.
1. Selezionare la risorsa a cui viene applicato un criterio, quindi in **Criteri**selezionare e modificare il criterio in modo che sia meno restrittivo.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Sincronizzazione non eseguita da qualche tempo

### <a name="alert-message"></a>Messaggio di avviso

*Il dominio gestito è stato sincronizzato l'ultima volta con Azure AD in [data]. Gli utenti potrebbero non essere in grado di accedere al dominio gestito o le appartenenze ai gruppi potrebbero non essere sincronizzate con Azure AD.*

### <a name="resolution"></a>Risoluzione

[Controllare l'integrità](check-health.md) di Servizi di dominio Active Directory di Azure per eventuali avvisi che indicano problemi nella configurazione del dominio gestito. I problemi relativi alla configurazione di rete possono bloccare la sincronizzazione da Azure AD. Se si è in grado di risolvere gli avvisi che indicano un problema di configurazione, attendere due ore e verificare se la sincronizzazione è stata completata correttamente.

I motivi comuni seguenti causano l'interruzione della sincronizzazione in un dominio gestito di Azure AD DS:The following common reasons cause synchronization to stop in an Azure AD DS managed domains:

* La connettività di rete necessaria è bloccata. Per altre informazioni su come verificare la presenza di problemi nella rete virtuale di Azure, vedere [Risolvere i problemi relativi ai gruppi](alert-nsg.md) di sicurezza di rete e ai requisiti di rete per Servizi di dominio Azure [AD.](network-considerations.md)
*  La sincronizzazione delle password non è stata configurata o è stata completata correttamente quando è stato distribuito il dominio gestito di Servizi di dominio Active Directory di Azure.Password synchronization wasn't set up or successfully completed when the Azure AD DS managed domain was deployed. È possibile impostare la sincronizzazione delle password per [gli utenti solo cloud](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) o gli utenti [ibridi da](tutorial-configure-password-hash-sync.md)quella precedente.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Backup non eseguito da qualche tempo

### <a name="alert-message"></a>Messaggio di avviso

*L'ultimo backup del dominio gestito è stato eseguito in data [data].*

### <a name="resolution"></a>Risoluzione

[Controllare l'integrità](check-health.md) di Servizi di dominio Active Directory di Azure per gli avvisi che indicano problemi nella configurazione del dominio gestito. I problemi relativi alla configurazione di rete possono impedire alla piattaforma Azure di eseguire correttamente i backup. Se si è in grado di risolvere gli avvisi che indicano un problema di configurazione, attendere due ore e verificare se la sincronizzazione è stata completata correttamente.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Sospensione per sottoscrizione disabilitata

### <a name="alert-message"></a>Messaggio di avviso

*Il dominio gestito è stato sospeso perché la sottoscrizione di Azure associata al dominio non è attiva.*

### <a name="resolution"></a>Risoluzione

> [!WARNING]
> Se un dominio gestito di Servizi di dominio Active Directory di Azure viene sospeso per un periodo di tempo prolungato, è possibile che venga eliminato. Risolvere il motivo della sospensione il più rapidamente possibile. Per altre informazioni, vedere Informazioni sugli stati sospesi per Servizi di dominio Active Directory di Azure.For more information, see [Understand the suspended states for Azure AD DS](suspension.md).

Servizi di dominio Active Directory richiede una sottoscrizione attiva. Se la sottoscrizione di Azure a cui è stato associato il dominio gestito di Servizi di dominio Active Directory non è attiva, è necessario rinnovarla per riattivare la sottoscrizione.

1. [Rinnovare la sottoscrizione di Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Una volta rinnovata la sottoscrizione, una notifica di Servizi di dominio Active Directory di Azure consente di riabilitare il dominio gestito.

Quando il dominio gestito viene abilitato di nuovo, l'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Sospensione per configurazione non valida

### <a name="alert-message"></a>Messaggio di avviso

*Il dominio gestito è stato sospeso a causa di una configurazione non valida. Il servizio non è stato in grado di gestire, applicare patch o aggiornare i controller di dominio per il dominio gestito per molto tempo.*

### <a name="resolution"></a>Risoluzione

> [!WARNING]
> Se un dominio gestito di Servizi di dominio Active Directory di Azure viene sospeso per un periodo di tempo prolungato, è possibile che venga eliminato. Risolvere il motivo della sospensione il più rapidamente possibile. Per altre informazioni, vedere Informazioni sugli stati sospesi per Servizi di dominio Active Directory di Azure.For more information, see [Understand the suspended states for Azure AD DS](suspension.md).

[Controllare l'integrità](check-health.md) di Servizi di dominio Active Directory di Azure per gli avvisi che indicano problemi nella configurazione del dominio gestito. Se si è in grado di risolvere gli avvisi che indicano un problema di configurazione, attendere due ore e verificare se la sincronizzazione è stata completata. Quando sei pronto, [apri una richiesta][azure-support] di supporto di Azure per riabilitare il dominio gestito di Azure AD DS.

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
