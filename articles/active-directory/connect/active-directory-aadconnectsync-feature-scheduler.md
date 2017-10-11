---
title: "Servizio di sincronizzazione Azure AD Connect: utilità di pianificazione | Documentazione Microsoft"
description: "Questo argomento illustra la funzionalità utilità di pianificazione predefinita nel servizio di sincronizzazione Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 63f69756b3933fecdec75cc677e1098447e5b94e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-scheduler"></a>Servizio di sincronizzazione Azure AD Connect: utilità di pianificazione
Questo argomento illustra l'utilità di pianificazione predefinita nel servizio di sincronizzazione Azure AD Connect, anche detto motore di sincronizzazione.

Questa funzionalità è stata introdotta nella build 1.1.105.0 rilasciata nel mese di febbraio 2016.

## <a name="overview"></a>Panoramica
Il servizio di sincronizzazione Azure AD Connect sincronizza le modifiche rilevate nella directory locale usando un'utilità di pianificazione. Sono disponibili due processi dell'utilità di pianificazione, uno per la sincronizzazione delle password e uno per la sincronizzazione di oggetti/attributi e per le attività di manutenzione. Questo argomento illustra il secondo.

Nelle versioni precedenti l'utilità di pianificazione per oggetti e attributi è esterna al motore di sincronizzazione. Usa l'utilità di pianificazione di Windows o un servizio di Windows separato per attivare il processo di sincronizzazione. L'utilità di pianificazione è disponibile nelle versioni di tipo 1.1 incluse nel motore di sincronizzazione e consente una personalizzazione parziale. La nuova frequenza di sincronizzazione predefinita è pari a 30 minuti.

L'utilità di pianificazione è responsabile di due attività:

* **Ciclo di sincronizzazione**. processo di importazione, sincronizzazione ed esportazione delle modifiche.
* **Attività di manutenzione**. rinnovo di chiavi e certificati per la reimpostazione delle password e per il servizio Registrazione dispositivo. Consente di ripulire le voci obsolete nel log operazioni.

L'utilità di pianificazione stessa è sempre in esecuzione, ma può essere configurata per eseguire solo una o nessuna di queste attività. Ad esempio, se è necessario avere un processo di ciclo di sincronizzazione personalizzato, è possibile disabilitare questa attività nell'utilità di pianificazione, ma eseguire comunque l'attività di manutenzione.

## <a name="scheduler-configuration"></a>Configurazione dell'utilità di pianificazione
Per visualizzare le impostazioni attuali della configurazione, passare a PowerShell ed eseguire il comando `Get-ADSyncScheduler`. Il risultato visualizzato è simile al seguente:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

Se quando si esegue questo cmdlet viene visualizzato il messaggio **Non è disponibile il comando o il cmdlet di sincronizzazione** , il modulo PowerShell non viene caricato. Questo problema può verificarsi se si esegue Azure AD Connect in un controller di dominio o in un server con livelli di restrizione di PowerShell più elevati rispetto alle impostazioni predefinite. Se si visualizza questo errore, eseguire `Import-Module ADSync` per rendere disponibile il cmdlet.

* **AllowedSyncCycleInterval**. L'intervallo di tempo più breve tra i cicli di sincronizzazione consentito da Azure AD. La sincronizzazione con una frequenza superiore a quella di questa impostazione non è supportata.
* **CurrentlyEffectiveSyncCycleInterval**. pianificazione attualmente applicata. Ha lo stesso valore di CustomizedSyncInterval (se impostato), se non ha una frequenza superiore ad AllowedSyncInterval. Se si usa una build precedente alla 1.1.281 e si modifica il valore di CustomizedSyncCycleInterval, la modifica viene applicata dopo il ciclo di sincronizzazione successivo. Con la build 1.1.281 la modifica viene applicata immediatamente.
* **CustomizedSyncCycleInterval**. Se si vuole che l'utilità di pianificazione sia eseguita con una frequenza diversa dal valore predefinito di 30 minuti, configurare questa impostazione. Nell'immagine precedente l'utilità di pianificazione è stata impostata per essere eseguita ogni ora. Se si configura questa impostazione su un valore inferiore a quello di AllowedSyncInterval, viene usato quest'ultimo valore.
* **NextSyncCyclePolicyType**. differenziale o iniziale. Definisce se l'esecuzione successiva deve elaborare solo le modifiche differenziali o se deve eseguire un'importazione e una sincronizzazione complete, per poter rielaborare anche eventuali regole nuove o modificate.
* **NextSyncCycleStartTimeInUTC**. Ora di inizio del prossimo ciclo di sincronizzazione da parte dell'utilità di pianificazione.
* **PurgeRunHistoryInterval**. tempo di conservazione dei log operazioni. È possibile esaminare i log in Synchronization Service Manager. Per impostazione predefinita, i log vengono conservati per 7 giorni.
* **SyncCycleEnabled**. indica se l'utilità di pianificazione esegue i processi di importazione, sincronizzazione ed esportazione come parte del funzionamento normale.
* **MaintenanceEnabled**. indica se il processo di manutenzione è abilitato. Aggiorna i certificati o le chiavi e ripulisce i log operazioni.
* **StagingModeEnabled**. Indica se la [modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode) è abilitata. Se questa impostazione è abilitata, impedisce la riesecuzione delle esportazioni, ma esegue ugualmente l'importazione e la sincronizzazione.
* **SchedulerSuspended**. Impostato da Connect durante un aggiornamento per bloccare temporaneamente l'esecuzione dell'utilità di pianificazione.

Alcune di queste impostazioni possono essere modificate con `Set-ADSyncScheduler`. È possibile modificare i parametri seguenti:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Nelle versioni precedenti di Azure AD Connect, **isStagingModeEnabled** era esposto in Set-ADSyncScheduler. L'impostazione di questa proprietà **non è supportata**. La proprietà **SchedulerSuspended** deve essere modificata solo da Connect. L'impostazione di questa proprietà direttamente con PowerShell **non è supportata**.

La configurazione dell'utilità di pianificazione viene archiviata in Azure AD. Se si ha un server di staging, qualsiasi modifica apportata nel server primario ha effetto anche nel server di staging (fatta eccezione per IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintassi: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - giorni, HH - ore, mm - minuti, ss - secondi

Esempio: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Modifica l'utilità di pianificazione in modo che venga eseguita ogni 3 ore.

Esempio: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Modifica l'utilità di pianificazione in modo che venga eseguita ogni giorno.

### <a name="disable-the-scheduler"></a>Disabilitare l'utilità di pianificazione  
Se si devono apportare modifiche alla configurazione, sarà necessario disabilitare l'utilità di pianificazione, ad esempio quando si [configurano i filtri](active-directory-aadconnectsync-configure-filtering.md) o [si apportano modifiche alle regole di sincronizzazione](active-directory-aadconnectsync-change-the-configuration.md).

Per disabilitare l'utilità di pianificazione, eseguire `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Disabilitare l'utilità di pianificazione](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

Dopo avere apportato le modifiche, ricordare di abilitare di nuovo l'utilità di pianificazione con `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Avviare l'utilità di pianificazione
Per impostazione predefinita, l'utilità di pianificazione viene eseguita ogni 30 minuti. In alcuni casi, è possibile che si voglia eseguire un ciclo di sincronizzazione tra i cicli pianificati o che sia necessario eseguirne un tipo diverso.

**Ciclo di sincronizzazione differenziale**  
Un ciclo di sincronizzazione differenziale include i passaggi seguenti:

* Importazione differenziale su tutti i connettori
* Sincronizzazione differenziale su tutti i connettori
* Esportazione su tutti i connettori

Potrebbe essere presente una modifica urgente da sincronizzare immediatamente e potrebbe essere quindi necessario eseguire manualmente un ciclo. Se occorre eseguire manualmente un ciclo, in PowerShell eseguire il comando `Start-ADSyncSyncCycle -PolicyType Delta`.

**Ciclo di sincronizzazione completo**  
Se è stata apportata una delle modifiche seguenti alla configurazione, è necessario eseguire un ciclo di sincronizzazione completo, anche detto Iniziale:

* Aggiunta di altri oggetti o attributi da importare da una directory di origine
* Modifiche alle regole di sincronizzazione
* Modifica dei [filtri](active-directory-aadconnectsync-configure-filtering.md) in modo che venga incluso un numero diverso di oggetti

Se è stata apportata una di queste modifiche, è necessario eseguire un ciclo di sincronizzazione completa, in modo che il motore di sincronizzazione possa riconsolidare gli spazi connettore. Un ciclo di sincronizzazione completa include i passaggi seguenti:

* Importazione completa su tutti i connettori
* Sincronizzazione completa su tutti i connettori
* Esportazione su tutti i connettori

Per avviare un ciclo di sincronizzazione completa, eseguire `Start-ADSyncSyncCycle -PolicyType Initial` a un prompt di PowerShell. Questo comando avvia un ciclo di sincronizzazione completo.

## <a name="stop-the-scheduler"></a>Arrestare l'utilità di pianificazione
Se l'utilità di pianificazione sta eseguendo un ciclo di sincronizzazione, potrebbe essere necessario interromperlo. Ad esempio, se si avvia l'installazione guidata e viene visualizzato questo errore:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Quando un ciclo di sincronizzazione è in esecuzione, non è possibile modificare la configurazione. Si può attendere il completamento del processo da parte dell'utilità di pianificazione oppure è possibile interromperlo, per potere apportare immediatamente le modifiche. L'arresto del ciclo corrente non è dannoso e le modifiche in sospeso vengono elaborate all'esecuzione successiva.

1. Richiedere prima di tutto all'utilità di pianificazione di interrompere il ciclo corrente con il cmdlet `Stop-ADSyncSyncCycle`di PowerShell.
2. Se si usa una build precedente alla 1.1.281, l'arresto dell'utilità di pianificazione non comporta l'interruzione dell'attività corrente del connettore attuale. Per imporre l'arresto del connettore, eseguire queste azioni: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Avviare **Synchronization Service** (Servizio di sincronizzazione) dal menu Start. Passare a **Connettori**, evidenziare il connettore con stato **In esecuzione** e selezionare **Arresta** fra le azioni.

L'utilità di pianificazione è ancora attiva e viene riavviata alla successiva opportunità.

## <a name="custom-scheduler"></a>Utilità di pianificazione personalizzata
I cmdlet illustrati in questa sezione sono disponibili solo nella build [1.1.130.0](active-directory-aadconnect-version-history.md#111300) e successive.

Se l'utilità di pianificazione predefinita non soddisfa i requisiti, è possibile pianificare i connettori con PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
È possibile avviare un profilo per un connettore in questo modo:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

I nomi da usare come [nomi di connettore](active-directory-aadconnectsync-service-manager-ui-connectors.md) e [nomi di profilo di esecuzione](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) sono indicati nell'[interfaccia utente di Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

![Richiamare il profilo di esecuzione](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

Il cmdlet `Invoke-ADSyncRunProfile` è sincrono, ovvero non restituisce il controllo fino a quando il connettore non ha completato l'operazione correttamente o con un errore.

Quando si pianificano i connettori, è consigliabile pianificarli nell'ordine seguente:

1. (Completa/differenziale) Importazione da directory locali, ad esempio Active Directory
2. (Completa/differenziale) Importazione da Azure AD
3. (Completa/differenziale) Sincronizzazione da directory locali, ad esempio Active Directory
4. (Completa/differenziale) Sincronizzazione da Azure AD
5. Esportazione in Azure AD
6. Esportazione in directory locali, ad esempio Active Directory

Questo è l'ordine in cui l'utilità di pianificazione predefinita esegue i connettori.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
È anche possibile monitorare il motore di sincronizzazione per verificare se è occupato o inattivo. Il cmdlet restituisce un risultato vuoto se il motore di sincronizzazione è inattivo e non è in esecuzione un connettore. Se un connettore è in esecuzione, restituisce il nome del connettore.

```
Get-ADSyncConnectorRunStatus
```

![Stato di esecuzione del connettore](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Nella figura precedente, la prima riga proviene da uno stato in cui il motore di sincronizzazione è inattivo. La seconda riga proviene da uno stato in cui Azure AD Connector è in esecuzione.

## <a name="scheduler-and-installation-wizard"></a>Utilità di pianificazione e installazione guidata
Se si avvia l'installazione guidata, l'utilità di pianificazione viene sospesa temporaneamente. Si presuppone infatti che vengano apportate modifiche alla configurazione e queste impostazioni non possono essere applicate se il motore di sincronizzazione è attivamente in esecuzione. Non lasciare quindi aperta l'installazione guidata, perché impedisce al motore di sincronizzazione di eseguire azioni di sincronizzazione.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
