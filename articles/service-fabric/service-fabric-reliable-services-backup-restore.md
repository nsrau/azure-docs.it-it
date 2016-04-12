<properties
   pageTitle="Backup e ripristino di Reliable Services | Microsoft Azure"
   description="Documentazione concettuale per il backup e ripristino di Reliable Services di Service Fabric"
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
   ms.date="03/03/2016"
   ms.author="mcoskun"/>

# Backup e ripristino di Reliable Services

Azure Service Fabric è una piattaforma a disponibilità elevata che replica lo stato in più nodi per mantenere questa disponibilità elevata. Anche in caso di errore di un nodo nel cluster, il servizio rimarrà quindi comunque disponibile. Anche se questa ridondanza predefinita fornita dalla piattaforma può essere sufficiente per alcune situazioni, in determinati casi è preferibile che il servizio esegua il backup dei dati in un archivio esterno.

Ad esempio, è possibile che in un servizio sia consigliabile eseguire il backup dei dati negli scenari seguenti:

* In caso di perdita permanente di un intero Service Fabric Cluster o di tutti i nodi in esecuzione in una partizione specifica.

* Errori amministrativi che provocano l'eliminazione o il danneggiamento accidentale dello stato. Ad esempio, questo problema può verificarsi se un amministratore con privilegi sufficienti elimina accidentalmente il servizio.

* Bug nel servizio che provocano il danneggiamento dei dati. Ad esempio, questo problema può verificarsi quando un aggiornamento del codice di servizio inizia a scrivere dati non corretti in una raccolta Reliable Collections. In tal caso, potrebbe essere necessario ripristinare uno stato precedente sia per il codice che per i dati.

* Elaborazione dati offline. Potrebbe essere utile eseguire offline l'elaborazione dei dati per la business intelligence separatamente dal servizio che genera i dati.

La funzionalità Backup/Ripristino consente ai servizi basati sull'API Reliable Services di creare e ripristinare i backup. Le API di backup fornite dalla piattaforma consentono l'esecuzione dei backup dello stato di una partizione senza bloccare le operazioni di lettura o scrittura. Le API di ripristino consentono il ripristino dello stato di una partizione da un backup specificato.


## Operazione di backup

L'autore del servizio ha il controllo completo sul momento di esecuzione dei backup e sulla posizione in cui vengono archiviati.

Per avviare un backup, il servizio deve richiamare **IReliableStateManager.BackupAsync**. I backup possono essere eseguiti solo da repliche primarie e richiedono la concessione dello stato di scrittura.

Come illustrato di seguito, il più semplice overload di **BackupAsync** accetta Func<< BackupInfo  bool >> denominata **backupCallback**.

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo** fornisce le informazioni relative al backup, incluso il percorso della cartella in cui il runtime ha salvato il backup (**BackupInfo.Directory**). La funzione di callback prevede lo spostamento di **BackupInfo.Directory** in un archivio esterno o una posizione diversa. Questa funzione restituisce anche un valore booleano che indica se è riuscita a spostare la cartella di backup nel percorso di destinazione.

Il codice seguente illustra in che modo è possibile usare backupCallback per caricare il backup nell'Archiviazione di Azure:

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

Nell'esempio precedente **ExternalBackupStore** corrisponde alla classe di esempio usata per interfacciarsi con l'archivio BLOB di Azure e **UploadBackupFolderAsync** è il metodo che comprime la cartella e la inserisce nell'archivio BLOB di Azure.

Si noti che:

- In un dato momento può esistere solo una chiamata **BackupAsync** per ogni esecuzione di replica. Più chiamate **BackupAsync** contemporanee provocheranno l'eccezione **FabricBackupInProgressException** che richiede di limitare a una le esecuzioni dei backup.

- In caso di failover di una replica durante l'esecuzione di un backup, è possibile che il backup non venga completato. Al termine del failover, il servizio dovrà quindi riavviare il backup richiamando **BackupAsync**, se necessario.

## Operazione di ripristino

In generale, i casi in cui potrebbe essere necessario eseguire un'operazione di ripristino rientrano in una di queste categorie:


- La partizione del servizio ha perso dati. Ad esempio, il disco per due su tre repliche per una partizione, inclusa la replica primaria, viene danneggiato o cancellato. La nuova replica primaria potrebbe dover ripristinare i dati da un backup.

- L'intero servizio è andato perso. Ad esempio, un amministratore rimuove l'intero servizio ed è quindi necessario ripristinare il servizio e i dati.

- Il servizio ha replicato dati danneggiati dell'applicazione, ad esempio a causa di un bug dell'applicazione. Sarà quindi necessario aggiornare o ripristinare il servizio per rimuovere la causa del danneggiamento e ripristinare dati non danneggiati.

Anche se sono possibili molti approcci, gli esempi seguenti sono basati sull'uso di **RestoreAsync** per il ripristino dagli scenari precedenti.

## Perdita di dati della partizione

In questo caso, il runtime rileva automaticamente la perdita dei dati e richiama l'API **OnDataLossAsync**.

L'autore del servizio deve eseguire le operazioni seguenti per il ripristino:

- Eseguire l'override di **CreateReliableStateManager** per restituire un nuovo valore **ReliableStateManager** e fornire una funzione di callback da chiamare nel caso di un evento di perdita di dati.

- Trovare il backup più recente nella posizione esterna che include i backup del servizio.

- Se lo stato del backup più recente è precedente alla replica primaria, restituisce false. In questo modo la nuova replica primaria non viene sovrascritta con dati meno recenti.

- Scaricare il backup più recente e decomprimerlo nella cartella di backup, se è compresso

- Chiamare **IReliableStateManager.RestoreAsync** specificando il percorso della cartella di backup.

- Restituire true se il ripristino è stato completato.

L'esempio seguente illustra un'implementazione del metodo **OnDataLossAsync** insieme all'override di **IReliableStateManager**.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(new ReliableStateManagerConfiguration(
            onDataLossEvent: this.OnDataLossAsync));
}

protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    await this.StateManager.RestoreAsync(backupFolder);

    return true;
}
```

>[AZURE.NOTE] Il valore RestorePolicy è Safe per impostazione predefinita. L'API **RestoreAsync** non riuscirà con ArgumentException se rileva che una cartella di backup include uno stato precedente o uguale allo stato contenuto nella replica. È possibile usare **RestorePolicy.Force** per ignorare questo controllo di sicurezza.

## Servizio eliminato o perso

Se un servizio viene rimosso, per ripristinare i dati sarà prima di tutto necessario ricrearlo. È importante creare il servizio con la stessa configurazione, ad esempio con lo stesso schema di partizionamento, in modo che sia possibile ripristinare i dati senza problemi. Quando il servizio è attivo, è necessario richiamare l'API per il ripristino dei dati (**OnDataLossAsync** precedente) su ogni partizione del servizio. Un modo per ottenere questo risultato consiste nell'usare **FabricClient.ServiceManager.InvokeDataLossAsync** su ogni partizione.

A partire da questo punto, l'implementazione è uguale a quella dello scenario precedente. Ogni partizione deve ripristinare il backup rilevante più recente dall'archivio esterno. Occorre prestare attenzione al fatto che ora l'ID della partizione potrebbe essere diverso, perché il runtime crea dinamicamente gli ID delle partizioni. Il servizio deve quindi archiviare le informazioni sulla partizione e il nome del servizio appropriati per identificare il più recente backup corretto da ripristinare per ogni partizione.


## Replica di dati dell'applicazione danneggiati

Se l'aggiornamento dell'applicazione appena distribuito include un bug, potrebbe provocare il danneggiamento dei dati. È ad esempio possibile che l'aggiornamento di un'applicazione inizi ad aggiornare ogni record relativo a numeri di telefono in un oggetto ReliableDictionary specificando un prefisso non valido. In questo caso, i numeri di telefono non validi verranno replicati, perché Service Fabric non conosce la natura dei dati archiviati.

Dopo avere rilevato un bug così grave da causare il danneggiamento dei dati, è prima di tutto necessario bloccare il servizio a livello di applicazione e, se possibile, eseguire l'aggiornamento alla versione del codice dell'applicazione che non include il bug. Anche dopo la correzione del codice del servizio è tuttavia possibile che i dati siano ancora danneggiati e che sia necessario ripristinarli. In questi casi potrebbe non essere sufficiente ripristinare il backup più recente, perché è possibile che anche i backup più recenti siano danneggiati. È quindi necessario trovare il backup più recente eseguito prima del danneggiamento dei dati.

Se non si sa con esattezza quali siano i backup danneggiati, è possibile distribuire un nuovo Service Fabric Cluster e ripristinare i backup delle partizioni interessate con la stessa procedura dello scenario "Servizio eliminato o perso" precedente. Per ogni partizione, avviare il ripristino dei backup dal più recente al meno recente. Dopo aver trovato un backup non danneggiato, spostare o eliminare tutti i backup della partizione più recenti di quel backup. Ripetere questo processo per ogni partizione. Quando **OnDataLossAsync** viene chiamato sulla partizione nel cluster di produzione, il backup più recente trovato nell'archivio esterno verrà selezionato dal processo precedente.

È ora possibile usare la procedura illustrata nella sezione "Servizio eliminato o perso " per ripristinare lo stato del backup del servizio sul valore precedente al danneggiamento da parte del codice con bug.

Si noti che:

- Quando si esegue il ripristino è possibile che il backup ripristinato sia precedente allo stato della partizione prima della perdita dei dati. È quindi necessario procedere al ripristino solo come ultima risorsa per recuperare la quantità maggiore possibile di dati.

- La stringa che rappresenta il percorso della cartella di backup e i percorsi dei file nella cartella di backup può superare i 255 caratteri, in base al percorso FabricDataRoot e alla lunghezza del nome del tipo di applicazione. È quindi possibile che alcuni metodi .NET come **Directory.Move** generino l'eccezione **PathTooLongException**. Una soluzione alternativa consiste nel chiamare direttamente le API kernel32, come **CopyFile**.


## Approfondimento: altri dettagli sul backup e ripristino

### Backup
Reliable State Manager consente di creare backup coerenti senza bloccare alcuna operazione di lettura o scrittura. A questo scopo, utilizza un checkpoint e un meccanismo di persistenza dei log. Reliable State Manager accetta checkpoint fuzzy (semplici) in determinati punti per ridurre la pressione sul log transazionale e migliorare i tempi di ripristino. Quando si chiama **IReliableStateManager.BackupAsync**, Reliable State Manager indica a tutti gli oggetti Reliable di copiare i file di checkpoint più recenti in una cartella di backup locale. Reliable State Manager copia quindi tutti i record del log a partire dal "puntatore iniziale" fino all'ultimo record del log nella cartella di backup. Poiché tutti i record del log, fino all'ultimo, sono inclusi nel backup e Reliable State Manager conserva i log write-ahead, è garantito che tutte le transazioni di cui viene eseguito il commit (**CommitAsync** restituito correttamente) siano incluse nel backup.

Eventuali transazioni di cui viene eseguito il commit dopo la chiamata di **BackupAsync** potrebbero essere o meno incluse nel backup. Dopo il popolamento della cartella di backup locale da parte della piattaforma, ovvero dopo un backup locale completato dal runtime, verrà richiamato il callback del backup del servizio. Il callback è responsabile dello spostamento della cartella di backup in una posizione esterna, ad esempio nell'Archiviazione di Azure.

### Ripristino

Reliable State Manager consente di eseguire il ripristino da un backup con l'API **IReliableStateManager.RestoreAsync**. Il metodo **RestoreAsync** può essere chiamato solo all'interno del metodo **OnDataLossAsync**. Il valore booleano restituito da **OnDataLossAsync** indica se il servizio ha ripristinato il rispettivo stato da un'origine esterna. Se **OnDataLossAsync** restituisce true, Service Fabric ricompila tutte le altre repliche sulla base di questa replica primaria. Service Fabric fa in modo che le repliche che devono ricevere **OnDataLossAsync** eseguano prima la transizione al ruolo primario, senza che venga concesso lo stato di lettura o scrittura. Per i responsabili dell'implementazione di StatefulService **RunAsync** verrà chiamato solo dopo il completamento corretto di **OnDataLossAsync**. **OnDataLossAsync** viene quindi richiamato sulla nuova replica primaria. Fino a quando un servizio non completa correttamente l'API, restituendo true o false, e non viene completata la relativa riconfigurazione, l'API continuerà a essere chiamata singolarmente.


**RestoreAsync** rilascia prima di tutto ogni stato esistente nella replica primaria su cui è stato chiamato. Reliable State Manager crea quindi tutti gli oggetti Reliable esistenti nella cartella di backup. Viene quindi indicato agli oggetti Reliable di eseguire il ripristino dai rispettivi checkpoint nella cartella di backup. Reliable State Manager ripristina infine il proprio stato dai record dei log nella cartella di backup ed esegue il ripristino. Come parte del processo di ripristino, le operazioni a partire dal "punto iniziale" con record di log di commit nella cartella di backup vengono riprodotte negli oggetti Reliable. Questo passaggio assicura che lo stato ripristinato sia coerente.

<!---HONumber=AcomDC_0309_2016-->