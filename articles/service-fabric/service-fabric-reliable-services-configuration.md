<properties
   pageTitle="Panoramica della configurazione di Reliable Services di Azure Service Fabric | Microsoft Azure"
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
   ms.date="01/26/2016"
   ms.author="sumukhs"/>

# Configurazione di Reliable Services con stato
È possibile modificare le configurazioni predefinite di Reliable Services con stato usando il pacchetto di configurazione (Config) o l'implementazione del servizio (codice).

+ **Config**: la configurazione tramite il pacchetto config viene realizzata modificando il file "Settings.xml" generato nella radice del pacchetto Microsoft Visual Studio presente nella cartella "Config" per ogni servizio dell'applicazione.
+ **Codice**: la configurazione tramite il codice viene realizzata ignorando StatefulService.CreateReliableStateManager e creando un ReliableStateManager usando un oggetto ReliableStateManagerConfiguration con il set di opzioni appropriato.

Per impostazione predefinita, il runtime di Azure Service Fabric cerca i nomi di sezione predefiniti nel file settings.xml e usa i valori di configurazione durante la creazione dei componenti di runtime sottostanti.

>[AZURE.NOTE] **Non** eliminare i nomi di sezione delle configurazioni seguenti nel file "Settings.xml" generato nella soluzione Visual Studio, a meno che non si preveda di configurare il servizio tramite codice. La ridenominazione del pacchetto di configurazione o dei nomi di sezione richiede una modifica del codice quando si configura ReliableStateManager.


## Configurazione della sicurezza del replicatore
Le configurazioni della sicurezza del replicatore consentono di proteggere il canale di comunicazione usato durante la replica. Questo significa che i servizi non potranno visualizzare l'uno il traffico di replica dell'altro, garantendo la sicurezza dei dati a disponibilità elevata. Per impostazione predefinita, una sezione di configurazione della sicurezza vuota non abilita la sicurezza della replica.

### Nome predefinito della sezione
ReplicatorSecurityConfig

>[AZURE.NOTE] Per modificare questo nome di sezione, sostituire il parametro replicatorSecuritySectionName del costruttore ReliableStateManagerConfiguration durante la creazione di ReliableStateManager per questo servizio.


## Configurazione del replicatore
Le configurazioni del replicatore consentono di configurare il replicatore responsabile di garantire l'elevata affidabilità dello stato di Reliable Services con stato replicando e rendendo permanente lo stato in locale. La configurazione predefinita viene generata dal modello di Visual Studio e dovrebbe essere sufficiente. Questa sezione descrive le configurazioni aggiuntive che sono disponibili per ottimizzare il replicatore.

### Nome predefinito della sezione
ReplicatorConfig

>[AZURE.NOTE] Per modificare questo nome di sezione, sostituire il parametro replicatorSettingsSectionName del costruttore ReliableStateManagerConfiguration durante la creazione di ReliableStateManager per questo servizio.


### Nomi delle configurazioni
|Nome|Unità|Valore predefinito|Osservazioni|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondi|0,05|Periodo di tempo per cui il replicatore, dopo aver ricevuto un'operazione, attende presso il replicatore secondario prima di inviare un acknowledgement al replicatore principale. Gli altri acknowledgement relativi alle operazioni elaborate all'interno di questo intervallo vengono inviati come risposta unica.|
|ReplicatorEndpoint|N/D|Nessun valore predefinito: parametro obbligatorio|Indirizzo IP e porta che il replicatore principale/secondario userà per comunicare con altri replicatori nel set di repliche. Deve fare riferimento a un endpoint di risorsa TCP nel manifesto del servizio. Per altre informazioni sulla definizione delle risorse dell'endpoint in un manifesto del servizio, vedere [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md). |
|MaxPrimaryReplicationQueueSize|Numero di operazioni|8192|Numero massimo di operazioni nella coda principale. Un'operazione viene liberata quando il replicatore principale riceve un acknowledgement da tutti i replicatori secondari. Questo valore deve essere maggiore di 64 ed essere una potenza di 2.|
|MaxSecondaryReplicationQueueSize|Numero di operazioni|16384|Numero massimo di operazioni nella coda secondaria. Un'operazione viene liberata quando il relativo stato viene reso altamente disponibile tramite persistenza. Questo valore deve essere maggiore di 64 ed essere una potenza di 2.|
|CheckpointThresholdInMB|MB|200|Quantità di spazio del file di log dopo il quale viene eseguito un checkpoint dello stato.|
|MaxRecordSizeInKB|KB|1024|La dimensione massima dei record che il replicatore può scrivere nel log. Questo valore deve essere un multiplo di 4 ed essere maggiore di 16.|
|OptimizeLogForLowerDiskUsage|Boolean|true|Se il valore è true, il log viene configurato in modo che il file di log dedicato della replica venga creato mediante un file NTFS sparse. Questo riduce l'utilizzo effettivo di spazio su disco per il file. Se il valore è false, il file viene creato con allocazioni predefinite che assicurano le migliori prestazioni in scrittura.|
|MaxRecordSizeInKB|KB|1024|La dimensione massima dei record che il replicatore può scrivere nel log. Questo valore deve essere un multiplo di 4 ed essere maggiore di 16.|
|SharedLogId|guid|""|Specifica un GUID da usare per l'identificazione del file di log condiviso usato con la replica in oggetto. In genere, i servizi non devono usare questa impostazione. Tuttavia, se è stato specificato SharedLogId, lo deve essere anche SharedLogPath.|
|SharedLogPath|Nome di percorso completo|""|Specifica il percorso completo in cui verrà creato il file di log condiviso per la replica in oggetto. In genere, i servizi non devono usare questa impostazione. Tuttavia, se è stato specificato SharedLogPath, lo deve essere anche SharedLogId.|


## Configurazione di esempio tramite codice
```csharp
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            new ReliableStateManagerReplicatorSettings
            {
                RetryInterval = TimeSpan.FromSeconds(3)
            }));
}
```


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

Il valore per CheckpointThresholdInMB controlla la quantità di spazio su disco che il replicatore può usare per archiviare le informazioni sullo stato nel file di log dedicato della replica. Aumentando questo valore rispetto all'impostazione predefinita, è possibile favorire tempi di riconfigurazione più rapidi quando una nuova replica viene aggiunta al set. Questo a causa del trasferimento di stato parziale che viene eseguito in seguito alla disponibilità di una maggiore cronologia di operazioni nel log e che può aumentare il tempo di ripristino di una replica dopo un arresto anomalo.

Se si imposta OptimizeForLowerDiskUsage su true, lo spazio del file di log viene sottoposto a over-provisioning per consentire alle repliche attive di archiviare nei propri file di log una maggiore quantità di informazioni sullo stato, lasciando alle repliche inattive una minore quantità di spazio su disco. In questo modo, è possibile ospitare più repliche in un nodo. Se si imposta OptimizeForLowerDiskUsage su false, le informazioni sullo stato vengono scritte nei file di log più rapidamente.

L'impostazione MaxRecordSizeInKB definisce la dimensione massima dei record che il replicatore può scrivere nel file di log. Nella maggior parte dei casi, la dimensione predefinita del record, 1024 KB, è ottimale. Tuttavia, se il servizio richiede che nelle informazioni sullo stato vengano inclusi elementi dati di dimensioni maggiori, è possibile che sia necessario aumentare questo valore. La riduzione delle dimensioni di MaxRecordSizeInKB a meno di 1024 KB comporta vantaggi relativi, perché i record di dimensioni minori usano soltanto lo spazio necessario. È probabile che questo valore debba essere modificato solo in rari casi.

Le impostazioni SharedLogId e SharedLogPath vengono sempre usate insieme e consentono a un servizio di usare un log condiviso separato dal log condiviso predefinito per il nodo. Per ottenere migliori prestazioni, il maggior numero di servizi possibile dovrebbe specificare lo stesso log condiviso. I file di log condivisi devono essere memorizzati su dischi riservati esclusivamente a questo tipo di file, in modo da ridurre le situazioni di contesa della testina. È probabile che questo valore debba essere modificato solo in rari casi.

<!---HONumber=AcomDC_0204_2016-->