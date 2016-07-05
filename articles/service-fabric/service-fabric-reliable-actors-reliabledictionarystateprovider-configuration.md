<properties
   pageTitle="Panoramica della configurazione di ReliableDictionaryActorStateProvider per Reliable Actors di Service Fabric di Azure | Microsoft Azure"
   description="Informazioni sulla configurazione di attori con stato di tipo ReliableDictionaryActorStateProvider in Service Fabric di Azure."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="sumukhs"/>

# Configurare ReliableDictionaryActorStateProvider di Reliable Actors
È possibile modificare la configurazione predefinita di ReliableDictionaryActorStateProvider cambiando il file settings.xml generato nella radice del pacchetto di Visual Studio all'interno della cartella Config per l'attore specificato.

Il runtime di Service Fabric di Azure cerca i nomi di sezione predefiniti nel file settings.xml e utilizza i valori di configurazione durante la creazione dei componenti di runtime sottostanti.

>[AZURE.NOTE] **Non** eliminare o modificare i nomi di sezione delle configurazioni seguenti nel file settings.xml generato nella soluzione Visual Studio.

Sono disponibili anche impostazioni globali che influiscono sulla configurazione di ReliableDictionaryActorStateProvider.

## Configurazione globale

La configurazione globale viene specificata nella sezione KtlLogger del manifesto del cluster. Consente di configurare il percorso di log condiviso e la dimensione oltre i limiti di memoria globali utilizzati dal logger. Si noti che le modifiche apportate al manifesto del cluster influiscono su tutti i servizi che usano ReliableDictionaryActorStateProvider e i servizi affidabili con stato.

Il manifesto del cluster è un unico file XML che contiene le impostazioni e le configurazioni applicabili a tutti i nodi e ai servizi del cluster. Il file tipicamente si chiama ClusterManifest.xml. È possibile visualizzare il manifesto del cluster utilizzando il comando Powershell Get-ServiceFabricClusterManifest.

### Nomi delle configurazioni

|Nome|Unità|Valore predefinito|Osservazioni|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Kilobyte|8388608|Numero minimo di KB da allocare in modalità kernel per il pool di memoria del buffer di scrittura del logger. Questo pool di memoria viene utilizzato per il caching delle informazioni sullo stato prima della scrittura su disco.|
|WriteBufferMemoryPoolMaximumInKB|Kilobyte|Nessun limite|La dimensione massima raggiungibile dal pool di memoria del buffer di scrittura del logger.|
|SharedLogId|GUID|""|Specifica un GUID utile per individuare il file di log condiviso predefinito utilizzato da tutti i Reliable Services su tutti i nodi del cluster che non specificano lo SharedLogId nella configurazione specifica del servizio. Se viene specificato lo SharedLogId, deve anche essere specificato lo SharedLogPath.|
|SharedLogPath|Nome di percorso completo|""|Specifica il percorso completo in cui il file di log condiviso viene utilizzato da tutti i Reliable Services su tutti i nodi del cluster che non specificano lo SharedLogPath nella configurazione specifica del servizio. Tuttavia, se è stato specificato SharedLogPath, lo deve essere anche SharedLogId.|
|SharedLogSizeInMB|Megabyte|8192|Specifica il numero di MB di spazio su disco da allocare in modo statico per il log condiviso. Il valore deve essere maggiore di 2048.|

### Sezione manifesto del cluster di esempio
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### Osservazioni
Il logger dispone di un pool di memoria globale allocato dalla memoria non di paging del kernel disponibile per tutti i Reliable Services su un nodo per il caching dei dati sullo stato prima che siano scritti sul log dedicato associato alla replica del Reliable Services. La dimensione del pool è controllata dalle impostazioni WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB specifica la dimensione iniziale del pool di memoria e la dimensione minima alla quale è possibile ridurre la memoria. WriteBufferMemoryPoolMaximumInKB è la dimensione massima che può raggiungere il pool di memoria. Ogni replica Reliable Services aperta può aumentare la dimensione del pool di memoria di una quantità determinata dal sistema, fino a WriteBufferMemoryPoolMaximumInKB. Qualora la domanda per la memoria sia superiore alla disponibilità del pool di memoria, le richieste per la memoria sono ritardate finché la memoria non è disponibile. Pertanto se il pool di memoria del buffer di scrittura è troppo piccolo per una configurazione specifica, le prestazioni potrebbero risentirne.

Le impostazioni SharedLogId e SharedLogPath vengono sempre utilizzate per definire GUID e percorso del log condiviso predefinito per tutti i nodi del cluster. Il log condiviso predefinito viene utilizzato per tutti i Reliable Services che non specificano le impostazioni nel file settings.xml per il servizio specifico. Per ottenere le migliori prestazioni, i file di log condivisi devono essere memorizzati su dischi riservati esclusivamente al file di log condiviso, in modo da ridurre le situazioni di contesa della testina.

SharedLogSizeInMB specifica la quantità di spazio su disco da preallocare per il log condiviso predefinito su tutti i nodi. Le impostazioni SharedLogId e SharedLogPath non devono essere specificate per specificare SharedLogSizeInMB.

## Configurazione della sicurezza del replicatore
Le configurazioni della sicurezza del replicatore consentono di proteggere il canale di comunicazione usato durante la replica. Questo significa che i servizi non possono visualizzare l'uno il traffico di replica dell'altro, garantendo la sicurezza dei dati a disponibilità elevata. Per impostazione predefinita, una sezione di configurazione della sicurezza vuota non abilita la sicurezza della replica.

### Nome della sezione
&lt;NomeAttore&gt;ServiceReplicatorSecurityConfig

## Configurazione del replicatore
Le configurazioni del replicatore vengono usate per configurare il replicatore responsabile di garantire l'elevata affidabilità dello stato del provider di stato degli attori replicando e rendendo permanente tale stato in locale. La configurazione predefinita viene generata dal modello di Visual Studio e dovrebbe essere sufficiente. Questa sezione descrive le configurazioni aggiuntive che sono disponibili per ottimizzare il replicatore.

### Nome della sezione
&lt;NomeAttore&gt;ServiceReplicatorConfig

### Nomi delle configurazioni

|Nome|Unità|Valore predefinito|Osservazioni|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondi|0,05|Periodo di tempo per cui il replicatore, dopo aver ricevuto un'operazione, attende presso il replicatore secondario prima di inviare un acknowledgement al replicatore principale. Gli altri acknowledgement relativi alle operazioni elaborate all'interno di questo intervallo vengono inviati come risposta unica.||
|ReplicatorEndpoint|N/D|Nessun valore predefinito: parametro obbligatorio|Indirizzo IP e porta che il replicatore principale/secondario userà per comunicare con altri replicatori nel set di repliche. Questo dovrebbe fare riferimento a un endpoint di risorsa TCP nel manifesto del servizio. Per altre informazioni sulla definizione delle risorse dell'endpoint nel manifesto del servizio, vedere [Risorse del manifesto del servizio](service-fabric-service-manifest-resources.md). |
|MaxReplicationMessageSize|Byte|50 MB|Dimensione massima dei dati di replica che è possibile trasmettere in un singolo messaggio.|
|MaxPrimaryReplicationQueueSize|Numero di operazioni|8192|Numero massimo di operazioni nella coda principale. Un'operazione viene liberata quando il replicatore principale riceve un acknowledgement da tutti i replicatori secondari. Questo valore deve essere maggiore di 64 ed essere una potenza di 2.|
|MaxSecondaryReplicationQueueSize|Numero di operazioni|16384|Numero massimo di operazioni nella coda secondaria. Un'operazione viene liberata quando il relativo stato viene reso altamente disponibile tramite persistenza. Questo valore deve essere maggiore di 64 ed essere una potenza di 2.|
|CheckpointThresholdInMB|MB|200|Quantità di spazio del file di log dopo il quale viene eseguito un checkpoint dello stato.|
|MaxRecordSizeInKB|KB|1024|La dimensione massima dei record che il replicatore può scrivere nel log. Questo valore deve essere un multiplo di 4 ed essere maggiore di 16.|
|OptimizeLogForLowerDiskUsage|Boolean|true|Se il valore è true, il log viene configurato in modo che il file di log dedicato della replica venga creato mediante un file NTFS sparse. Questo riduce l'utilizzo effettivo di spazio su disco per il file. Quando il valore è false, il file viene creato con allocazioni predefinite che assicurano le migliori prestazioni in scrittura.|
|SharedLogId|guid|""|Specifica un GUID da usare per l'identificazione del file di log condiviso usato con la replica in oggetto. In genere, i servizi non devono usare questa impostazione. Tuttavia, se è stato specificato SharedLogId, lo deve essere anche SharedLogPath.|
|SharedLogPath|Nome di percorso completo|""|Specifica il percorso completo in cui verrà creato il file di log condiviso per la replica in oggetto. In genere, i servizi non devono usare questa impostazione. Tuttavia, se SharedLogPath è stato specificato, lo deve essere anche SharedLogId.|


## Esempio file di configurazione

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## Osservazioni
Il parametro BatchAcknowledgementInterval controlla la latenza di replica. Il valore '0' determina la latenza più bassa possibile a scapito della velocità effettiva, in quanto è necessario inviare ed elaborare una maggiore quantità di messaggi di acknowledgement, ciascuno dei quali contenente un numero minore di acknowledgement. Più alto è il valore di BatchAcknowledgementInterval, maggiore sarà la velocità effettiva di replica complessiva, ma con una latenza delle operazioni più elevata. Questo ha un impatto diretto sulla latenza dei commit delle transazioni.

Il parametro CheckpointThresholdInMB controlla la quantità di spazio su disco che il replicatore può usare per archiviare le informazioni sullo stato nel file di log dedicato della replica. Aumentandone il valore rispetto al valore predefinito potrebbe causare tempi di riconfigurazione più rapidi quando una nuova replica viene aggiunta al set. Questo a causa del trasferimento di stato parziale che viene eseguito in seguito alla disponibilità di una maggiore cronologia di operazioni nel log e che può aumentare il tempo di ripristino di una replica dopo un arresto anomalo.

Se si imposta OptimizeForLowerDiskUsage su true, lo spazio del file di log viene sottoposto a over-provisioning per consentire alle repliche attive di archiviare nei propri file di log una maggiore quantità di informazioni sullo stato, lasciando alle repliche inattive una minore quantità di spazio su disco. In questo modo, è possibile ospitare più repliche in un nodo. Se si imposta OptimizeForLowerDiskUsage su false, le informazioni sullo stato vengono scritte nei file di log più rapidamente.

L'impostazione MaxRecordSizeInKB definisce la dimensione massima dei record che il replicatore può scrivere nel file di log. Nella maggior parte dei casi, la dimensione predefinita del record, 1024 KB, è ottimale. Tuttavia, se il servizio richiede che nelle informazioni sullo stato vengano inclusi elementi dati di dimensioni maggiori, è possibile che sia necessario aumentare questo valore. La riduzione delle dimensioni di MaxRecordSizeInKB a meno di 1024 KB è relativamente vantaggiosa perché i record di dimensioni minori usano soltanto lo spazio necessario. È probabile che questo valore debba essere modificato solo in rari casi.

Le impostazioni SharedLogId e SharedLogPath vengono sempre usate insieme e consentono a un servizio di usare un log condiviso separato dal log condiviso predefinito per il nodo. Per ottenere migliori prestazioni, il maggior numero di servizi possibile dovrebbe specificare lo stesso log condiviso. I file di log condivisi devono essere memorizzati su dischi riservati esclusivamente a questo tipo di file per ridurre le situazioni di contesa della testina. È probabile che questi valori debbano essere modificati solo in rari casi.

<!---HONumber=AcomDC_0330_2016-->
