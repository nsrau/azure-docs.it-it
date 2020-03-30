---
title: Come clonare un hub IoT di AzureHow to clone an Azure IoT hub
description: Come clonare un hub IoT di AzureHow to clone an Azure IoT hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429161"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Come clonare un hub di Azure IoT in un'altra areaHow to clone an Azure IoT hub to another region

Questo articolo illustra i modi per clonare un hub IoT e fornisce alcune domande a cui devi rispondere prima di iniziare. Ecco diversi motivi per cui potresti voler clonare un hub IoT:
 
* Si sta spostando l'azienda da un'area all'altra, ad esempio dall'Europa al Nord America (o viceversa), e si desidera che le risorse e i dati siano geograficamente vicini alla nuova posizione, pertanto è necessario spostare l'hub.

* Si sta configurando un hub per un ambiente di sviluppo e di produzione.

* Si vuole eseguire un'implementazione personalizzata della disponibilità elevata multihub. Per ulteriori informazioni, vedere la sezione Come ottenere disponibilità elevata tra aree di disponibilità elevata e ripristino di [emergenza dell'hub IoT](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Si desidera aumentare il numero di [partizioni](iot-hub-scaling.md#partitions) configurate per l'hub. Questo viene impostato quando si crea l'hub per la prima volta e non può essere modificato. È possibile utilizzare le informazioni contenute in questo articolo per clonare l'hub e quando viene creato il clone, aumentare il numero di partizioni.

Per clonare un hub, è necessaria una sottoscrizione con accesso amministrativo all'hub originale. È possibile inserire il nuovo hub in un nuovo gruppo di risorse e un'area, nella stessa sottoscrizione dell'hub originale o persino in una nuova sottoscrizione. Non è possibile usare lo stesso nome perché il nome dell'hub deve essere univoco a livello globale.

> [!NOTE]
> Al momento, non è disponibile alcuna funzionalità per la clonazione automatica di un hub IoT.At this time, there's no feature available for cloning an IoT hub automatically. È principalmente un processo manuale, e quindi è abbastanza soggetto a errori. La complessità della clonazione di un hub è direttamente proporzionale alla complessità dell'hub. Ad esempio, la clonazione di un hub IoT senza routing dei messaggi è abbastanza semplice. Se si aggiunge il routing dei messaggi come una sola complessità, la clonazione dell'hub diventa almeno un ordine di grandezza più complicato. Se si spostano anche le risorse usate per il routing degli endpoint, è più complesso un altro ordine di ingrandimento. 

## <a name="things-to-consider"></a>Aspetti da considerare

Ci sono diversi aspetti da considerare prima di clonare un hub IoT.There are several things to consider before cloning an IoT hub.

* Assicurarsi che tutte le funzioni disponibili nella posizione originale siano disponibili anche nella nuova posizione. Alcuni servizi sono in anteprima e non tutte le funzionalità sono disponibili ovunque.

* Non rimuovere le risorse originali prima di creare e verificare la versione clonata. Dopo aver rimosso un hub, è andato per sempre e non c'è modo di recuperarlo per controllare le impostazioni o i dati per assicurarsi che l'hub viene replicato correttamente.

* Molte risorse richiedono nomi univoci a livello globale, pertanto è necessario utilizzare nomi diversi per le versioni clonate. È inoltre necessario usare un nome diverso per il gruppo di risorse a cui appartiene l'hub clonato. 

* Non viene eseguita la migrazione dei dati per l'hub IoT originale. Sono inclusi i messaggi di telemetria, i comandi da cloud a dispositivo (C2D) e informazioni correlate al processo, ad esempio pianificazioni e cronologia. Anche le metriche e i risultati della registrazione non vengono migrati. 

* Per i dati o i messaggi instradati ad Archiviazione di Azure, è possibile lasciare i dati nell'account di archiviazione originale, trasferire i dati a un nuovo account di archiviazione nella nuova area o lasciare i dati precedenti sul posto e creare un nuovo account di archiviazione nella nuova posizione per i nuovi dati. Per altre informazioni sullo spostamento dei dati nell'archivio BLOB, vedere [Introduzione a AzCopy.](../storage/common/storage-use-azcopy-v10.md)

* Non è possibile eseguire la migrazione dei dati per gli hub eventi e per gli argomenti e le code del bus di servizio. Si tratta di dati temporizzati e non vengono archiviati dopo l'elaborazione dei messaggi.

* È necessario pianificare i tempi di inattività per la migrazione. La clonazione dei dispositivi nel nuovo hub richiede tempo. Se si utilizza il metodo di importazione/esportazione, i test di benchmark hanno rivelato che potrebbero essere necessarie circa due ore per spostare 500.000 dispositivi e quattro ore per spostare un milione di dispositivi. 

* È possibile copiare i dispositivi nel nuovo hub senza arrestarli o modificarli. 

    * Se il provisioning originale dei dispositivi è stato eseguito tramite DPS, il provisioning di tali dispositivi aggiorna le informazioni di connessione archiviate in ogni dispositivo. 
    
    * In caso contrario, è necessario utilizzare il metodo import/export per spostare i dispositivi e quindi i dispositivi devono essere modificati per usare il nuovo hub. Ad esempio, è possibile configurare il dispositivo in modo che utilizzi il nome host dell'hub IoT dalle proprietà gemelle desiderate. Il dispositivo prenderà il nome host dell'hub IoT, disconnetterà il dispositivo dall'hub precedente e lo riconnetterà a quello nuovo.
    
* È necessario aggiornare tutti i certificati in uso in modo da poterli utilizzare con le nuove risorse. Inoltre, probabilmente l'hub è definito in una tabella DNS da qualche parte - sarà necessario aggiornare le informazioni DNS.

## <a name="methodology"></a>Metodologia

Questo è il metodo generale consigliato per spostare un hub IoT da un'area a un'altra. Per il routing dei messaggi, si presuppone che le risorse non vengano spostate nella nuova area. Per ulteriori informazioni, vedere la [sezione sul routing dei](#how-to-handle-message-routing)messaggi .

   1. Esportare l'hub e le relative impostazioni in un modello di Resource Manager.Export the hub and its settings to a Resource Manager template. 
   
   1. Apportare le modifiche necessarie al modello, ad esempio aggiornando tutte le occorrenze del nome e la posizione dell'hub clonato. Per tutte le risorse nel modello usate per gli endpoint di routing dei messaggi, aggiornare la chiave nel modello per tale risorsa.
   
   1. Importare il modello in un nuovo gruppo di risorse nella nuova posizione. Questo crea il clone.

   1. Eseguire il debug in base alle esigenze. 
   
   1. Aggiungere tutto ciò che non è stato esportato nel modello. 
   
       Ad esempio, i gruppi di consumer non vengono esportati nel modello. È necessario aggiungere manualmente i gruppi di consumer al modello o usare il portale di [Azure](https://portal.azure.com) dopo la creazione dell'hub. Nell'articolo Usare un modello di Azure Resource Manager per configurare il routing dei [messaggi dell'hub IoT](tutorial-routing-config-message-routing-rm-template.md)è disponibile un esempio di aggiunta di un gruppo di consumer a un modello.
       
   1. Copiare i dispositivi dall'hub originale al clone. Questa operazione è trattata nella sezione [Gestione dei dispositivi registrati nell'hub IoT](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Come gestire il routing dei messaggi

Se l'hub usa il [routing personalizzato,](iot-hub-devguide-messages-read-custom.md)l'esportazione del modello per l'hub include la configurazione di routing, ma non include le risorse stesse. È necessario scegliere se spostare le risorse di instradamento nella nuova posizione o lasciarle sul posto e continuare a utilizzarle "così com'è". 

Si supponga, ad esempio, di disporre di un hub negli Stati Uniti occidentali che instrada i messaggi a un account di archiviazione (anche negli Stati Uniti occidentali) e di voler spostare l'hub negli Stati Uniti orientali. È possibile spostare l'hub e fare in modo che instradasse comunque i messaggi all'account di archiviazione negli Stati Uniti occidentali oppure spostare l'hub e anche l'account di archiviazione. Potrebbe esserci un piccolo riscontro in termini di prestazioni dal routing dei messaggi alle risorse endpoint in un'area diversa.

È possibile spostare un hub che usa il routing dei messaggi abbastanza facilmente se non si spostano anche le risorse usate per gli endpoint di routing. 

Se l'hub utilizza il routing dei messaggi, sono disponibili due opzioni. 

1. Spostare le risorse utilizzate per gli endpoint di routing nella nuova posizione.

    * È necessario creare manualmente le nuove risorse nel portale di Azure o tramite l'uso di modelli di Resource Manager.You must create the new resources or manually in the [Azure portal](https://portal.azure.com) or through the use of Resource Manager templates. 

    * È necessario rinominare tutte le risorse quando vengono create nella nuova posizione, in quanto hanno nomi univoci a livello globale. 
     
    * È necessario aggiornare i nomi delle risorse e le chiavi di risorsa nel nuovo modello dell'hub, prima di creare il nuovo hub. Le risorse devono essere presenti quando viene creato il nuovo hub.

1. Non spostare le risorse utilizzate per gli endpoint di routing. Usali "sul posto".

   * Nel passaggio in cui si modifica il modello, sarà necessario recuperare le chiavi per ogni risorsa di routing e inserirle nel modello prima di creare il nuovo hub. 

   * L'hub fa ancora riferimento alle risorse di routing originali e indirizza i messaggi a tali risorse come configurato.

   * Si avrà un piccolo calo delle prestazioni perché l'hub e le risorse dell'endpoint di routing non si trovano nella stessa posizione.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Preparare la migrazione dell'hub in un'altra areaPrepare to migrate the hub to another region

In questa sezione vengono fornite istruzioni specifiche per la migrazione dell'hub.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Individuare l'hub originale ed esportarlo in un modello di risorsa.

1. Accedere al portale di [Azure](https://portal.azure.com). 

1. Passare a **Gruppi di risorse** e selezionare il gruppo di risorse che contiene l'hub da spostare. È anche possibile passare a **Risorse** e trovare l'hub in questo modo. Selezionare l'hub.

1. Selezionare **Esporta modello** dall'elenco delle proprietà e delle impostazioni per l'hub. 

   ![Screenshot che mostra il comando per esportare il modello per l'hub IoT.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selezionare **Scarica** per scaricare il modello. Salvare il file da qualche parte si può trovare di nuovo. 

   ![Screenshot che mostra il comando per scaricare il modello per l'hub IoT.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Visualizzare il modello 

1. Passare alla cartella Download (o alla cartella utilizzata durante l'esportazione del modello) e trovare il file zip. Aprire il file zip e `template.json`trovare il file denominato . Selezionarlo, quindi selezionare Ctrl -C per copiare il modello. Passare a un'altra cartella che non si è nel file zip e incollare il file (CTRL-V). Ora è possibile modificarlo.
 
    L'esempio seguente è per un hub generico senza configurazione di routing. Si tratta di un hub di livello S1 (con 1 unità) denominato **ContosoTestHub29358** nell'area **westus**. Ecco il modello esportato.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Modificare il modello 

È necessario apportare alcune modifiche prima di poter utilizzare il modello per creare il nuovo hub nella nuova area. Utilizzare [il codice VS](https://code.visualstudio.com) o un editor di testo per modificare il modello.

#### <a name="edit-the-hub-name-and-location"></a>Modificare il nome e la posizione dell'hub

1. Rimuovere la sezione dei parametri nella parte superiore - è molto più semplice usare solo il nome dell'hub perché non avremo più parametri. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Modificare il nome in modo da utilizzare il nome effettivo (nuovo) anziché recuperarlo da un parametro (rimosso nel passaggio precedente). 

    Per il nuovo hub, usare il nome dell'hub originale più il *clone* della stringa per creare il nuovo nome. Iniziare pulendo il nome e la posizione dell'hub.
    
    Versione obsoleta: 

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nuova versione:  

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Successivamente, si scoprirà che i valori per **il percorso** contengono il nome dell'hub precedente. Cambiali per usare quello nuovo. Questi sono i valori di percorso in **eventHubEndpoints denominati** **eventi** e **OperationsMonitoringEvents**.

    Al termine, la sezione degli endpoint dell'hub eventi dovrebbe essere simile alla seguente:When you're done, your event hub endpoints section should look like this:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Aggiornare le chiavi per le risorse di routing che non vengono spostate

Quando si esporta il modello di Resource Manager per un hub con routing configurato, si noterà che le chiavi per tali risorse non vengono fornite nel modello esportato, ovvero la loro posizione è indicata da asterischi. È necessario compilarli accedendo a tali risorse nel portale e recuperando le chiavi **prima** di importare il modello del nuovo hub e creare l'hub. 

1. Recuperare le chiavi necessarie per una qualsiasi delle risorse di routing e inserirle nel modello. È possibile recuperare le chiavi dalla risorsa nel portale di [Azure.](https://portal.azure.com) 

   Ad esempio, se si instradano i messaggi a un contenitore di archiviazione, trovare l'account di archiviazione nel portale. Nella sezione Impostazioni selezionare Chiavi di **accesso**, quindi copiare una delle chiavi. Ecco l'aspetto della chiave quando si esporta il modello per la prima volta:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Dopo aver recuperato la chiave dell'account di archiviazione, `AccountKey=****` inserirla nel modello nella clausola al posto degli asterischi. 

1. Per le code del bus di servizio, ottenere la chiave di accesso condiviso corrispondente a SharedAccessKeyName.For service bus queues, get the Shared Access Key matching the SharedAccessKeyName. Ecco la chiave `SharedAccessKeyName` e il nel json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Lo stesso vale per gli argomenti del bus di servizio e le connessioni dell'hub eventi.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Creare le nuove risorse di instradamento nella nuova posizioneCreate the new routing resources in the new location

Questa sezione si applica solo se si spostano le risorse utilizzate dall'hub per gli endpoint di routing.

Se si desidera spostare le risorse di instradamento, è necessario impostare manualmente le risorse nella nuova ubicazione. È possibile creare le risorse di routing usando il portale di [Azure](https://portal.azure.com)oppure esportando il modello Resource Manager per ognuna delle risorse usate dal routing dei messaggi, modificandole e importandole. Dopo aver configurato le risorse, è possibile importare il modello dell'hub, che include la configurazione di routing.

1. Creare ogni risorsa utilizzata dal ciclo. È possibile eseguire questa operazione manualmente usando il portale di Azure oppure creare le risorse usando i modelli di Resource Manager.You can do this manually using the [Azure portal,](https://portal.azure.com)or create the resources using Resource Manager templates. Se si desidera utilizzare i modelli, questi sono i passaggi da seguire:

    1. Per ogni risorsa utilizzata dal routing, esportarla in un modello di Resource Manager.For each resource used by the routing, export it to a Resource Manager template.
    
    1. Aggiornare il nome e il percorso della risorsa. 

    1. Aggiornare eventuali riferimenti incrociati tra le risorse. Ad esempio, se si crea un modello per un nuovo account di archiviazione, è necessario aggiornare il nome dell'account di archiviazione in tale modello e qualsiasi altro modello che vi faccia riferimento. Nella maggior parte dei casi, la sezione di routing nel modello per l'hub è l'unico altro modello che fa riferimento alla risorsa. 

    1. Importare ognuno dei modelli, che distribuisce ogni risorsa.

    Una volta impostate e in esecuzione le risorse utilizzate dal ciclo, è possibile continuare.

1. Nel modello per l'hub IoT modificare il nome di ogni risorsa di routing con il nuovo nome e aggiornare il percorso, se necessario. 

A questo punto si dispone di un modello che creerà un nuovo hub che ha un aspetto quasi esattamente come il vecchio hub, a seconda di come si è deciso di gestire il routing.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Sposta: crea il nuovo hub nella nuova area caricando il modello

Creare il nuovo hub nella nuova posizione usando il modello. Se si dispone di risorse di instradamento che verranno spostate, le risorse devono essere impostate nella nuova posizione e i riferimenti nel modello aggiornati in modo che corrispondano. Se non si spostano le risorse di routing, devono essere nel modello con le chiavi aggiornate.

1. Accedere al portale di [Azure](https://portal.azure.com).

1. Selezionare **Crea una risorsa**. 

1. Nella casella di ricerca, inserisci "distribuzione del modello" e seleziona Invio.

1. Selezionare la distribuzione dei **modelli (distribuire utilizzando modelli personalizzati)**. Verrà visualizzata una schermata per la distribuzione del modello. Selezionare **Crea**. Verrà visualizzata la schermata seguente:

   ![Schermata che mostra il comando per la creazione di un modello personalizzato](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Selezionare **Crea un modello personalizzato nell'editor**, che consente di caricare il modello da un file. 

1. Selezionare **Carica file**. 

   ![Screenshot che mostra il comando per il caricamento di un file modello](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Cercare il nuovo modello modificato e selezionarlo, quindi selezionare **Apri**. Carica il modello nella finestra di modifica. Selezionare **Salva**. 

   ![Schermata che mostra il caricamento del modello](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Compilare i campi seguenti.

   **Sottoscrizione**: selezionare la sottoscrizione da utilizzare.

   **Gruppo di risorse:** creare un nuovo gruppo di risorse in una nuova posizione. Se ne hai già configurato uno nuovo, puoi selezionarlo invece di crearne uno nuovo.

   **Posizione:** se è stato selezionato un gruppo di risorse esistente, questo viene compilato in modo che corrisponda alla posizione del gruppo di risorse. Se è stato creato un nuovo gruppo di risorse, questa sarà la sua posizione.

   **Sono d'accordo casella di controllo**: questo dice fondamentalmente che si accetta di pagare per le risorse che si sta creando.

1. Selezionare il pulsante **Acquista**.

Il portale ora convalida il modello e distribuisce l'hub clonato. Se si dispone di dati di configurazione di routing, questi verranno inclusi nel nuovo hub, ma punteranno alle risorse nella posizione precedente.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Gestione dei dispositivi registrati nell'hub IoT

Ora che il clone è in funzione, è necessario copiare tutti i dispositivi dall'hub originale al clone. 

A tale scopo, è possibile eseguire diverse procedure. Il servizio [Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps)è stato utilizzato in origine per eseguire il provisioning dei dispositivi oppure non è stato possibile eseguire il provisioning dei dispositivi. Se l'hai fatto, non è difficile. Se non l'hai fatto, questo può essere molto complicato. 

Se non è stato utilizzato DPS per eseguire il provisioning dei dispositivi, è possibile ignorare la sezione successiva e iniziare con [Utilizzo dell'importazione/esportazione per spostare i dispositivi nel nuovo hub.](#using-import-export-to-move-the-devices-to-the-new-hub)

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Utilizzo di DPS per eseguire nuovamente il provisioning dei dispositivi nel nuovo hub

Per utilizzare DPS per spostare i dispositivi nella nuova posizione, vedere Come eseguire nuovamente il provisioning dei [dispositivi.](../iot-dps/how-to-reprovision.md) Al termine, è possibile visualizzare i dispositivi nel portale di [Azure](https://portal.azure.com) e verificare che si trovino nella nuova posizione.

Passare al nuovo hub usando il [portale di Azure](https://portal.azure.com). Selezionare l'hub, quindi selezionare **Dispositivi IoT**. Vengono visualizzati i dispositivi di cui è stato nuovamente eseguito il provisioning nell'hub clonato. È inoltre possibile visualizzare le proprietà dell'hub clonato. 

Se è stato implementato il routing, verificare e assicurarsi che i messaggi vengano instradati correttamente alle risorse.

### <a name="committing-the-changes-after-using-dps"></a>Commit delle modifiche dopo l'utilizzo di DPS

Questa modifica è stata sottoposta a commit dal servizio DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Rollback delle modifiche dopo l'utilizzo di DPS. 

Se si desidera eseguire il rollback delle modifiche, eseguire nuovamente il provisioning dei dispositivi dal nuovo hub a quello precedente.

La migrazione dell'hub e dei relativi dispositivi è stata completata. È possibile passare a [Pulizia](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Uso dell'importazione-esportazione per spostare i dispositivi nel nuovo hub

L'applicazione è destinata a .NET Core, pertanto è possibile eseguirla in Windows o Linux.The application targets .NET Core, so you can run it on either Windows or Linux. È possibile scaricare l'esempio, recuperare le stringhe di connessione, impostare i flag per i quali si desidera eseguire i bit ed eseguirlo. È possibile farlo senza mai aprire il codice.

### <a name="downloading-the-sample"></a>Download dell'esempio

1. Usare gli esempi IoT in C' di questa pagina: Esempi di [Azure IoT per C.](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/) Scaricare il file zip e decomprimerlo sul computer. 

1. Il codice pertinente è in ./iot-hub/Samples/service/ImportExportDevicesSample.The pertinent code is in ./iot-hub/Samples/service/ImportExportDevicesSample. Non è necessario visualizzare o modificare il codice per eseguire l'applicazione.

1. Per eseguire l'applicazione, specificare tre stringhe di connessione e cinque opzioni. Questi dati vengono passati come argomenti della riga di comando o si utilizzano variabili di ambiente oppure si utilizza una combinazione dei due. Passeremo le opzioni come argomenti della riga di comando e le stringhe di connessione come variabili di ambiente. 

   Il motivo è che le stringhe di connessione sono lunghe e inopportuna e improbabile che vengano modificate, ma è possibile modificare le opzioni ed eseguire l'applicazione più di una volta. Per modificare il valore di una variabile di ambiente, è necessario chiudere la finestra di comando e Visual Studio o codice VS, a seconda di quale si sta utilizzando. 

### <a name="options"></a>Opzioni

Di seguito sono riportate le cinque opzioni specificate quando si esegue l'applicazione. Metteremo questi sulla riga di comando in un minuto.

*   **addDevices** (argomento 1): impostare true se si desidera aggiungere dispositivi virtuali generati automaticamente. Questi vengono aggiunti all'hub di origine. Inoltre, impostare **numToAdd** (argomento 2) per specificare il numero di dispositivi che si desidera aggiungere. Il numero massimo di dispositivi che è possibile registrare in un hub è di un milione. Lo scopo di questa opzione è per il test: è possibile generare un numero specifico di dispositivi e quindi copiarli in un altro hub.

*   **copyDevices** (argomento 3) -- impostare true per copiare i dispositivi da un hub all'altro. 

*   **deleteSourceDevices** (argomento 4) -- imposta questa proprietà su true per eliminare tutti i dispositivi registrati nell'hub di origine. Si consiglia di attendere fino a quando non si è certi che tutti i dispositivi sono stati trasferiti prima di eseguire questo. Una volta eliminati, non è possibile restituirli.

*   **deleteDestDevices** (argomento 5) -- imposta questa proprietà su true per eliminare tutti i dispositivi registrati nell'hub di destinazione (il clone). È possibile eseguire questa operazione se si desidera copiare i dispositivi più di una volta. 

Il comando di base sarà *dotnet run* -- questo indica a .NET di compilare il file csproj locale e quindi eseguirlo. Aggiungere gli argomenti della riga di comando alla fine prima di eseguirlo. 

La riga di comando sarà simile a questi esempi:Your command-line will look like these examples:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Utilizzo delle variabili di ambiente per le stringhe di connessioneUsing environment variables for the connection strings

1. Per eseguire l'esempio, sono necessarie le stringhe di connessione agli hub IoT vecchi e nuovi e a un account di archiviazione che è possibile usare per i file di lavoro temporanei. I valori verranno archiviati in variabili di ambiente.

1. Per ottenere i valori della stringa di connessione, accedere al portale di [Azure.](https://portal.azure.com) 

1. Inserire le stringhe di connessione in un punto qualsiasi è possibile recuperarle, ad esempio NotePad.Put the connection strings somewhere you can retrieve them, such as NotePad. Se si copia quanto segue, è possibile incollare le stringhe di connessione direttamente nel punto in cui sono disponibili. Non aggiungere spazi intorno al segno di uguale o modificare il nome della variabile. Inoltre, non sono necessarie virgolette doppie intorno alle stringhe di connessione. Se si inserisce tra virgolette intorno alla stringa di connessione dell'account di archiviazione, non funzionerà.

   Per Windows, questo è il modo in cui si impostano le variabili di ambiente:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Per Linux, questo è il modo in cui si definiscono le variabili di ambiente:For Linux, this is how you define the environment variables:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Per le stringhe di connessione dell'hub IoT, passare a ogni hub nel portale. È possibile cercare l'hub in **Risorse.You** can search in Resources for the hub. Se si conosce il gruppo di risorse, è possibile passare a **Gruppi**di risorse, selezionare il gruppo di risorse e quindi selezionare l'hub dall'elenco delle risorse in tale gruppo di risorse. 

1. Selezionare **Criteri di accesso condiviso** da Impostazioni per l'hub, quindi selezionare **iothubowner** e copiare una delle stringhe di connessione. Eseguire la stessa operazione per l'hub di destinazione. Aggiungerli ai comandi SET appropriati.

1. Per la stringa di connessione dell'account di archiviazione, trovare l'account di archiviazione in **Risorse** o nel **relativo gruppo di risorse** e aprirlo. 
   
1. Nella sezione Impostazioni selezionare Chiavi di **accesso** e copiare una delle stringhe di connessione. Inserire la stringa di connessione nel file di testo per il comando SET appropriato. 

Ora si dispone delle variabili di ambiente in un file con i comandi SET e si conosce gli argomenti della riga di comando. Eseguiamo l'esempio.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Esecuzione dell'applicazione di esempio e utilizzo di argomenti della riga di comando

1. Aprire una finestra del prompt dei comandi. Selezionare Windows `command prompt` e digitare per visualizzare la finestra del prompt dei comandi.

1. Copiare i comandi che impostano le variabili di ambiente, uno alla volta, e incollarli nella finestra del prompt dei comandi e selezionare INVIO. Al termine, digitare `SET` nella finestra del prompt dei comandi per visualizzare le variabili di ambiente e i relativi valori. Dopo aver copiato questi nella finestra del prompt dei comandi, non è necessario copiarli di nuovo, a meno che non si apre una nuova finestra del prompt dei comandi.

1. Nella finestra del prompt dei comandi, modificare le directory fino a quando non ci si trova in ./ImportExportDevicesSample (dove è presente il file ImportExportDevicesSample.csproj). Digitare quindi quanto segue e includere gli argomenti della riga di comando.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Il comando dotnet compila ed esegue l'applicazione. Poiché si passano le opzioni quando si esegue l'applicazione, è possibile modificarne i valori ogni volta che si esegue l'applicazione. Ad esempio, è possibile eseguirlo una volta e creare nuovi dispositivi, quindi eseguirlo di nuovo e copiare tali dispositivi in un nuovo hub e così via. È inoltre possibile eseguire tutti i passaggi nella stessa esecuzione, anche se è consigliabile non eliminare i dispositivi fino a quando non si è certi di aver completato la clonazione. Di seguito è riportato un esempio che crea 1000 dispositivi e quindi li copia nell'altro hub.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Dopo aver verificato che i dispositivi sono stati copiati correttamente, è possibile rimuovere i dispositivi dall'hub di origine in questo modo:After you verify that the devices were copied successfully, you can remove the devices from the source hub like this:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Esecuzione dell'applicazione di esempio con Visual StudioRunning the sample application using Visual Studio

1. Se si desidera eseguire l'applicazione in Visual Studio, modificare la directory corrente nella cartella in cui si trova il file IoTHubServiceSamples.sln. Quindi eseguire questo comando nella finestra del prompt dei comandi per aprire la soluzione in Visual Studio.Then run this command in the command prompt window to open the solution in Visual Studio. È necessario eseguire questa operazione nella stessa finestra di comando in cui si impostano le variabili di ambiente, in modo che tali variabili siano note.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Fare clic con il pulsante destro del mouse sul progetto *ImportExportDevicesSample* e selezionare Imposta come progetto di **avvio**.    
    
1. Impostare le variabili nella parte superiore di Program.cs nella cartella ImportExportDevicesSample per le cinque opzioni.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Selezionare F5 per eseguire l'applicazione. Al termine dell'esecuzione, è possibile visualizzare i risultati.

### <a name="view-the-results"></a>View the results 

È possibile visualizzare i dispositivi nel portale di [Azure](https://portal.azure.com) e verificare che si trovino nella nuova posizione.

1. Passare al nuovo hub usando il [portale di Azure](https://portal.azure.com). Selezionare l'hub, quindi selezionare **Dispositivi IoT**. Vengono visualizzati i dispositivi appena copiati dall'hub precedente all'hub clonato. È inoltre possibile visualizzare le proprietà dell'hub clonato. 

1. Verificare la presenza di errori di importazione/esportazione passando all'account di `ImportErrors.log`archiviazione di Azure nel portale di [Azure](https://portal.azure.com) e cercando nel contenitore il `devicefiles` metodo . Se questo file è vuoto (la dimensione è 0), non si sono verificati errori. Se si tenta di importare lo stesso dispositivo più di una volta, il dispositivo viene rifiutato la seconda volta e viene aggiunto un messaggio di errore al file di registro.

### <a name="committing-the-changes"></a>Commitdelle delle modifiche 

A questo punto, l'hub è stato copiato nella nuova posizione e ne è stata eseguita la migrazione nel nuovo clone. A questo punto è necessario apportare modifiche in modo che i dispositivi funzionino con l'hub clonato.

Per eseguire il commit delle modifiche, ecco i passaggi da eseguire: 

* Aggiornare ogni dispositivo per modificare il nome host dell'hub IoT in modo che punti il nome host dell'hub IoT al nuovo hub. È consigliabile eseguire questa operazione usando lo stesso metodo usato al primo provisioning del dispositivo.

* Modificare tutte le applicazioni che si hanno che fanno riferimento al vecchio hub in modo che puntino al nuovo hub.

* Al termine, il nuovo hub dovrebbe essere operativo. L'hub precedente non deve avere dispositivi attivi ed essere disconnesso. 

### <a name="rolling-back-the-changes"></a>Rollback delle modifiche

Se si decide di eseguire il rollback delle modifiche, ecco i passaggi da eseguire:

* Aggiornare ogni dispositivo per modificare il nome host dell'hub IoT in modo che punti al nome host dell'hub IoT per l'hub precedente. È consigliabile eseguire questa operazione usando lo stesso metodo usato al primo provisioning del dispositivo.

* Modificare tutte le applicazioni che si hanno che fanno riferimento al nuovo hub in modo che puntino all'hub precedente. Ad esempio, se si usa Analisi di Azure, potrebbe essere necessario riconfigurare l'input di Analisi di flusso di Azure.For example, if you are using Azure Analytics, you may need to reconfigure your [Azure Stream Analytics input](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Eliminare il nuovo hub. 

* Se si dispone di risorse di routing, la configurazione nell'hub precedente deve comunque puntare alla configurazione di routing corretta e deve funzionare con tali risorse dopo il riavvio dell'hub.

### <a name="checking-the-results"></a>Controllo dei risultati 

Per verificare i risultati, modificare la soluzione IoT in modo che punti all'hub nella nuova posizione ed eseguirla. In altre parole, eseguire le stesse azioni con il nuovo hub eseguito con l'hub precedente e assicurarsi che funzionino correttamente. 

Se è stato implementato il routing, verificare e assicurarsi che i messaggi vengano instradati correttamente alle risorse.

## <a name="clean-up"></a>Eliminazione

Non pulire fino a quando non si è certi che il nuovo hub è in esecuzione e i dispositivi funzionano correttamente. Assicurarsi inoltre di testare il routing se si utilizza tale funzionalità. Quando si è pronti, pulire le risorse precedenti eseguendo la procedura seguente:When you're ready, clean up the old resources by performing these steps:

* Se non è già stato fatto, eliminare l'hub precedente. In questo modo vengono rimossi tutti i dispositivi attivi dall'hub.

* Se si dispone di risorse di routing spostate nella nuova posizione, è possibile eliminare le risorse di routing precedenti.

## <a name="next-steps"></a>Passaggi successivi

È stato clonato un hub IoT in un nuovo hub in una nuova area, completa dei dispositivi. Per altre informazioni sull'esecuzione di operazioni in blocco sul registro delle identità in un hub IoT, vedere [Importare ed esportare le identità dei dispositivi dell'hub IoT in blocco.](iot-hub-bulk-identity-mgmt.md)

Per altre informazioni sull'hub IoT e sullo sviluppo per l'hub, vedere gli articoli seguenti.

* [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide.md)

* [Esercitazione sul routing dell'hub IoT](tutorial-routing.md)

* [Panoramica sulla gestione dei dispositivi dell'hub IoT](iot-hub-device-management-overview.md)

* Se si desidera distribuire l'applicazione di esempio, vedere Distribuzione di [applicazioni .NET Core](https://docs.microsoft.com/dotnet/core/deploying/index).
