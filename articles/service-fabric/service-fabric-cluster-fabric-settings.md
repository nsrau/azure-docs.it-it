---
title: Modificare le impostazioni di un cluster di Azure Service Fabric | Documentazione Microsoft
description: "Questo articolo descrive le impostazioni dell'infrastruttura e i criteri di aggiornamento dell'infrastruttura che è possibile personalizzare."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: chackdan
ms.openlocfilehash: 19caa05f0de7b4ff4ed7f4eafe50839d04f4ab50
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Personalizzare le impostazioni del cluster Service Fabric e dei criteri di aggiornamento dell'infrastruttura
Questo documento illustra come personalizzare le varie impostazioni dell'infrastruttura e i criteri di aggiornamento della stessa per il cluster di Service Fabric. Le impostazioni possono essere personalizzate nel [portale di Azure](https://portal.azure.com) o con un modello di Azure Resource Manager.

> [!NOTE]
> Non tutte le impostazioni sono disponibili nel portale. Se una delle impostazioni elencate di seguito non è disponibile tramite il portale, personalizzarlo usando un modello di Azure Resource Manager.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalizzare le impostazioni del cluster usando i modelli di Gestione risorse
I passaggi riportati di seguito illustrano come aggiungere una nuova impostazione *MaxDiskQuotaInMB* alla sezione *Diagnostica*.

1. Passare ad https://resources.azure.com
2. Passare alla sottoscrizione espandendo **sottoscrizioni** -> **gruppi di risorse** -> **Microsoft.ServiceFabric** -> **\<nome del cluster>**
3. Nell'angolo in alto a destra selezionare **Lettura/Scrittura**.
4. Selezionare **Modifica**, aggiornare l'elemento JSON `fabricSettings` e aggiungere un nuovo elemento:

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Di seguito è riportato un elenco di impostazioni dell'infrastruttura che è possibile personalizzare, organizzate per sezione.

### <a name="section-name-diagnostics"></a>Nome della sezione: Diagnostics
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| ConsumerInstances |String | Dinamico |L'elenco delle istanze di consumer DCA. |
| ProducerInstances |String | Dinamico |L'elenco delle istanze di producer DCA. |
| AppEtwTraceDeletionAgeInDays |Int, valore predefinito: 3 | Dinamico |Il numero di giorni dopo il quale i file ETL meno recenti contenenti tracce ETW di applicazioni vengono eliminati. |
| AppDiagnosticStoreAccessRequiresImpersonation |Bool, valore predefinito: true | Dinamico |Indica se è richiesta la rappresentazione quando si accede a un archivio di diagnostica per conto dell'applicazione. |
| MaxDiskQuotaInMB |Int, valore predefinito: 65536 | Dinamico |Quota del disco in MB per i file di log di Windows Fabric. |
| DiskFullSafetySpaceInMB |Int, valore predefinito: 1024 | Dinamico |Spazio su disco rimanente in MB per la protezione dall'uso di DCA. |
| ApplicationLogsFormatVersion |Int, valore predefinito: 0 | Dinamico |Versione per il formato dei log applicazioni. I valori supportati sono 0 e 1. La versione 1 include più campi dal record di eventi ETW rispetto alla versione 0. |
| ClusterId |String | Dinamico |L'ID univoco del cluster. Viene generato quando viene creato il cluster. |
| EnableTelemetry |Bool, valore predefinito: true | Dinamico |Parametro per abilitare o disabilitare la telemetria. |
| EnableCircularTraceSession |Bool, valore predefinito: false | statico |Il flag indica se devono essere usate le sessioni di traccia circolari. |

### <a name="section-name-traceetw"></a>Nome della sezione: Trace/Etw
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| Level |Int, valore predefinito: 4 | Dinamico |Il livello di traccia ETW può accettare valori 1, 2, 3 e 4. Per assicurare il supporto è necessario mantenere il livello di traccia a 4 |

### <a name="section-name-performancecounterlocalstore"></a>Nome della sezione: PerformanceCounterLocalStore
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| IsEnabled |Bool, valore predefinito: true | Dinamico |Il flag indica se è abilitata la raccolta dei contatori delle prestazioni nel nodo locale. |
| SamplingIntervalInSeconds |Int, valore predefinito: 60 | Dinamico |Intervallo di campionamento per i contatori delle prestazioni che vengono raccolti. |
| Counters |String | Dinamico |Elenco delimitato da virgole dei contatori delle prestazioni da raccogliere. |
| MaxCounterBinaryFileSizeInMB |Int, valore predefinito: 1 | Dinamico |Dimensione massima (in MB) per ogni file binario del contatore delle prestazioni. |
| NewCounterBinaryFileCreationIntervalInMinutes |Int, valore predefinito: 10 | Dinamico |Intervallo massimo (in secondi) dopo il quale viene creato un nuovo file binario di contatore delle prestazioni. |

### <a name="section-name-setup"></a>Nome della sezione: Setup
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| FabricDataRoot |String | Non consentito |La directory radice dei dati di Service Fabric. L'impostazione predefinita per Azure è d:\svcfab |
| FabricLogRoot |String | Non consentito |La directory radice dei log di Service Fabric. Si tratta della posizione in cui vengono collocate le tracce e i log di Service Fabric. |
| ServiceRunAsAccountName |String | Non consentito |Il nome dell'account con cui eseguire il servizio host infrastruttura. |
| SkipFirewallConfiguration |Bool, valore predefinito: false | Non consentito |Specifica se le impostazioni del firewall devono essere impostate dal sistema. Si applica solo se si usa Windows Firewall. Se si usano firewall di terze parti, è necessario aprire le porte per il sistema e le applicazioni da usare |
|NodesToBeRemoved|stringa, il valore predefinito è ""| Dinamico |Nodi che devono essere rimossi come parte dell'aggiornamento della configurazione. (Solo per le distribuzioni autonome)|
|ContainerNetworkSetup|bool, valore predefinito: FALSE| statico |Se configurare una rete di contenitori.|
|ContainerNetworkName|string, valore predefinito: L""| statico |Nome di rete da usare quando si configura una rete di contenitori.|

### <a name="section-name-transactionalreplicator"></a>Nome della sezione: TransactionalReplicator
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| MaxCopyQueueSize |Uint, valore predefinito: 16384 | statico |Valore massimo che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica. Si noti che deve essere una potenza di 2. Se durante il runtime la coda raggiunge tale dimensione, l'operazione verrà limitata tra i replicatori primario e secondario. |
| BatchAcknowledgementInterval | Tempo in secondi, valore predefinito: 0,015 | statico | Specificare l'intervallo di tempo in secondi. Periodo di tempo di attesa del Replicator dopo aver ricevuto un'operazione prima di inviare un acknowledgement in risposta. Altre operazioni ricevuti durante questo periodo di tempo comporteranno un invio dei vari acknowledgement in un unico messaggio, riducendo il traffico di rete ma anche potenzialmente la velocità effettiva del Replicator. |
| MaxReplicationMessageSize |Uint, valore predefinito: 52428800 | statico | Dimensioni massime dei messaggi delle operazioni di replica. Il valore predefinito è 50 MB. |
| ReplicatorAddress |stringa, il valore predefinito è "localhost:0" | statico | L'endpoint in forma di stringa "IP:Port" usato dal Replicator di Windows Fabric per stabilire connessioni con altre repliche per inviare/ricevere operazioni. |
| InitialPrimaryReplicationQueueSize |Uint, valore predefinito: 64 | statico |Valore che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica sul Replicator primario. Si noti che deve essere una potenza di 2.|
| MaxPrimaryReplicationQueueSize |Uint, valore predefinito: 8192 | statico |Numero massimo di operazioni che possono essere presenti nella coda di replica primaria. Si noti che deve essere una potenza di 2. |
| MaxPrimaryReplicationQueueMemorySize |Uint, valore predefinito: 0 | statico |Valore massimo della coda di replica primaria in byte. |
| InitialSecondaryReplicationQueueSize |Uint, valore predefinito: 64 | statico |Valore che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica sul Replicator secondario. Si noti che deve essere una potenza di 2. |
| MaxSecondaryReplicationQueueSize |Uint, valore predefinito: 16384 | statico |Numero massimo di operazioni che possono essere presenti nella coda di replica secondaria. Si noti che deve essere una potenza di 2. |
| MaxSecondaryReplicationQueueMemorySize |Uint, valore predefinito: 0 | statico |Valore massimo della coda di replica secondaria in byte. |
| SecondaryClearAcknowledgedOperations |Bool, valore predefinito: false | statico |Bool che controlla se le operazioni per il Replicator secondario vengono cancellate e scaricate sul disco dopo aver inviato l'acknowledgement al Replicator primario. Impostando il parametro su TRUE si potranno avere letture di disco aggiuntive sul nuovo Replicator primario in caso di ricostruzione delle repliche dopo un failover. |
| MaxMetadataSizeInKB |Int, valore predefinito: 4 |Non consentito|Dimensione massima dei metadati del flusso di log. |
| MaxRecordSizeInKB |Uint, valore predefinito: 1024 |Non consentito| Dimensione massima del record di un flusso di log. |
| CheckpointThresholdInMB |Int, valore predefinito: 50 |statico|Verrà avviato un checkpoint quando l'uso del log supera questo valore. |
| MaxAccumulatedBackupLogSizeInMB |Int, valore predefinito: 800 |statico|Dimensioni massime accumulate (in MB) dei log di backup in una data catena di log di backup. Una richiesta di backup incrementale avrà esito negativo se il backup incrementale dovesse generare un log di backup che causasse i log di backup accumulati poiché il backup completo rilevante sarebbe più grande di tali dimensioni. In questi casi, l'utente deve eseguire un backup completo. |
| MaxWriteQueueDepthInKB |Int, valore predefinito: 0 |Non consentito| Int la profondità massima della coda di scrittura. Il logger di base può usare il valore specificato in kilobyte per il log associato a questa replica. Questo valore è il numero massimo di byte che può rimanere in sospeso durante gli aggiornamenti del logger di base. Se il valore è 0, il logger di base calcolerà un valore appropriato o un multiplo di 4. |
| SharedLogId |String |Non consentito|Identificatore di un log condiviso. È un valore GUID e deve essere univoco per ciascun log condiviso. |
| SharedLogPath |String |Non consentito|Percorso del log condiviso. Se questo valore è vuoto viene usato il log condiviso predefinito. |
| SlowApiMonitoringDuration |Tempo in secondi, il valore predefinito è 300 |statico| Specificare la durata per l'API prima che venga generato l'evento di integrità dell'avviso.|
| MinLogSizeInMB |Int, valore predefinito: 0 |statico|Dimensioni minime del log delle transazioni. Il log non potrà essere troncato a una dimensione inferiore a quella impostata. 0 indica che il Replicator determinerà le dimensioni del log minimo in base alle altre impostazioni. Aumentando il valore si aumenta la possibilità di eseguire copie parziali e backup incrementali poiché le probabilità di troncamento dei record dei log rilevanti si riducono. |

### <a name="section-name-fabricclient"></a>Nome della sezione: FabricClient
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| NodeAddresses |stringa, il valore predefinito è "" |statico|Un insieme di indirizzi (stringhe di connessione) in nodi diversi usabile per comunicare con il Naming Service. Inizialmente, il client si connette scegliendo casualmente uno degli indirizzi. Se viene specificata più di una stringa e una connessione non riesce a causa di un errore di comunicazione o timeout, il client usa l'indirizzo successivo in maniera sequenziale. Per i dettagli di semantica sui nuovi tentativi, vedere la sezione relativa del Naming Service. |
| ConnectionInitializationTimeout |Tempo in secondi, valore predefinito: 2 |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di timeout di connessione per ogni tentativo di apertura di connessione del client al gateway. |
| PartitionLocationCacheLimit |Int, valore predefinito: 100000 |statico|Numero di partizioni memorizzate nella cache per la risoluzione di servizio. Impostarlo su 0 per non avere limiti. |
| ServiceChangePollInterval |Tempo in secondi, valore predefinito: 120 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo tra i polling consecutivi per le modifiche di servizio dal client al gateway, per i callback di notifica in caso di modifiche al servizio registrato. |
| KeepAliveIntervalInSeconds |Int, valore predefinito: 20 |statico|L'intervallo con cui il trasporto FabricClient invia messaggi keep-alive al gateway. Se il valore è 0, keepAlive è disabilitato. Deve essere un valore positivo. |
| HealthOperationTimeout |Tempo in secondi, valore predefinito: 120 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout per un messaggio di report inviato a Health Manager. |
| HealthReportSendInterval |Tempo in secondi, il valore predefinito è 30 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo dopo il quale il componente di report invia i report sull'integrità accumulati a Health Manager. |
| HealthReportRetrySendInterval |Tempo in secondi, il valore predefinito è 30 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo dopo il quale il componente di report invia nuovamente i report sull'integrità accumulati a Health Manager. |
| RetryBackoffInterval |Tempo in secondi, valore predefinito: 3 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo di backoff prima di ritentare l'operazione. |
| MaxFileSenderThreads |Uint, valore predefinito: 10 |statico|Il numero massimo di file trasferiti in parallelo. |

### <a name="section-name-common"></a>Nome della sezione: Common
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| PerfMonitorInterval |Tempo in secondi, valore predefinito: 1 |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo del monitoraggio delle prestazioni. Con valore 0 o negativo, il monitoraggio è disabilitato. |

### <a name="section-name-healthmanager"></a>Nome della sezione: HealthManager
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, valore predefinito: false |statico|Criteri di valutazione dell'integrità del cluster: abilitare il parametro per la valutazione dell'integrità del tipo di applicazione. |

### <a name="section-name-nodedomainids"></a>Nome della sezione: NodeDomainIds
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| UpgradeDomainId |stringa, il valore predefinito è "" |statico|Descrivi il dominio di aggiornamento a cui appartiene un nodo. |
| PropertyGroup |NodeFaultDomainIdCollection |statico|Descrive il dominio di errore a cui appartiene un nodo. Il dominio di errore viene definito tramite un URI che descrive la posizione del nodo nel data center.  Gli URI dei domini di errore sono nel formato fd:/fd/ seguito da un segmento di percorso URI.|

### <a name="section-name-nodeproperties"></a>Nome della sezione: NodeProperties
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |statico|Una raccolta di coppie di stringhe chiave-valore per le proprietà del nodo. |

### <a name="section-name-nodecapacities"></a>Nome della sezione: NodeCapacities
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |statico|Una raccolta di capacità dei nodi per diverse metriche. |

### <a name="section-name-fabricnode"></a>Nome della sezione: FabricNode
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| StateTraceInterval |Tempo in secondi, il valore predefinito è 300 |statico|Specificare l'intervallo di tempo in secondi. L'intervallo per la traccia dello stato di nodo in ogni nodo e dei nodi in FM/FMM. |
| StartApplicationPortRange |Int, valore predefinito: 0 |statico|Avvio delle porte di applicazione gestite dal sottosistema di hosting. Obbligatorio se EndpointFilteringEnabled è impostato su true in Hosting. |
| EndApplicationPortRange |Int, valore predefinito: 0 |statico|Termine (non inclusivo) delle porte di applicazione gestite dal sottosistema di hosting. Obbligatorio se EndpointFilteringEnabled è impostato su true in Hosting. |
| ClusterX509StoreName |stringa, il valore predefinito è "My" |Dinamico|Nome dell'archivio certificati X.509 che contiene il certificato del cluster per proteggere la comunicazione all'interno del cluster. |
| ClusterX509FindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico|Indica la modalità di ricerca di un certificato del cluster nell'archivio specificato da ClusterX509StoreName. Valori supportati: "FindByThumbprint"; "FindBySubjectName". Con "FindBySubjectName", in caso di più corrispondenze, viene usato il certificato con la scadenza più lontana. |
| ClusterX509FindValue |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare il certificato del cluster. |
| ClusterX509FindValueSecondary |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare il certificato del cluster. |
| ServerAuthX509StoreName |stringa, il valore predefinito è "My" |Dinamico|Nome dell'archivio certificati X.509 che contiene il certificato server per il servizio di entrata. |
| ServerAuthX509FindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico|Indica la modalità di ricerca del certificato server nell'archivio specificato da ServerAuthX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare il certificato del server. |
| ServerAuthX509FindValueSecondary |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare il certificato del server. |
| ClientAuthX509StoreName |stringa, il valore predefinito è "My" |Dinamico|Nome dell'archivio certificati X.509 che contiene un certificato per il ruolo di amministratore predefinito FabricClient. |
| ClientAuthX509FindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico|Indica la modalità di ricerca del certificato nell'archivio specificato da ClientAuthX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |stringa, il valore predefinito è "" | Dinamico|Valore di filtro di ricerca usato per individuare un certificato per il ruolo di amministratore predefinito FabricClient. |
| ClientAuthX509FindValueSecondary |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare un certificato per il ruolo di amministratore predefinito FabricClient. |
| UserRoleClientX509StoreName |stringa, il valore predefinito è "My" |Dinamico|Nome dell'archivio certificati X.509 che contiene un certificato per il ruolo di utente predefinito FabricClient. |
| UserRoleClientX509FindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico|Indica la modalità di ricerca del certificato nell'archivio specificato da UserRoleClientX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare un certificato per il ruolo di utente predefinito FabricClient. |
| UserRoleClientX509FindValueSecondary |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare un certificato per il ruolo di utente predefinito FabricClient. |

### <a name="section-name-paas"></a>Nome della sezione: Paas
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| ClusterId |stringa, il valore predefinito è "" |Non consentito|Archivio certificati X.509 usato da Service Fabric per la protezione della configurazione. |

### <a name="section-name-fabrichost"></a>Nome della sezione: FabricHost
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| StopTimeout |Tempo in secondi, il valore predefinito è 300 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout per l'attivazione, la disattivazione e l'aggiornamento del servizio ospitato. |
| StartTimeout |Tempo in secondi, il valore predefinito è 300 |Dinamico|Specificare l'intervallo di tempo in secondi. Timeout per l'avvio di fabricactivationmanager. |
| ActivationRetryBackoffInterval |Tempo in secondi, valore predefinito: 5 |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di backoff per ogni errore di attivazione; in caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in MaxActivationFailureCount. L'intervallo tra tentativi è il prodotto dell'errore di attivazione continua e dell'intervallo di backoff di attivazione. |
| ActivationMaxRetryInterval |Tempo in secondi, il valore predefinito è 300 |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo massimo tra i tentativi di attivazione. In casi di errori periodici, l'intervallo tra i tentativi viene calcolato come segue: Min( ActivationMaxRetryInterval; conteggio errori continui * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Int, valore predefinito: 10 |Dinamico|Conteggio massimo di tentativi di attivazione da parte del sistema prima di interrompersi. |
| EnableServiceFabricAutomaticUpdates |Bool, valore predefinito: false |Dinamico|Parametro per abilitare l'aggiornamento automatico di Service Fabric tramite Windows Update. |
| EnableServiceFabricBaseUpgrade |Bool, valore predefinito: false |Dinamico|Parametro per abilitare l'aggiornamento base del server. |
| EnableRestartManagement |Bool, valore predefinito: false |Dinamico|Parametro per abilitare il riavvio del server. |


### <a name="section-name-failovermanager"></a>Nome della sezione: FailoverManager
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| UserReplicaRestartWaitDuration |Tempo in secondi, valore predefinito: 60,0 * 30 |Dinamico|Specificare l'intervallo di tempo in secondi. Quando una replica persistente va offline, Windows Fabric attende che torni online per questo intervallo di tempo, passato il quale crea nuove repliche sostitutive, che richiederanno una copia dello stato. |
| QuorumLossWaitDuration |Tempo in secondi, valore predefinito: MaxValue |Dinamico|Specificare l'intervallo di tempo in secondi. Durata massima per cui una partizione può essere in stato di perdita del quorum. Se la partizione si trova ancora in questo stato passato questo intervallo di tempo, viene ripristinata considerando come perse le repliche offline. Ciò può comportare una potenziale perdita di dati. |
| UserStandByReplicaKeepDuration |Tempo in secondi, il valore predefinito è 3600.0 * 24 * 7 |Dinamico|Specificare l'intervallo di tempo in secondi. Quando una replica persistente torna online, potrebbe già essere stata sostituita. Questo timer determina per quanto tempo FM manterrà la replica in standby prima di rimuoverla. |
| UserMaxStandByReplicaCount |Int, valore predefinito: 1 |Dinamico|Il numero massimo predefinito di repliche in standby che il sistema conserva per i servizi utente. |
| ExpectedClusterSize|int, valore predefinito: 1|Dinamico|Durante l'avvio iniziale del cluster, il servizio FM aspetterà che questo numero di nodi segnali lo stato di attivato prima di iniziare a selezionare altri servizi, inclusi i servizi di sistema come quello di denominazione.  Aumentando questo valore aumenta il tempo necessario per l'avvio di un cluster, ma si evitano sovraccarichi per i primi nodi, oltre agli ulteriori spostamenti che risulterebbero necessari man mano che vengono portati online altri nodi.  Questo valore deve essere impostato in genere su una piccola frazione della dimensione iniziale del cluster. |
|ClusterPauseThreshold|int, valore predefinito: 1|Dinamico|Se il numero di nodi nel sistema scende al di sotto di questo valore, la selezione, il bilanciamento del carico e il failover vengono interrotti. |
|TargetReplicaSetSize|int, valore predefinito: 7|Non consentito|Questo è il numero di destinazione delle repliche di FM che manterrà Windows Fabric.  Un numero maggiore comporta una maggiore affidabilità dei dati di FM, con effetti negativi limitati sulle prestazioni. |
|MinReplicaSetSize|int, valore predefinito: 3|Non consentito|Questa è la dimensione del set di repliche minima per il servizio FM.  Se il numero di repliche attive di FM scende al di sotto di questo valore, il servizio FM rifiuterà le modifiche al cluster almeno fino a quando non viene recuperato il numero minimo di repliche. |
|ReplicaRestartWaitDuration|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(60.0 * 30)|Non consentito|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per FMService |
|StandByReplicaKeepDuration|Timespan, valore predefinito: Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Non consentito|Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per FMService |
|PlacementConstraints|string, valore predefinito: L""|Non consentito|Eventuali vincoli di selezione per le repliche di Gestione failover. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamico|Specificare l'intervallo di tempo in secondi. Si tratta della durata prevista per l'aggiornamento di un nodo durante l'aggiornamento di Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamico|Specificare l'intervallo di tempo in secondi. Si tratta della durata prevista per l'aggiornamento di tutte le repliche in un nodo durante l'aggiornamento dell'applicazione. |
|ExpectedNodeDeactivationDuration|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamico|Specificare l'intervallo di tempo in secondi. Si tratta della durata prevista per il completamento della disattivazione in un nodo. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, valore predefinito: TRUE|Dinamico|Se impostato su true, sarà consentito lo spostamento delle repliche con una dimensione del set di repliche di destinazione pari a 1 durante l'aggiornamento. |
|ReconfigurationTimeLimit|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(300)|Dinamico|Specificare l'intervallo di tempo in secondi. Limite di tempo per la riconfigurazione, dopo il quale verrà avviato un report sull'integrità di avviso. |
|BuildReplicaTimeLimit|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(3600)|Dinamico|Specificare l'intervallo di tempo in secondi. Limite di tempo per la creazione di una replica con stato, dopo il quale verrà avviato un report sull'integrità di avviso. |
|CreateInstanceTimeLimit|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(300)|Dinamico|Specificare l'intervallo di tempo in secondi. Limite di tempo per la creazione di un'istanza senza stato, dopo il quale verrà avviato un report sull'integrità di avviso. |
|PlacementTimeLimit|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(600)|Dinamico|Specificare l'intervallo di tempo in secondi. Limite di tempo per il raggiungimento del numero di repliche di destinazione, dopo il quale verrà avviato un report sull'integrità di avviso. |

### <a name="section-name-namingservice"></a>Nome della sezione: NamingService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, valore predefinito: 7 |Non consentito|Il numero di set di repliche per ogni partizione dell'archivio di Naming Service. L'aumento del numero di set di repliche rafforzerà l'affidabilità delle informazioni nell'archivio di Naming Service, riducendo i rischi di perdita di tali informazioni in caso di errori nei nodi. Tutto ciò comporterà tuttavia un carico maggiore per Windows Fabric, nonché un aumento del tempo necessario per aggiornare i dati di denominazione.|
|MinReplicaSetSize | Int, valore predefinito: 3 |Non consentito| Il numero minimo di repliche di Naming Service in cui scrivere per completare un aggiornamento. Se nel sistema sono presenti meno repliche rispetto a questo valore, Reliability System non effettua aggiornamenti dell'archivio Naming Service fino a quando le repliche non vengono ripristinate. Questo valore non deve mai essere maggiore di TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Tempo in secondi, valore predefinito: (60.0 * 30)|Non consentito| Specificare l'intervallo di tempo in secondi. Quando una replica di Naming Service si arresta, si avvia questo timer.  Alla scadenza FM inizierà a sostituire le repliche non attive; queste non vengono ancora considerate perse. |
|QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |Non consentito| Specificare l'intervallo di tempo in secondi. Quando un Naming Service entra in uno stato di perdita del quorum, si avvia questo timer.  Una volta scaduto, FM considererà perse le repliche offline e tenterà di ripristinare il quorum. Questa azione può comportare la perdita di dati. |
|StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: è 3600.0 * 2 |Non consentito| Specificare l'intervallo di tempo in secondi. Quando una replica per Naming Service torna online, potrebbe già essere stata sostituita.  Questo timer determina per quanto tempo FM manterrà la replica in standby prima di rimuoverla. |
|PlacementConstraints | stringa, il valore predefinito è "" |Non consentito| Vincolo di posizionamento per il Naming Service. |
|ServiceDescriptionCacheLimit | Int, valore predefinito: 0 |statico| Numero massimo di voci conservate nella cache della descrizione del servizio LRU nell'archivio di Naming Service. Impostarlo su 0 per non avere limiti. |
|RepairInterval | Tempo in secondi, valore predefinito: 5 |statico| Specificare l'intervallo di tempo in secondi. Intervallo in cui verrà avviata la correzione delle incoerenze di denominazione tra il proprietario dell'autorità e il proprietario del nome. |
|MaxNamingServiceHealthReports | Int, valore predefinito: 10 |Dinamico|Numero massimo di operazioni lente non integre di cui il servizio Naming Service esegue globalmente il report. Se il valore è 0, verranno incluse tutte le operazioni lente. |
| MaxMessageSize |Int, valore predefinito: 4\*1024\*1024 |statico|Dimensioni massime dei messaggi di comunicazioni del nodo client quando si usa la denominazione. Attenuazione di attacchi DOS, valore predefinito: 4 MB. |
| MaxFileOperationTimeout |Tempo in secondi, il valore predefinito è 30 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout massimo consentito per l'operazione del servizio di un archivio file. Le richieste che specificano un timeout maggiore verranno respinte. |
| MaxOperationTimeout |Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout massimo consentito per le operazioni client. Le richieste che specificano un timeout maggiore verranno respinte. |
| MaxClientConnections |Int, valore predefinito: 1000 |Dinamico|Numero massimo di connessioni client consentito per gateway. |
| ServiceNotificationTimeout |Tempo in secondi, il valore predefinito è 30 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout usato per il recapito delle notifiche di servizio al client. |
| MaxOutstandingNotificationsPerClient |Int, valore predefinito: 1000 |Dinamico|Numero massimo di notifiche in sospeso prima della chiusura forzata di una registrazione cliente da parte del gateway. |
| MaxIndexedEmptyPartitions |Int, valore predefinito: 1000 |Dinamico|Numero massimo di partizioni vuote che rimarranno indicizzate nella cache di notifica per la sincronizzazione dei client in fase di riconnessione. Tutte le partizioni vuote oltre questo numero verranno rimosse dall'indice in ordine crescente di versione. Riconnettendo i client sarà comunque possibile sincronizzare e ricevere gli aggiornamenti saltati delle partizioni vuote, ma il protocollo di sincronizzazione sarà più oneroso. |
| GatewayServiceDescriptionCacheLimit |Int, valore predefinito: 0 |statico|Numero massimo di voci conservate nella cache della descrizione del servizio LRU nell'archivio di Naming Gateway. Impostarlo su 0 per non avere limiti. |
| PartitionCount |Int, valore predefinito: 3 |Non consentito|Numero di partizioni dell'archivio Naming Service da creare. Ogni partizione possiede una chiave di partizione singola che corrisponde al relativo indice. Esistono quindi chiavi di partizione [0; PartitionCount). Aumentando il numero di partizioni Naming Service, verrà aumentata anche la scalabilità delle relative operazioni riducendo la quantità media di dati presenti in un dato set di repliche di riserva. Tutto questo comporterà però un maggiore uso di risorse, dato che devono essere conservate repliche di servizio pari a PartitionCount*ReplicaSetSize.|

### <a name="section-name-runas"></a>Nome della sezione: RunAs
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| RunAsAccountName |stringa, il valore predefinito è "" |Dinamico|Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Dinamico|Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|stringa, il valore predefinito è "" |Dinamico|Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

### <a name="section-name-runasfabric"></a>Nome della sezione: RunAs_Fabric
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| RunAsAccountName |stringa, il valore predefinito è "" |Dinamico|Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Dinamico|Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Dinamico|Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

### <a name="section-name-runashttpgateway"></a>Nome della sezione: RunAs_HttpGateway
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| RunAsAccountName |stringa, il valore predefinito è "" |Dinamico|Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Dinamico|Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Dinamico|Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

### <a name="section-name-runasdca"></a>Nome della sezione: RunAs_DCA
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| RunAsAccountName |stringa, il valore predefinito è "" |Dinamico|Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Dinamico|Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Dinamico|Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

### <a name="section-name-httpgateway"></a>Nome della sezione: HttpGateway
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|IsEnabled|Bool, valore predefinito: false |statico| Abilita/disabilita HttpGateway. HttpGateway è disabilitato per impostazione predefinita. |
|ActiveListeners |Uint, valore predefinito: 50 |statico| Numero di letture da pubblicare nella coda del server http. Consente di controllare il numero di richieste simultanee che possono essere espletate da HttpGateway. |
|MaxEntityBodySize |Uint, valore predefinito: 4194304 |Dinamico|Indica la dimensione massima del corpo prevista da una richiesta HTTP. Il valore predefinito è 4 MB. Httpgateway non porterà a termine una richiesta se questa ha un corpo di dimensioni superiori al valore indicato. La dimensione minima dei blocchi di lettura è di 4096 byte. Il valore deve pertanto essere >= 4096. |
|HttpGatewayHealthReportSendInterval |Tempo in secondi, il valore predefinito è 30 |statico|Specificare l'intervallo di tempo in secondi. Intervallo dopo il quale il gateway HTTP invia i report sull'integrità accumulati a Health Manager. |

### <a name="section-name-ktllogger"></a>Nome della sezione: KtlLogger
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, valore predefinito: 1 |Dinamico|Flag che indica se le impostazioni di memoria devono essere configurate in modo dinamico e automatico. Se il valore è 0, le impostazioni vengono usate direttamente e non vengono modificate in base alle condizioni di sistema. Se il valore è 1, le impostazioni vengono configurate automaticamente e possono cambiare in base alle condizioni di sistema. |
|WriteBufferMemoryPoolMinimumInKB |Int, valore predefinito: 8388608 |Dinamico|Il numero di KB da allocare inizialmente per il pool di memoria buffer in scrittura. Usare 0 per non avere limiti. L'impostazione predefinita deve essere coerente con il parametro SharedLogSizeInMB di seguito. |
|WriteBufferMemoryPoolMaximumInKB | Int, valore predefinito: 0 |Dinamico|Il numero di KB raggiungibile dal pool di memoria buffer in scrittura. Usare 0 per non avere limiti. |
|MaximumDestagingWriteOutstandingInKB | Int, valore predefinito: 0 |Dinamico|Il numero di KB per cui il log condiviso può superare in dimensioni il log dedicato. Usare 0 per non avere limiti.
|SharedLogPath |stringa, il valore predefinito è "" |statico|Percorso e nome del file in cui inserire il contenitore del log condiviso. Usare "" per indicare il percorso predefinito nella radice dati di Service Fabric. |
|SharedLogId |stringa, il valore predefinito è "" |statico|GUID univoco per il contenitore di log condivisi. Usare "" se si usa il percorso predefinito nella radice dati di Service Fabric. |
|SharedLogSizeInMB |Int, valore predefinito: 8192 |statico|Il numero di MB da allocare nel contenitore di log condivisi. |

### <a name="section-name-applicationgatewayhttp"></a>Nome della sezione: ApplicationGateway/Http
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|IsEnabled |Bool, valore predefinito: false |statico| Abilita/disabilita HttpApplicationGateway. HttpApplicationGateway è disabilitato per impostazione predefinita. È necessario impostare questa configurazione per abilitarlo. |
|NumberOfParallelOperations | Uint, valore predefinito: 5000 |statico|Numero di letture da pubblicare nella coda del server http. Consente di controllare il numero di richieste simultanee che possono essere espletate da HttpGateway. |
|DefaultHttpRequestTimeout |Tempo in secondi, Il valore predefinito è 120 |Dinamico|Specificare l'intervallo di tempo in secondi.  Indica il timeout di richiesta predefinito per le richieste http in elaborazione nel gateway applicazione http. |
|ResolveServiceBackoffInterval |Tempo in secondi, valore predefinito: 5 |Dinamico|Specificare l'intervallo di tempo in secondi.  Indica l'intervallo di backoff predefinito prima di ritentare l'operazione di risoluzione del servizio. |
|BodyChunkSize |Uint, valore predefinito: 16384 |Dinamico| Indica la dimensione in byte per il blocco usato per leggere il corpo. |
|GatewayAuthCredentialType |stringa, il valore predefinito è "None" |statico| Indica il tipo di credenziali di sicurezza da usare nell'endpoint del gateway applicazione http. I valori validi sono "None/X509. |
|GatewayX509CertificateStoreName |stringa, il valore predefinito è "My" |Dinamico| Nome dell'archivio certificati X.509 che contiene il certificato per il gateway applicazione http. |
|GatewayX509CertificateFindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico| Indica la modalità di ricerca del certificato nell'archivio specificato da GatewayX509CertificateStoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | stringa, il valore predefinito è "" |Dinamico| Valore del filtro di ricerca usato per individuare il certificato del gateway applicazione http. Il certificato è configurato nell'endpoint https e può essere usato anche per verificare l'identità dell'app se richiesto dai servizi. Viene prima cercato FindValue. Se non esiste, viene cercato FindValueSecondary. |
|GatewayX509CertificateFindValueSecondary | stringa, il valore predefinito è "" |Dinamico|Valore del filtro di ricerca usato per individuare il certificato del gateway applicazione http. Il certificato è configurato nell'endpoint https e può essere usato anche per verificare l'identità dell'app se richiesto dai servizi. Viene prima cercato FindValue. Se non esiste, viene cercato FindValueSecondary.|
|HttpRequestConnectTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(5)|Dinamico|Specificare l'intervallo di tempo in secondi.  Indica il timeout di connessione per le richieste HTTP inviate dal gateway applicazione HTTP.  |
|RemoveServiceResponseHeaders|string, valore predefinito: L"Date; Server"|statico|Elenco delimitato da punti e virgola/virgole delle intestazioni di risposta che verranno rimosse dalla risposta del servizio prima di inoltrarla al client. Se è impostato su una stringa vuota, tutte le intestazioni restituite dal servizio vengono passate così come sono, vale a dire non vengono sovrascritti i valori Date e Server. |
|ApplicationCertificateValidationPolicy|string, valore predefinito: L"None"|statico| ApplicationCertificateValidationPolicy: None: non convalidare il certificato server. Esito positivo della richiesta. ServiceCertificateThumbprints: fare riferimento alla configurazione ServiceCertificateThumbprints per un elenco delimitato da virgole di identificazioni personali dei certificati remoti che il proxy inverso può considerare attendibili. ServiceCommonNameAndIssuer: fare riferimento alla configurazione ServiceCommonNameAndIssuer per il nome soggetto e l'identificazione personale dell'autorità di certificazione dei certificati remoti che il proxy inverso può considerare attendibili. |
|ServiceCertificateThumbprints|string, valore predefinito: L""|Dinamico| |
|CrlCheckingFlag|uint, valore predefinito: 0x40000000 |Dinamico| Flag per la convalida della catena di certificati di applicazione/servizio. Ad esempio i controlli di CRL 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY L'impostazione 0 disabilita il controllo di CRL L'elenco completo dei valori supportati è documentato per dwFlags di CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|IgnoreCrlOfflineError|bool, valore predefinito: TRUE|Dinamico|Indica se ignorare l'errore di CRL offline per la verifica del certificato di applicazioni/servizi. |
|SecureOnlyMode|bool, valore predefinito: FALSE|Dinamico| SecureOnlyMode: true: il proxy inverso eseguirà l'inoltro solo ai servizi che pubblicano endpoint sicuri. false: il proxy inverso può inoltrare le richieste a endpoint sicuri/non sicuri.  |
|ForwardClientCertificate|bool, valore predefinito: FALSE|Dinamico| |

### <a name="section-name-applicationgatewayhttpservicecommonnameandissuer"></a>Nome della sezione: ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valore predefinito: None|Dinamico|  |

### <a name="section-name-management"></a>Nome della sezione: Management
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| ImageStoreConnectionString |SecureString |statico|Stringa di connessione per la radice di ImageStore. |
| ImageStoreMinimumTransferBPS | Int, valore predefinito: 1024 |Dinamico|La velocità di trasferimento minima tra il cluster e ImageStore. Questo valore viene usato per determinare il timeout durante l'accesso a ImageStore esterno. Modificarlo solo in caso di latenza elevata tra il cluster e ImageStore per dare più tempo al cluster di scaricare da ImageStore esterno. |
|AzureStorageMaxWorkerThreads | Int, valore predefinito: 25 |Dinamico|Il numero massimo di thread di ruoli di lavoro in parallelo. |
|AzureStorageMaxConnections | Int, valore predefinito: 5000 |Dinamico|Il numero massimo di connessioni simultanee all'archiviazione di Azure. |
|AzureStorageOperationTimeout | Tempo in secondi, valore predefinito: 6000 |Dinamico|Specificare l'intervallo di tempo in secondi. Timeout per il completamento dell'operazione xstore. |
|ImageCachingEnabled | Bool, valore predefinito: true |statico|Questa configurazione consente di abilitare o disabilitare la memorizzazione nella cache. |
|DisableChecksumValidation | Bool, valore predefinito: false |statico| Questa configurazione consente di abilitare o disabilitare la convalida di checksum durante il provisioning dell'applicazione. |
|DisableServerSideCopy | Bool, valore predefinito: false |statico|Questa configurazione consente di abilitare o disabilitare la copia sul lato server del pacchetto dell'applicazione in ImageStore durante il provisioning dell'applicazione. |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>Nome della sezione: HealthManager/ClusterHealthPolicy
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| ConsiderWarningAsError |Bool, valore predefinito: false |statico|Criteri di valutazione dell'integrità del cluster: gli avvisi vengano considerati come errori. |
| MaxPercentUnhealthyNodes | Int, valore predefinito: 0 |statico|Criteri di valutazione dell'integrità del cluster: la percentuale massima di nodi non integri consentita per indicare come integro il cluster stesso. |
| MaxPercentUnhealthyApplications | Int, valore predefinito: 0 |statico|Criteri di valutazione dell'integrità del cluster: la percentuale massima di applicazioni non integre consentita per indicare come integro il cluster stesso. |

### <a name="section-name-healthmanagerclusterupgradehealthpolicy"></a>Nome della sezione: HealthManager/ClusterUpgradeHealthPolicy
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, valore predefinito: 10|statico|Criteri di valutazione dell'integrità dell'aggiornamento del cluster: la percentuale massima di nodi non integri differenziali consentita per considerare integro il cluster stesso. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, valore predefinito: 15|statico|Criteri di valutazione dell'integrità dell'aggiornamento del cluster: la percentuale massima di nodi non integri differenziali consentita in un dominio di aggiornamento per considerare integro il cluster stesso. |

### <a name="section-name-faultanalysisservice"></a>Nome della sezione: FaultAnalysisService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, valore predefinito: 0 |statico|NOT_PLATFORM_UNIX_START TargetReplicaSetSize per FaultAnalysisService. |
| MinReplicaSetSize |Int, valore predefinito: 0 |statico|MinReplicaSetSize per FaultAnalysisService. |
| ReplicaRestartWaitDuration |Tempo in secondi, valore predefinito: 60 minuti|statico|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per FaultAnalysisService. |
| QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |statico|Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per FaultAnalysisService. |
| StandByReplicaKeepDuration| Tempo in secondi, valore predefinito: (60*24*7) minuti |statico|Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per FaultAnalysisService. |
| PlacementConstraints | stringa, il valore predefinito è ""|statico| PlacementConstraints per FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Int, valore predefinito: 3600 |statico|Indica la frequenza di pulizia dell'archivio.  Verranno rimosse solo le azioni in stato terminale e completate da un numero di secondi pari almeno a CompletedActionKeepDurationInSeconds. |
| CompletedActionKeepDurationInSeconds | Int, valore predefinito: 604800 |statico| Tempo di conservazione approssimativo delle azioni in stato terminale.  Dipende inoltre StoredActionCleanupIntervalInSeconds, poiché l'operazione di pulizia è eseguita solo in quell'intervallo. 604800 equivale a 7 giorni. |
| StoredChaosEventCleanupIntervalInSeconds | Int, valore predefinito: 3600 |statico|Frequenza con cui l'archivio verrà controllato per eseguire eventualmente la pulizia. Se il numero di eventi è superiore a 30.000, la pulizia verrà avviata. |
|DataLossCheckWaitDurationInSeconds|int, valore predefinito: 25|statico|La quantità totale di tempo, in secondi, per cui il sistema attenderà che si verifichi una perdita di dati.  Questo parametro viene usato internamente quando viene chiamata l'API StartPartitionDataLossAsync(). |
|DataLossCheckPollIntervalInSeconds|int, valore predefinito: 5|statico|Tempo che intercorre tra i controlli eseguiti dal sistema durante l'attesa del verificarsi di una perdita di dati.  Il numero di controlli del numero di perdite di dati per ogni iterazione interna è dato da DataLossCheckWaitDurationInSeconds diviso per questo parametro. |
|ReplicaDropWaitDurationInSeconds|int, valore predefinito: 600|statico|Questo parametro viene usato quando viene chiamata l'API per la perdita di dati.  Controlla il tempo di attesa del sistema per l'eliminazione di una replica dopo una chiamata interna di rimozione della replica. |

### <a name="section-name-filestoreservice"></a>Nome della sezione: FileStoreService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| NamingOperationTimeout |Tempo in secondi, il valore predefinito è 60 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout per eseguire un'operazione di denominazione. |
| QueryOperationTimeout | Tempo in secondi, il valore predefinito è 60 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout per eseguire un'operazione di query. |
| MaxCopyOperationThreads | Uint, valore predefinito: 0 |Dinamico| Il numero massimo di file paralleli che il Replicator secondario può copiare da quello primario. '0' == numero di memorie centrali. |
| MaxFileOperationThreads | Uint, valore predefinito: 100 |statico| Numero massimo di thread paralleli che possono eseguire FileOperations (copia o spostamento) nel Replicator primario. '0' == numero di memorie centrali. |
| MaxStoreOperations | Uint, valore predefinito: 4096 |statico|Numero massimo di transazioni di archivi paralleli eseguibili nel Replicator primario. '0' == numero di memorie centrali. |
| MaxRequestProcessingThreads | Uint, valore predefinito: 200 |statico|Numero massimo di thread paralleli che possono elaborare richieste nel Replicator primario. '0' == numero di memorie centrali. |
| MaxSecondaryFileCopyFailureThreshold | Uint, valore predefinito: 25|Dinamico|Numero massimo di tentativi di copia di file nel Replicator secondario prima di interrompere l'operazione. |
| AnonymousAccessEnabled | Bool, valore predefinito: true |statico|Abilita o disabilita l'accesso anonimo alle condivisioni di FileStoreService. |
| PrimaryAccountType | stringa, il valore predefinito è "" |statico|L'AccountType primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| PrimaryAccountUserName | stringa, il valore predefinito è "" |statico|Il nome utente dell'account primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| PrimaryAccountUserPassword | SecureString, valore predefinito: vuoto |statico|La password dell'account primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| PrimaryAccountNTLMPasswordSecret | SecureString, valore predefinito: vuoto |statico| Il segreto della password usato come valore di inizializzazione per generare la stessa password quando si usa l'autenticazione NTLM. |
| PrimaryAccountNTLMX509StoreLocation | stringa, il valore predefinito è "LocalMachine"|statico| Il percorso dell'archivio del certificato X509 usato per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| PrimaryAccountNTLMX509StoreName | stringa, il valore predefinito è "MY"|statico| Il nome dell'archivio del certificato X509 usato per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| PrimaryAccountNTLMX509Thumbprint | stringa, il valore predefinito è ""|statico|L'identificazione personale dell'archivio del certificato X509 usato per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| SecondaryAccountType | stringa, il valore predefinito è ""|statico| L'AccountType secondario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| SecondaryAccountUserName | stringa, il valore predefinito è ""| statico|Il nome utente dell'account secondario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| SecondaryAccountUserPassword | SecureString, valore predefinito: vuoto |statico|La password dell'account secondario dell'entità per l'ACL delle condivisioni di FileStoreService.  |
| SecondaryAccountNTLMPasswordSecret | SecureString, valore predefinito: vuoto |statico| Il segreto della password usato come valore di inizializzazione per generare la stessa password quando si usa l'autenticazione NTLM. |
| SecondaryAccountNTLMX509StoreLocation | stringa, il valore predefinito è "LocalMachine" |statico|Il percorso dell'archivio del certificato X509 usato per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| SecondaryAccountNTLMX509StoreName | stringa, il valore predefinito è "MY" |statico|Il nome dell'archivio del certificato X509 usato per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| SecondaryAccountNTLMX509Thumbprint | stringa, il valore predefinito è ""| statico|L'identificazione personale dell'archivio del certificato X509 usato per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
|CommonNameNtlmPasswordSecret|SecureString, valore predefinito: Common::SecureString(L"")| statico|Segreto della password usato come valore di inizializzazione per generare la stessa password quando si usa l'autenticazione NTLM. |
|CommonName1Ntlmx509StoreLocation|string, valore predefinito: L"LocalMachine"|statico|Percorso dell'archivio del certificato X509 usato per generare HMAC in CommonName1NtlmPasswordSecret quando si usa l'autenticazione NTLM. |
|CommonName1Ntlmx509StoreName|string, valore predefinito: L"MY"| statico|Nome dell'archivio del certificato X509 usato per generare HMAC in CommonName1NtlmPasswordSecret quando si usa l'autenticazione NTLM. |
|CommonName1Ntlmx509CommonName|string, valore predefinito: L""|statico| Nome comune dell'archivio del certificato X509 usato per generare HMAC in CommonName1NtlmPasswordSecret quando si usa l'autenticazione NTLM. |
|CommonName2Ntlmx509StoreLocation|string, valore predefinito: L"LocalMachine"| statico|Percorso dell'archivio del certificato X509 usato per generare HMAC in CommonName2NtlmPasswordSecret quando si usa l'autenticazione NTLM. |
|CommonName2Ntlmx509StoreName|string, valore predefinito: L"MY"|statico| Nome dell'archivio del certificato X509 usato per generare HMAC in CommonName2NtlmPasswordSecret quando si usa l'autenticazione NTLM. |
|CommonName2Ntlmx509CommonName|string, valore predefinito: L""|statico|Nome comune del certificato X509 usato per generare HMAC in CommonName2NtlmPasswordSecret quando si usa l'autenticazione NTLM. |

### <a name="section-name-imagestoreservice"></a>Nome della sezione: ImageStoreService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| Enabled |Bool, valore predefinito: false |statico|Flag Enabled per ImageStoreService. Valore predefinito: false |
| TargetReplicaSetSize | Int, valore predefinito: 7 |statico|TargetReplicaSetSize per ImageStoreService. |
| MinReplicaSetSize | Int, valore predefinito: 3 |statico|MinReplicaSetSize per ImageStoreService. |
| ReplicaRestartWaitDuration | Tempo in secondi, valore predefinito: 60,0 * 30 |statico|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per ImageStoreService. |
| QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |statico| Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per ImageStoreService. |
| StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: è 3600.0 * 2 |statico| Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per ImageStoreService. |
| PlacementConstraints | stringa, il valore predefinito è "" |statico| PlacementConstraints per ImageStoreService. |

### <a name="section-name-imagestoreclient"></a>Nome della sezione: ImageStoreClient
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| ClientUploadTimeout |Tempo in secondi, valore predefinito: 1800 |Dinamico|Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di caricamento principale al servizio di archivio immagini. |
| ClientCopyTimeout | Tempo in secondi, valore predefinito: 1800 |Dinamico| Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di copia principale al servizio di archivio immagini. |
|ClientDownloadTimeout | Tempo in secondi, valore predefinito: 1800 |Dinamico| Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di download principale al servizio di archivio immagini. |
|ClientListTimeout | Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di elenco principale al servizio di archivio immagini. |
|ClientDefaultTimeout | Tempo in secondi, valore predefinito: 180 |Dinamico| Specificare l'intervallo di tempo in secondi. Valore di timeout per tutte le richieste non di caricamento o download (ad esempio richieste di esistenza o di eliminazione) al servizio di archivio immagini. |

### <a name="section-name-tokenvalidationservice"></a>Nome della sezione: TokenValidationService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| Providers |stringa, il valore predefinito è "DSTS" |statico|Elenco separato da virgole dei provider di convalida dei token per l'abilitazione. I provider validi sono: DSTS, AAD. Attualmente è possibile abilitare un singolo provider alla volta. |

### <a name="section-name-upgradeorchestrationservice"></a>Nome della sezione: UpgradeOrchestrationService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, valore predefinito: 0 |statico |TargetReplicaSetSize per UpgradeOrchestrationService. |
| MinReplicaSetSize |Int, valore predefinito: 0 |statico |MinReplicaSetSize per UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Tempo in secondi, valore predefinito: 60 minuti|statico| Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |statico| Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: 60*24*7 minuti |statico| Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per UpgradeOrchestrationService. |
| PlacementConstraints | stringa, il valore predefinito è "" |statico| PlacementConstraints per UpgradeOrchestrationService. |
| AutoupgradeEnabled | Bool, valore predefinito: true |statico| Polling e aggiornamenti automatici in base a un file di stato obiettivo. |
| UpgradeApprovalRequired | Bool, valore predefinito: false | statico|Impostazione per richiedere l'approvazione di un amministratore prima di procedere con l'aggiornamento del codice. |

### <a name="section-name-upgradeservice"></a>Nome della sezione: UpgradeService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| PlacementConstraints |stringa, il valore predefinito è "" |Non consentito|PlacementConstraints per UpgradeService. |
| TargetReplicaSetSize | Int, valore predefinito: 3 |Non consentito| TargetReplicaSetSize per UpgradeService. |
| MinReplicaSetSize | Int, valore predefinito: 2 |Non consentito| MinReplicaSetSize per UpgradeService. |
| CoordinatorType | stringa, il valore predefinito è "WUTest"|Non consentito|CoordinatorType per UpgradeService. |
| BaseUrl | stringa, il valore predefinito è "" |statico|BaseUrl per UpgradeService. |
| ClusterId | stringa, il valore predefinito è "" |statico|ClusterId per UpgradeService. |
| X509StoreName | stringa, il valore predefinito è "My"|Dinamico|X509StoreName per UpgradeService. |
| X509StoreLocation | stringa, il valore predefinito è "" |Dinamico| X509StoreLocation per UpgradeService. |
| X509FindType | stringa, il valore predefinito è ""|Dinamico| X509FindType per UpgradeService. |
| X509FindValue | stringa, il valore predefinito è "" |Dinamico| X509FindValue per UpgradeService. |
| X509SecondaryFindValue | stringa, il valore predefinito è "" |Dinamico| X509SecondaryFindValue per UpgradeService. |
| OnlyBaseUpgrade | Bool, valore predefinito: false |Dinamico|OnlyBaseUpgrade per UpgradeService. |
| TestCabFolder | stringa, il valore predefinito è "" |statico| TestCabFolder per UpgradeService. |

### <a name="section-name-security"></a>Nome della sezione: Security
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ClusterCredentialType|string, valore predefinito: L"None"|Non consentito|Indica il tipo di credenziali di sicurezza da usare per proteggere il cluster. I valori validi sono "None/X509/Windows" |
|ServerAuthCredentialType|string, valore predefinito: L"None"|statico|Indica il tipo di credenziali di sicurezza da usare per proteggere la comunicazione tra FabricClient e il cluster. I valori validi sono "None/X509/Windows" |
|ClientRoleEnabled|bool, valore predefinito: FALSE|statico|Indica se il ruolo di client è abilitato. Se impostato su true, ai client vengono assegnati i ruoli in base alle rispettive identità. Nella versione 2, l'abilitazione di questo parametro indica che un client non incluso in AdminClientCommonNames/AdminClientIdentities può solo eseguire operazioni di sola lettura. |
|ClusterCertThumbprints|string, valore predefinito: L""|Dinamico|Identificazioni personali dei certificati per cui è consentita l'aggiunta al cluster. Elenco di nomi separati da virgole. |
|ServerCertThumbprints|string, valore predefinito: L""|Dinamico|Identificazioni personali dei certificati del server usati dal cluster per le comunicazioni con i client. I client usano questi valori per autenticare il cluster. Elenco di nomi delimitati da virgole. |
|ClientCertThumbprints|string, valore predefinito: L""|Dinamico|Identificazioni personali dei certificati usati dai client per comunicare con il cluster. Il cluster usa questi valori per autorizzare la connessione in ingresso. Elenco di nomi delimitati da virgole. |
|AdminClientCertThumbprints|string, valore predefinito: L""|Dinamico|Identificazioni personali dei certificati usati dai client nel ruolo di amministratore. Elenco di nomi delimitati da virgole. |
|CrlCheckingFlag|uint, valore predefinito: 0x40000000|Dinamico|Flag di convalida della catena di certificati predefinito. Può essere sovrascritto dal flag specifico del componente. Ad esempio Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY L'impostazione 0 disabilita il controllo di CRL L'elenco completo dei valori supportati è documentato per dwFlags di CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|IgnoreCrlOfflineError|bool, valore predefinito: FALSE|Dinamico|Se ignorare l'errore di CRL offline quando il lato server verifica i certificati client in ingresso. |
|IgnoreSvrCrlOfflineError|bool, valore predefinito: TRUE|Dinamico|Se ignorare l'errore di CRL offline quando il lato client verifica i certificati server in ingresso. Il valore predefinito è true. Gli attacchi basati su certificati server revocati richiedono di compromettere il sistema DNS, ovvero sono più complessi rispetto a quelli basati sui certificati client revocati. |
|CrlDisablePeriod|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(15)|Dinamico|Specificare l'intervallo di tempo in secondi. Per quanto tempo viene disabilitato il controllo di CRL dopo il rilevamento di un errore di offline. Se l'errore di CRL offline può essere ignorato. |
|CrlOfflineHealthReportTtl|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(1440)|Dinamico|Specificare l'intervallo di tempo in secondi. |
|CertificateHealthReportingInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(3600 * 8)|statico|Specificare l'intervallo di tempo in secondi. Specificare l'intervallo per il report di integrità del certificato. Il valore predefinito è 8 ore. L'impostazione del valore 0 disabilita i report sull'integrità dei certificati. |
|CertificateExpirySafetyMargin|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(43200)|statico|Specificare l'intervallo di tempo in secondi. Margine di sicurezza per la scadenza del certificato. Lo stato del report di integrità del certificato passa da OK ad avviso quando la scadenza è inferiore a questo margine. Il valore predefinito è 30 giorni. |
|ClientClaimAuthEnabled|bool, valore predefinito: FALSE|statico|Indica se l'autenticazione basata sulle attestazioni è abilitata nei client. L'impostazione di questo parametro su true comporta l'impostazione implicita di ClientRoleEnabled. |
|ClientClaims|string, valore predefinito: L""|Dinamico|Tutte le attestazioni possibili previste dai client per la connessione al gateway. Questo è un elenco di 'OR': ClaimsEntry || ClaimsEntry || ClaimsEntry ... ogni ClaimsEntry è un elenco "AND": ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|AdminClientClaims|string, valore predefinito: L""|Dinamico|Tutte le attestazioni possibili previste dai client di amministrazione. Stesso formato di ClientClaims. Questo elenco viene aggiunto internamente a ClientClaims, pertanto non occorre aggiungere le stesse voci a ClientClaims. |
|ClusterSpn|string, valore predefinito: L""|Non consentito|Nome dell'entità servizio del cluster quando l'infrastruttura viene eseguita come singolo utente di dominio (gMSA/account utente di dominio). Nome dell'entità servizio dei listener di lease e dei listener in fabric.exe: listener di federazione; listener di replica interna; listener del servizio di runtime e listener del gateway di denominazione. Questo parametro deve essere lasciato vuoto quando l'infrastruttura viene eseguita con account computer, connettendo in questo caso il nome dell'entità servizio del listener di calcolo dall'indirizzo di trasporto del listener. |
|ClusterIdentities|string, valore predefinito: L""|Dinamico|Identità di Windows dei nodi del cluster, usata per l'autorizzazione delle appartenenze al cluster. Si tratta di un elenco delimitato da virgole, in cui ogni voce è un nome di account di dominio o un nome di gruppo. |
|ClientIdentities|string, valore predefinito: L""|Dinamico|Identità di Windows di FabricClient. Il gateway di denominazione usa questi valori per autorizzare le connessioni in ingresso. Si tratta di un elenco delimitato da virgole, in cui ogni voce è un nome di account di dominio o un nome di gruppo. Per motivi di praticità, l'account che esegue fabric.exe viene autorizzato automaticamente, così come i gruppi ServiceFabricAllowedUsers e ServiceFabricAdministrators. |
|AdminClientIdentities|string, valore predefinito: L""|Dinamico|Identità di Windows dei client dell'infrastruttura nel ruolo di amministratore. Questo parametro viene usato per autorizzare operazioni sull'infrastruttura con privilegi. Si tratta di un elenco delimitato da virgole, in cui ogni voce è un nome di account di dominio o un nome di gruppo. Per motivi di praticità, all'account che esegue fabric.exe viene assegnato automaticamente il ruolo di amministratore, così come al gruppo ServiceFabricAdministrators. |
|AADTenantId|string, valore predefinito: L""|statico|ID del tenant (GUID) |
|AADClusterApplication|string, valore predefinito: L""|statico|Nome dell'applicazione API Web o ID che rappresenta il cluster. |
|AADClientApplication|string, valore predefinito: L""|statico|Nome dell'applicazione client nativa o ID che rappresenta i client dell'infrastruttura. |
|X509Folder|string, valore predefinito: /var/lib/waagent|statico|Cartella in cui si trovano i certificati e le chiavi private X509. |
|FabricHostSpn| string, valore predefinito: L"" |statico| Nome dell'entità servizio di FabricHost, quando l'infrastruttura viene eseguita come singolo utente di dominio (gMSA/account utente di dominio) e FabricHost viene eseguito con l'account computer. Nome dell'entità servizio del listener IPC per FabricHost, che deve essere lasciato vuoto per impostazione predefinita perché FabricHost viene eseguito con l'account computer. |
|DisableFirewallRuleForPublicProfile| bool, valore predefinito: TRUE | statico|Indica se la regola del firewall non deve essere abilitata per un profilo pubblico. |
|DisableFirewallRuleForPrivateProfile| bool, valore predefinito: TRUE |statico| Indica se la regola del firewall non deve essere abilitata per un profilo privato. | 
|DisableFirewallRuleForDomainProfile| bool, valore predefinito: TRUE |statico| Indica se la regola del firewall non deve essere abilitata per un profilo di dominio. |
|SettingsX509StoreName| string, valore predefinito: L"MY"| Dinamico|Archivio certificati X509 usato dall'infrastruttura per la protezione della configurazione. |

### <a name="section-name-securityadminclientx509names"></a>Nome della sezione: Security/AdminClientX509Names
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valore predefinito: None|Dinamico| |

### <a name="section-name-securityclientx509names"></a>Nome della sezione: Security/ClientX509Names
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, valore predefinito: None|Dinamico| |

### <a name="section-name-securityclusterx509names"></a>Nome della sezione: Security/ClusterX509Names
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, valore predefinito: None|Dinamico| |

### <a name="section-name-securityserverx509names"></a>Nome della sezione: Security/ServerX509Names
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, valore predefinito: None|Dinamico| |

### <a name="section-name-securityclientaccess"></a>Nome della sezione: Security/ClientAccess
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| CreateName |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per creare un URI di denominazione. |
| DeleteName |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per eliminare un URI di denominazione. |
| PropertyWriteBatch |stringa, il valore predefinito è "Admin" |Dinamico|Configurazioni di sicurezza per le operazioni di scrittura di proprietà di denominazione. |
| CreateService |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per creare un servizio. |
| CreateServiceFromTemplate |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per creare un servizio da un modello. |
| UpdateService |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per gli aggiornamenti dei servizi. |
| DeleteService  |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per eliminare un servizio. |
| ProvisionApplicationType |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il provisioning dei tipi di applicazione. |
| CreateApplication |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per creare un'applicazione. |
| DeleteApplication |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per eliminare un'applicazione. |
| UpgradeApplication |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per avviare o interrompere gli aggiornamenti dell'applicazione. |
| RollbackApplicationUpgrade |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per eseguire il rollback degli aggiornamenti dell'applicazione. |
| UnprovisionApplicationType |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per annullare il provisioning dei tipi di applicazione. |
| MoveNextUpgradeDomain |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riprendere gli aggiornamenti dell'applicazione con un dominio di aggiornamento esplicito. |
| ReportUpgradeHealth |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riprendere gli aggiornamenti dell'applicazione con lo stato di avanzamento corrente. |
| ReportHealth |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per i report di integrità. |
| ProvisionFabric |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il provisioning del manifesto del cluster e/o del file con estensione msi. |
| UpgradeFabric |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per avviare gli aggiornamenti del cluster. |
| RollbackFabricUpgrade |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il rollback degli aggiornamenti del cluster. |
| UnprovisionFabric |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per l'annullamento del provisioning del manifesto del cluster e/o del file con estensione msi. |
| MoveNextFabricUpgradeDomain |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riprendere gli aggiornamenti del cluster con un dominio di aggiornamento esplicito. |
| ReportFabricUpgradeHealth |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riprendere gli aggiornamenti del cluster con lo stato di avanzamento corrente. |
| StartInfrastructureTask |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per avviare le attività di infrastruttura. |
| FinishInfrastructureTask |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per terminare le attività di infrastruttura. |
| ActivateNode |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per attivare un nodo. |
| DeactivateNode |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per disattivare un nodo. |
| DeactivateNodesBatch |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per disattivare più nodi. |
| RemoveNodeDeactivations |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per annullare la disattivazione di più nodi. |
| GetNodeDeactivationStatus |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per controllare lo stato di disattivazione. |
| NodeStateRemoved |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per i report sulla rimozione dello stato di un nodo. |
| RecoverPartition |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per ripristinare una partizione. |
| RecoverPartitions |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per ripristinare più partizioni. |
| RecoverServicePartitions |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per ripristinare partizioni di servizio. |
| RecoverSystemPartitions |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per ripristinare partizioni di servizio di sistema. |
| ReportFault |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per i report di errori. |
| InvokeInfrastructureCommand |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di protezione per i comandi di gestione delle attività di infrastruttura. |
| FileContent |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il trasferimento del file del client dell'archivio immagini (esterno al cluster). |
| FileDownload |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per l'avvio del download del file del client dell'archivio immagini (esterno al cluster). |
| InternalList |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per l'operazione di elenco del file del client dell'archivio immagini (interno). |
| Elimina |stringa, il valore predefinito è "Admin" |Dinamico| Configurazioni di sicurezza per l'operazione di eliminazione del client dell'archivio immagini. |
| Carica |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per l'operazione di copia del client dell'archivio immagini. |
| GetStagingLocation |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il recupero del percorso di gestione temporanea del client dell'archivio immagini. |
| GetStoreLocation |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il recupero del percorso dell'archivio del client dell'archivio immagini. |
| NodeControl |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per avviare, arrestare e riavviare i nodi. |
| CodePackageControl |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riavviare i pacchetti di codice. |
| UnreliableTransportControl |stringa, il valore predefinito è "Admin" |Dinamico| Trasporto non affidabile per l'aggiunta e la rimozione di comportamenti. |
| MoveReplicaControl |stringa, il valore predefinito è "Admin" | Dinamico|Spostamento di repliche. |
| PredeployPackageToNode |stringa, il valore predefinito è "Admin" |Dinamico| API per la pre-distribuzione. |
| StartPartitionDataLoss |stringa, il valore predefinito è "Admin" |Dinamico| Provoca la perdita di dati in una partizione. |
| StartPartitionQuorumLoss |stringa, il valore predefinito è "Admin" |Dinamico| Provoca la perdita di quorum in una partizione. |
| StartPartitionRestart |stringa, il valore predefinito è "Admin" |Dinamico| Riavvia contemporaneamente alcune o tutte le repliche di una partizione. |
| CancelTestCommand |stringa, il valore predefinito è "Admin" |Dinamico| Annulla un TestCommand specifico, se è in corso. |
| StartChaos |stringa, il valore predefinito è "Admin" |Dinamico| Avvia Chaos, se non è già avviato. |
| StopChaos |stringa, il valore predefinito è "Admin" |Dinamico| Arresta Chaos, se è stato avviato. |
| StartNodeTransition |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per avviare la transizione di un nodo. |
| StartClusterConfigurationUpgrade |stringa, il valore predefinito è "Admin" |Dinamico| Provoca StartClusterConfigurationUpgrade in una partizione. |
| GetUpgradesPendingApproval |stringa, il valore predefinito è "Admin" |Dinamico| Provoca GetUpgradesPendingApproval in una partizione. |
| StartApprovedUpgrades |stringa, il valore predefinito è "Admin" |Dinamico| Provoca StartApprovedUpgrades in una partizione. |
| Ping |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per i ping di client. |
| Query |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per le query. |
| NameExists |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per i controlli sulla presenza di URI di denominazione. |
| EnumerateSubnames |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per l'enumerazione degli URI di denominazione. |
| EnumerateProperties |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per l'enumerazione delle proprietà di denominazione. |
| PropertyReadBatch |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per le operazioni di lettura delle proprietà di denominazione. |
| GetServiceDescription |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per le notifiche del servizio di long polling e lettura delle descrizioni dei servizi. |
| ResolveService |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per la risoluzione di servizi in base a reclami. |
| ResolveNameOwner |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per la risoluzione dei proprietari degli URI di denominazione. |
| ResolvePartition |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per la risoluzione dei servizi di sistema. |
| ServiceNotifications |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per le notifiche di servizi basati su eventi. |
| PrefixResolveService |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per la risoluzione dei prefissi di basati sui reclami. |
| GetUpgradeStatus |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per il polling dello stato degli aggiornamenti dell'applicazione. |
| GetFabricUpgradeStatus |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per il polling dello stato degli aggiornamenti del cluster. |
| InvokeInfrastructureQuery |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per eseguire query sulle attività di infrastruttura. |
| Elenco |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per l'operazione di elenco del file del client dell'archivio immagini. |
| ResetPartitionLoad |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per reimpostare il carico per failoverUnit. |
| ToggleVerboseServicePlacementHealthReporting | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per attivare o disattivare ServicePlacement HealthReporting dettagliati. |
| GetPartitionDataLossProgress | stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Recupera lo stato di avanzamento per una chiamata API di richiamo di perdita dei dati. |
| GetPartitionQuorumLossProgress | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Recupera lo stato di avanzamento per una chiamata API di richiamo di perdita del quorum. |
| GetPartitionRestartProgress | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Recupera lo stato di avanzamento di una chiamata API di riavvio di una partizione. |
| GetChaosReport | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Recupera lo stato di Chaos all'interno di un intervallo di tempo specificato. |
| GetNodeTransitionProgress | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per recuperare lo stato di avanzamento di un comando di transizione nodo. |
| GetClusterConfigurationUpgradeStatus | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Provoca GetClusterConfigurationUpgradeStatus in una partizione. |
| GetClusterConfiguration | stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Provoca GetClusterConfiguration in una partizione. |
|CreateComposeDeployment|string, valore predefinito: L"Admin"| Dinamico|Crea una distribuzione Compose descritta dai file Compose. |
|DeleteComposeDeployment|string, valore predefinito: L"Admin"| Dinamico|Elimina la distribuzione Compose. |
|UpgradeComposeDeployment|string, valore predefinito: L"Admin"| Dinamico|Aggiorna la distribuzione Compose. |
|ResolveSystemService|string, valore predefinito: L"Admin\|\|User"|Dinamico| Configurazione di sicurezza per la risoluzione dei servizi di sistema. |
|GetUpgradeOrchestrationServiceState|string, valore predefinito: L"Admin"| Dinamico|Attiva GetUpgradeOrchestrationServiceState su una partizione. |
|SetUpgradeOrchestrationServiceState|string, valore predefinito: L"Admin"| Dinamico|Attiva SetUpgradeOrchestrationServiceState su una partizione. |

### <a name="section-name-reconfigurationagent"></a>Nome della sezione: ReconfigurationAgent
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 |Dinamico|Specificare l'intervallo di tempo in secondi. Durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura durante l'aggiornamento dell'applicazione.|
| ServiceApiHealthDuration | Tempo in secondi, valore predefinito: 30 minuti |Dinamico| Specificare l'intervallo di tempo in secondi. ServiceApiHealthDuration definisce l'attesa massima per l'esecuzione di un'API prima che questa venga indicata come non integra. |
| ServiceReconfigurationApiHealthDuration | Tempo in secondi, il valore predefinito è 30 |Dinamico| Specificare l'intervallo di tempo in secondi. ServiceReconfigurationApiHealthDuration definisce l'attesa massima per l'esecuzione di un'API prima che questa venga indicata come non integra. Si applica alle chiamate API con impatto sulla disponibilità.|
| PeriodicApiSlowTraceInterval | Tempo in secondi, valore predefinito: 5 minuti |Dinamico| Specificare l'intervallo di tempo in secondi. PeriodicApiSlowTraceInterval definisce l'intervallo in cui le chiamate API lente verranno ritracciate dal monitoraggio API. |
| NodeDeactivationMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 |Dinamico|Specificare l'intervallo di tempo in secondi. Durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura durante la disattivazione del nodo. |
| FabricUpgradeMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 |Dinamico| Specificare l'intervallo di tempo in secondi. Durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura durante l'aggiornamento dell'infrastruttura. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico|Specificare l'intervallo di tempo in secondi. La durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura. Se questo valore è impostato su 0, le repliche non verranno avviate alla chiusura.|
|ReplicaChangeRoleFailureRestartThreshold|int, valore predefinito: 10|Dinamico| Integer. Specificare il numero di errori API durante la promozione della replica primaria dopo il quale verrà applicata l'azione di mitigazione automatica (riavvio della replica). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, valore predefinito: 2147483647|Dinamico| Integer. Specificare il numero di errori API durante la promozione della replica primaria dopo il quale verrà generato il rapporto di integrità di avviso.|

### <a name="section-name-placementandloadbalancing"></a>Nome della sezione: PlacementAndLoadBalancing
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| TraceCRMReasons |Bool, valore predefinito: true |Dinamico|Specifica se tracciare i motivi dei movimenti indicati da CRM al canale degli eventi operativi. |
| ValidatePlacementConstraint | Bool, valore predefinito: true |Dinamico| Specifica se l'espressione PlacementConstraint per un servizio viene convalidata quando viene aggiornato il parametro ServiceDescription di un servizio. |
| PlacementConstraintValidationCacheSize | Int, valore predefinito: 10000 |Dinamico| Limita le dimensioni della tabella usata per la convalida e la memorizzazione nella cache rapide delle espressioni dei vincoli di posizionamento. |
|VerboseHealthReportLimit | Int, valore predefinito: 20 | Dinamico|Definisce il numero di posizionamenti non riusciti di una replica prima di inviare un avviso di integrità a riguardo (se è abilitata la creazione di report di integrità dettagliati). |
|ConstraintViolationHealthReportLimit | Int, valore predefinito: 50 |Dinamico| Definisce il numero di violazioni di vincoli di tempo non risolte da parte di una replica prima di eseguire la diagnostica e di creare report di integrità a riguardo. |
|DetailedConstraintViolationHealthReportLimit | Int, valore predefinito: 200 |Dinamico| Definisce il numero di violazioni di vincoli di tempo non risolte da parte di una replica prima di eseguire la diagnostica e di creare report di integrità dettagliati a riguardo. |
|DetailedVerboseHealthReportLimit | Int, valore predefinito: 200 | Dinamico|Definisce il numero di posizionamenti errati continui di una replica prima di creare report di integrità dettagliati. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, valore predefinito: 20 | Dinamico|Definisce il numero di volte consecutive in cui i movimenti inviati da ResourceBalancer devono essere eliminati prima di eseguire la diagnostica e creare avvisi di integrità. Valore negativo: non vengono emessi avvisi. |
|DetailedNodeListLimit | Int, valore predefinito: 15 |Dinamico| Definisce il numero di nodi per vincolo da includere prima del troncamento nei report sulle repliche non spostate. |
|DetailedPartitionListLimit | Int, valore predefinito: 15 |Dinamico| Definisce il numero di partizioni per ciascuna voce diagnostica di un vincolo da includere prima del troncamento della diagnostica. |
|DetailedDiagnosticsInfoListLimit | Int, valore predefinito: 15 |Dinamico| Definisce il numero di voci di diagnostica di un vincolo (con informazioni dettagliate) da includere prima del troncamento della diagnostica.|
|PLBRefreshGap | Tempo in secondi, valore predefinito: 1 |Dinamico| Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che PLB aggiorni lo stato. |
|MinPlacementInterval | Tempo in secondi, valore predefinito: 1 |Dinamico| Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di posizionamento consecutivi. |
|MinConstraintCheckInterval | Tempo in secondi, valore predefinito: 1 |Dinamico| Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di controllo dei vincoli consecutivi. |
|MinLoadBalancingInterval | Tempo in secondi, valore predefinito: 5 |Dinamico| Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di bilanciamento consecutivi. |
|BalancingDelayAfterNodeDown | Tempo in secondi, valore predefinito: 120 |Dinamico|Specificare l'intervallo di tempo in secondi. Non avviare attività di bilanciamento in questo periodo se un nodo va offline. |
|BalancingDelayAfterNewNode | Tempo in secondi, valore predefinito: 120 |Dinamico|Specificare l'intervallo di tempo in secondi. Non avviare attività di bilanciamento in questo periodo dopo aver aggiunto un nuovo nodo. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo in secondi, valore predefinito: 120 |Dinamico| Specificare l'intervallo di tempo in secondi. Non risolvere violazioni di vincoli FaultDomain e UpgradeDomain in questo periodo dopo che un nodo va offline. |
|ConstraintFixPartialDelayAfterNewNode | Tempo in secondi, valore predefinito: 120 |Dinamico| Specificare l'intervallo di tempo in secondi. Non risolvere violazioni di vincoli FaultDomain e UpgradeDomain in questo periodo dopo aver aggiunto un nuovo nodo. |
|GlobalMovementThrottleThreshold | Uint, valore predefinito: 1000 |Dinamico| Numero massimo di movimenti consentiti nella fase di bilanciamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Uint, valore predefinito: 0 |Dinamico| Numero massimo di movimenti consentiti nella fase di posizionamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti.|
|GlobalMovementThrottleThresholdForBalancing | Uint, valore predefinito: 0 | Dinamico|Numero massimo di movimenti consentiti nella fase di bilanciamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti. |
|GlobalMovementThrottleCountingInterval | Tempo in secondi, valore predefinito: 600 |statico| Specificare l'intervallo di tempo in secondi. Indicare la lunghezza dell'intervallo precedente per cui si desidera tenere traccia dei movimenti di replica di dominio. Usato insieme a GlobalMovementThrottleThreshold. È possibile impostarlo su 0 per ignorare del tutto la limitazione globale. |
|MovementPerPartitionThrottleThreshold | Uint, valore predefinito: 50 |Dinamico| Non verranno eseguiti spostamenti correlati al bilanciamento per una partizione se il numero di tali spostamenti per le repliche della partizione ha raggiunto o superato MovementPerFailoverUnitThrottleThreshold nell'intervallo precedente indicato in MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Tempo in secondi, valore predefinito: 600 |statico| Specificare l'intervallo di tempo in secondi. Indicare la lunghezza dell'intervallo precedente per cui si desidera tenere traccia dei movimenti di replica per ciascuna partizione. Usato insieme a MovementPerPartitionThrottleThreshold. |
|PlacementSearchTimeout | Tempo in secondi, valore predefinito: 0.5 |Dinamico| Specificare l'intervallo di tempo in secondi. Tempo massimo di ricerca durante il posizionamento dei servizi prima di restituire un risultato. |
|UseMoveCostReports | Bool, valore predefinito: false | Dinamico|Indica al servizio di bilanciamento del carico di ignorare l'elemento di costo della funzione di assegnazione dei punteggio. Comporterà potenzialmente un numero elevato di spostamenti per un posizionamento più bilanciato. |
|PreventTransientOvercommit | Bool, valore predefinito: false | Dinamico|Determina se PLB deve conteggiare immediatamente le risorse che saranno liberate dagli spostamenti avviati. Per impostazione predefinita, PLB può avviare gli spostamenti in entrata e in uscita nello stesso nodo che può creare un overcommit temporaneo. Impostando il parametro su true sarà possibile prevenire overcommit simili e disabilitare le deframmentazioni su richiesta, note anche come placementWithMove. |
|InBuildThrottlingEnabled | Bool, valore predefinito: false |Dinamico| Determina se la limitazione in build è abilitata. |
|InBuildThrottlingAssociatedMetric | stringa, il valore predefinito è "" |statico| Il nome della metrica associato a questa limitazione. |
|InBuildThrottlingGlobalMaxValue | Int, valore predefinito: 0 |Dinamico|Il numero massimo di repliche in build consentite a livello globale. |
|SwapPrimaryThrottlingEnabled | Bool, valore predefinito: false|Dinamico| Determina se la limitazione swap-primary è abilitata. |
|SwapPrimaryThrottlingAssociatedMetric | stringa, il valore predefinito è ""|statico| Il nome della metrica associato a questa limitazione. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, valore predefinito: 0 |Dinamico| Il numero massimo di repliche swap-primary consentite a livello globale. |
|PlacementConstraintPriority | Int, valore predefinito: 0 | Dinamico|Determina la priorità del vincolo di posizionamento: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|PreferredLocationConstraintPriority | Int, valore predefinito: 2| Dinamico|Determina la priorità del vincolo di posizionamento preferito: 0 indica priorità elevata, 1 indica priorità minore; 2 indica ottimizzazione e un numero negativo indica "ignorare" |
|CapacityConstraintPriority | Int, valore predefinito: 0 | Dinamico|Determina la priorità del vincolo di capacità: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|AffinityConstraintPriority | Int, valore predefinito: 0 | Dinamico|Determina la priorità del vincolo di affinità: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|FaultDomainConstraintPriority | Int, valore predefinito: 0 |Dinamico| Determina la priorità del vincolo di dominio di errore: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|UpgradeDomainConstraintPriority | Int, valore predefinito: 1| Dinamico|Determina la priorità del vincolo di dominio di aggiornamento: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|ScaleoutCountConstraintPriority | Int, valore predefinito: 0 |Dinamico| Determina la priorità del vincolo del conteggio di scalabilità orizzontale: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|ApplicationCapacityConstraintPriority | Int, valore predefinito: 0 | Dinamico|Determina la priorità del vincolo di capacità: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|MoveParentToFixAffinityViolation | Bool, valore predefinito: false |Dinamico| Impostazione che determina se le repliche padre possono essere spostate per correggere i vincoli di affinità.|
|MoveExistingReplicaForPlacement | Bool, valore predefinito: true |Dinamico|Impostazione che determina se spostare le repliche esistenti durante il posizionamento. |
|UseSeparateSecondaryLoad | Bool, valore predefinito: true | Dinamico|Impostazione che determina se usare un carico secondario differente. |
|PlaceChildWithoutParent | Bool, valore predefinito: true | Dinamico|Impostazione che determina se è possibile posizionare una replica di servizio figlia in caso non sia presente una replica padre. |
|PartiallyPlaceServices | Bool, valore predefinito: true |Dinamico| Determina se tutte le repliche servizio nel cluster verranno posizionate in modo "tutto o niente" in caso di nodi appropriati limitati.|
|InterruptBalancingForAllFailoverUnitUpdates | Bool, valore predefinito: false | Dinamico|Determina se qualsiasi tipo di aggiornamento di un'unità di failover deve interrompere un'esecuzione di un bilanciamento rapido o lento. Specificare "false" per interrompere l'esecuzione del bilanciamento se FailoverUnit: viene creata/eliminata; ha repliche mancanti; ha modificato il percorso di replica primario o il numero di repliche. L'esecuzione del bilanciamento NON verrà interrotta in altri casi, ossia se FailoverUnit: ha repliche extra; ha modificato flag della replica; ha modificato solo la versione della partizione e tutti gli altri casi. |
|GlobalMovementThrottleThresholdPercentage|double, valore predefinito: 0|Dinamico|Numero massimo di spostamenti totali consentiti nelle fasi di bilanciamento e selezione (espresso come percentuale del numero totale di repliche nel cluster) nell'intervallo trascorso indicato da GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti. Se vengono specificati sia questa impostazione che GlobalMovementThrottleThreshold, viene usato il limite più conservativo.|
|GlobalMovementThrottleThresholdPercentageForBalancing|double, valore predefinito: 0|Dinamico|Numero massimo di spostamenti consentiti nella fase di bilanciamento (espresso come percentuale del numero totale di repliche in PLB) nell'intervallo trascorso indicato da GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti. Se vengono specificati sia questa impostazione che GlobalMovementThrottleThresholdForBalancing, viene usato il limite più conservativo.|
|AutoDetectAvailableResources|bool, valore predefinito: TRUE|statico|Questa configurazione attiverà il rilevamento automatico delle risorse disponibili nel nodo (CPU e memoria). Quando questa configurazione è impostata su true, verranno lette le capacità reali che verranno corrette se l'utente ha specificato capacità del nodo non corrette o non le ha definite affatto. Se questo parametro di configurazione viene impostato su false, si terrà traccia di un avviso relativo alla specifica di capacità del nodo non corrette da parte dell'utente, ma tali capacità non verranno corrette. Questo significa che verranno presupposte capacità illimitate se l'utente vuole specificare capacità maggiori di quelle effettivamente disponibili oppure se le capacità non vengono definite. |

### <a name="section-name-hosting"></a>Nome della sezione: Hosting
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| ServiceTypeRegistrationTimeout |Tempo in secondi, il valore predefinito è 300 |Dinamico|Tempo massimo consentito per la registrazione di ServiceType nell'infrastruttura |
| ServiceTypeDisableFailureThreshold |Numero intero, il valore predefinito è 1 |Dinamico|Si tratta della soglia per il conteggio degli errori superata la quale a FailoverManager (FM) viene notificato di disabilitare il tipo di servizio sul nodo e di provare il posizionamento su un altro nodo. |
| ActivationRetryBackoffInterval |Tempo in secondi, il valore predefinito è 5 |Dinamico|Intervallo di backoff per ogni errore di attivazione; in caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in MaxActivationFailureCount. L'intervallo tra tentativi è il prodotto dell'errore di attivazione continua e dell'intervallo di backoff di attivazione. |
| ActivationMaxRetryInterval |Tempo in secondi, il valore predefinito è 300 |Dinamico|In ogni caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in ActivationMaxFailureCount. ActivationMaxRetryInterval specifica l'intervallo di tempo di attesa prima di un nuovo tentativo dopo ogni errore di attivazione |
| ActivationMaxFailureCount |Numero intero, il valore predefinito è 10 |Dinamico|Numero di volte che il sistema ritenta l'attivazione non riuscita prima di rinunciare |
|ActivationTimeout| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(180)|Dinamico| Specificare l'intervallo di tempo in secondi. Timeout per l'attivazione, la disattivazione e l'aggiornamento dell'applicazione. |
|ApplicationHostCloseTimeout| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico| Specificare l'intervallo di tempo in secondi. Quando viene rilevata l'uscita dall'infrastruttura in processi ad attivazione automatica, FabricRuntime chiude tutte le repliche nel processo host (applicationhost) dell'utente. Questo è il timeout per l'operazione di chiusura. |
|ApplicationUpgradeTimeout| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(360)|Dinamico| Specificare l'intervallo di tempo in secondi. Timeout per l'aggiornamento dell'applicazione. Se il timeout è minore di "ActivationTimeout" l'implementazione avrà esito negativo. |
|CreateFabricRuntimeTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico| Specificare l'intervallo di tempo in secondi. Valore di timeout per la chiamata di FabricCreateRuntime. |
|DeploymentMaxFailureCount|int, valore predefinito: 20| Dinamico|Verranno eseguiti altri tentativi di distribuzione dell'applicazione per le volte specificate in DeploymentMaxFailureCount prima di considerare non riuscita la distribuzione dell'applicazione nel nodo.| 
|DeploymentMaxRetryInterval| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(3600)|Dinamico| Specificare l'intervallo di tempo in secondi. Intervallo tra tentativi massimo per la distribuzione. In caso di errori continui, l'intervallo tra i tentativi viene calcolato come segue: Min( DeploymentMaxRetryInterval; conteggio errori continui * DeploymentRetryBackoffInterval). |
|DeploymentRetryBackoffInterval| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(10)|Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di backoff per l'errore di distribuzione. Per ogni errore di distribuzione continuo, il sistema ritenterà la distribuzione per il massimo di volte specificato in MaxDeploymentFailureCount. L'intervallo tra tentativi è un prodotto degli errori di distribuzione continui e dell'intervallo di backoff della distribuzione. |
|EnableActivateNoWindow| bool, valore predefinito: FALSE|Dinamico| Il processo attivato viene creato in background senza alcuna console. |
|EnableProcessDebugging|bool, valore predefinito: FALSE|Dinamico| Abilita l'avvio degli host dell'applicazione nel debugger. |
|EndpointProviderEnabled| bool, valore predefinito: FALSE|statico| Abilita la gestione delle risorse degli endpoint tramite l'infrastruttura. Richiede la specifica di un intervallo di porte dell'applicazione iniziale e finale in FabricNode. |
|FabricContainerAppsEnabled| bool, valore predefinito: FALSE|statico| |
|FirewallPolicyEnabled|bool, valore predefinito: FALSE|statico| Abilita l'apertura delle porte del firewall per le risorse degli endpoint con porte esplicite specificate in ServiceManifest. |
|GetCodePackageActivationContextTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico|Specificare l'intervallo di tempo in secondi. Valore di timeout per le chiamate CodePackageActivationContext. Non applicabile ai servizi ad hoc. |
|IPProviderEnabled|bool, valore predefinito: FALSE|statico|Abilita la gestione degli indirizzi IP. |
|NTLMAuthenticationEnabled|bool, valore predefinito: FALSE|statico| Abilita il supporto per l'uso di NTLM da parte dei pacchetti di codice eseguiti con account di altri utenti, in modo che i processi tra computer diversi possano comunicare in modo sicuro. |
|NTLMAuthenticationPasswordSecret|SecureString, valore predefinito: Common::SecureString(L"")|statico|Hash crittografato usato per generare la password per gli utenti NTLM. Deve essere impostato se NTLMAuthenticationEnabled è true. Convalidato dal deployer. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(3)|Dinamico|Specificare l'intervallo di tempo in secondi. Impostazioni specifiche dell'ambiente. Intervallo periodico in base al quale l'hosting cerca nuovi certificati da usare per la configurazione di NTLM per FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(4)|Dinamico| Specificare l'intervallo di tempo in secondi. Timeout per la configurazione di utenti NTLM usando nomi comuni del certificato. Gli utenti NTLM sono necessari per le condivisioni FileStoreService. |
|RegisterCodePackageHostTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico| Specificare l'intervallo di tempo in secondi. Valore di timeout per la chiamata di sincronizzazione di FabricRegisterCodePackageHost. Applicabile solo per gli host di applicazioni con più pacchetti di codice come FWP. |
|RequestTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(30)|Dinamico| Specificare l'intervallo di tempo in secondi. Rappresenta il timeout per la comunicazione tra l'host dell'applicazione dell'utente e il processo di Fabric per varie operazioni correlate all'hosting, come la registrazione di factory e la registrazione di runtime. |
|RunAsPolicyEnabled| bool, valore predefinito: FALSE|statico| Abilita l'esecuzione di pacchetti di codice con l'account utente locale, anziché con l'account utente usato per l'esecuzione del processo dell'infrastruttura. Per abilitare questo criterio è necessario che Fabric sia eseguito con l'account SYSTEM o con un account utente con SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico|Specificare l'intervallo di tempo in secondi. Valore di timeout per la chiamata Register(Stateless/Stateful)ServiceFactory di sincronizzazione |
|ServiceTypeDisableGraceInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(30)|Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di tempo dopo il quale il tipo di servizio può essere disabilitato |

### <a name="section-name-federation"></a>Nome della sezione: Federation
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| LeaseDuration |Tempo in secondi, il valore predefinito è 30 |Dinamico|Durata di un lease tra un nodo e gli elementi adiacenti. |
| LeaseDurationAcrossFaultDomain |Tempo in secondi, il valore predefinito è 30 |Dinamico|Durata di un lease tra un nodo e gli elementi adiacenti nei domini di errore. |

### <a name="section-name-clustermanager"></a>Nome della sezione: ClusterManager
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| UpgradeStatusPollInterval |Tempo in secondi, il valore predefinito è 60 |Dinamico|Frequenza di polling sullo stato di aggiornamento dell'applicazione. Questo valore determina la frequenza di aggiornamento di qualsiasi chiamata GetApplicationUpgradeProgress |
| UpgradeHealthCheckInterval |Tempo in secondi, il valore predefinito è 60 |Dinamico|Frequenza dei controlli sullo stato di integrità durante gli aggiornamenti di un'applicazione monitorata |
| FabricUpgradeStatusPollInterval |Tempo in secondi, il valore predefinito è 60 |Dinamico|Frequenza di polling dello stato di aggiornamento dell'infrastruttura. Questo valore determina la frequenza di aggiornamento di qualsiasi chiamata GetFabricUpgradeProgress |
| FabricUpgradeHealthCheckInterval |Tempo in secondi, il valore predefinito è 60 |Dinamico|Frequenza dei controlli sullo stato di integrità durante un aggiornamento di un'infrastruttura monitorata |
|InfrastructureTaskProcessingInterval | Tempo in secondi, il valore predefinito è 10 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo di elaborazione usato dal computer dello stato di elaborazione dell'attività di infrastruttura. |
|TargetReplicaSetSize |Int, valore predefinito: 7 |Non consentito|TargetReplicaSetSize per ClusterManager. |
|MinReplicaSetSize |Int, valore predefinito: 3 |Non consentito|MinReplicaSetSize per ClusterManager. |
|ReplicaRestartWaitDuration |Tempo in secondi, valore predefinito: (60.0 * 30)|Non consentito|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per ClusterManager. |
|QuorumLossWaitDuration |Tempo in secondi, valore predefinito: MaxValue |Non consentito| Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per ClusterManager. |
|StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: (3600.0 * 2)|Non consentito|Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per ClusterManager. |
|PlacementConstraints | stringa, il valore predefinito è "" |Non consentito|PlacementConstraints per ClusterManager. |
|SkipRollbackUpdateDefaultService | Bool, valore predefinito: false |Dinamico|CM non riporterà allo stato precedente i servizi predefiniti aggiornati durante il rollback dell'aggiornamento dell'applicazione. |
|EnableDefaultServicesUpgrade | Bool, valore predefinito: false |Dinamico|Abilita l'aggiornamento dei servizi predefiniti durante l'aggiornamento dell'applicazione. Le descrizioni dei servizi predefiniti vengono sovrascritte in seguito all'aggiornamento. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo in secondi, valore predefinito: 0|Dinamico| Specificare l'intervallo di tempo in secondi. Il tempo di attesa prima di avviare controlli di integrità dopo la post-elaborazione di un'attività di infrastruttura. |
|InfrastructureTaskHealthCheckStableDuration | Tempo in secondi, valore predefinito: 0|Dinamico| Specificare l'intervallo di tempo in secondi. Il tempo in cui osservare controlli di integrità positivi e consecutivi prima del completamento positivo della post-elaborazione di un'attività di infrastruttura. In caso di un controllo di integrità negativo, il timer verrà reimpostato. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo in secondi, il valore predefinito è 60 |Dinamico|Specificare l'intervallo di tempo in secondi. Il tempo impiegato per eseguire nuovamente i controlli di integrità negativi durante la post-elaborazione di un'attività di infrastruttura. In caso di un controllo di integrità positivo, il timer verrà reimpostato. |
|ImageBuilderTimeoutBuffer |Tempo in secondi, valore predefinito: 3 |Dinamico|Specificare l'intervallo di tempo in secondi. Il tempo per consentire la restituzione al client degli errori di timeout specifici di Image Builder. Se il buffer è troppo piccolo, il client giunge a un timeout prima del server e riceve un errore di timeout generico. |
|MinOperationTimeout | Tempo in secondi, il valore predefinito è 60 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout minimo globale per l'elaborazione interna delle operazioni in ClusterManager. |
|MaxOperationTimeout |Tempo in secondi, valore predefinito: MaxValue |Dinamico| Specificare l'intervallo di tempo in secondi. Il timeout massimo globale per l'elaborazione interna delle operazioni in ClusterManager. |
|MaxTimeoutRetryBuffer | Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout massimo dell'operazione durante i tentativi interni dovuti a timeout è <Original Time out> + <MaxTimeoutRetryBuffer>. Viene aggiunto timeout aggiuntivo in incrementi di MinOperationTimeout. |
|MaxCommunicationTimeout |Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Timeout massimo per le comunicazioni interne tra ClusterManager e altri servizi di sistema, ad esempio Naming Service, Gestione failover e così via. Questo timeout deve essere inferiore a MaxOperationTimeout globale, poiché potrebbero avvenire più comunicazioni tra i componenti di sistema per ciascuna operazione client. |
|MaxDataMigrationTimeout |Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Timeout massimo per le operazioni di ripristino di migrazione di dati dopo un aggiornamento di Service Fabric. |
|MaxOperationRetryDelay |Tempo in secondi, valore predefinito: 5|Dinamico| Specificare l'intervallo di tempo in secondi. Ritardo massimo per i tentativi interni quando vengono rilevati errori. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo in secondi, valore predefinito: 1200 |Dinamico| Specificare l'intervallo di tempo in secondi. Se ReplicaSetCheckTimeout è impostato sul valore massimo di DWORD, questo verrà sovrascritto dal valore di questa configurazione ai fini del rollback. Il valore usato per il roll forward non viene mai sovrascritto. |
|ImageBuilderJobQueueThrottle |Int, valore predefinito: 10 |Dinamico|Limitazione del conteggio dei thread per le code di processi di Image Builder nelle richieste di applicazione. |
|MaxExponentialOperationRetryDelay|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(30)|Dinamico|Specificare l'intervallo di tempo in secondi. Ritardo esponenziale massimo per i tentativi interni quando vengono rilevati errori ripetutamente. |

### <a name="section-name-defragmentationemptynodedistributionpolicy"></a>Nome della sezione: DefragmentationEmptyNodeDistributionPolicy
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, valore predefinito: None|Dinamico|Specifica i criteri per la deframmentazione da eseguire dopo lo svuotamento dei nodi. Per una determinata metrica, 0 indica che Service Fabric deve provare a deframmentare i nodi in modo uniforme tra i domini di aggiornamento e i domini di errore. 1 indica solo che i nodi devono essere deframmentati. |

### <a name="section-name-defragmentationmetrics"></a>Nome della sezione: DefragmentationMetrics
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, valore predefinito: None|Dinamico|Determina il set di metriche che deve essere usato per la deframmentazione e non per il bilanciamento del carico. |

### <a name="section-name-defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>Nome della sezione: DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valore predefinito: None|Dinamico|Determina il numero di nodi liberi necessari per considerare il cluster deframmentato specificando un intervallo in percentuale [0,0-1,0) o il numero di nodi vuoti > = 1,0 |

### <a name="section-name-dnsservice"></a>Nome della sezione: DnsService
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|IsEnabled|bool, valore predefinito: FALSE|statico| |
|InstanceCount|int, valore predefinito: -1|statico|  |

### <a name="section-name-metricactivitythresholds"></a>Nome della sezione: MetricActivityThresholds
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, valore predefinito: None|Dinamico|Determina il set di MetricActivityThresholds per le metriche nel cluster. Il bilanciamento funzionerà se maxNodeLoad è maggiore di MetricActivityThresholds. Per le metriche di deframmentazione definisce la quantità di carico raggiunta la quale o al di sotto della quale Service Fabric considererà il nodo vuoto. |

### <a name="section-name-metricbalancingthresholds"></a>Nome della sezione: MetricBalancingThresholds
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valore predefinito: None|Dinamico|Determina il set di MetricBalancingThresholds per le metriche nel cluster. Il bilanciamento funzionerà se maxNodeLoad/minNodeLoad è maggiore di MetricBalancingThresholds. La deframmentazione funzionerà se maxNodeLoad/minNodeLoad in almeno un dominio di errore o dominio di aggiornamento è minore di MetricBalancingThresholds. |

### <a name="section-name-nodebufferpercentage"></a>Nome della sezione: NodeBufferPercentage
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valore predefinito: None|Dinamico|Percentuale di capacità del nodo per ogni nome di metrica. Usato come buffer per mantenere spazio libero in un nodo per il caso di failover. |

### <a name="section-name-replication"></a>Nome della sezione: Replication
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|MaxCopyQueueSize|uint, valore predefinito: 1024|statico|Valore massimo che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica.  Si noti che deve essere una potenza di 2.  Se durante il runtime la coda raggiunge tale dimensione, l'operazione verrà limitata tra i replicatori primario e secondario.|
|BatchAcknowledgementInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromMilliseconds(15)|statico|Specificare l'intervallo di tempo in secondi. Periodo di tempo di attesa del Replicator dopo aver ricevuto un'operazione prima di inviare un acknowledgement in risposta. Altre operazioni ricevuti durante questo periodo di tempo comporteranno un invio dei vari acknowledgement in un unico messaggio, riducendo il traffico di rete ma anche potenzialmente la velocità effettiva del Replicator.|
|MaxReplicationMessageSize|uint, valore predefinito: 52428800|statico|Dimensioni massime dei messaggi delle operazioni di replica. Il valore predefinito è 50 MB.|
|ReplicatorAddress|string, valore predefinito: : L"localhost:0"|statico|L'endpoint in forma di stringa "IP:Port" usato dal Replicator di Windows Fabric per stabilire connessioni con altre repliche per inviare/ricevere operazioni.|
|ReplicatorListenAddress|string, valore predefinito: : L"localhost:0"|statico|Endpoint in forma di stringa -'IP:Porta' usato dal Replicator di Windows Fabric per ricevere operazioni da altre repliche.|
|ReplicatorPublishAddress|string, valore predefinito: : L"localhost:0"|statico|Endpoint in forma di stringa -'IP:Porta' usato dal Replicator di Windows Fabric per inviare operazioni ad altre repliche.|
|MaxPrimaryReplicationQueueSize|uint, valore predefinito: 1024|statico|Numero massimo di operazioni che possono essere presenti nella coda di replica primaria. Si noti che deve essere una potenza di 2.|
|MaxPrimaryReplicationQueueMemorySize|uint, valore predefinito: 0|statico|Valore massimo della coda di replica primaria in byte.|
|MaxSecondaryReplicationQueueSize|uint, valore predefinito: 2048|statico|Numero massimo di operazioni che possono essere presenti nella coda di replica secondaria. Si noti che deve essere una potenza di 2.|
|MaxSecondaryReplicationQueueMemorySize|uint, valore predefinito: 0|statico|Valore massimo della coda di replica secondaria in byte.|
|QueueHealthMonitoringInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(30)|statico|Specificare l'intervallo di tempo in secondi. Questo valore determina il periodo di tempo impiegato dal Replicator per monitorare gli eventi di integrità di avviso/errore nelle code delle operazioni di replica. Il valore '0' disabilita il monitoraggio dell'integrità |
|QueueHealthWarningAtUsagePercent|uint, valore predefinito: 80|statico|Questo valore determina l'utilizzo della coda di replica (in percentuale) dopo il quale verrà generato un avviso per segnalare un utilizzo elevato della coda. La segnalazione avviene dopo un intervallo di tolleranza corrispondente a QueueHealthMonitoringInterval. Se l'utilizzo della coda scende sotto questa percentuale nell'intervallo di tolleranza.|
|RetryInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(5)|statico|Specificare l'intervallo di tempo in secondi. Quando un'operazione viene persa o rifiutata, questo timer determina la frequenza con cui il replicatore ritenterà l'operazione di invio.|

### <a name="section-name-transport"></a>Nome della sezione: Transport
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento** |**Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ResolveOption|string, valore predefinito: L"unspecified"|statico|Determina la modalità di risoluzione del nome di dominio completo.  I valori validi sono "unspecified/ipv4/ipv6". |
|ConnectionOpenTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(60)|statico|Specificare l'intervallo di tempo in secondi. Timeout per la configurazione della connessione sul lato in ingresso e di accettazione (inclusa la negoziazione di sicurezza in modalità protetta) |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla gestione del cluster, leggere questi articoli:

[Aggiunta, rollover e rimozione di certificati dal cluster di Azure ](service-fabric-cluster-security-update-certs-azure.md) 

