<properties
   pageTitle="Panoramica della configurazione di Reliable Services dell’infrastruttura di servizi | Microsoft Azure"
   description="Informazioni sulla configurazione di Reliable Services con stato in Azure in infrastruttura di servizi."
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
   ms.date="10/28/2015"
   ms.author="sumukhs"/>

# Configurazione di Reliable Services con stato
È possibile modificare la configurazione predefinita di Reliable Services con stato cambiando il file "settings.xml" generato nella directory radice del pacchetto di Visual Studio all'interno della cartella "Config" per ogni servizio dell'applicazione.

Il runtime di Service Fabric cerca i nomi di sezione predefiniti nel file "settings.xml" e utilizza i valori di configurazione durante la creazione dei componenti di runtime sottostanti.

> [AZURE.NOTE]**NON** eliminare/modificare i nomi di sezione delle configurazioni seguenti nel file "settings.xml" generato nella soluzione Visual Studio.

## Configurazione della sicurezza del replicatore
Le configurazioni della sicurezza del replicatore consentono di proteggere il canale di comunicazione usato durante la replica. Questo significa che i servizi non saranno in grado di vedere l'uno il traffico di replica dell'altro, garantendo la sicurezza dei dati a disponibilità elevata. Per impostazione predefinita, una sezione di configurazione della sicurezza vuota non abilita la sicurezza della replica.

### Nome della sezione
ReplicatorSecurityConfig

## Configurazione del replicatore
Le configurazioni del replicatore vengono usate per configurare il replicatore responsabile di garantire l'elevata affidabilità dello stato di Reliable Services con stato replicando e rendendo permanente tale stato in locale. La configurazione predefinita viene generata dal modello di Visual Studio e dovrebbe essere sufficiente. Questa sezione descrive le configurazioni aggiuntive che sono disponibili per ottimizzare il replicatore.

### Nome della sezione
ReplicatorConfig

### Nomi delle configurazioni

|Nome|Unità|Valore predefinito|Osservazioni|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondi|0,05|Periodo di tempo per cui il replicatore, dopo aver ricevuto un'operazione, attende presso il replicatore secondario prima di inviare un acknowledgement al replicatore principale. Gli altri acknowledgement relativi alle operazioni elaborate all'interno di questo intervallo vengono inviati come risposta unica.|
|ReplicatorEndpoint|N/D|N/D - RequiredParameter|Indirizzo IP e porta che il replicatore principale/secondario userà per comunicare con altri replicatori nel set di repliche. Questo dovrebbe fare riferimento a un endpoint di risorsa TCP nel manifesto del servizio. Per altre informazioni sulla definizione delle risorse dell'endpoint nel manifesto del servizio, vedere l'articolo relativo alle [risorse del manifesto del servizio](service-fabric-service-manifest-resources.md). |
|MaxPrimaryReplicationQueueSize|Numero di operazioni|8192|Numero massimo di operazioni nella coda principale. Un'operazione viene liberata dopo che il replicatore principale riceve un acknowledgement da tutti i replicatori secondari. Questo valore deve essere maggiore di 64 ed essere una potenza di 2.|
|MaxSecondaryReplicationQueueSize|Numero di operazioni|16384|Numero massimo di operazioni nella coda secondaria. Un'operazione viene liberata dopo che il relativo stato è stato reso altamente disponibile mediante la persistenza. Questo valore deve essere maggiore di 64 ed essere una potenza di 2.|
|CheckpointThresholdInMB|MB|200|Quantità di spazio del file di log dopo il quale viene eseguito un checkpoint dello stato.|
|MaxRecordSizeInKB|KB|1024|La dimensione massima dei record che il replicatore può scrivere nel log. Questo valore deve essere un multiplo di 4 ed essere maggiore di 16.|
|OptimizeLogForLowerDiskUsage|boolean|true|Quando il valore è true, il log viene configurato in modo che il file di log dedicato della replica venga creato mediante un file NTFS sparse. Questo riduce l'utilizzo effettivo di spazio su disco per il file. Quando il valore è false, il file viene creato con allocazioni predefinite che assicurano le migliori prestazioni in scrittura.|
|MaxRecordSizeInKB|KB|1024|La dimensione massima dei record che il replicatore può scrivere nel log. Questo valore deve essere un multiplo di 4 ed essere maggiore di 16.|
|SharedLogId|guid|""|Specifica un GUID da usare per l'identificazione del file di log condiviso usato con la replica in oggetto. In genere i servizi non dovrebbero usare questa impostazione. Tuttavia, se è stato specificato SharedLogId, è necessario specificare anche SharedLogPath.|
|SharedLogPath|Nome di percorso completo|""|Specifica il percorso completo in cui verrà creato il file di log condiviso per la replica in oggetto. In genere i servizi non dovrebbero usare questa impostazione. Tuttavia, se è stato specificato SharedLogPath, è necessario specificare anche SharedLogId.|

## File di configurazione di esempio

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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
BatchAcknowledgementInterval controlla la latenza di replica. Il valore '0' determina la latenza più bassa possibile a scapito della velocità effettiva, in quanto è necessario inviare ed elaborare una maggiore quantità di messaggi di acknowledgement, ciascuno dei quali contenente un numero minore di acknowledgement. Più alto è il valore di BatchAcknowledgementInterval, maggiore sarà la velocità effettiva di replica complessiva, ma con una latenza delle operazioni più elevata. Questo ha un impatto diretto sulla latenza dei commit delle transazioni.

Il valore per CheckpointThresholdInMB controlla la quantità di spazio su disco che il replicatore può usare per archiviare le informazioni sullo stato nel file di log dedicato della replica. Questo aumento su un valore superiore a quello predefinito potrebbe comportare tempi di riconfigurazione più veloci quando viene aggiunta una nuova replica al set a causa del trasferimento di stato parziale che si verifica a causa di una maggiore cronologia disponibile delle operazioni nel log, mentre aumenta potenzialmente il tempo di recupero di una replica dopo un arresto anomalo.

L'impostazione OptimizeForLowerDiskUsage consente di sottoporre lo spazio del file di log a un "super provisioning". In questo modo, le repliche attive possono archiviare nei propri file di log un numero maggiore di informazioni sullo stato, lasciando alle repliche inattive una minore quantità di spazio su disco. Sebbene questo consenta di ospitare su un nodo un numero maggiore di repliche rispetto a quanto sarebbe possibile a causa della mancanza di spazio su disco, l'impostazione del valore di OptimizeForLowerDiskUsage su false determina una scrittura più rapida delle informazioni sullo stato nei file di log.

L'impostazione MaxRecordSizeInKB definisce la dimensione massima di un record che il replicatore può scrivere nel file di log. Nella maggior parte dei casi la dimensione predefinita del record, 1024 KB, è ottimale. Tuttavia, se il servizio richiede che nelle informazioni sullo stato vengano inclusi elementi dati di maggiori dimensioni, è possibile che sia necessario aumentare questo valore. Ridurre la dimensione di MaxRecordSizeInKB a meno di 1024 KB offre un piccolo vantaggio: in questo modo, infatti, i record di dimensioni minori usano soltanto lo spazio necessario. Si prevede che l'impostazione dovrà essere modificata solo in casi rari.

Le impostazioni SharedLogId e SharedLogPath vengono sempre usate insieme e consentono a un servizio di usare un log condiviso separato dal log condiviso predefinito per il nodo. Per ottenere migliori prestazioni, il maggior numero di servizi possibile dovrebbe specificare lo stesso log condiviso. I file di log condivisi devono essere memorizzati su dischi riservati esclusivamente a questo tipo di file, in modo da ridurre le situazioni di contesa della testina. Si prevede che l'impostazione dovrà essere modificata solo in casi rari.

<!---HONumber=Nov15_HO4-->