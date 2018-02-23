---
title: Backup e ripristino di Service Fabric | Documentazione Microsoft
description: Documentazione concettuale per il backup e ripristino di Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: d276ce9233da9137c49faf8c4d975bd1dcf2ff81
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="back-up-and-restore-reliable-services-and-reliable-actors"></a>Eseguire il backup e il ripristino di Reliable Services e Reliable Actors
Azure Service Fabric è una piattaforma a disponibilità elevata che replica lo stato in più nodi per mantenere questa disponibilità elevata.  Anche in caso di errore di un nodo nel cluster, il servizio rimarrà quindi comunque disponibile. Anche se questa ridondanza predefinita fornita dalla piattaforma può essere sufficiente per alcune situazioni, in determinati casi è preferibile che il servizio esegua il backup dei dati in un archivio esterno.

> [!NOTE]
> È fondamentale eseguire il backup e il ripristino dei dati (e verificare che funzionino come previsto) per poter eseguire il ripristino in caso di perdita di dati.
> 
> 

Ad esempio, è possibile che in un servizio sia consigliabile eseguire il backup dei dati per evitare gli scenari seguenti:

- Perdita definitiva di un intero cluster di Service Fabric.
- Perdita definitiva della maggior parte delle repliche di una partizione del servizio.
- Errori amministrativi che provocano l'eliminazione o il danneggiamento accidentale dello stato. Ad esempio, questo problema può verificarsi se un amministratore con privilegi sufficienti elimina accidentalmente il servizio.
- Bug nel servizio che provocano il danneggiamento dei dati. Ad esempio, questo problema può verificarsi quando un aggiornamento del codice di servizio inizia a scrivere dati non corretti in una raccolta Reliable Collections. In tal caso, potrebbe essere necessario ripristinare uno stato precedente sia per il codice che per i dati.
- Elaborazione dati offline. Potrebbe essere utile eseguire offline l'elaborazione dei dati per la business intelligence separatamente dal servizio che genera i dati.

La funzionalità Backup/Ripristino consente ai servizi basati sull'API Reliable Services di creare e ripristinare i backup. Le API di backup fornite dalla piattaforma consentono il backup dello stato di una partizione del servizio senza bloccare le operazioni di lettura o scrittura. Le API di ripristino consentono il ripristino dello stato di una partizione del servizio da un backup specificato.

## <a name="types-of-backup"></a>Tipi di backup
Sono disponibili due opzioni di backup: completo e incrementale.
Un backup completo contiene tutti i dati necessari per ricreare lo stato della replica, ossia i checkpoint e tutti i record di log.
Visto che contiene i checkpoint e il log, un backup completo può ripristinarsi autonomamente.

I problemi con i backup completi si verificano quando i checkpoint sono di grandi dimensioni.
Ad esempio, una replica con un stato di 16 GB avrà checkpoint che aggiungono circa 16 GB.
Se l'obiettivo del punto di ripristino è di 5 minuti, il backup della replica deve essere eseguito ogni 5 minuti.
A ogni backup, dovranno essere copiati 16 GB dei checkpoint oltre a 50 MB di log (configurabili usando `CheckpointThresholdInMB`).

![Esempio di backup completo.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Per risolvere questo problema è possibile usare i backup incrementali, in cui il backup contiene solo i record del log modificati dopo l'ultimo backup.

![Esempio di backup incrementale.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

I backup incrementali includono solo le modifiche successive all'ultimo backup, senza checkpoint, quindi tendono a essere più veloci ma non possono essere ripristinati in modo indipendente.
Per ripristinare un backup incrementale, è necessaria l'intera catena di backup.
Una catena di backup inizia con un backup completo e prosegue con diversi backup incrementali contigui.

## <a name="backup-reliable-services"></a>Eseguire il backup di Reliable Services
L'autore del servizio ha il controllo completo sul momento di esecuzione dei backup e sulla posizione in cui vengono archiviati.

Per avviare un backup, il servizio deve chiamare la funzione membro ereditata `BackupAsync`.  
I backup possono essere eseguiti solo da repliche primarie e richiedono la concessione dello stato di scrittura.

Come mostrato di seguito, `BackupAsync` accetta un oggetto `BackupDescription`, in cui è possibile specificare un backup completo o incrementale, nonché una funzione di callback, `Func<< BackupInfo, CancellationToken, Task<bool>>>`, che viene chiamata quando la cartella di backup è stata creata in locale e può essere spostata in un archivio esterno.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

La richiesta di esecuzione di un backup incrementale può non riuscire con `FabricMissingFullBackupException`. Questa eccezione segnala il verificarsi di una delle condizioni seguenti:

- la replica non ha mai eseguito un backup completo dopo essere diventata primaria,
- che alcuni dei record di log dall'ultimo backup sono stati troncati o
- la replica ha superato il limite `MaxAccumulatedBackupLogSizeInMB`.

Gli utenti possono aumentare le probabilità di riuscire a eseguire i backup incrementali configurando `MinLogSizeInMB` o `TruncationThresholdFactor`.
Si noti che l'aumento di questi valori comporta un aumento dell'utilizzo del disco per replica.
Per altre informazioni, vedere [Configurazione di Reliable Services](service-fabric-reliable-services-configuration.md)

`BackupInfo` fornisce le informazioni relative al backup, incluso il percorso della cartella in cui il runtime ha salvato il backup (`BackupInfo.Directory`). La funzione di callback può spostare `BackupInfo.Directory` in un archivio esterno o in un'altra posizione.  Questa funzione restituisce anche un valore booleano che indica se è riuscita a spostare la cartella di backup nel percorso di destinazione.

Il codice seguente illustra in che modo è possibile usare il metodo `BackupCallbackAsync` per caricare il backup in Archiviazione di Azure:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

Nell'esempio precedente `ExternalBackupStore` corrisponde alla classe di esempio usata per interfacciarsi con il servizio di archiviazione BLOB di Azure e `UploadBackupFolderAsync` è il metodo che comprime la cartella e la inserisce nell'archivio BLOB di Azure.

Si noti che:

  - In un dato momento può essere in corso una sola operazione di backup per replica. Se si hanno più chiamate `BackupAsync` simultanee, `FabricBackupInProgressException` limita a una le esecuzioni dei backup.
  - In caso di failover di una replica durante l'esecuzione di un backup, è possibile che il backup non venga completato. Al termine del failover, il servizio dovrà quindi riavviare il backup chiamando `BackupAsync`, se necessario.

## <a name="restore-reliable-services"></a>Ripristinare Reliable Services
In generale, i casi in cui potrebbe essere necessario eseguire un'operazione di ripristino rientrano in una di queste categorie:

  - La partizione del servizio ha perso dati. Ad esempio, il disco per due su tre repliche per una partizione, inclusa la replica primaria, viene danneggiato o cancellato. La nuova replica primaria potrebbe dover ripristinare i dati da un backup.
  - L'intero servizio è andato perso. Ad esempio, un amministratore rimuove l'intero servizio ed è quindi necessario ripristinare il servizio e i dati.
  - Il servizio ha replicato dati danneggiati dell'applicazione, ad esempio a causa di un bug dell'applicazione. Sarà quindi necessario aggiornare o ripristinare il servizio per rimuovere la causa del danneggiamento e ripristinare dati non danneggiati.

Anche se sono possibili molti approcci, gli esempi seguenti sono basati sull'uso di `RestoreAsync` per il ripristino dagli scenari precedenti.

## <a name="partition-data-loss-in-reliable-services"></a>Perdita di dati della partizione in Reliable Services
In questo caso, il runtime rileva automaticamente la perdita dei dati e chiama l'API `OnDataLossAsync`.

L'autore del servizio deve eseguire le operazioni seguenti per il ripristino:

  - Eseguire l'override del metodo della classe base virtuale `OnDataLossAsync`.
  - Trovare il backup più recente nella posizione esterna che include i backup del servizio.
  - Scaricare il backup più recente e decomprimerlo nella cartella di backup, se è compresso
  - Il metodo `OnDataLossAsync` restituisce `RestoreContext`. Chiamare l'API `RestoreAsync` nel `RestoreContext` restituito.
  - Restituire true se il ripristino è stato completato.

Di seguito è riportato un esempio di implementazione del metodo `OnDataLossAsync`.

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

Il metodo `RestoreDescription` passato alla chiamata `RestoreContext.RestoreAsync` contiene un membro chiamato `BackupFolderPath`.
Quando si ripristina un singolo backup completo, `BackupFolderPath` deve essere impostato sul percorso locale della cartella che contiene il backup completo.
Quando si ripristina un backup completo e alcuni backup incrementali, `BackupFolderPath` deve essere impostato sul percorso locale della cartella che contiene sia il backup completo che tutti i backup incrementali.
La chiamata `RestoreAsync` può generare `FabricMissingFullBackupException` se il metodo `BackupFolderPath` restituito non contiene un backup completo.
Può anche generare `ArgumentException` se `BackupFolderPath` contiene una catena interrotta di backup incrementali,
ad esempio, se contiene il backup completo e il primo e il terzo backup incrementale, ma non il secondo.

> [!NOTE]
> Il valore RestorePolicy è Safe per impostazione predefinita.  L'API `RestoreAsync` non riuscirà con ArgumentException se rileva che la cartella di backup include uno stato precedente o uguale allo stato contenuto nella replica.  Per ignorare questo controllo di sicurezza è possibile usare `RestorePolicy.Force`. Viene specificato come parte di `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Servizio eliminato o perso
Se un servizio viene rimosso, per ripristinare i dati sarà prima di tutto necessario ricrearlo.  È importante creare il servizio con la stessa configurazione, ad esempio con lo stesso schema di partizionamento, in modo che sia possibile ripristinare i dati senza problemi.  Quando il servizio è attivo, è necessario chiamare l'API per il ripristino dei dati (`OnDataLossAsync`, come indicato sopra) su ogni partizione del servizio. A tal fine, è possibile usare `[FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx)` su ogni partizione.  

A partire da questo punto, l'implementazione è uguale a quella dello scenario precedente. Ogni partizione deve ripristinare il backup rilevante più recente dall'archivio esterno. Occorre prestare attenzione al fatto che ora l'ID della partizione potrebbe essere diverso, perché il runtime crea dinamicamente gli ID delle partizioni. Il servizio deve quindi archiviare le informazioni sulla partizione e il nome del servizio appropriati per identificare il più recente backup corretto da ripristinare per ogni partizione.

> [!NOTE]
> Non è consigliabile usare `FabricClient.ServiceManager.InvokeDataLossAsync` su ogni partizione per ripristinare l'intero servizio perché può danneggiare lo stato del cluster.
> 

## <a name="replication-of-corrupt-application-data"></a>Replica di dati dell'applicazione danneggiati
Se l'aggiornamento dell'applicazione appena distribuito include un bug, potrebbe provocare il danneggiamento dei dati. È ad esempio possibile che l'aggiornamento di un'applicazione inizi ad aggiornare ogni record relativo a numeri di telefono in un oggetto ReliableDictionary specificando un prefisso non valido.  In questo caso, i numeri di telefono non validi verranno replicati, perché Service Fabric non conosce la natura dei dati archiviati.

Dopo avere rilevato un bug così grave da causare il danneggiamento dei dati, è prima di tutto necessario bloccare il servizio a livello di applicazione e, se possibile, eseguire l'aggiornamento alla versione del codice dell'applicazione che non include il bug.  Anche dopo la correzione del codice del servizio è tuttavia possibile che i dati siano ancora danneggiati e che sia necessario ripristinarli.  In questi casi potrebbe non essere sufficiente ripristinare il backup più recente, perché è possibile che anche i backup più recenti siano danneggiati.  È quindi necessario trovare il backup più recente eseguito prima del danneggiamento dei dati.

Se non si sa con esattezza quali siano i backup danneggiati, è possibile distribuire un nuovo Service Fabric Cluster e ripristinare i backup delle partizioni interessate con la stessa procedura dello scenario "Servizio eliminato o perso" precedente.  Per ogni partizione, avviare il ripristino dei backup dal più recente al meno recente. Dopo aver trovato un backup non danneggiato, spostare o eliminare tutti i backup della partizione più recenti di quel backup. Ripetere questo processo per ogni partizione. Quando `OnDataLossAsync` viene chiamata sulla partizione nel cluster di produzione, il backup più recente trovato nell'archivio esterno verrà selezionato dal processo precedente.

È ora possibile usare la procedura illustrata nella sezione "Servizio eliminato o perso" per ripristinare lo stato del servizio sul valore precedente al danneggiamento da parte del codice con bug.

Si noti che:

  - Quando si esegue il ripristino è possibile che il backup ripristinato sia precedente allo stato della partizione prima della perdita dei dati. È quindi necessario procedere al ripristino solo come ultima risorsa per recuperare la quantità maggiore possibile di dati.
  - La stringa che rappresenta il percorso della cartella di backup e i percorsi dei file nella cartella di backup può superare i 255 caratteri, in base al percorso FabricDataRoot e alla lunghezza del nome del tipo di applicazione. Questo approccio può far sì che alcuni metodi .NET, come `Directory.Move`, generino l'eccezione `PathTooLongException`. Una soluzione alternativa consiste nel chiamare direttamente le API kernel32, come `CopyFile`.

## <a name="backup-and-restore-reliable-actors"></a>Eseguire il backup e il ripristino di Reliable Actors


Reliable Actors Framework si basa su Reliable Services. L'ActorService che ospita l'attore o gli attori è un servizio Reliable con stato. Di conseguenza, tutte le funzionalità di backup e ripristino disponibili in Reliable Services sono disponibili anche per Reliable Actors (tranne i comportamenti specifici per il provider di stato). Poiché i backup verranno eseguiti per partizione, verrà eseguito il backup degli stati per tutti gli attori in quella partizione (il ripristino è simile e verrà eseguito anch'esso per partizione). Per eseguire il backup/ripristino, il proprietario del servizio deve creare un servizio Actor personalizzato che deriva da ActorService e quindi eseguire il backup/ripristino analogamente a quanto descritto nella sezione precedente per Reliable Services.

```csharp
class MyCustomActorService : ActorService
{
     public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
            : base(context, actorTypeInfo)
     {                  
     }
    
    //
   // Method overrides and other code.
    //
}
```

Quando si crea una classe di servizio Actor personalizzata, è necessario registrarla insieme all'attore.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
   (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Il provider di stato predefinito per Reliable Actors è `KvsActorStateProvider`. Per impostazione predefinita, il backup incrementale non è abilitato per `KvsActorStateProvider`. È possibile abilitare il backup incrementale creando `KvsActorStateProvider` con l'impostazione appropriata nel relativo costruttore e quindi passandolo al costruttore ActorService come illustrato nel frammento di codice seguente:

```csharp
class MyCustomActorService : ActorService
{
     public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
            : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
     {                  
     }
    
    //
   // Method overrides and other code.
    //
}
```

Dopo aver abilitato il backup incrementale, quest'ultimo può avere esito negativo con FabricMissingFullBackupException per uno dei motivi seguenti. Sarà pertanto necessario eseguire un backup completo prima di effettuarne di incrementali:

  - La replica non ha mai eseguito un backup completo dopo essere diventata primaria.
  - Alcuni dei record di log sono stati troncati in seguito all'ultimo backup.

Quando è abilitato il backup incrementale, `KvsActorStateProvider` non usa il buffer circolare per gestire i propri record di log e lo tronca periodicamente. Se in 45 minuti non viene eseguito alcun backup da parte dell'utente, il sistema tronca automaticamente i record di log. Questo intervallo può essere configurato specificando `logTrunctationIntervalInMinutes` nel costruttore `KvsActorStateProvider`, in maniera simile a quando si abilita il backup incrementale. I record di log potrebbero essere troncati anche se la replica primaria deve creare un'altra replica inviando tutti i propri dati.

Quando si esegue il ripristino da una catena di backup, in maniera simile a Reliable Services, BackupFolderPath deve contenere sottodirectory con una di queste contenente il backup completo e le altre contenenti i backup incrementali. Se la convalida della catena di backup non riesce, l'API di ripristino genererà FabricException con un messaggio di errore appropriato. 

> [!NOTE]
> Al momento l'opzione RestorePolicy.Safe viene ignorata da `KvsActorStateProvider`. Il supporto per questa funzionalità è pianificato per una versione futura.
> 

## <a name="testing-backup-and-restore"></a>Test del backup e del ripristino
È importante assicurarsi che i dati critici vengano sottoposti a backup e che possano essere ripristinati. Per farlo, chiamare il cmdlet `Start-ServiceFabricPartitionDataLoss` in PowerShell che può indurre la perdita di dati in una determinata partizione per verificare se la funzionalità di backup e ripristino dei dati per il servizio funziona nel modo previsto.  Si può anche richiamare la perdita e il ripristino dei dati a livello di codice da tale evento.

> [!NOTE]
> Nell'app di riferimento Web su GitHub è disponibile un esempio di implementazione della funzionalità di backup e ripristino. Per informazioni più dettagliate, vedere il servizio `Inventory.Service`.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Approfondimento: altri dettagli sul backup e ripristino
Ecco altri dettagli sul backup e ripristino.

### <a name="backup"></a>Backup
Reliable State Manager consente di creare backup coerenti senza bloccare alcuna operazione di lettura o scrittura. A questo scopo, utilizza un checkpoint e un meccanismo di persistenza dei log.  Reliable State Manager accetta checkpoint fuzzy (semplici) in determinati punti per ridurre la pressione sul log transazionale e migliorare i tempi di ripristino.  Quando si chiama `BackupAsync`, Reliable State Manager indica a tutti gli oggetti Reliable di copiare i file di checkpoint più recenti in una cartella di backup locale.  Reliable State Manager copia quindi tutti i record del log a partire dal "puntatore iniziale" fino all'ultimo record del log nella cartella di backup.  Nel backup sono inclusi tutti i record di log fino all'ultimo e Reliable State Manager conserva i log write-ahead, di conseguenza Reliable State Manager assicura che nel backup siano incluse tutte le transazioni di cui viene eseguito il commit, ovvero di cui viene restituito correttamente `CommitAsync`.

Eventuali transazioni di cui viene eseguito il commit dopo la chiamata di `BackupAsync` potrebbero essere incluse o meno nel backup.  Dopo il popolamento della cartella di backup locale da parte della piattaforma, ovvero dopo un backup locale completato dal runtime, verrà richiamato il callback del backup del servizio.  Il callback è responsabile dello spostamento della cartella di backup in una posizione esterna, ad esempio nell'Archiviazione di Azure.

### <a name="restore"></a>Restore
Reliable State Manager consente di eseguire il ripristino da un backup con l'API `RestoreAsync`.  
È possibile chiamare il metodo `RestoreAsync` su `RestoreContext` solo all'interno del metodo `OnDataLossAsync`.
Il valore booleano restituito da `OnDataLossAsync` indica se il servizio ha ripristinato il rispettivo stato da un'origine esterna.
Se `OnDataLossAsync` restituisce true, Service Fabric ricompila tutte le altre repliche sulla base di questa replica primaria. Service Fabric fa in modo che le repliche che ricevono la chiamata `OnDataLossAsync` eseguano prima la transizione al ruolo primario, senza che venga concesso lo stato di lettura o scrittura.
Per i responsabili dell'implementazione di StatefulService significa che `RunAsync` viene chiamato solo dopo il completamento corretto di `OnDataLossAsync`.
`OnDataLossAsync` viene quindi chiamato nella nuova replica primaria.
Fino a quando un servizio non completa correttamente l'API, restituendo true o false, e non viene completata la relativa riconfigurazione, l'API continuerà a essere chiamata singolarmente.

`RestoreAsync` rilascia prima di tutto ogni stato esistente nella replica primaria in cui è stato chiamato.  
Reliable State Manager crea quindi tutti gli oggetti Reliable esistenti nella cartella di backup.  
Viene quindi indicato agli oggetti Reliable di eseguire il ripristino dai rispettivi checkpoint nella cartella di backup.  
Reliable State Manager ripristina infine il proprio stato dai record dei log nella cartella di backup ed esegue il ripristino.  
Come parte del processo di ripristino, le operazioni a partire dal "punto iniziale" con record di log di commit nella cartella di backup vengono riprodotte negli oggetti Reliable.  
Questo passaggio assicura che lo stato ripristinato sia coerente.

## <a name="next-steps"></a>Passaggi successivi
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
  - [Notifiche di Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Configurazione di Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

