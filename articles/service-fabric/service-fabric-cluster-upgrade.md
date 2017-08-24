---
title: Aggiornare un cluster di Azure Service Fabric | Documentazione Microsoft
description: "Aggiornamento del codice di Service Fabric e/o della configurazione eseguita in un cluster di Service Fabric, con impostazione della modalità di aggiornamento del cluster, aggiornamento dei certificati, aggiunta di porte dell'applicazione, applicazione di patch del sistema operativo e così via. Possibili risultati degli aggiornamenti."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 6efb5c20140d3ea76a2a9cd1eb495db69f14048d
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Aggiornare un cluster di Azure Service Fabric
> [!div class="op_single_selector"]
> * [Cluster di Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autonomo](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Per i sistemi attuali la progettazione a livello di aggiornamento è fondamentale per il successo a lungo termine di un prodotto. Un cluster di Azure Service Fabric è una risorsa di proprietà dell'utente parzialmente gestita da Microsoft. Questo articolo descrive ciò che viene gestito automaticamente e ciò che è possibile configurare manualmente.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controllo della versione di Fabric eseguita nel cluster
È possibile impostare il cluster per ricevere gli aggiornamenti automatici di Fabric quando Microsoft rilascia una nuova versione oppure scegliere di selezionare una versione supportata di Fabric da eseguire nel cluster.

A questo scopo, impostare l'opzione di configurazione "upgradeMode" del cluster nel portale oppure usare Resource Manager al momento della creazione o successivamente in un cluster attivo. 

> [!NOTE]
> Verificare che il cluster esegua sempre una versione di Fabric supportata. Quando viene annunciato il rilascio di una nuova versione di Service Fabric, viene segnalato il termine del periodo di supporto per la versione precedente dopo un minimo di 60 giorni da tale data. Le nuove versioni vengono annunciate nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A questo punto è possibile scegliere la nuova versione. 
> 
> 

14 giorni prima della scadenza della versione in esecuzione nel cluster, viene generato un evento di integrità e il cluster passa a uno stato di avviso. Il cluster rimane in stato di avviso fino a quando non viene eseguito l'aggiornamento a una versione di Fabric supportata.

### <a name="setting-the-upgrade-mode-via-portal"></a>Impostazione della modalità di aggiornamento tramite il portale
È possibile impostare l'aggiornamento automatico o manuale durante la creazione del cluster.

![Creazione - Modalità manuale][Create_Manualmode]

È possibile impostare l'aggiornamento automatico o manuale in un cluster attivo con l'esperienza di gestione. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Aggiornamento a una nuova versione in un cluster impostato sulla modalità manuale tramite il portale
Per eseguire l'aggiornamento a una nuova versione, è sufficiente selezionare la versione disponibile nell'elenco a discesa e salvare. L'aggiornamento di Fabric viene avviato automaticamente. Durante l'aggiornamento vengono rispettati i criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster).

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Per altre informazioni su come impostare questi criteri di integrità personalizzati, scorrere questo documento verso il basso. 

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento, seguendo la stessa procedura precedente.

![Gestione - Modalità automatica][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Impostazione della modalità di aggiornamento tramite un modello di Resource Manager
Aggiungere l'opzione di configurazione "upgradeMode" alla definizione della risorsa Microsoft.ServiceFabric/clusters e impostare "clusterCodeVersion" su una delle versioni di Fabric supportate come illustrato di seguito, quindi distribuire il modello. I valori validi per "upgradeMode" sono "Manual" e "Automatic"

![Modalità di aggiornamento tramite Azure Resource Manager][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Aggiornamento a una nuova versione in un cluster impostato sulla modalità manuale tramite un modello di Resource Manager
Quando il cluster è in modalità manuale, per eseguire l'aggiornamento a una nuova versione modificare "clusterCodeVersion" impostando una versione supportata e quindi eseguire la distribuzione. Con la distribuzione del modello, l'aggiornamento di Fabric viene avviato automaticamente. Durante l'aggiornamento vengono rispettati i criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster).

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Per altre informazioni su come impostare questi criteri di integrità personalizzati, scorrere questo documento verso il basso. 

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento, seguendo la stessa procedura precedente.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Ottenere l'elenco di tutte le versioni disponibili per tutti gli ambienti per una determinata sottoscrizione
Eseguendo questo comando si otterrà un output simile al seguente.

"supportExpiryUtc" indica la scadenza di una determinata versione. La versione più recente non ha una data valida, ma un valore "9999-12-31T23:59:59.9999999", che indica che la data di scadenza non è ancora stata impostata.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportamento di aggiornamento di Fabric in caso di cluster con modalità automatica di aggiornamento
Microsoft gestisce il codice dell'infrastruttura e la configurazione eseguita in un cluster di Azure e, in base alle esigenze, esegue aggiornamenti monitorati del software in modo automatico. Gli aggiornamenti possono interessare il codice, la configurazione o entrambi. Per assicurarsi che gli aggiornamenti abbiano un impatto minimo o nullo sull'applicazione, vengono eseguiti nelle fasi descritte di seguito.

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Un aggiornamento viene eseguito usando tutti i criteri di integrità del cluster
In questa fase gli aggiornamenti interessano un dominio di aggiornamento per volta e le applicazioni in esecuzione sul cluster non subiscono tempi di inattività. Durante l'aggiornamento vengono rispettati i criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster).

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Al proprietario della sottoscrizione verrà quindi inviato un messaggio di posta elettronica contenente le informazioni seguenti:

* Notifica che era necessario eseguire il rollback di un aggiornamento del cluster.
* Eventuali azioni correttive suggerite.
* Il numero di giorni (n) prima dell'avvio della fase 2.

Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. Dopo che gli n giorni dalla data di invio del messaggio di posta elettronica sono trascorsi, si passa alla fase 2.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita. in modo da evitare l'invio di troppi messaggi e per far sì che la ricezione di un messaggio rappresenti un'eccezione alla norma. Si prevede che la maggior parte degli aggiornamenti del cluster abbia esito positivo, senza alcun impatto sulla disponibilità dell'applicazione.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Un aggiornamento viene eseguito usando solo i criteri di integrità predefiniti
I criteri di integrità in questa fase vengono impostati in modo che il numero di applicazioni integre all'inizio dell'aggiornamento rimanga invariato per l'intera durata del processo di aggiornamento. Come nella fase 1, in questa fase gli aggiornamenti interessano un dominio di aggiornamento per volta e le applicazioni in esecuzione sul cluster non subiscono tempi di inattività. I criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione sul cluster) vengono rispettati per l'intera durata dell'aggiornamento.

Se i criteri di integrità del cluster in vigore non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Al proprietario della sottoscrizione verrà quindi inviato un messaggio di posta elettronica contenente le informazioni seguenti:

* Notifica che era necessario eseguire il rollback di un aggiornamento del cluster.
* Eventuali azioni correttive suggerite.
* Il numero di giorni (n) prima dell'avvio della fase 3.

Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. Viene inviato un messaggio di sollecito alcuni giorni prima della scadenza degli n giorni. Dopo che gli n giorni dalla data di invio del messaggio di posta elettronica sono trascorsi, si passa alla fase 3. I messaggi di posta elettronica inviati nella fase 2 devono essere tenuti in alta considerazione e devono essere intraprese azioni correttive.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Un aggiornamento viene eseguito usando criteri di integrità aggressivi
Tali criteri di integrità in questa fase sono pensati per il completamento dell'aggiornamento piuttosto che per l'integrità delle applicazioni. In questa fase verranno completati pochi aggiornamenti del cluster. Se il cluster giunge a questa fase, è molto probabile che l'applicazione divenga non integra e/o perda disponibilità.

In modo analogo alle altre due fasi, gli aggiornamenti della fase 3 procedono in base a un dominio di aggiornamento alla volta.

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. A questo punto, il cluster viene bloccato in modo che non riceva più supporto e/o aggiornamenti.

Al proprietario della sottoscrizione viene inviato un messaggio di posta elettronica contenente queste informazioni e le azioni correttive. Se la fase 3 ha esito negativo, si prevede che il cluster non venga impostato su alcun tipo di stato.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

## <a name="cluster-configurations-that-you-control"></a>Configurazioni del cluster controllate manualmente
Oltre a poter impostare la modalità di aggiornamento del cluster, in un cluster attivo è possibile modificare le configurazioni riportate di seguito.

### <a name="certificates"></a>Certificati
È facile aggiungere o eliminare i certificati di cluster e client tramite il portale. Fare riferimento a [questo documento per altre informazioni](service-fabric-cluster-security-update-certs-azure.md)

![Schermata che illustra le identificazioni personali del certificato nel portale di Azure.][CertificateUpgrade]

### <a name="application-ports"></a>Porte dell'applicazione
È possibile modificare le porte dell'applicazione modificando le proprietà della risorsa del servizio di bilanciamento del carico associate al tipo di nodo. A questo scopo, è possibile usare il portale oppure direttamente PowerShell per Gestione risorse.

Per aprire una nuova porta in tutte le VM di un tipo di nodo, seguire questa procedura.

1. Aggiungere un nuovo probe al servizio di bilanciamento del carico appropriato.
   
    Se il cluster è stato distribuito tramite il portale, il bilanciamento del carico verrà denominato "LB-nome del gruppo di risorse-NodeTypename", uno per ogni tipo di nodo. Poiché i nomi del servizio di bilanciamento del carico sono univoci solo nell'ambito di un gruppo di risorse, è consigliabile cercarli in uno specifico gruppo di risorse.
   
    ![Schermata che illustra l'aggiunta di un probe a un servizio di bilanciamento del carico nel portale.][AddingProbes]
2. Aggiungere una nuova regola al servizio di bilanciamento del carico.
   
    Aggiungere una nuova regola allo stesso servizio di bilanciamento del carico usando il probe creato nel passaggio precedente.
   
    ![Aggiunta di una nuova regola a un servizio di bilanciamento del carico nel portale][AddingLBRules]

### <a name="placement-properties"></a>Proprietà di posizionamento
Per ogni tipo di nodo è possibile aggiungere proprietà di posizionamento personalizzate da usare nelle applicazioni. NodeType è una proprietà predefinita che è possibile usare senza aggiungerla in modo esplicito.

> [!NOTE]
> Per informazioni dettagliate sull'uso dei vincoli di posizionamento, le proprietà dei nodi e le relative definizioni, vedere la sezione relativa ai vincoli di posizionamento e proprietà dei nodi nell'articolo di Gestione risorse del cluster di Service Fabric relativo alla [descrizione del cluster](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Metriche della capacità
Per ogni tipo di nodo è possibile aggiungere metriche di capacità personalizzate da usare nelle applicazioni per creare report sul carico. Per informazioni dettagliate sull'uso di metriche di capacità per la segnalazione del carico, vedere i documenti di Gestione risorse del cluster di Service Fabric relativi a [descrizione del cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [metriche e carico](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Impostazioni di aggiornamento di Fabric: criteri di integrità
È possibile specificare criteri di integrità personalizzati per l'aggiornamento di Fabric. Se nel cluster è stato impostato l'aggiornamento automatico di Fabric, questi criteri vengono applicati alla fase 1 degli aggiornamenti automatici di Fabric.
Se nel cluster è stato impostato l'aggiornamento manuale di Fabric, questi criteri vengono applicati ogni volta che si seleziona una nuova versione attivando nel sistema l'avvio dell'aggiornamento di Fabric nel cluster. Se non si esegue l'override dei criteri, vengono usati quelli predefiniti.

È possibile specificare criteri di integrità personalizzati o esaminare le impostazioni correnti nel pannello relativo agli aggiornamenti di Fabric, selezionando le impostazioni di aggiornamento avanzate. La figura seguente illustra questo passaggio. 

![Gestire criteri di integrità personalizzati][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personalizzare le impostazioni di Fabric per il cluster
Per informazioni sulle impostazioni personalizzabili e su come possono essere personalizzate, vedere l'articolo relativo alle [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md) .

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Patch del sistema operativo nelle VM che costituiscono il cluster
Fare riferimento a [Patch Orchestration Application](service-fabric-patch-orchestration-application.md) che può essere distribuito nel cluster per installare le patch di Windows Update in modo orchestrato, mantenendo i servizi sempre disponibili. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Aggiornamenti del sistema operativo nelle VM che costituiscono il cluster
Se è necessario aggiornare l'immagine del sistema operativo nelle macchine virtuali del cluster, eseguire questa operazione su una VM per volta. L'esecuzione dell'aggiornamento è un'operazione manuale, non è attualmente disponibile alcun tipo di automazione.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come personalizzare alcune [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md)
* Informazioni su come [aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-up-down.md)
* Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG

