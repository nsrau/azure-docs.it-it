<properties
   pageTitle="Servizio di sincronizzazione Azure AD Connect: utilità di pianificazione | Microsoft Azure"
   description="Questo argomento illustra la funzionalità utilità di pianificazione predefinita nel servizio di sincronizzazione Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/26/2016"
   ms.author="andkjell"/>

# Servizio di sincronizzazione Azure AD Connect: utilità di pianificazione
Questo argomento illustra l'utilità di pianificazione predefinita nel servizio di sincronizzazione Azure AD Connect, definito anche motore di sincronizzazione.

Questa funzionalità è stata introdotta nella build 1.1.105.0 rilasciata nel mese di febbraio 2016.

## Panoramica
Il servizio di sincronizzazione Azure AD Connect sincronizzerà le modifiche rilevate nella directory locale usando un'utilità di pianificazione. Sono disponibili due processi dell'utilità di pianificazione, uno per la sincronizzazione delle password e uno per la sincronizzazione di oggetti/attributi e per le attività di manutenzione, illustrato in questo argomento.

Nelle versioni precedenti l'utilità di pianificazione per oggetti e attributi è esterna al motore di sincronizzazione e per attivare il processo di sincronizzazione viene usata l'Utilità di pianificazione di Windows o un servizio separato di Windows. L'utilità di pianificazione è disponibile nelle versioni di tipo 1.1 incluse nel motore di sincronizzazione e consente una personalizzazione parziale. La nuova frequenza di sincronizzazione predefinita è pari a 30 minuti.

L'utilità di pianificazione è responsabile di due attività:

- **Ciclo di sincronizzazione**: processo di importazione, sincronizzazione ed esportazione delle modifiche.
- **Attività di manutenzione**: rinnovo di chiavi e certificati per la reimpostazione delle password e per il servizio Registrazione dispositivo. Consente di ripulire le voci obsolete nel log operazioni.

L'utilità di pianificazione stessa è sempre in esecuzione, ma può essere configurata per eseguire solo una o nessuna di queste attività. Ad esempio, se è necessario avere un processo di ciclo di sincronizzazione personalizzato, è possibile disabilitare questa attività nell'utilità di pianificazione, ma eseguire comunque l'attività di manutenzione.

## Configurazione dell'utilità di pianificazione
Per visualizzare le impostazioni attuali della configurazione, passare a PowerShell ed eseguire il comando `Get-ADSyncScheduler`. Si otterrà un risultato simile al seguente:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

- **AllowedSyncCycleInterval**: frequenza massima consentita da Azure AD per l'esecuzione della sincronizzazione. La sincronizzazione con una frequenza superiore a quella specificata non è supportata.
- **CurrentlyEffectiveSyncCycleInterval**: pianificazione attualmente applicata. Avrà lo stesso valore di CustomizedSyncInterval (se impostato), se non ha una frequenza superiore ad AllowedSyncInterval. Se si modifica il valore di CustomizedSyncCycleInterval, la modifica verrà applicata dopo il ciclo di sincronizzazione successivo.
- **CustomizedSyncCycleInterval**: se si vuole che l'utilità di pianificazione sia eseguita con una frequenza diversa dal valore predefinito di 30 minuti, è possibile configurare questa impostazione. Nell'immagine precedente l'utilità di pianificazione è stata impostata per essere eseguita ogni ora. Se si imposta un valore inferiore a quello di AllowedSyncInterval, verrà usato quest'ultimo valore.
- **NextSyncCyclePolicyType**: differenziale o iniziale. Definisce se l'esecuzione successiva deve elaborare solo le modifiche differenziali o se deve eseguire un'importazione e una sincronizzazione complete, in modo da rielaborare anche eventuali regole nuove o cambiate.
- **NextSyncCycleStartTimeInUTC**: ora di inizio del prossimo ciclo di sincronizzazione da parte dell'utilità di pianificazione.
- **PurgeRunHistoryInterval**: tempo di conservazione dei log operazioni. È possibile esaminarli in Synchronization Service Manager. Per impostazione predefinita, vengono conservati per 7 giorni.
- **SyncCycleEnabled**: indica se l'utilità di pianificazione esegue i processi di importazione, sincronizzazione ed esportazione come parte del funzionamento normale.
- **MaintenanceEnabled**: indica se il processo di manutenzione è abilitato. Aggiornerà i certificati/le chiavi e ripulirà i log operazioni.
- **IsStagingModeEnabled**: indica se il [modello di staging](active-directory-aadconnectsync-operations.md#staging-mode) è abilitato.

È possibile modificare tutte queste impostazioni con `Set-ADSyncScheduler`. Il parametro IsStagingModeEnabled può essere configurato solo dall'installazione guidata.

La configurazione dell'utilità di pianificazione viene archiviata in Azure AD. Se si dispone di un server di gestione temporanea, qualsiasi modifica apportata nel server primario avrà effetto anche nel server di gestione temporanea (fatta eccezione per IsStagingModeEnabled).

## Avviare l'utilità di pianificazione
Per impostazione predefinita, l'utilità di pianificazione verrà eseguita ogni 30 minuti. In alcuni casi è possibile che si voglia eseguire un ciclo di sincronizzazione tra i cicli pianificati o che sia necessario eseguirne un tipo diverso.

**Ciclo di sincronizzazione differenziale** Un ciclo di sincronizzazione differenziale include i passaggi seguenti:

- Importazione differenziale su tutti i connettori
- Sincronizzazione differenziale su tutti i connettori
- Esportazione su tutti i connettori

È possibile che sia presente una modifica urgente da sincronizzare immediatamente e che sia quindi necessario eseguire manualmente un ciclo. Se occorre eseguire manualmente un ciclo, in PowerShell eseguire il comando `Start-ADSyncSyncCycle -PolicyType Delta`.

**Ciclo di sincronizzazione completa** Se è stata apportata una delle modifiche seguenti alla configurazione, è necessario eseguire un ciclo di sincronizzazione completa, definito anche Iniziale:

- Aggiunta di altri oggetti o attributi da importare da una directory di origine
- Modifiche alle regole di sincronizzazione
- Modifica dei [filtri](active-directory-aadconnectsync-configure-filtering.md) in modo che venga incluso un numero diverso di oggetti

Se è stata apportata una di queste modifiche, è necessario eseguire un ciclo di sincronizzazione completa, in modo che il motore di sincronizzazione possa riconsolidare gli spazi connettore. Un ciclo di sincronizzazione completa include i passaggi seguenti:

- Importazione completa su tutti i connettori
- Sincronizzazione completa su tutti i connettori
- Esportazione su tutti i connettori

Per avviare un ciclo di sincronizzazione completa, eseguire `Start-ADSyncSyncCycle -PolicyType Initial` da un prompt di PowerShell. Verrà avviato un ciclo di sincronizzazione completa.

## Arrestare l'utilità di pianificazione
Se l'utilità di pianificazione sta eseguendo un ciclo di sincronizzazione, potrebbe essere necessario interromperlo. Ad esempio, se si avvia l'installazione guidata e viene visualizzato questo errore:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Quando un ciclo di sincronizzazione è in esecuzione, non è possibile modificare la configurazione. Si può attendere il completamento del processo da parte dell'utilità di pianificazione oppure è possibile interromperlo, per potere apportare immediatamente le modifiche. L'interruzione del ciclo corrente non è dannosa ed eventuali modifiche non ancora elaborate verranno elaborate all'esecuzione successiva.

1. Richiedere prima di tutto all'utilità di pianificazione di interrompere il ciclo corrente con il cmdlet `Stop-ADSyncSyncCycle` di PowerShell.
2. L'interruzione dell'utilità di pianificazione non comporta l'interruzione dell'attività corrente del connettore attuale. Per imporre l'arresto del connettore, eseguire queste azioni: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Avviare **Sychronization Service Manager** dal menu Start. Passare a **Connectors**, evidenziare il connettore con stato **Running** e selezionare **Stop** dalle azioni.

L'utilità di pianificazione è ancora attiva e verrà riavviata alla successiva opportunità.

## Utilità di pianificazione e installazione guidata
Se si avvia l'installazione guidata, l'utilità di pianificazione verrà sospesa temporaneamente. Si presuppone infatti che verranno apportate modifiche alla configurazione, che non possono essere applicate se il motore di sincronizzazione è attivamente in esecuzione. Non lasciare quindi aperta l'installazione guidata, perché impedirà al motore di sincronizzazione di eseguire azioni di sincronizzazione.

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0302_2016-->