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
ms.openlocfilehash: cedfba78d8d1186618cf4f61154c5d88a00eb12b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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

## <a name="diagnostics"></a>Diagnostica
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ConsumerInstances |String |L'elenco delle istanze di consumer DCA. |
| ProducerInstances |String |L'elenco delle istanze di producer DCA. |
| AppEtwTraceDeletionAgeInDays |Int, valore predefinito: 3 |Il numero di giorni dopo il quale i file ETL meno recenti contenenti tracce ETW di applicazioni vengono eliminati. |
| AppDiagnosticStoreAccessRequiresImpersonation |Bool, valore predefinito: true |Indica se è richiesta la rappresentazione quando si accede a un archivio di diagnostica per conto dell'applicazione. |
| MaxDiskQuotaInMB |Int, valore predefinito: 65536 |Quota del disco in MB per i file di log di Windows Fabric. |
| DiskFullSafetySpaceInMB |Int, valore predefinito: 1024 |Spazio su disco rimanente in MB per la protezione dall'uso di DCA. |
| ApplicationLogsFormatVersion |Int, valore predefinito: 0 |Versione per il formato dei log applicazioni. I valori supportati sono 0 e 1. La versione 1 include più campi dal record di eventi ETW rispetto alla versione 0. |
| ClusterId |String |L'ID univoco del cluster. Viene generato quando viene creato il cluster. |
| EnableTelemetry |Bool, valore predefinito: true |Parametro per abilitare o disabilitare la telemetria. |
| EnableCircularTraceSession |Bool, valore predefinito: false |Il flag indica se devono essere usate le sessioni di traccia circolari. |

## <a name="traceetw"></a>Traccia/Etw
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| Level |Int, valore predefinito: 4 |Il livello di traccia ETW può accettare valori 1, 2, 3 e 4. Per assicurare il supporto è necessario mantenere il livello di traccia a 4 |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| IsEnabled |Bool, valore predefinito: true |Il flag indica se è abilitata la raccolta dei contatori delle prestazioni nel nodo locale. |
| SamplingIntervalInSeconds |Int, valore predefinito: 60 |Intervallo di campionamento per i contatori delle prestazioni che vengono raccolti. |
| Counters |String |Elenco delimitato da virgole dei contatori delle prestazioni da raccogliere. |
| MaxCounterBinaryFileSizeInMB |Int, valore predefinito: 1 |Dimensione massima (in MB) per ogni file binario del contatore delle prestazioni. |
| NewCounterBinaryFileCreationIntervalInMinutes |Int, valore predefinito: 10 |Intervallo massimo (in secondi) dopo il quale viene creato un nuovo file binario di contatore delle prestazioni. |

## <a name="setup"></a>Configurazione
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| FabricDataRoot |string |La directory radice dei dati di Service Fabric. L'impostazione predefinita per Azure è d:\svcfab |
| FabricLogRoot |string |La directory radice dei log di Service Fabric. Si tratta della posizione in cui vengono collocate le tracce e i log di Service Fabric. |
| ServiceRunAsAccountName |String |Il nome dell'account con cui eseguire il servizio host infrastruttura. |
| ServiceStartupType |string |Il tipo di avvio del servizio host infrastruttura. |
| SkipFirewallConfiguration |Bool, valore predefinito: false |Specifica se le impostazioni del firewall devono essere impostate dal sistema. Si applica solo se si usa Windows Firewall. Se si usano firewall di terze parti, è necessario aprire le porte per il sistema e le applicazioni da usare |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| MaxCopyQueueSize |Uint, valore predefinito: 16384 |Valore massimo che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica. Si noti che deve essere una potenza di 2. Se durante il runtime la coda raggiunge tale dimensione, le operazioni verranno limitate tra i replicatori primario e secondario. |
| BatchAcknowledgementInterval | Tempo in secondi, valore predefinito: 0,015 | Specificare l'intervallo di tempo in secondi. Periodo di tempo di attesa del Replicator dopo aver ricevuto un'operazione prima di inviare un acknowledgement in risposta. Altre operazioni ricevuti durante questo periodo di tempo comporteranno un invio dei vari acknowledgement in un unico messaggio, riducendo il traffico di rete ma anche potenzialmente la velocità effettiva del Replicator. |
| MaxReplicationMessageSize |Uint, valore predefinito: 52428800 | Dimensioni massime dei messaggi delle operazioni di replica. Il valore predefinito è 50 MB. |
| ReplicatorAddress |stringa, il valore predefinito è "localhost:0" | L'endpoint in forma di stringa "IP:Port" usato dal Replicator di Windows Fabric per stabilire connessioni con altre repliche per inviare/ricevere operazioni. |
| InitialPrimaryReplicationQueueSize |Uint, valore predefinito: 64 | Valore che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica sul Replicator primario. Si noti che deve essere una potenza di 2.|
| MaxPrimaryReplicationQueueSize |Uint, valore predefinito: 8192 |Numero massimo di operazioni che possono essere presenti nella coda di replica primaria. Si noti che deve essere una potenza di 2. |
| MaxPrimaryReplicationQueueMemorySize |Uint, valore predefinito: 0 |Valore massimo della coda di replica primaria in byte. |
| InitialSecondaryReplicationQueueSize |Uint, valore predefinito: 64 |Valore che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica sul Replicator secondario. Si noti che deve essere una potenza di 2. |
| MaxSecondaryReplicationQueueSize |Uint, valore predefinito: 16384 |Numero massimo di operazioni che possono essere presenti nella coda di replica secondaria. Si noti che deve essere una potenza di 2. |
| MaxSecondaryReplicationQueueMemorySize |Uint, valore predefinito: 0 |Valore massimo della coda di replica secondaria in byte. |
| SecondaryClearAcknowledgedOperations |Bool, valore predefinito: false |Bool che controlla se le operazioni per il Replicator secondario vengono cancellate e scaricate sul disco dopo aver inviato l'acknowledgement al Replicator primario. Impostando il parametro su TRUE si potranno avere letture di disco aggiuntive sul nuovo Replicator primario in caso di ricostruzione delle repliche dopo un failover. |
| MaxMetadataSizeInKB |Int, valore predefinito: 4 |Dimensione massima dei metadati del flusso di log. |
| MaxRecordSizeInKB |Uint, valore predefinito: 1024 | Dimensione massima del record di un flusso di log. |
| CheckpointThresholdInMB |Int, valore predefinito: 50 |Verrà avviato un checkpoint quando l'uso del log supera questo valore. |
| MaxAccumulatedBackupLogSizeInMB |Int, valore predefinito: 800 |Dimensioni massime accumulate (in MB) dei log di backup in una data catena di log di backup. Una richiesta di backup incrementale avrà esito negativo se il backup incrementale dovesse generare un log di backup che causasse i log di backup accumulati poiché il backup completo rilevante sarebbe più grande di tali dimensioni. In questi casi, l'utente deve eseguire un backup completo. |
| MaxWriteQueueDepthInKB |Int, valore predefinito: 0 | Int la profondità massima della coda di scrittura. Il logger di base può usare il valore specificato in kilobyte per il log associato a questa replica. Questo valore è il numero massimo di byte che può rimanere in sospeso durante gli aggiornamenti del logger di base. Se il valore è 0, il logger di base calcolerà un valore appropriato o un multiplo di 4. |
| SharedLogId |String |Identificatore di un log condiviso. È un valore GUID e deve essere univoco per ciascun log condiviso. |
| SharedLogPath |String |Percorso del log condiviso. Se questo valore è vuoto viene usato il log condiviso predefinito. |
| SlowApiMonitoringDuration |Tempo in secondi, il valore predefinito è 300 | Specificare la durata per l'API prima che venga generato l'evento di integrità dell'avviso.|
| MinLogSizeInMB |Int, valore predefinito: 0 |Dimensioni minime del log delle transazioni. Il log non potrà essere troncato a una dimensione inferiore a quella impostata. 0 indica che il Replicator determinerà le dimensioni del log minimo in base alle altre impostazioni. Aumentando il valore si aumenta la possibilità di eseguire copie parziali e backup incrementali poiché le probabilità di troncamento dei record dei log rilevanti si riducono. |

## <a name="fabricclient"></a>FabricClient
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| NodeAddresses |stringa, il valore predefinito è "" |Un insieme di indirizzi (stringhe di connessione) in nodi diversi usabile per comunicare con il Naming Service. Inizialmente, il client si connette scegliendo casualmente uno degli indirizzi. Se viene specificata più di una stringa e una connessione non riesce a causa di un errore di comunicazione o timeout, il client usa l'indirizzo successivo in maniera sequenziale. Per i dettagli di semantica sui nuovi tentativi, vedere la sezione relativa del Naming Service. |
| ConnectionInitializationTimeout |Tempo in secondi, valore predefinito: 2 |Specificare l'intervallo di tempo in secondi. Intervallo di timeout di connessione per ogni tentativo di apertura di connessione del client al gateway. |
| PartitionLocationCacheLimit |Int, valore predefinito: 100000 |Numero di partizioni memorizzate nella cache per la risoluzione di servizio. Impostarlo su 0 per non avere limiti. |
| ServiceChangePollInterval |Tempo in secondi, valore predefinito: 120 |Specificare l'intervallo di tempo in secondi. L'intervallo tra i polling consecutivi per le modifiche di servizio dal client al gateway, per i callback di notifica in caso di modifiche al servizio registrato. |
| KeepAliveIntervalInSeconds |Int, valore predefinito: 20 |L'intervallo con cui il trasporto FabricClient invia messaggi keep-alive al gateway. Se il valore è 0, keepAlive è disabilitato. Deve essere un valore positivo. |
| HealthOperationTimeout |Tempo in secondi, valore predefinito: 120 |Specificare l'intervallo di tempo in secondi. Il timeout per un messaggio di report inviato a Health Manager. |
| HealthReportSendInterval |Tempo in secondi, il valore predefinito è 30 |Specificare l'intervallo di tempo in secondi. L'intervallo dopo il quale il componente di report invia i report sull'integrità accumulati a Health Manager. |
| HealthReportRetrySendInterval |Tempo in secondi, il valore predefinito è 30 |Specificare l'intervallo di tempo in secondi. L'intervallo dopo il quale il componente di report invia nuovamente i report sull'integrità accumulati a Health Manager. |
| RetryBackoffInterval |Tempo in secondi, valore predefinito: 3 |Specificare l'intervallo di tempo in secondi. L'intervallo di backoff prima di ritentare l'operazione. |
| MaxFileSenderThreads |Uint, valore predefinito: 10 |Il numero massimo di file trasferiti in parallelo. |

## <a name="common"></a>Comune
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| PerfMonitorInterval |Tempo in secondi, valore predefinito: 1 |Specificare l'intervallo di tempo in secondi. Intervallo del monitoraggio delle prestazioni. Con valore 0 o negativo, il monitoraggio è disabilitato. |

## <a name="healthmanager"></a>HealthManager
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, valore predefinito: false |Criteri di valutazione dell'integrità del cluster: abilitare il parametro per la valutazione dell'integrità del tipo di applicazione. |

## <a name="fabricnode"></a>FabricNode
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| StateTraceInterval |Tempo in secondi, il valore predefinito è 300 |Specificare l'intervallo di tempo in secondi. L'intervallo per la traccia dello stato di nodo in ogni nodo e dei nodi in FM/FMM. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| UpgradeDomainId |stringa, il valore predefinito è "" |Descrivi il dominio di aggiornamento a cui appartiene un nodo. |
| PropertyGroup |NodeFaultDomainIdCollection |Descrive il dominio di errore a cui appartiene un nodo. Il dominio di errore viene definito tramite un URI che descrive la posizione del nodo nel data center.  Gli URI dei domini di errore sono nel formato fd:/fd/ seguito da un segmento di percorso URI.|

## <a name="nodeproperties"></a>NodeProperties
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |Una raccolta di coppie di stringhe chiave-valore per le proprietà del nodo. |

## <a name="nodecapacities"></a>NodeCapacities
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |Una raccolta di capacità dei nodi per diverse metriche. |

## <a name="fabricnode"></a>FabricNode
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| StartApplicationPortRange |Int, valore predefinito: 0 |Avvio delle porte di applicazione gestite dal sottosistema di hosting. Obbligatorio se EndpointFilteringEnabled è impostato su true in Hosting. |
| EndApplicationPortRange |Int, valore predefinito: 0 |Termine (non inclusivo) delle porte di applicazione gestite dal sottosistema di hosting. Obbligatorio se EndpointFilteringEnabled è impostato su true in Hosting. |
| ClusterX509StoreName |stringa, il valore predefinito è "My" |Nome dell'archivio certificati X.509 che contiene il certificato del cluster per proteggere la comunicazione all'interno del cluster. |
| ClusterX509FindType |stringa, valore predefinito è "FindByThumbprint" |Indica la modalità di ricerca di un certificato del cluster nell'archivio specificato da ClusterX509StoreName. Valori supportati: "FindByThumbprint"; "FindBySubjectName". Con "FindBySubjectName", in caso di più corrispondenze, viene usato il certificato con la scadenza più lontana. |
| ClusterX509FindValue |stringa, il valore predefinito è "" |Valore di filtro di ricerca usato per individuare il certificato del cluster. |
| ClusterX509FindValueSecondary |stringa, il valore predefinito è "" |Valore di filtro di ricerca usato per individuare il certificato del cluster. |
| ServerAuthX509StoreName |stringa, il valore predefinito è "My" |Nome dell'archivio certificati X.509 che contiene il certificato server per il servizio di entrata. |
| ServerAuthX509FindType |stringa, valore predefinito è "FindByThumbprint" |Indica la modalità di ricerca del certificato server nell'archivio specificato da ServerAuthX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |stringa, il valore predefinito è "" |Valore di filtro di ricerca usato per individuare il certificato del server. |
| ServerAuthX509FindValueSecondary |stringa, il valore predefinito è "" |Valore di filtro di ricerca usato per individuare il certificato del server. |
| ClientAuthX509StoreName |stringa, il valore predefinito è "My" |Nome dell'archivio certificati X.509 che contiene un certificato per il ruolo di amministratore predefinito FabricClient. |
| ClientAuthX509FindType |stringa, valore predefinito è "FindByThumbprint" |Indica la modalità di ricerca del certificato nell'archivio specificato da ClientAuthX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |stringa, il valore predefinito è "" | Valore di filtro di ricerca usato per individuare un certificato per il ruolo di amministratore predefinito FabricClient. |
| ClientAuthX509FindValueSecondary |stringa, il valore predefinito è "" |Valore di filtro di ricerca usato per individuare un certificato per il ruolo di amministratore predefinito FabricClient. |
| UserRoleClientX509StoreName |stringa, il valore predefinito è "My" |Nome dell'archivio certificati X.509 che contiene un certificato per il ruolo di utente predefinito FabricClient. |
| UserRoleClientX509FindType |stringa, valore predefinito è "FindByThumbprint" |Indica la modalità di ricerca del certificato nell'archivio specificato da UserRoleClientX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |stringa, il valore predefinito è "" |Valore di filtro di ricerca usato per individuare un certificato per il ruolo di utente predefinito FabricClient. |
| UserRoleClientX509FindValueSecondary |stringa, il valore predefinito è "" |Valore di filtro di ricerca usato per individuare un certificato per il ruolo di utente predefinito FabricClient. |

## <a name="paas"></a>PaaS
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ClusterId |stringa, il valore predefinito è "" |Archivio certificati X.509 usato da Service Fabric per la protezione della configurazione. |

## <a name="fabrichost"></a>FabricHost
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| StopTimeout |Tempo in secondi, il valore predefinito è 300 |Specificare l'intervallo di tempo in secondi. Il timeout per l'attivazione, la disattivazione e l'aggiornamento del servizio ospitato. |
| StartTimeout |Tempo in secondi, il valore predefinito è 300 |Specificare l'intervallo di tempo in secondi. Timeout per l'avvio di fabricactivationmanager. |
| ActivationRetryBackoffInterval |Tempo in secondi, valore predefinito: 5 |Specificare l'intervallo di tempo in secondi. Intervallo di backoff per ogni errore di attivazione; in caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in MaxActivationFailureCount. L'intervallo tra tentativi è il prodotto dell'errore di attivazione continua e dell'intervallo di backoff di attivazione. |
| ActivationMaxRetryInterval |Tempo in secondi, il valore predefinito è 300 |Specificare l'intervallo di tempo in secondi. Intervallo massimo tra i tentativi di attivazione. In casi di errori periodici, l'intervallo tra i tentativi viene calcolato come segue: Min( ActivationMaxRetryInterval; conteggio errori continui * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Int, valore predefinito: 10 |Conteggio massimo di tentativi di attivazione da parte del sistema prima di interrompersi. |
| EnableServiceFabricAutomaticUpdates |Bool, valore predefinito: false |Parametro per abilitare l'aggiornamento automatico di Service Fabric tramite Windows Update. |
| EnableServiceFabricBaseUpgrade |Bool, valore predefinito: false |Parametro per abilitare l'aggiornamento base del server. |
| EnableRestartManagement |Bool, valore predefinito: false |Parametro per abilitare il riavvio del server. |


## <a name="failovermanager"></a>FailoverManager
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| UserReplicaRestartWaitDuration |Tempo in secondi, valore predefinito: 60,0 * 30 |Specificare l'intervallo di tempo in secondi. Quando una replica persistente va offline, Windows Fabric attende che torni online per questo intervallo di tempo, passato il quale crea nuove repliche sostitutive, che richiederanno una copia dello stato. |
| QuorumLossWaitDuration |Tempo in secondi, valore predefinito: MaxValue |Specificare l'intervallo di tempo in secondi. Durata massima per cui una partizione può essere in stato di perdita del quorum. Se la partizione si trova ancora in questo stato passato questo intervallo di tempo, viene ripristinata considerando come perse le repliche offline. Ciò può comportare una potenziale perdita di dati. |
| UserStandByReplicaKeepDuration |Tempo in secondi, il valore predefinito è 3600.0 * 24 * 7 |Specificare l'intervallo di tempo in secondi. Quando una replica persistente torna online, potrebbe già essere stata sostituita. Questo timer determina per quanto tempo FM manterrà la replica in standby prima di rimuoverla. |
| UserMaxStandByReplicaCount |Int, valore predefinito: 1 |Il numero massimo predefinito di repliche in standby che il sistema conserva per i servizi utente. |

## <a name="namingservice"></a>NamingService
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, valore predefinito: 7 |Il numero di set di repliche per ogni partizione dell'archivio di Naming Service. L'aumento del numero di set di repliche rafforzerà l'affidabilità delle informazioni nell'archivio di Naming Service, riducendo i rischi di perdita di tali informazioni in caso di errori nei nodi. Tutto ciò comporterà tuttavia un carico maggiore per Windows Fabric, nonché un aumento del tempo necessario per aggiornare i dati di denominazione.|
|MinReplicaSetSize | Int, valore predefinito: 3 | Il numero minimo di repliche di Naming Service in cui scrivere per completare un aggiornamento. Se nel sistema sono presenti meno repliche rispetto a questo valore, Reliability System non effettua aggiornamenti dell'archivio Naming Service fino a quando le repliche non vengono ripristinate. Questo valore non deve mai essere maggiore di TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Tempo in secondi, valore predefinito: (60.0 * 30)| Specificare l'intervallo di tempo in secondi. Quando una replica di Naming Service si arresta, si avvia questo timer.  Alla scadenza FM inizierà a sostituire le repliche non attive; queste non vengono ancora considerate perse. |
|QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue | Specificare l'intervallo di tempo in secondi. Quando un Naming Service entra in uno stato di perdita del quorum, si avvia questo timer.  Una volta scaduto, FM considererà perse le repliche offline e tenterà di ripristinare il quorum. Questa azione può comportare la perdita di dati. |
|StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: è 3600.0 * 2 | Specificare l'intervallo di tempo in secondi. Quando un Naming Service torna online, potrebbe già essere stata sostituito.  Questo timer determina per quanto tempo FM manterrà la replica in standby prima di rimuoverla. |
|PlacementConstraints | stringa, il valore predefinito è "" | Vincolo di posizionamento per il Naming Service. |
|ServiceDescriptionCacheLimit | Int, valore predefinito: 0 | Numero massimo di voci conservate nella cache della descrizione del servizio LRU nell'archivio di Naming Service. Impostarlo su 0 per non avere limiti. |
|RepairInterval | Tempo in secondi, valore predefinito: 5 | Specificare l'intervallo di tempo in secondi. Intervallo in cui verrà avviata la correzione delle incoerenze di denominazione tra il proprietario dell'autorità e il proprietario del nome. |
|MaxNamingServiceHealthReports | Int, valore predefinito: 10 | Numero massimo di operazioni lente non integre di cui il servizio Naming Service esegue globalmente il report. Se il valore è 0, verranno incluse tutte le operazioni lente. |
| MaxMessageSize |Int, valore predefinito: 4*1024*1024 |Dimensioni massime dei messaggi di comunicazioni del nodo client quando si usa la denominazione. Attenuazione di attacchi DOS, valore predefinito: 4 MB. |
| MaxFileOperationTimeout |Tempo in secondi, il valore predefinito è 30 |Specificare l'intervallo di tempo in secondi. Il timeout massimo consentito per l'operazione del servizio di un archivio file. Le richieste che specificano un timeout maggiore verranno respinte. |
| MaxOperationTimeout |Tempo in secondi, valore predefinito: 600 |Specificare l'intervallo di tempo in secondi. Il timeout massimo consentito per le operazioni client. Le richieste che specificano un timeout maggiore verranno respinte. |
| MaxClientConnections |Int, valore predefinito: 1000 |Numero massimo di connessioni client consentito per gateway. |
| ServiceNotificationTimeout |Tempo in secondi, il valore predefinito è 30 |Specificare l'intervallo di tempo in secondi. Il timeout usato per il recapito delle notifiche di servizio al client. |
| MaxOutstandingNotificationsPerClient |Int, valore predefinito: 1000 |Numero massimo di notifiche in sospeso prima della chiusura forzata di una registrazione cliente da parte del gateway. |
| MaxIndexedEmptyPartitions |Int, valore predefinito: 1000 |Numero massimo di partizioni vuote che rimarranno indicizzate nella cache di notifica per la sincronizzazione dei client in fase di riconnessione. Tutte le partizioni vuote oltre questo numero verranno rimosse dall'indice in ordine crescente di versione. Riconnettendo i client sarà comunque possibile sincronizzare e ricevere gli aggiornamenti saltati delle partizioni vuote, ma il protocollo di sincronizzazione sarà più oneroso. |
| GatewayServiceDescriptionCacheLimit |Int, valore predefinito: 0 |Numero massimo di voci conservate nella cache della descrizione del servizio LRU nell'archivio di Naming Gateway. Impostarlo su 0 per non avere limiti. |
| PartitionCount |Int, valore predefinito: 3 |Numero di partizioni dell'archivio Naming Service da creare. Ogni partizione possiede una chiave di partizione singola che corrisponde al relativo indice. Esistono quindi chiavi di partizione [0; PartitionCount). Aumentando il numero di partizioni Naming Service, verrà aumentata anche la scalabilità delle relative operazioni riducendo la quantità media di dati presenti in un dato set di repliche di riserva. Tutto questo comporterà però un maggiore uso di risorse, dato che devono essere conservate repliche di servizio pari a PartitionCount*ReplicaSetSize.|

## <a name="runas"></a>RunAs
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| RunAsAccountName |stringa, il valore predefinito è "" |Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|stringa, il valore predefinito è "" |Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| RunAsAccountName |stringa, il valore predefinito è "" |Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| RunAsAccountName |stringa, il valore predefinito è "" |Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

## <a name="runasdca"></a>RunAs_DCA
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| RunAsAccountName |stringa, il valore predefinito è "" |Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

## <a name="httpgateway"></a>HttpGateway
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
|IsEnabled|Bool, valore predefinito: false | Abilita/disabilita httpgateway. Httpgateway è disabilitato per impostazione predefinita. È necessario impostare questa configurazione per abilitarlo. |
|ActiveListeners |Uint, valore predefinito: 50 | Numero di letture da pubblicare nella coda del server http. Consente di controllare il numero di richieste simultanee che possono essere espletate da HttpGateway. |
|MaxEntityBodySize |Uint, valore predefinito: 4194304 |  Indica la dimensione massima del corpo prevista in una richiesta http. Il valore predefinito è 4 MB. Httpgateway non porterà a termine una richiesta se questa ha un corpo di dimensioni superiori al valore indicato. La dimensione minima dei blocchi di lettura è di 4096 byte. Il valore deve pertanto essere >= 4096. |
|HttpGatewayHealthReportSendInterval |Tempo in secondi, il valore predefinito è 30 | Specificare l'intervallo di tempo in secondi. Intervallo dopo il quale l'Http gateway invia i report sull'integrità accumulati a Health Manager. |

## <a name="ktllogger"></a>KtlLogger
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
|AutomaticMemoryConfiguration |Int, valore predefinito: 1 | Flag che indica se le impostazioni di memoria devono essere configurate in modo dinamico e automatico. Se il valore è 0, le impostazioni vengono usate direttamente e non vengono modificate in base alle condizioni di sistema. Se il valore è 1, le impostazioni vengono configurate automaticamente e possono cambiare in base alle condizioni di sistema. |
|WriteBufferMemoryPoolMinimumInKB |Int, valore predefinito: 8388608 |Il numero di KB da allocare inizialmente per il pool di memoria buffer in scrittura. Usare 0 per non avere limiti. L'impostazione predefinita deve essere coerente con il parametro SharedLogSizeInMB di seguito. |
|WriteBufferMemoryPoolMaximumInKB | Int, valore predefinito: 0 |Il numero di KB raggiungibile dal pool di memoria buffer in scrittura. Usare 0 per non avere limiti. |
|MaximumDestagingWriteOutstandingInKB | Int, valore predefinito: 0 | Il numero di KB per cui il log condiviso può superare in dimensioni il log dedicato. Usare 0 per non avere limiti.
|SharedLogPath |stringa, il valore predefinito è "" | Percorso e nome del file in cui inserire il contenitore del log condiviso. Usare "" per indicare il percorso predefinito nella radice dati di Service Fabric. |
|SharedLogId |stringa, il valore predefinito è "" |GUID univoco per il contenitore di log condivisi. Usare "" se si usa il percorso predefinito nella radice dati di Service Fabric. |
|SharedLogSizeInMB |Int, valore predefinito: 8192 | Il numero di MB da allocare nel contenitore di log condivisi. |

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
|IsEnabled |Bool, valore predefinito: false | Abilita/disabilita HttpApplicationGateway. HttpApplicationGateway è disabilitato per impostazione predefinita. È necessario impostare questa configurazione per abilitarlo. |
|NumberOfParallelOperations | Uint, valore predefinito: 5000 | Numero di letture da pubblicare nella coda del server http. Consente di controllare il numero di richieste simultanee che possono essere espletate da HttpGateway. |
|DefaultHttpRequestTimeout |Tempo in secondi, Il valore predefinito è 120 |Specificare l'intervallo di tempo in secondi.  Indica il timeout di richiesta predefinito per le richieste http in elaborazione nel gateway applicazione http. |
|ResolveServiceBackoffInterval |Tempo in secondi, valore predefinito: 5 |Specificare l'intervallo di tempo in secondi.  Indica l'intervallo di backoff predefinito prima di ritentare l'operazione di risoluzione del servizio. |
|BodyChunkSize |Uint, valore predefinito: 16384 |  Indica la dimensione in byte per il blocco usato per leggere il corpo. |
|GatewayAuthCredentialType |stringa, il valore predefinito è "None" | Indica il tipo di credenziali di sicurezza da usare nell'endpoint del gateway applicazione http. I valori validi sono "None/X509. |
|GatewayX509CertificateStoreName |stringa, il valore predefinito è "My" | Nome dell'archivio certificati X.509 che contiene il certificato per il gateway applicazione http. |
|GatewayX509CertificateFindType |stringa, valore predefinito è "FindByThumbprint" | Indica la modalità di ricerca del certificato nell'archivio specificato da GatewayX509CertificateStoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | stringa, il valore predefinito è "" | Valore del filtro di ricerca usato per individuare il certificato del gateway applicazione http. Il certificato è configurato nell'endpoint https e può essere usato anche per verificare l'identità dell'app se richiesto dai servizi. Viene prima cercato FindValue. Se non esiste, viene cercato FindValueSecondary. |
|GatewayX509CertificateFindValueSecondary | stringa, il valore predefinito è "" |Valore del filtro di ricerca usato per individuare il certificato del gateway applicazione http. Il certificato è configurato nell'endpoint https e può essere usato anche per verificare l'identità dell'app se richiesto dai servizi. Viene prima cercato FindValue. Se non esiste, viene cercato FindValueSecondary.|

## <a name="management"></a>gestione
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ImageStoreConnectionString |SecureString | Stringa di connessione per la radice di ImageStore. |
| ImageStoreMinimumTransferBPS | Int, valore predefinito: 1024 |La velocità di trasferimento minima tra il cluster e ImageStore. Questo valore viene usato per determinare il timeout durante l'accesso a ImageStore esterno. Modificarlo solo in caso di latenza elevata tra il cluster e ImageStore per dare più tempo al cluster di scaricare da ImageStore esterno. |
|AzureStorageMaxWorkerThreads | Int, valore predefinito: 25 | Il numero massimo di thread di ruoli di lavoro in parallelo. |
|AzureStorageMaxConnections | Int, valore predefinito: 5000 | Il numero massimo di connessioni simultanee all'archiviazione di Azure. |
|AzureStorageOperationTimeout | Tempo in secondi, valore predefinito: 6000 | Specificare l'intervallo di tempo in secondi. Timeout per il completamento dell'operazione xstore. |
|ImageCachingEnabled | Bool, valore predefinito: true | Questa configurazione consente di abilitare o disabilitare la memorizzazione nella cache. |
|DisableChecksumValidation | Bool, valore predefinito: false | Questa configurazione consente di abilitare o disabilitare la convalida di checksum durante il provisioning dell'applicazione. |
|DisableServerSideCopy | Bool, valore predefinito: false | Questa configurazione consente di abilitare o disabilitare la copia sul lato server del pacchetto dell'applicazione in ImageStore durante il provisioning dell'applicazione. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ConsiderWarningAsError |Bool, valore predefinito: false |Criteri di valutazione dell'integrità del cluster: gli avvisi vengano considerati come errori. |
| MaxPercentUnhealthyNodes | Int, valore predefinito: 0 |Criteri di valutazione dell'integrità del cluster: la percentuale massima di nodi non integri consentita per indicare come integro il cluster stesso. |
| MaxPercentUnhealthyApplications | Int, valore predefinito: 0 |Criteri di valutazione dell'integrità del cluster: la percentuale massima di applicazioni non integre consentita per indicare come integro il cluster stesso. |
|MaxPercentDeltaUnhealthyNodes | Int, valore predefinito: 10 |Criteri di valutazione dell'integrità dell'aggiornamento del cluster: la percentuale massima di nodi non integri consentita per indicare come integro il cluster stesso. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes | Int, valore predefinito: 15 |Criteri di valutazione dell'integrità dell'aggiornamento del cluster: la percentuale massima di nodi non integri consentita in un dominio di aggiornamento per indicare come integro il cluster stesso.|

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, valore predefinito: 0 |NOT_PLATFORM_UNIX_START TargetReplicaSetSize per FaultAnalysisService. |
| MinReplicaSetSize |Int, valore predefinito: 0 |MinReplicaSetSize per FaultAnalysisService. |
| ReplicaRestartWaitDuration |Tempo in secondi, valore predefinito: 60 minuti|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per FaultAnalysisService. |
| QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per FaultAnalysisService. |
| StandByReplicaKeepDuration| Tempo in secondi, valore predefinito: (60*24*7) minuti |Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per FaultAnalysisService. |
| PlacementConstraints | stringa, il valore predefinito è ""| PlacementConstraints per FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Int, valore predefinito: 3600 |Indica la frequenza di pulizia dell'archivio.  Verranno rimosse solo le azioni in stato terminale e completate da un numero di secondi pari almeno a CompletedActionKeepDurationInSeconds. |
| CompletedActionKeepDurationInSeconds | Int, valore predefinito: 604800 | Tempo di conservazione approssimativo delle azioni in stato terminale.  Dipende inoltre StoredActionCleanupIntervalInSeconds, poiché l'operazione di pulizia è eseguita solo in quell'intervallo. 604800 equivale a 7 giorni. |
| StoredChaosEventCleanupIntervalInSeconds | Int, valore predefinito: 3600 |Frequenza con cui l'archivio verrà controllato per eseguire eventualmente la pulizia. Se il numero di eventi è superiore a 30.000, la pulizia verrà avviata. |

## <a name="filestoreservice"></a>FileStoreService
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| NamingOperationTimeout |Tempo in secondi, il valore predefinito è 60 |Specificare l'intervallo di tempo in secondi. Il timeout per eseguire un'operazione di denominazione. |
| QueryOperationTimeout | Tempo in secondi, il valore predefinito è 60 |Specificare l'intervallo di tempo in secondi. Il timeout per eseguire un'operazione di query. |
| MaxCopyOperationThreads | Uint, valore predefinito: 0 | Il numero massimo di file paralleli che il Replicator secondario può copiare da quello primario. '0' == numero di memorie centrali. |
| MaxFileOperationThreads | Uint, valore predefinito: 100 | Numero massimo di thread paralleli che possono eseguire FileOperations (copia o spostamento) nel Replicator primario. '0' == numero di memorie centrali. |
| MaxStoreOperations | Uint, valore predefinito: 4096 |Numero massimo di transazioni di archivi paralleli eseguibili nel Replicaotr primario. '0' == numero di memorie centrali. |
| MaxRequestProcessingThreads | Uint, valore predefinito: 200 |Numero massimo di thread paralleli che possono elaborare richieste nel Replicator primario. '0' == numero di memorie centrali. |
| MaxSecondaryFileCopyFailureThreshold | Uint, valore predefinito: 25| Numero massimo di tentativi di copia di file nel Replicator secondario prima di interrompere l'operazione. |
| AnonymousAccessEnabled | Bool, valore predefinito: true |Abilita o disabilita l'accesso anonimo alle condivisioni di FileStoreService. |
| PrimaryAccountType | stringa, il valore predefinito è "" |L'AccountType primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| PrimaryAccountUserName | stringa, il valore predefinito è "" |Il nome utente dell'account primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| PrimaryAccountUserPassword | SecureString, valore predefinito: vuoto |La password dell'account primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| FileStoreService | PrimaryAccountNTLMPasswordSecret | SecureString, valore predefinito: vuoto | Il segreto della password usato come valore di inizializzazione per generare la stessa password quando si usa l'autenticazione NTLM. |
| PrimaryAccountNTLMX509StoreLocation | stringa, il valore predefinito è "LocalMachine"| Il percorso dell'archivio del certificato X509 usato per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| PrimaryAccountNTLMX509StoreName | stringa, il valore predefinito è "MY"| Il nome dell'archivio del certificato X509 usato per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| PrimaryAccountNTLMX509Thumbprint | stringa, il valore predefinito è ""|L'identificazione personale dell'archivio del certificato X509 usato per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| SecondaryAccountType | stringa, il valore predefinito è ""| L'AccountType secondario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| SecondaryAccountUserName | stringa, il valore predefinito è ""| Il nome utente dell'account secondario dell'entità per l'ACL delle condivisioni di FileStoreService. |
| SecondaryAccountUserPassword | SecureString, valore predefinito: vuoto |La password dell'account secondario dell'entità per l'ACL delle condivisioni di FileStoreService.  |
| SecondaryAccountNTLMPasswordSecret | SecureString, valore predefinito: vuoto | Il segreto della password usato come valore di inizializzazione per generare la stessa password quando si usa l'autenticazione NTLM. |
| SecondaryAccountNTLMX509StoreLocation | stringa, il valore predefinito è "LocalMachine" |Il percorso dell'archivio del certificato X509 usato per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| SecondaryAccountNTLMX509StoreName | stringa, il valore predefinito è "MY" |Il nome dell'archivio del certificato X509 usato per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
| SecondaryAccountNTLMX509Thumbprint | stringa, il valore predefinito è ""| L'identificazione personale dell'archivio del certificato X509 usato per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| Enabled |Bool, valore predefinito: false |Flag Enabled per ImageStoreService. |
| TargetReplicaSetSize | Int, valore predefinito: 7 |TargetReplicaSetSize per ImageStoreService. |
| MinReplicaSetSize | Int, valore predefinito: 3 |MinReplicaSetSize per ImageStoreService. |
| ReplicaRestartWaitDuration | Tempo in secondi, valore predefinito: 60,0 * 30 | Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per ImageStoreService. |
| QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue | Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per ImageStoreService. |
| StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: è 3600.0 * 2 | Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per ImageStoreService. |
| PlacementConstraints | stringa, il valore predefinito è "" | PlacementConstraints per ImageStoreService. |
| ClientUploadTimeout | Tempo in secondi, valore predefinito: 1800 |Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di caricamento principale al servizio di archivio immagini. |
| ClientCopyTimeout | Tempo in secondi, valore predefinito: 1800 | Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di copia principale al servizio di archivio immagini. |
| ClientDownloadTimeout | Tempo in secondi, valore predefinito: 1800 | Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di download principale al servizio di archivio immagini |
| ClientListTimeout | Tempo in secondi, valore predefinito: 600 | Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di elenco principale al servizio di archivio immagini. |
| ClientDefaultTimeout | Tempo in secondi, valore predefinito: 180 | Specificare l'intervallo di tempo in secondi. Valore di timeout per tutte le richieste non di caricamento o download (ad esempio richieste exists o di eliminazione) al servizio di archivio immagini. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ClientUploadTimeout |Tempo in secondi, valore predefinito: 1800 | Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di caricamento principale al servizio di archivio immagini. |
| ClientCopyTimeout | Tempo in secondi, valore predefinito: 1800 | Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di copia principale al servizio di archivio immagini. |
|ClientDownloadTimeout | Tempo in secondi, valore predefinito: 1800 | Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di download principale al servizio di archivio immagini. |
|ClientListTimeout | Tempo in secondi, valore predefinito: 600 |Specificare l'intervallo di tempo in secondi. Valore di timeout per la richiesta di elenco principale al servizio di archivio immagini. |
|ClientDefaultTimeout | Tempo in secondi, valore predefinito: 180 | Specificare l'intervallo di tempo in secondi. Valore di timeout per tutte le richieste non di caricamento o download (ad esempio richieste exists o di eliminazione) al servizio di archivio immagini. |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| Providers |stringa, il valore predefinito è "DSTS" |Elenco separato da virgole dei provider di convalida dei token per l'abilitazione. I provider validi sono: DSTS, AAD. Attualmente è possibile abilitare un singolo provider alla volta. |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, valore predefinito: 0 |TargetReplicaSetSize per UpgradeOrchestrationService. |
| MinReplicaSetSize |Int, valore predefinito: 0 | MinReplicaSetSize per UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Tempo in secondi, valore predefinito: 60 minuti| Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue | Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: 60*24*7 minuti | Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per UpgradeOrchestrationService. |
| PlacementConstraints | stringa, il valore predefinito è "" | PlacementConstraints per UpgradeOrchestrationService. |
| AutoupgradeEnabled | Bool, valore predefinito: true | Polling e aggiornamenti automatici in base a un file di stato obiettivo. |
| UpgradeApprovalRequired | Bool, valore predefinito: false | Impostazione per richiedere l'approvazione di un amministratore prima di procedere con l'aggiornamento del codice. |

## <a name="upgradeservice"></a>UpgradeService
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| PlacementConstraints |stringa, il valore predefinito è "" |PlacementConstraints per UpgradeService. |
| TargetReplicaSetSize | Int, valore predefinito: 3 | TargetReplicaSetSize per UpgradeService. |
| MinReplicaSetSize | Int, valore predefinito: 2 | MinReplicaSetSize per UpgradeService. |
| CoordinatorType | stringa, il valore predefinito è "WUTest"| CoordinatorType per UpgradeService. |
| BaseUrl | stringa, il valore predefinito è "" |BaseUrl per UpgradeService. |
| ClusterId | stringa, il valore predefinito è "" | ClusterId per UpgradeService. |
| X509StoreName | stringa, il valore predefinito è "My"| X509StoreName per UpgradeService. |
| X509StoreLocation | stringa, il valore predefinito è "" | X509StoreLocation per UpgradeService. |
| X509FindType | stringa, il valore predefinito è ""| X509FindType per UpgradeService. |
| X509FindValue | stringa, il valore predefinito è "" | X509FindValue per UpgradeService. |
| X509SecondaryFindValue | stringa, il valore predefinito è "" | X509SecondaryFindValue per UpgradeService. |
| OnlyBaseUpgrade | Bool, valore predefinito: false | OnlyBaseUpgrade per UpgradeService. |
| TestCabFolder | stringa, il valore predefinito è "" | TestCabFolder per UpgradeService. |

## <a name="securityclientaccess"></a>Security/ClientAccess
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| CreateName |stringa, il valore predefinito è "Admin" |Configurazione di sicurezza per creare un URI di denominazione. |
| DeleteName |stringa, il valore predefinito è "Admin" |Configurazione di sicurezza per eliminare un URI di denominazione. |
| PropertyWriteBatch |stringa, il valore predefinito è "Admin" |Configurazione di sicurezza per le operazioni di scrittura di proprietà di denominazione. |
| CreateService |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per creare un servizio. |
| CreateServiceFromTemplate |stringa, il valore predefinito è "Admin" |Configurazione di sicurezza per creare un servizio da un modello. |
| UpdateService |stringa, il valore predefinito è "Admin" |Configurazione di sicurezza per gli aggiornamenti dei servizi. |
| DeleteService  |stringa, il valore predefinito è "Admin" |Configurazione di sicurezza per eliminare un servizio. |
| ProvisionApplicationType |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per il provisioning dei tipi di applicazione. |
| CreateApplication |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per creare un'applicazione. |
| DeleteApplication |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per eliminare un'applicazione. |
| UpgradeApplication |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per avviare o interrompere gli aggiornamenti dell'applicazione. |
| RollbackApplicationUpgrade |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per eseguire il rollback degli aggiornamenti dell'applicazione. |
| UnprovisionApplicationType |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per annullare il provisioning dei tipi di applicazione. |
| MoveNextUpgradeDomain |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per riprendere gli aggiornamenti dell'applicazione con un dominio di aggiornamento esplicito. |
| ReportUpgradeHealth |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per riprendere gli aggiornamenti dell'applicazione con lo stato di avanzamento corrente. |
| ReportHealth |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per i report di integrità. |
| ProvisionFabric |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per il provisioning del manifesto del cluster e/o del file con estensione msi. |
| UpgradeFabric |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per avviare gli aggiornamenti del cluster. |
| RollbackFabricUpgrade |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per il rollback degli aggiornamenti del cluster. |
| UnprovisionFabric |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per l'annullamento del provisioning del manifesto del cluster e/o del file con estensione msi. |
| MoveNextFabricUpgradeDomain |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per riprendere gli aggiornamenti del cluster con un dominio di aggiornamento esplicito. |
| ReportFabricUpgradeHealth |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per riprendere gli aggiornamenti del cluster con lo stato di avanzamento corrente. |
| StartInfrastructureTask |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per avviare le attività di infrastruttura. |
| FinishInfrastructureTask |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per terminare le attività di infrastruttura. |
| ActivateNode |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per attivare un nodo. |
| DeactivateNode |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per disattivare un nodo. |
| DeactivateNodesBatch |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per disattivare più nodi. |
| RemoveNodeDeactivations |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per annullare la disattivazione di più nodi. |
| GetNodeDeactivationStatus |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per controllare lo stato di disattivazione. |
| NodeStateRemoved |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per i report sulla rimozione dello stato di un nodo. |
| RecoverPartition |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per ripristinare una partizione. |
| RecoverPartitions |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per ripristinare più partizioni. |
| RecoverServicePartitions |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per ripristinare partizioni di servizio. |
| RecoverSystemPartitions |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per ripristinare partizioni di servizio di sistema. |
| ReportFault |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per i report di errori. |
| InvokeInfrastructureCommand |stringa, il valore predefinito è "Admin" | Configurazione di protezione per i comandi di gestione delle attività di infrastruttura. |
| FileContent |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per il trasferimento del file del client dell'archivio immagini (esterno al cluster). |
| FileDownload |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per l'avvio del download del file del client dell'archivio immagini (esterno al cluster). |
| InternalList |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per l'operazione di elenco del file del client dell'archivio immagini (interno). |
| Elimina |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per l'operazione di eliminazione del client dell'archivio immagini. |
| Carica |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per l'operazione di copia del client dell'archivio immagini. |
| GetStagingLocation |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per il recupero del percorso di gestione temporanea del client dell'archivio immagini. |
| GetStoreLocation |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per il recupero del percorso dell'archivio del client dell'archivio immagini. |
| NodeControl |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per avviare, arrestare e riavviare i nodi. |
| CodePackageControl |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per riavviare i pacchetti di codice. |
| UnreliableTransportControl |stringa, il valore predefinito è "Admin" | Trasporto non affidabile per l'aggiunta e la rimozione di comportamenti. |
| MoveReplicaControl |stringa, il valore predefinito è "Admin" | Spostamento di repliche. |
| PredeployPackageToNode |stringa, il valore predefinito è "Admin" | API per la pre-distribuzione. |
| StartPartitionDataLoss |stringa, il valore predefinito è "Admin" | Provoca la perdita di dati in una partizione. |
| StartPartitionQuorumLoss |stringa, il valore predefinito è "Admin" | Provoca la perdita di quorum in una partizione. |
| StartPartitionRestart |stringa, il valore predefinito è "Admin" | Riavvia contemporaneamente alcune o tutte le repliche di una partizione. |
| CancelTestCommand |stringa, il valore predefinito è "Admin" | Annulla un TestCommand specifico, se è in corso. |
| StartChaos |stringa, il valore predefinito è "Admin" | Avvia Chaos, se non è già avviato. |
| StopChaos |stringa, il valore predefinito è "Admin" | Arresta Chaos, se è stato avviato. |
| StartNodeTransition |stringa, il valore predefinito è "Admin" | Configurazione di sicurezza per avviare la transizione di un nodo. |
| StartClusterConfigurationUpgrade |stringa, il valore predefinito è "Admin" | Provoca StartClusterConfigurationUpgrade in una partizione. |
| GetUpgradesPendingApproval |stringa, il valore predefinito è "Admin" | Provoca GetUpgradesPendingApproval in una partizione. |
| StartApprovedUpgrades |stringa, il valore predefinito è "Admin" | Provoca StartApprovedUpgrades in una partizione. |
| Ping |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per i ping di client. |
| Query |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per le query. |
| NameExists |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per i controlli sulla presenza di URI di denominazione. |
| EnumerateSubnames |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per l'enumerazione degli URI di denominazione. |
| EnumerateProperties |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per l'enumerazione delle proprietà di denominazione. |
| PropertyReadBatch |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per le operazioni di lettura delle proprietà di denominazione. |
| GetServiceDescription |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per le notifiche del servizio di long polling e lettura delle descrizioni dei servizi. |
| ResolveService |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per la risoluzione di servizi in base a reclami. |
| ResolveNameOwner |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per la risoluzione dei proprietari degli URI di denominazione. |
| ResolvePartition |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per la risoluzione dei servizi di sistema. |
| ServiceNotifications |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per le notifiche di servizi basati su eventi. |
| PrefixResolveService |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per la risoluzione dei prefissi di basati sui reclami. |
| GetUpgradeStatus |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per il polling dello stato degli aggiornamenti dell'applicazione. |
| GetFabricUpgradeStatus |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per il polling dello stato degli aggiornamenti del cluster. |
| InvokeInfrastructureQuery |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per eseguire query sulle attività di infrastruttura. |
| Elenco |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per l'operazione di elenco del file del client dell'archivio immagini. |
| ResetPartitionLoad |stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per reimpostare il carico per failoverUnit. |
| ToggleVerboseServicePlacementHealthReporting | stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per attivare o disattivare ServicePlacement HealthReporting dettagliati. |
| GetPartitionDataLossProgress | stringa, il valore predefinito è "Admin\|\|User" | Recupera lo stato di avanzamento per una chiamata API di richiamo di perdita dei dati. |
| GetPartitionQuorumLossProgress | stringa, il valore predefinito è "Admin\|\|User" | Recupera lo stato di avanzamento per una chiamata API di richiamo di perdita del quorum. |
| GetPartitionRestartProgress | stringa, il valore predefinito è "Admin\|\|User" | Recupera lo stato di avanzamento di una chiamata API di riavvio di una partizione. |
| GetChaosReport | stringa, il valore predefinito è "Admin\|\|User" | Recupera lo stato di Chaos all'interno di un intervallo di tempo specificato. |
| GetNodeTransitionProgress | stringa, il valore predefinito è "Admin\|\|User" | Configurazione di sicurezza per recuperare lo stato di avanzamento di un comando di transizione nodo. |
| GetClusterConfigurationUpgradeStatus | stringa, il valore predefinito è "Admin\|\|User" | Provoca GetClusterConfigurationUpgradeStatus in una partizione. |
| GetClusterConfiguration | stringa, il valore predefinito è "Admin\|\|User" | Provoca GetClusterConfiguration in una partizione. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 |Specificare l'intervallo di tempo in secondi. La durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura. |
| ServiceApiHealthDuration | Tempo in secondi, valore predefinito: 30 minuti | Specificare l'intervallo di tempo in secondi. ServiceApiHealthDuration definisce l'attesa massima per l'esecuzione di un'API prima che questa venga indicata come non integra. |
| ServiceReconfigurationApiHealthDuration | Tempo in secondi, il valore predefinito è 30 | Specificare l'intervallo di tempo in secondi. ServiceReconfigurationApiHealthDuration definisce l'attesa massima prima che un servizio in riconfigurazione venga indicato come non integro. |
| PeriodicApiSlowTraceInterval | Tempo in secondi, valore predefinito: 5 minuti | Specificare l'intervallo di tempo in secondi. PeriodicApiSlowTraceInterval definisce l'intervallo in cui le chiamate API lente verranno ritracciate dal monitoraggio API. |
| NodeDeactivationMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 | Specificare l'intervallo di tempo in secondi. Il tempo massimo di attesa prima dell'arresto di un host del servizio che blocca la disattivazione di un nodo. |
| FabricUpgradeMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 | Specificare l'intervallo di tempo in secondi. L'attesa massima da parte di RA prima di terminare l'host di servizio di una replica che non riesce a chiudersi correttamente. |
| IsDeactivationInfoEnabled | Bool, valore predefinito: true | Determina se RA userà le informazioni di disattivazione per eseguire nuovamente l'elezione primaria. Per i nuovi cluster, si consiglia di impostare la configurazione su true. Per i cluster esistenti e in aggiornamento, vedere le note di rilascio per altri dettagli su questa configurazione. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| TraceCRMReasons |Bool, valore predefinito: true |Specifica se tracciare i motivi dei movimenti indicati da CRM al canale degli eventi operativi. |
| ValidatePlacementConstraint | Bool, valore predefinito: true | Specifica se l'espressione PlacementConstraint per un servizio viene convalidata quando viene aggiornato il parametro ServiceDescription di un servizio. |
| PlacementConstraintValidationCacheSize | Int, valore predefinito: 10000 | Limita le dimensioni della tabella usata per la convalida e la memorizzazione nella cache rapide delle espressioni dei vincoli di posizionamento. |
|VerboseHealthReportLimit | Int, valore predefinito: 20 | Definisce il numero di posizionamenti non riusciti di una replica prima di inviare un avviso di integrità a riguardo (se è abilitata la creazione di report di integrità dettagliati). |
|ConstraintViolationHealthReportLimit | Int, valore predefinito: 50 | Definisce il numero di violazioni di vincoli di tempo non risolte da parte di una replica prima di eseguire la diagnostica e di creare report di integrità a riguardo. |
|DetailedConstraintViolationHealthReportLimit | Int, valore predefinito: 200 | Definisce il numero di violazioni di vincoli di tempo non risolte da parte di una replica prima di eseguire la diagnostica e di creare report di integrità dettagliati a riguardo. |
|DetailedVerboseHealthReportLimit | Int, valore predefinito: 200 | Definisce il numero di posizionamenti errati continui di una replica prima di creare report di integrità dettagliati. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, valore predefinito: 20 | Definisce il numero di volte consecutive in cui i movimenti inviati da ResourceBalancer devono essere eliminati prima di eseguire la diagnostica e creare avvisi di integrità. Valore negativo: non vengono emessi avvisi. |
|DetailedNodeListLimit | Int, valore predefinito: 15 | Definisce il numero di nodi per vincolo da includere prima del troncamento nei report sulle repliche non spostate. |
|DetailedPartitionListLimit | Int, valore predefinito: 15 | Definisce il numero di partizioni per ciascuna voce diagnostica di un vincolo da includere prima del troncamento della diagnostica. |
|DetailedDiagnosticsInfoListLimit | Int, valore predefinito: 15 | Definisce il numero di voci di diagnostica di un vincolo (con informazioni dettagliate) da includere prima del troncamento della diagnostica.|
|PLBRefreshGap | Tempo in secondi, valore predefinito: 1 | Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che PLB aggiorni lo stato. |
|MinPlacementInterval | Tempo in secondi, valore predefinito: 1 | Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di posizionamento consecutivi. |
|MinConstraintCheckInterval | Tempo in secondi, valore predefinito: 1 | Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di controllo dei vincoli consecutivi. |
|MinLoadBalancingInterval | Tempo in secondi, valore predefinito: 5 | Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di bilanciamento consecutivi. |
|BalancingDelayAfterNodeDown | Tempo in secondi, valore predefinito: 120 |Specificare l'intervallo di tempo in secondi. Non avviare attività di bilanciamento in questo periodo se un nodo va offline. |
|BalancingDelayAfterNewNode | Tempo in secondi, valore predefinito: 120 |Specificare l'intervallo di tempo in secondi. Non avviare attività di bilanciamento in questo periodo dopo aver aggiunto un nuovo nodo. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo in secondi, valore predefinito: 120 | Specificare l'intervallo di tempo in secondi. Non risolvere violazioni di vincoli FaultDomain e UpgradeDomain in questo periodo dopo che un nodo va offline. |
|ConstraintFixPartialDelayAfterNewNode | Tempo in secondi, valore predefinito: 120 | Specificare l'intervallo di tempo in secondi. Non risolvere violazioni di vincoli FaultDomain e UpgradeDomain in questo periodo dopo aver aggiunto un nuovo nodo. |
|GlobalMovementThrottleThreshold | Uint, valore predefinito: 1000 | Numero massimo di movimenti consentiti nella fase di bilanciamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Uint, valore predefinito: 0 | Numero massimo di movimenti consentiti nella fase di posizionamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti.|
|GlobalMovementThrottleThresholdForBalancing | Uint, valore predefinito: 0 | Numero massimo di movimenti consentiti nella fase di bilanciamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti. |
|GlobalMovementThrottleCountingInterval | Tempo in secondi, valore predefinito: 600 | Specificare l'intervallo di tempo in secondi. Indicare la lunghezza dell'intervallo precedente per cui si desidera tenere traccia dei movimenti di replica di dominio. Usato insieme a GlobalMovementThrottleThreshold. È possibile impostarlo su 0 per ignorare del tutto la limitazione globale. |
|MovementPerPartitionThrottleThreshold | Uint, valore predefinito: 50 | Non avverranno movimenti legati a bilanciamenti per una partizione se il numero di tali movimenti per le repliche della partizione ha raggiunto o superato MovementPerFailoverUnitThrottleThreshold nell'intervallo precedente indicato in MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Tempo in secondi, valore predefinito: 600 | Specificare l'intervallo di tempo in secondi. Indicare la lunghezza dell'intervallo precedente per cui si desidera tenere traccia dei movimenti di replica per ciascuna partizione. Usato insieme a MovementPerPartitionThrottleThreshold. |
|PlacementSearchTimeout | Tempo in secondi, valore predefinito: 0.5 | Specificare l'intervallo di tempo in secondi. Tempo massimo di ricerca durante il posizionamento dei servizi prima di restituire un risultato. |
|UseMoveCostReports | Bool, valore predefinito: false | Indica al servizio di bilanciamento del carico di ignorare l'elemento di costo della funzione di assegnazione dei punteggio. Comporterà potenzialmente un numero elevato di spostamenti per un posizionamento più bilanciato. |
|PreventTransientOvercommit | Bool, valore predefinito: false | Determina se PLB deve conteggiare immediatamente le risorse che saranno liberate dagli spostamenti avviati. Per impostazione predefinita, PLB può avviare gli spostamenti in entrata e in uscita nello stesso nodo che può creare un overcommit temporaneo. Impostando il parametro su true sarà possibile prevenire overcommit simili e disabilitare le deframmentazioni su richiesta, note anche come placementWithMove. |
|InBuildThrottlingEnabled | Bool, valore predefinito: false | Determina se la limitazione in build è abilitata. |
|InBuildThrottlingAssociatedMetric | stringa, il valore predefinito è "" | Il nome della metrica associato a questa limitazione. |
|InBuildThrottlingGlobalMaxValue | Int, valore predefinito: 0 |Il numero massimo di repliche in build consentite a livello globale. |
|SwapPrimaryThrottlingEnabled | Bool, valore predefinito: false| Determina se la limitazione swap-primary è abilitata. |
|SwapPrimaryThrottlingAssociatedMetric | stringa, il valore predefinito è ""| Il nome della metrica associato a questa limitazione. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, valore predefinito: 0 | Il numero massimo di repliche swap-primary consentite a livello globale. |
|PlacementConstraintPriority | Int, valore predefinito: 0 | Determina la priorità del vincolo di posizionamento: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|PreferredLocationConstraintPriority | Int, valore predefinito: 2| Determina la priorità del vincolo di posizionamento preferito: 0 indica priorità elevata, 1 indica priorità minore; 2 indica ottimizzazione e un numero negativo indica "ignorare" |
|CapacityConstraintPriority | Int, valore predefinito: 0 | Determina la priorità del vincolo di capacità: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|AffinityConstraintPriority | Int, valore predefinito: 0 | Determina la priorità del vincolo di affinità: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|FaultDomainConstraintPriority | Int, valore predefinito: 0 | Determina la priorità del vincolo di dominio di errore: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|UpgradeDomainConstraintPriority | Int, valore predefinito: 1| Determina la priorità del vincolo di dominio di aggiornamento: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|ScaleoutCountConstraintPriority | Int, valore predefinito: 0 | Determina la priorità del vincolo del conteggio di scalabilità orizzontale: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|ApplicationCapacityConstraintPriority | Int, valore predefinito: 0 | Determina la priorità del vincolo di capacità: 0: priorità elevata; 1: priorità minore; numero negativo: ignorare. |
|MoveParentToFixAffinityViolation | Bool, valore predefinito: false | Impostazione che determina se le repliche padre possono essere spostate per correggere i vincoli di affinità.|
|MoveExistingReplicaForPlacement | Bool, valore predefinito: true |Impostazione che determina se spostare le repliche esistenti durante il posizionamento. |
|UseSeparateSecondaryLoad | Bool, valore predefinito: true | Impostazione che determina se usare un carico secondario differente. |
|PlaceChildWithoutParent | Bool, valore predefinito: true | Impostazione che determina se è possibile posizionare una replica di servizio figlia in caso non sia presente una replica padre. |
|PartiallyPlaceServices | Bool, valore predefinito: true | Determina se tutte le repliche servizio nel cluster verranno posizionate in modo "tutto o niente" in caso di nodi appropriati limitati.|
|InterruptBalancingForAllFailoverUnitUpdates | Bool, valore predefinito: false | Determina se qualsiasi tipo di aggiornamento di un'unità di failover deve interrompere un'esecuzione di un bilanciamento rapido o lento. Specificare "false" per interrompere l'esecuzione del bilanciamento se FailoverUnit: viene creata/eliminata; ha repliche mancanti; ha modificato il percorso di replica primario o il numero di repliche. L'esecuzione del bilanciamento NON verrà interrotta in altri casi, ossia se FailoverUnit: ha repliche extra; ha modificato flag della replica; ha modificato solo la versione della partizione e tutti gli altri casi. |

## <a name="security"></a>Sicurezza
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ClusterProtectionLevel |Nessuno o EncryptAndSign |Nessuno (predefinito) per i cluster non protetti, EncryptAndSign per i cluster protetti. |

## <a name="hosting"></a>Hosting
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ServiceTypeRegistrationTimeout |Tempo in secondi, il valore predefinito è 300 |Tempo massimo consentito per la registrazione di ServiceType nell'infrastruttura |
| ServiceTypeDisableFailureThreshold |Numero intero, il valore predefinito è 1 |Si tratta della soglia per il conteggio degli errori superata la quale a FailoverManager (FM) viene notificato di disabilitare il tipo di servizio sul nodo e di provare il posizionamento su un altro nodo. |
| ActivationRetryBackoffInterval |Tempo in secondi, il valore predefinito è 5 |Intervallo di backoff per ogni errore di attivazione; in caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in MaxActivationFailureCount. L'intervallo tra tentativi è il prodotto dell'errore di attivazione continua e dell'intervallo di backoff di attivazione. |
| ActivationMaxRetryInterval |Tempo in secondi, il valore predefinito è 300 |In ogni caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in ActivationMaxFailureCount. ActivationMaxRetryInterval specifica l'intervallo di tempo di attesa prima di un nuovo tentativo dopo ogni errore di attivazione |
| ActivationMaxFailureCount |Numero intero, il valore predefinito è 10 |Numero di volte che il sistema ritenta l'attivazione non riuscita prima di rinunciare |

## <a name="failovermanager"></a>FailoverManager
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| PeriodicLoadPersistInterval |Tempo in secondi, il valore predefinito è 10 |Determina la frequenza con cui FM controlla i nuovi report di carico |

## <a name="federation"></a>Federazione
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| LeaseDuration |Tempo in secondi, il valore predefinito è 30 |Durata di un lease tra un nodo e gli elementi adiacenti. |
| LeaseDurationAcrossFaultDomain |Tempo in secondi, il valore predefinito è 30 |Durata di un lease tra un nodo e gli elementi adiacenti nei domini di errore. |

## <a name="clustermanager"></a>ClusterManager
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| UpgradeStatusPollInterval |Tempo in secondi, il valore predefinito è 60 |Frequenza di polling sullo stato di aggiornamento dell'applicazione. Questo valore determina la frequenza di aggiornamento di qualsiasi chiamata GetApplicationUpgradeProgress |
| UpgradeHealthCheckInterval |Tempo in secondi, il valore predefinito è 60 |Frequenza dei controlli sullo stato di integrità durante gli aggiornamenti di un'applicazione monitorata |
| FabricUpgradeStatusPollInterval |Tempo in secondi, il valore predefinito è 60 |Frequenza di polling dello stato di aggiornamento dell'infrastruttura. Questo valore determina la frequenza di aggiornamento di qualsiasi chiamata GetFabricUpgradeProgress |
| FabricUpgradeHealthCheckInterval |Tempo in secondi, il valore predefinito è 60 |Frequenza dei controlli sullo stato di integrità durante un aggiornamento di un'infrastruttura monitorata |
|InfrastructureTaskProcessingInterval | Tempo in secondi, il valore predefinito è 10 |Specificare l'intervallo di tempo in secondi. L'intervallo di elaborazione usato dal computer dello stato di elaborazione dell'attività di infrastruttura. |
|TargetReplicaSetSize |Int, valore predefinito: 7 |TargetReplicaSetSize per ClusterManager. |
|MinReplicaSetSize |Int, valore predefinito: 3 |MinReplicaSetSize per ClusterManager. |
|ReplicaRestartWaitDuration |Tempo in secondi, valore predefinito: (60.0 * 30)|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per ClusterManager. |
|QuorumLossWaitDuration |Tempo in secondi, valore predefinito: MaxValue | Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per ClusterManager. |
|StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: (3600.0 * 2)|Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per ClusterManager. |
|PlacementConstraints | stringa, il valore predefinito è "" |PlacementConstraints per ClusterManager. |
|SkipRollbackUpdateDefaultService | Bool, valore predefinito: false |CM non riporterà allo stato precedente i servizi predefiniti aggiornati durante il rollback dell'aggiornamento dell'applicazione. |
|EnableDefaultServicesUpgrade | Bool, valore predefinito: false |Abilita l'aggiornamento dei servizi predefiniti durante l'aggiornamento dell'applicazione. Le descrizioni dei servizi predefiniti vengono sovrascritte in seguito all'aggiornamento. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo in secondi, valore predefinito: 0| Specificare l'intervallo di tempo in secondi. Il tempo di attesa prima di avviare controlli di integrità dopo la post-elaborazione di un'attività di infrastruttura. |
|InfrastructureTaskHealthCheckStableDuration | Tempo in secondi, valore predefinito: 0| Specificare l'intervallo di tempo in secondi. Il tempo in cui osservare controlli di integrità positivi e consecutivi prima del completamento positivo della post-elaborazione di un'attività di infrastruttura. In caso di un controllo di integrità negativo, il timer verrà reimpostato. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo in secondi, il valore predefinito è 60 |Specificare l'intervallo di tempo in secondi. Il tempo impiegato per eseguire nuovamente i controlli di integrità negativi durante la post-elaborazione di un'attività di infrastruttura. In caso di un controllo di integrità positivo, il timer verrà reimpostato. |
|ImageBuilderTimeoutBuffer |Tempo in secondi, valore predefinito: 3 |Specificare l'intervallo di tempo in secondi. Il tempo per consentire la restituzione al client degli errori di timeout specifici di Image Builder. Se il buffer è troppo piccolo, il client giunge a un timeout prima del server e riceve un errore di timeout generico. |
|MinOperationTimeout | Tempo in secondi, il valore predefinito è 60 |Specificare l'intervallo di tempo in secondi. Il timeout minimo globale per l'elaborazione interna delle operazioni in ClusterManager. |
|MaxOperationTimeout |Tempo in secondi, valore predefinito: MaxValue | Specificare l'intervallo di tempo in secondi. Il timeout massimo globale per l'elaborazione interna delle operazioni in ClusterManager. |
|MaxTimeoutRetryBuffer | Tempo in secondi, valore predefinito: 600 |Specificare l'intervallo di tempo in secondi. Il timeout massimo dell'operazione durante i tentativi interni dovuti a timeout è <Original Timeout> + <MaxTimeoutRetryBuffer>. Viene aggiunto timeout aggiuntivo in incrementi di MinOperationTimeout. |
|MaxCommunicationTimeout |Tempo in secondi, valore predefinito: 600 |Specificare l'intervallo di tempo in secondi. Timeout massimo per le comunicazioni interne tra ClusterManager e altri servizi di sistema, ad esempio Naming Service, Gestione failover, ecc. Questo timeout deve essere inferiore a MaxOperationTimeout globale, poiché potrebbero avvenire più comunicazioni tra i componenti di sistema per ciascuna operazione client. |
|MaxDataMigrationTimeout |Tempo in secondi, valore predefinito: 600 |Specificare l'intervallo di tempo in secondi. Timeout massimo per le operazioni di ripristino di migrazione di dati dopo un aggiornamento di Service Fabric. |
|MaxOperationRetryDelay |Tempo in secondi, valore predefinito: 5| Specificare l'intervallo di tempo in secondi. Ritardo massimo per i tentativi interni quando vengono rilevati errori. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo in secondi, valore predefinito: 1200 | Specificare l'intervallo di tempo in secondi. Se ReplicaSetCheckTimeout è impostato sul valore massimo di DWORD, questo verrà sovrascritto dal valore di questa configurazione ai fini del rollback. Il valore usato per rollforward non viene mai sovrascritto. |
|ImageBuilderJobQueueThrottle |Int, valore predefinito: 10 |Limitazione del conteggio dei thread per le code di processi di Image Builder nelle richieste di applicazione. |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla gestione del cluster, leggere questi articoli:

[Aggiunta, rollover e rimozione di certificati dal cluster di Azure ](service-fabric-cluster-security-update-certs-azure.md) 

