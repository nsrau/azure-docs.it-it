---
title: Configurare microservizi di Azure affidabili | Documentazione Microsoft
description: Informazioni sulla configurazione di Reliable Services con stato in Azure in infrastruttura di servizi.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: fa77eaf44f4d90d1a78f5de69e36432a53d80ecd
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="configure-stateful-reliable-services"></a>Configurazione di servizi Reliable Services con stato
Esistono due set di impostazioni di configurazione per i servizi Reliable Services. Un set è globale per tutti i servizi Reliable Services del cluster, mentre l'altro è specifico per un particolare servizio Reliable Services.

## <a name="global-configuration"></a>Configurazione globale
La configurazione globale dei servizi Reliable Services viene specificata nel manifesto del cluster per il cluster nella sezione KtlLogger. Consente di configurare il percorso di log condiviso e la dimensione oltre i limiti di memoria globali utilizzati dal logger. Il manifesto del cluster è un unico file XML che contiene le impostazioni e le configurazioni applicabili a tutti i nodi e ai servizi del cluster. Il file tipicamente si chiama ClusterManifest.xml. È possibile visualizzare il manifesto del cluster utilizzando il comando Powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Nomi delle configurazioni
| Nome | Unità | Valore predefinito | Osservazioni |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Numero minimo di KB da allocare in modalità kernel per il pool di memoria del buffer di scrittura del logger. Questo pool di memoria viene utilizzato per il caching delle informazioni sullo stato prima della scrittura su disco. |
| WriteBufferMemoryPoolMaximumInKB |Kilobyte |Nessun limite |Dimensioni massime raggiungibili dal pool di memoria del buffer di scrittura del logger. |
| SharedLogId |GUID |"" |Specifica un GUID univoco per individuare il file di log condiviso predefinito usato da tutti i servizi Reliable Services su tutti i nodi del cluster che non specificano il parametro SharedLogId nella configurazione specifica del servizio. Se viene specificato lo SharedLogId, deve anche essere specificato lo SharedLogPath. |
| SharedLogPath |Nome di percorso completo |"" |Specifica il percorso completo in cui il file di log condiviso viene utilizzato da tutti i servizi Reliable Services su tutti i nodi del cluster che non specificano lo SharedLogPath nella configurazione specifica del servizio. Tuttavia, se è stato specificato SharedLogPath, lo deve essere anche SharedLogId. |
| SharedLogSizeInMB |Megabyte |8192 |Specifica il numero di MB di spazio su disco da allocare in modo statico per il log condiviso. Il valore deve essere maggiore di 2048. |

In ARM di Azure o nel modello JSON locale, l'esempio seguente illustra come modificare il log delle transazioni condiviso che viene creato per eseguire il backup di eventuali raccolte affidabili per i servizi con stato.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Esempio di sezione del manifesto del cluster per gli sviluppatori in locale
Per modificare questa sezione nell'ambiente di sviluppo locale, è necessario modificare il file clustermanifest.xml locale.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Osservazioni
Il logger dispone di un pool di memoria globale allocato dalla memoria non di paging del kernel disponibile per tutti i servizi Reliable Services su un nodo per il caching dei dati sullo stato prima che siano scritti sul log dedicato associato alla replica del servizio Reliable Services. La dimensione del pool è controllata dalle impostazioni WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB specifica la dimensione iniziale del pool di memoria e la dimensione minima alla quale è possibile ridurre la memoria. WriteBufferMemoryPoolMaximumInKB è la dimensione massima che può raggiungere il pool di memoria. Ogni replica del servizio Reliable Services aperta può aumentare la dimensione del pool di memoria di una quantità determinata dal sistema, fino a WriteBufferMemoryPoolMaximumInKB. Qualora la domanda per la memoria sia superiore alla disponibilità del pool di memoria, le richieste per la memoria sono ritardate finché la memoria non è disponibile. Pertanto se il pool di memoria del buffer di scrittura è troppo piccolo per una configurazione specifica, le prestazioni potrebbero risentirne.

Le impostazioni SharedLogId e SharedLogPath vengono sempre utilizzate per definire GUID e percorso del log condiviso predefinito per tutti i nodi del cluster. Il log condiviso predefinito viene utilizzato per tutti i servizi Reliable Services che non specificano le impostazioni nel file settings.xml per il servizio specifico. Per ottenere le migliori prestazioni, i file di log condivisi devono essere memorizzati su dischi riservati esclusivamente al file di log condiviso, in modo da ridurre le situazioni di contesa della testina.

SharedLogSizeInMB specifica la quantità di spazio su disco da preallocare per il log condiviso predefinito su tutti i nodi.  Le impostazioni SharedLogId e SharedLogPath non devono essere specificate per specificare SharedLogSizeInMB.

## <a name="service-specific-configuration"></a>Configurazione specifica del servizio
È possibile modificare le configurazioni predefinite di Reliable Services con stato usando il pacchetto di configurazione (Config) o l'implementazione del servizio (codice).

* **Config** : la configurazione tramite il pacchetto config viene realizzata modificando il file Settings.xml generato nella radice del pacchetto Microsoft Visual Studio presente nella cartella Config per ogni servizio dell'applicazione.
* **Codice**: la configurazione tramite il codice viene realizzata creando un ReliableStateManager usando un oggetto ReliableStateManagerConfiguration con il set di opzioni appropriato.

Per impostazione predefinita, il runtime di Azure Service Fabric cerca i nomi di sezione predefiniti nel file settings.xml e usa i valori di configurazione durante la creazione dei componenti di runtime sottostanti.

> [!NOTE]
> **Non** eliminare i nomi di sezione delle configurazioni seguenti nel file Settings.xml generato nella soluzione Visual Studio, a meno che non si preveda di configurare il servizio tramite codice.
> La ridenominazione del pacchetto di configurazione o dei nomi di sezione richiede una modifica del codice quando si configura ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Configurazione della sicurezza del replicatore
Le configurazioni della sicurezza del replicatore consentono di proteggere il canale di comunicazione usato durante la replica. Questo significa che i servizi non potranno visualizzare l'uno il traffico di replica dell'altro, garantendo la sicurezza dei dati a disponibilità elevata. Per impostazione predefinita, una sezione di configurazione della sicurezza vuota non abilita la sicurezza della replica.

### <a name="default-section-name"></a>Nome predefinito della sezione
ReplicatorSecurityConfig

> [!NOTE]
> Per modificare questo nome di sezione, sostituire il parametro replicatorSecuritySectionName del costruttore ReliableStateManagerConfiguration durante la creazione di ReliableStateManager per questo servizio.
> 
> 

### <a name="replicator-configuration"></a>Configurazione del replicatore
Le configurazioni del replicatore consentono di configurare il replicatore responsabile di garantire l'elevata affidabilità dello stato di Reliable Services con stato replicando e rendendo permanente lo stato in locale.
La configurazione predefinita viene generata dal modello di Visual Studio e dovrebbe essere sufficiente. Questa sezione descrive le configurazioni aggiuntive che sono disponibili per ottimizzare il replicatore.

### <a name="default-section-name"></a>Nome predefinito della sezione
ReplicatorConfig

> [!NOTE]
> Per modificare questo nome di sezione, sostituire il parametro replicatorSettingsSectionName del costruttore ReliableStateManagerConfiguration durante la creazione di ReliableStateManager per questo servizio.
> 
> 

### <a name="configuration-names"></a>Nomi delle configurazioni
| Nome | Unità | Valore predefinito | Osservazioni |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Secondi |0,015 |Periodo di tempo per cui il replicatore, dopo aver ricevuto un'operazione, attende presso il replicatore secondario prima di inviare un acknowledgement al replicatore principale. Gli altri acknowledgement relativi alle operazioni elaborate all'interno di questo intervallo vengono inviati come risposta unica. |
| ReplicatorEndpoint |N/D |Nessun valore predefinito: parametro obbligatorio |Indirizzo IP e porta che il replicatore principale/secondario userà per comunicare con altri replicatori nel set di repliche. Deve fare riferimento a un endpoint di risorsa TCP nel manifesto del servizio. Per ulteriori informazioni sulla definizione delle risorse dell'endpoint in un manifesto del servizio, vedere [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md) . |
| MaxPrimaryReplicationQueueSize |Numero di operazioni |8192 |Numero massimo di operazioni nella coda principale. Un'operazione viene liberata quando il replicatore principale riceve un acknowledgement da tutti i replicatori secondari. Questo valore deve essere maggiore di 64 ed essere una potenza di 2. |
| MaxSecondaryReplicationQueueSize |Numero di operazioni |16384 |Numero massimo di operazioni nella coda secondaria. Un'operazione viene liberata quando il relativo stato viene reso altamente disponibile tramite persistenza. Questo valore deve essere maggiore di 64 ed essere una potenza di 2. |
| CheckpointThresholdInMB |MB |50 |Quantità di spazio del file di log dopo il quale viene eseguito un checkpoint dello stato. |
| MaxRecordSizeInKB |KB |1024 |La dimensione massima dei record che il replicatore può scrivere nel log. Questo valore deve essere un multiplo di 4 ed essere maggiore di 16. |
| MinLogSizeInMB |MB |0 (determinato dal sistema) |Dimensioni minime del log delle transazioni. Il log non potrà essere troncato a una dimensione inferiore a quella impostata. 0 indica che il replicatore determinerà le dimensioni del log minimo. Aumentando il valore si aumenta la possibilità di eseguire copie parziali e backup incrementali poiché le probabilità di troncamento dei record dei log rilevanti si riducono. |
| TruncationThresholdFactor |Fattore |2 |Determina a quali dimensioni del log verrà attivato il troncamento. La soglia di troncamento è determinata dal parametro MinLogSizeInMB moltiplicato per il parametro TruncationThresholdFactor. TruncationThresholdFactor deve essere maggiore di 1. MinLogSizeInMB * TruncationThresholdFactor deve essere minore di MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Fattore |4 |Determina le dimensioni del log a cui la replica inizierà a essere limitata. La soglia di limitazione (in MB) viene determinata da Max((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Il valore della soglia di limitazione (in MB) deve essere superiore al valore della soglia di troncamento (in MB). Il valore della soglia di troncamento (in MB) deve essere inferiore al valore di MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Dimensioni massime accumulate (in MB) dei log di backup in una data catena di log di backup. Una richiesta di backup incrementale avrà esito negativo se il backup incrementale dovesse generare un log di backup che causasse i log di backup accumulati poiché il backup completo rilevante sarebbe più grande di tali dimensioni. In questi casi, l'utente deve eseguire un backup completo. |
| SharedLogId |GUID |"" |Specifica un GUID unico da utilizzare per l'identificazione del file di log condiviso usato con la replica in oggetto. In genere, i servizi non devono usare questa impostazione. Tuttavia, se è stato specificato SharedLogId, lo deve essere anche SharedLogPath. |
| SharedLogPath |Nome di percorso completo |"" |Specifica il percorso completo in cui verrà creato il file di log condiviso per la replica in oggetto. In genere, i servizi non devono usare questa impostazione. Tuttavia, se è stato specificato SharedLogPath, lo deve essere anche SharedLogId. |
| SlowApiMonitoringDuration |Secondi |300 |Imposta l'intervallo di monitoraggio per le chiamate API gestite. Esempio: funzione di callback di backup fornita dall'utente. Al termine dell'intervallo verrà inviato un report sull'integrità di avviso a Health Manager. |

### <a name="sample-configuration-via-code"></a>Configurazione di esempio tramite codice
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>File di configurazione di esempio
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


### <a name="remarks"></a>Osservazioni
BatchAcknowledgementInterval controlla la latenza di replica. Il valore '0' determina la latenza più bassa possibile a scapito della velocità effettiva, in quanto è necessario inviare ed elaborare una maggiore quantità di messaggi di acknowledgement, ciascuno dei quali contenente un numero minore di acknowledgement.
Più alto è il valore di BatchAcknowledgementInterval, maggiore sarà la velocità effettiva di replica complessiva, ma con una latenza delle operazioni più elevata. Questo ha un impatto diretto sulla latenza dei commit delle transazioni.

Il valore per CheckpointThresholdInMB controlla la quantità di spazio su disco che il replicatore può usare per archiviare le informazioni sullo stato nel file di log dedicato della replica. Aumentando questo valore rispetto all'impostazione predefinita, è possibile favorire tempi di riconfigurazione più rapidi quando una nuova replica viene aggiunta al set. Questo a causa del trasferimento di stato parziale che viene eseguito in seguito alla disponibilità di una maggiore cronologia di operazioni nel log e che può aumentare il tempo di ripristino di una replica dopo un arresto anomalo.

L'impostazione MaxRecordSizeInKB definisce la dimensione massima dei record che il replicatore può scrivere nel file di log. Nella maggior parte dei casi, la dimensione predefinita del record, 1024 KB, è ottimale. Tuttavia, se il servizio richiede che nelle informazioni sullo stato vengano inclusi elementi dati di dimensioni maggiori, è possibile che sia necessario aumentare questo valore. La riduzione delle dimensioni di MaxRecordSizeInKB a meno di 1024 KB comporta vantaggi relativi, perché i record di dimensioni minori usano soltanto lo spazio necessario. È probabile che questo valore debba essere modificato solo in rari casi.

Le impostazioni SharedLogId e SharedLogPath vengono sempre usate insieme e consentono a un servizio di usare un log condiviso separato dal log condiviso predefinito per il nodo. Per ottenere migliori prestazioni, il maggior numero di servizi possibile dovrebbe specificare lo stesso log condiviso. I file di log condivisi devono essere memorizzati su dischi riservati esclusivamente a questo tipo di file, in modo da ridurre le situazioni di contesa della testina. È probabile che questo valore debba essere modificato solo in rari casi.

## <a name="next-steps"></a>Passaggi successivi
* [Debug dell'applicazione di Service Fabric in Visual Studio](service-fabric-debugging-your-application.md)
* [Guida di riferimento per gli sviluppatori per Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)


