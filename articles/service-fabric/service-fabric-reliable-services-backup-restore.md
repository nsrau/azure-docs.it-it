<properties
   pageTitle="Backup e ripristino di Service Fabric | Microsoft Azure"
   description="Documentazione concettuale per il backup e ripristino di Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/19/2016"
   ms.author="mcoskun"/>

# Eseguire il backup e il ripristino di Reliable Services e Reliable Actors

Azure Service Fabric è una piattaforma a disponibilità elevata che replica lo stato in più nodi per mantenere questa disponibilità elevata. Anche in caso di errore di un nodo nel cluster, il servizio rimarrà quindi comunque disponibile. Anche se questa ridondanza predefinita fornita dalla piattaforma può essere sufficiente per alcune situazioni, in determinati casi è preferibile che il servizio esegua il backup dei dati in un archivio esterno.

>[AZURE.NOTE] È fondamentale eseguire il backup e il ripristino dei dati (e verificare che funzionino come previsto) per poter eseguire il ripristino in caso di perdita di dati.

Ad esempio, è possibile che in un servizio sia consigliabile eseguire il backup dei dati negli scenari seguenti:

* In caso di perdita permanente di un intero Service Fabric Cluster o di tutti i nodi in esecuzione in una partizione specifica.

* Errori amministrativi che provocano l'eliminazione o il danneggiamento accidentale dello stato. Ad esempio, questo problema può verificarsi se un amministratore con privilegi sufficienti elimina accidentalmente il servizio.

* Bug nel servizio che provocano il danneggiamento dei dati. Ad esempio, questo problema può verificarsi quando un aggiornamento del codice di servizio inizia a scrivere dati non corretti in una raccolta Reliable Collections. In tal caso, potrebbe essere necessario ripristinare uno stato precedente sia per il codice che per i dati.

* Elaborazione dati offline. Potrebbe essere utile eseguire offline l'elaborazione dei dati per la business intelligence separatamente dal servizio che genera i dati.

La funzionalità Backup/Ripristino consente ai servizi basati sull'API Reliable Services di creare e ripristinare i backup. Le API di backup fornite dalla piattaforma consentono l'esecuzione dei backup dello stato di una partizione del servizio senza bloccare le operazioni di lettura o scrittura. Le API di ripristino consentono il ripristino dello stato di una partizione del servizio da un backup specificato.

## Tipi di backup

Sono disponibili due opzioni di backup: completo e incrementale. Un backup completo contiene tutti i dati necessari per ricreare lo stato della replica, ossia i checkpoint e tutti i record di log. Visto che contiene i checkpoint e il log, un backup completo può ripristinarsi autonomamente.

I problemi con i backup completi si verificano quando i checkpoint sono di grandi dimensioni. Ad esempio, una replica con un stato di 16 GB avrà checkpoint che aggiungono circa 16 GB. Se l'obiettivo del punto di ripristino è di 5 minuti, il backup della replica deve essere eseguito ogni 5 minuti. A ogni backup, dovrà copiare 16 GB dei checkpoint oltre a 50 MB di log (configurabili usando **CheckpointThresholdInMB**).

![Esempio di backup completo.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Per risolvere questo problema è possibile usare i backup incrementali, che eseguono solo il backup dei record del log successivi all'ultimo backup.

![Esempio di backup incrementale.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

I backup incrementali includono solo le modifiche successive all'ultimo backup, senza checkpoint, quindi tendono a essere più veloci ma non possono essere ripristinati in modo indipendente. Per ripristinare un backup incrementale, è necessaria l'intera catena di backup. Una catena di backup inizia con un backup completo e prosegue con diversi backup incrementali contigui.

## Eseguire il backup di Reliable Services

L'autore del servizio ha il controllo completo sul momento di esecuzione dei backup e sulla posizione in cui vengono archiviati.

Per avviare un backup, il servizio deve richiamare la funzione membro ereditata **BackupAsync**. I backup possono essere eseguiti solo da repliche primarie e richiedono la concessione dello stato di scrittura.

Come mostrato di seguito, **BackupAsync** accetta un oggetto **BackupDescription**, in cui è possibile specificare un backup completo o incrementale, nonché una funzione di callback, **Func<< BackupInfo, CancellationToken, Task<bool>>>**, che viene richiamata quando la cartella di backup è stata creata in locale e può essere spostata in un archivio esterno.

```C#

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Le richieste di accettazione di un backup incrementale possono non riuscire con **FabricMissingFullBackupException**, che indica che la replica non ha mai eseguito un backup completo o che alcuni record di log eseguiti dopo l'ultimo backup sono stati troncati. Gli utenti possono cambiare la frequenza di troncamento modificando **CheckpointThresholdInMB**.

**BackupInfo** fornisce le informazioni relative al backup, incluso il percorso della cartella in cui il runtime ha salvato il backup (**BackupInfo.Directory**). La funzione di callback può spostare **BackupInfo.Directory** in un archivio esterno o in un'altra posizione. Questa funzione restituisce anche un valore booleano che indica se è riuscita a spostare la cartella di backup nel percorso di destinazione.

Il codice seguente illustra in che modo è possibile usare il metodo **BackupCallbackAsync** per caricare il backup in Archiviazione di Azure:

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

Nell'esempio precedente **ExternalBackupStore** corrisponde alla classe di esempio usata per interfacciarsi con il servizio di archiviazione BLOB di Azure e **UploadBackupFolderAsync** è il metodo che comprime la cartella e la inserisce nell'archivio BLOB di Azure.

Si noti che:

- In un dato momento può essere in corso una sola operazione di backup per replica. Più chiamate **BackupAsync** contemporanee provocheranno l'eccezione **FabricBackupInProgressException** che richiede di limitare a una le esecuzioni dei backup.

- In caso di failover di una replica durante l'esecuzione di un backup, è possibile che il backup non venga completato. Al termine del failover, il servizio dovrà quindi riavviare il backup richiamando **BackupAsync**, se necessario.

## Ripristinare Reliable Services

In generale, i casi in cui potrebbe essere necessario eseguire un'operazione di ripristino rientrano in una di queste categorie:

- La partizione del servizio ha perso dati. Ad esempio, il disco per due su tre repliche per una partizione, inclusa la replica primaria, viene danneggiato o cancellato. La nuova replica primaria potrebbe dover ripristinare i dati da un backup.

- L'intero servizio è andato perso. Ad esempio, un amministratore rimuove l'intero servizio ed è quindi necessario ripristinare il servizio e i dati.

- Il servizio ha replicato dati danneggiati dell'applicazione, ad esempio a causa di un bug dell'applicazione. Sarà quindi necessario aggiornare o ripristinare il servizio per rimuovere la causa del danneggiamento e ripristinare dati non danneggiati.

Anche se sono possibili molti approcci, gli esempi seguenti sono basati sull'uso di **RestoreAsync** per il ripristino dagli scenari precedenti.

## Perdita di dati della partizione in Reliable Services

In questo caso, il runtime rileva automaticamente la perdita dei dati e richiama l'API **OnDataLossAsync**.

L'autore del servizio deve eseguire le operazioni seguenti per il ripristino:

- Eseguire l'override del metodo della classe base virtuale **OnDataLossAsync**.

- Trovare il backup più recente nella posizione esterna che include i backup del servizio.

- Scaricare il backup più recente e decomprimerlo nella cartella di backup, se è compresso

- Il metodo **OnDataLossAsync** fornisce un oggetto **RestoreContext**. Chiamare l'API **RestoreAsync** sull'oggetto **RestoreContext** specificato.

- Restituire true se il ripristino è stato completato.

Ecco un esempio di implementazione del metodo **OnDataLossAsync**:

```C#

protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

**RestoreDescription** passato alla chiamata **RestoreContext.RestoreAsync** contiene un membro denominato **BackupFolderPath**. Quando si ripristina un singolo backup completo, **BackupFolderPath** deve essere impostato sul percorso locale della cartella che contiene il backup completo. Quando si ripristina un backup completo e alcuni backup incrementali, **BackupFolderPath** deve essere impostato sul percorso locale della cartella che contiene sia il backup completo che tutti i backup incrementali. La chiamata **RestoreAsync** può generare un'eccezione **FabricMissingFullBackupException** se l'oggetto **BackupFolderPath** specificato non contiene un backup completo. Può anche generare **ArgumentException** se **BackupFolderPath** ha una catena interrotta di backup incrementali, ad esempio, se contiene il backup completo e il primo e il terzo backup incrementale, ma non il secondo.

>[AZURE.NOTE] Il valore RestorePolicy è Safe per impostazione predefinita. L'API **RestoreAsync** non riuscirà con ArgumentException se rileva che la cartella di backup include uno stato precedente o uguale allo stato contenuto nella replica. Per ignorare questo controllo di sicurezza si può usare **RestorePolicy.Force**, specificato nell'ambito di **RestoreDescription**.

## Servizio eliminato o perso

Se un servizio viene rimosso, per ripristinare i dati sarà prima di tutto necessario ricrearlo. È importante creare il servizio con la stessa configurazione, ad esempio con lo stesso schema di partizionamento, in modo che sia possibile ripristinare i dati senza problemi. Quando il servizio è attivo, è necessario richiamare l'API per il ripristino dei dati (**OnDataLossAsync** precedente) su ogni partizione del servizio. Per farlo, usare **[FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx)** in ogni partizione.

A partire da questo punto, l'implementazione è uguale a quella dello scenario precedente. Ogni partizione deve ripristinare il backup rilevante più recente dall'archivio esterno. Occorre prestare attenzione al fatto che ora l'ID della partizione potrebbe essere diverso, perché il runtime crea dinamicamente gli ID delle partizioni. Il servizio deve quindi archiviare le informazioni sulla partizione e il nome del servizio appropriati per identificare il più recente backup corretto da ripristinare per ogni partizione.

>[AZURE.NOTE] Non è consigliabile usare **FabricClient.ServiceManager.InvokeDataLossAsync** in ogni partizione per ripristinare l'intero servizio perché può danneggiare lo stato del cluster.

## Replica di dati dell'applicazione danneggiati

Se l'aggiornamento dell'applicazione appena distribuito include un bug, potrebbe provocare il danneggiamento dei dati. È ad esempio possibile che l'aggiornamento di un'applicazione inizi ad aggiornare ogni record relativo a numeri di telefono in un oggetto ReliableDictionary specificando un prefisso non valido. In questo caso, i numeri di telefono non validi verranno replicati, perché Service Fabric non conosce la natura dei dati archiviati.

Dopo avere rilevato un bug così grave da causare il danneggiamento dei dati, è prima di tutto necessario bloccare il servizio a livello di applicazione e, se possibile, eseguire l'aggiornamento alla versione del codice dell'applicazione che non include il bug. Anche dopo la correzione del codice del servizio è tuttavia possibile che i dati siano ancora danneggiati e che sia necessario ripristinarli. In questi casi potrebbe non essere sufficiente ripristinare il backup più recente, perché è possibile che anche i backup più recenti siano danneggiati. È quindi necessario trovare il backup più recente eseguito prima del danneggiamento dei dati.

Se non si sa con esattezza quali siano i backup danneggiati, è possibile distribuire un nuovo Service Fabric Cluster e ripristinare i backup delle partizioni interessate con la stessa procedura dello scenario "Servizio eliminato o perso" precedente. Per ogni partizione, avviare il ripristino dei backup dal più recente al meno recente. Dopo aver trovato un backup non danneggiato, spostare o eliminare tutti i backup della partizione più recenti di quel backup. Ripetere questo processo per ogni partizione. Quando **OnDataLossAsync** viene chiamato sulla partizione nel cluster di produzione, il backup più recente trovato nell'archivio esterno verrà selezionato dal processo precedente.

È ora possibile usare la procedura illustrata nella sezione "Servizio eliminato o perso" per ripristinare lo stato del servizio sul valore precedente al danneggiamento da parte del codice con bug.

Si noti che:

- Quando si esegue il ripristino è possibile che il backup ripristinato sia precedente allo stato della partizione prima della perdita dei dati. È quindi necessario procedere al ripristino solo come ultima risorsa per recuperare la quantità maggiore possibile di dati.

- La stringa che rappresenta il percorso della cartella di backup e i percorsi dei file nella cartella di backup può superare i 255 caratteri, in base al percorso FabricDataRoot e alla lunghezza del nome del tipo di applicazione. In alcuni metodi .NET, come **Directory.Move**, ciò può causare l'eccezione **PathTooLongException**. Una soluzione alternativa consiste nel chiamare direttamente le API kernel32, come **CopyFile**.

## Eseguire il backup e il ripristino di Reliable Actors

La funzionalità di backup e ripristino per Reliable Actors si basa su quella fornita da Reliable Services. Il proprietario del servizio deve creare un servizio Actor personalizzato che deriva da **ActorService**, un servizio Reliable Service di Service Fabric che ospita gli attori, e quindi eseguire il backup/ripristino analogamente a quanto descritto nella sezione precedente per Reliable Services. Poiché i backup verranno eseguiti per partizione, verrà eseguito il backup degli stati per tutti gli attori in quella specifica partizione (il ripristino è simile e verrà eseguito anch'esso per partizione).


- Quando si crea un servizio Actor personalizzato, è necessario registrarlo insieme all'attore. Vedere **ActorRuntime.RegistorActorAsync**.
- Al momento, **KvsActorStateProvider** supporta solo il backup completo. Anche l'opzione **RestorePolicy.Safe** viene ignorata da **KvsActorStateProvider**.

>[AZURE.NOTE] L'oggetto predefinito ActorStateProvider, ovvero **KvsActorStateProvider**, **non** pulisce autonomamente le cartelle di backup nella cartella di lavoro dell'applicazione ottenuta con ICodePackageActivationContext.WorkDirectory, quindi la cartella di lavoro potrebbe riempirsi. È necessario pulire esplicitamente la cartella di backup nel callback di backup dopo aver spostato il backup in un archivio esterno.


## Test del backup e del ripristino

È importante assicurarsi che i dati critici vengano sottoposti a backup e che possano essere ripristinati. Per farlo, richiamare il cmdlet **Invoke-ServiceFabricPartitionDataLoss** in PowerShell che può indurre la perdita di dati in una determinata partizione per verificare se la funzionalità di backup e ripristino dei dati per il servizio funziona nel modo previsto. Si può anche richiamare la perdita e il ripristino dei dati a livello di codice da tale evento.

>[AZURE.NOTE] Nell'app di riferimento Web su Github è disponibile un esempio di implementazione della funzionalità di backup e ripristino. Per informazioni più dettagliate, esaminare il servizio Inventory.Service.

## Approfondimento: altri dettagli sul backup e ripristino

Ecco altri dettagli sul backup e ripristino.

### Backup
Reliable State Manager consente di creare backup coerenti senza bloccare alcuna operazione di lettura o scrittura. A questo scopo, utilizza un checkpoint e un meccanismo di persistenza dei log. Reliable State Manager accetta checkpoint fuzzy (semplici) in determinati punti per ridurre la pressione sul log transazionale e migliorare i tempi di ripristino. Quando si chiama **BackupAsync**, Reliable State Manager indica a tutti gli oggetti Reliable di copiare i file di checkpoint più recenti in una cartella di backup locale. Reliable State Manager copia quindi tutti i record del log a partire dal "puntatore iniziale" fino all'ultimo record del log nella cartella di backup. Nel backup sono inclusi tutti i record di log fino all'ultimo e Reliable State Manager conserva i log write-ahead, di conseguenza Reliable State Manager assicura che nel backup siano incluse tutte le transazioni di cui viene eseguito il commit, ovvero di cui viene restituito correttamente **CommitAsync**.

Eventuali transazioni di cui viene eseguito il commit dopo la chiamata di **BackupAsync** potrebbero essere incluse o meno nel backup. Dopo il popolamento della cartella di backup locale da parte della piattaforma, ovvero dopo un backup locale completato dal runtime, verrà richiamato il callback del backup del servizio. Il callback è responsabile dello spostamento della cartella di backup in una posizione esterna, ad esempio nell'Archiviazione di Azure.

### Ripristino

Reliable State Manager consente di eseguire il ripristino da un backup con l'API **RestoreAsync**. Il metodo **RestoreAsync** in **RestoreContext** può essere chiamato solo all'interno del metodo **OnDataLossAsync**. Il valore booleano restituito da **OnDataLossAsync** indica se il servizio ha ripristinato il rispettivo stato da un'origine esterna. Se **OnDataLossAsync** restituisce true, Service Fabric ricompila tutte le altre repliche sulla base di questa replica primaria. Service Fabric fa in modo che le repliche che ricevono la chiamata **OnDataLossAsync** eseguano prima la transizione al ruolo primario, senza che venga concesso lo stato di lettura o scrittura. Per i responsabili dell'implementazione di StatefulService significa che **RunAsync** viene chiamato solo dopo il completamento corretto di **OnDataLossAsync**. **OnDataLossAsync** viene quindi richiamato nella nuova replica primaria. Fino a quando un servizio non completa correttamente l'API, restituendo true o false, e non viene completata la relativa riconfigurazione, l'API continuerà a essere chiamata singolarmente.

**RestoreAsync** rilascia prima di tutto ogni stato esistente nella replica primaria in cui è stato chiamato. Reliable State Manager crea quindi tutti gli oggetti Reliable esistenti nella cartella di backup. Viene quindi indicato agli oggetti Reliable di eseguire il ripristino dai rispettivi checkpoint nella cartella di backup. Reliable State Manager ripristina infine il proprio stato dai record dei log nella cartella di backup ed esegue il ripristino. Come parte del processo di ripristino, le operazioni a partire dal "punto iniziale" con record di log di commit nella cartella di backup vengono riprodotte negli oggetti Reliable. Questo passaggio assicura che lo stato ripristinato sia coerente.

## Passaggi successivi

- [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
- [Notifiche di Reliable Services](service-fabric-reliable-services-notifications.md)
- [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0629_2016-->