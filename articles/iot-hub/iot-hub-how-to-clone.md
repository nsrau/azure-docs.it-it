---
title: Come clonare un hub Azure Internet
description: Come clonare un hub Azure Internet
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: robinsh
ms.openlocfilehash: 4d8771d49f30d94aeb6dfa855f5c2ef107076afb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083277"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Come clonare un hub Azure Internet in un'altra area

Questo articolo illustra i modi per clonare un hub Internet e fornisce alcune domande a cui è necessario rispondere prima di iniziare. Ecco diversi motivi per cui potrebbe essere necessario clonare un hub Internet:
 
* Si sta spostando la società da un'area a un'altra, ad esempio da Europa a America del Nord (o viceversa) e si vuole che le risorse e i dati siano geograficamente vicini alla nuova località, quindi è necessario spostare l'hub.

* Si sta configurando un hub per un ambiente di sviluppo rispetto a quello di produzione.

* Si vuole eseguire un'implementazione personalizzata della disponibilità elevata di più hub. Per altre informazioni, vedere la [sezione come ottenere la disponibilità elevata tra più aree della disponibilità elevata e del ripristino di emergenza dell'hub](iot-hub-ha-dr.md#achieve-cross-region-ha)Internet.

* Si vuole aumentare il numero di [partizioni](iot-hub-scaling.md#partitions) configurate per l'hub. Questa impostazione viene impostata quando si crea l'hub per la prima volta e non può essere modificata. È possibile usare le informazioni in questo articolo per clonare l'hub e al momento della creazione del clone, aumentare il numero di partizioni.

Per clonare un hub, è necessaria una sottoscrizione con accesso amministrativo all'Hub originale. È possibile inserire il nuovo hub in un nuovo gruppo di risorse e in un'area, nella stessa sottoscrizione dell'Hub originale o anche in una nuova sottoscrizione. Non è possibile usare lo stesso nome perché il nome dell'hub deve essere univoco a livello globale.

> [!NOTE]
> Attualmente non è disponibile una funzionalità di prima classe per la clonazione di un hub Internet. Si tratta principalmente di un processo manuale e pertanto è ragionevolmente soggetto a errori. La complessità della clonazione di un hub è direttamente proporzionale alla complessità dell'hub. Ad esempio, la clonazione di un hub Internet senza il routing dei messaggi è piuttosto semplice. Se si aggiunge il routing dei messaggi come una sola complessità, la clonazione dell'hub diventa almeno un ordine di grandezza più complicato. Se si spostano anche le risorse usate per il routing degli endpoint, si tratta di un altro ordine di magniture più complesso. 

## <a name="things-to-consider"></a>Aspetti da considerare

Prima di clonare un hub Internet è necessario prendere in considerazione diversi aspetti.

* Verificare che tutte le funzionalità disponibili nel percorso originale siano disponibili anche nella nuova posizione. Alcuni servizi sono in anteprima e non tutte le funzionalità sono disponibili in tutto il mondo.

* Non rimuovere le risorse originali prima di creare e verificare la versione clonata. Quando si rimuove un hub, questo non è più necessario e non è possibile ripristinarlo per controllare le impostazioni o i dati per assicurarsi che l'hub venga replicato correttamente.

* Molte risorse richiedono nomi univoci globali, quindi è necessario usare nomi diversi per le versioni clonate. È anche necessario usare un nome diverso per il gruppo di risorse a cui appartiene l'hub clonato. 

* Non viene eseguita la migrazione dei dati per l'hub di Internet delle cose originale. Sono inclusi i messaggi di telemetria, i comandi da cloud a dispositivo (C2D) e le informazioni relative ai processi, ad esempio pianificazioni e cronologia. Anche le metriche e i risultati di registrazione non vengono migrati. 

* Per i dati o i messaggi indirizzati ad archiviazione di Azure, è possibile lasciare i dati nell'account di archiviazione originale, trasferire i dati in un nuovo account di archiviazione nella nuova area oppure lasciare i dati obsoleti e creare un nuovo account di archiviazione nella nuova posizione per i nuovi dati. Per altre informazioni sullo stato di trasferimento dei dati nell'archiviazione BLOB, vedere [Introduzione a AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Non è possibile eseguire la migrazione dei dati per gli hub eventi e per gli argomenti e le code del bus di servizio. Si tratta di dati temporizzati che non vengono archiviati dopo l'elaborazione dei messaggi.

* È necessario pianificare i tempi di inattività per la migrazione. La clonazione dei dispositivi nel nuovo Hub richiede tempo. Se si usa il metodo di importazione/esportazione, i test di benchmark hanno rivelato che potrebbero essere necessarie circa due ore per spostare i dispositivi 500.000 e quattro ore per spostare un milione di dispositivi. 

* È possibile copiare i dispositivi nel nuovo hub senza arrestare o modificare i dispositivi. 

    * Se i dispositivi sono stati originariamente sottoposti a provisioning con DPS, il nuovo provisioning aggiorna le informazioni di connessione archiviate in ogni dispositivo. 
    
    * In caso contrario, è necessario usare il metodo di importazione/esportazione per spostare i dispositivi e quindi i dispositivi devono essere modificati per usare il nuovo hub. Ad esempio, è possibile configurare il dispositivo in modo che usi il nome host dell'hub Internet alle proprietà desiderate del dispositivo gemello. Il dispositivo prenderà il nome host dell'hub Internet, disconnettere il dispositivo dall'hub precedente e riconnetterlo a quello nuovo.
    
* È necessario aggiornare i certificati in uso, in modo da poterli usare con le nuove risorse. Inoltre, è probabile che l'hub sia definito in una tabella DNS in un punto qualsiasi, sarà necessario aggiornare le informazioni DNS.

## <a name="methodology"></a>Metodologia

Questo è il metodo generale consigliato per lo trasferimento di un hub Internet delle cose da un'area a un'altra. Per il routing dei messaggi, si presuppone che le risorse non vengano spostate nella nuova area. Per ulteriori informazioni, vedere la [sezione sul routing dei messaggi](#how-to-handle-message-routing).

   1. Esportare l'hub e le relative impostazioni in un modello di Gestione risorse. 
   
   1. Apportare le modifiche necessarie al modello, ad esempio l'aggiornamento di tutte le occorrenze del nome e il percorso per l'hub clonato. Per tutte le risorse nel modello usato per gli endpoint di routing dei messaggi, aggiornare la chiave nel modello per tale risorsa.
   
   1. Importare il modello in un nuovo gruppo di risorse nella nuova posizione. Verrà creato il clone.

   1. Eseguire il debug in base alle esigenze. 
   
   1. Aggiungere tutto ciò che non è stato esportato nel modello. 
   
       Ad esempio, i gruppi di consumer non vengono esportati nel modello. È necessario aggiungere manualmente i gruppi di consumer al modello o usare il [portale di Azure](https://portal.azure.com) dopo la creazione dell'hub. È disponibile un esempio di aggiunta di un gruppo di consumer a un modello nell'articolo [usare un modello di Azure Resource Manager per configurare il routing dei messaggi dell'hub](tutorial-routing-config-message-routing-rm-template.md)Internet.

       Anche le funzionalità di [arricchimento dei messaggi](iot-hub-message-enrichments-overview.md) non vengono esportate nel modello. Questi vengono usati insieme ai messaggi di routing e dovranno essere aggiornati manualmente nel nuovo hub quando viene aggiornata la configurazione del routing dei messaggi.

   1. Copiare i dispositivi dall'hub originale al clone. Questa operazione viene trattata nella sezione [gestione dei dispositivi registrati nell'hub](#managing-the-devices-registered-to-the-iot-hub)Internet.

## <a name="how-to-handle-message-routing"></a>Come gestire il routing del messaggio

Se l'hub usa il [routing personalizzato](iot-hub-devguide-messages-read-custom.md), l'esportazione del modello per l'Hub include la configurazione di routing, ma non include le risorse stesse. È necessario scegliere se spostare le risorse di routing nel nuovo percorso o lasciarle sul posto e continuare a usarle "così com'è". 

Si immagini ad esempio di avere un hub negli Stati Uniti occidentali che esegue il routing dei messaggi a un account di archiviazione (anche negli Stati Uniti occidentali) e che si vuole spostare l'Hub nell'area Stati Uniti orientali. È possibile spostare l'hub e fare in modo che i messaggi vengano indirizzati all'account di archiviazione negli Stati Uniti occidentali oppure spostare l'hub e spostare anche l'account di archiviazione. Il routing dei messaggi alle risorse endpoint in un'area diversa può compromettere un lieve impatto sulle prestazioni.

È possibile spostare facilmente un hub che utilizza il routing dei messaggi se non si spostano anche le risorse utilizzate per gli endpoint di routing. 

Se l'hub usa il routing dei messaggi, sono disponibili due opzioni. 

1. Spostare le risorse usate per gli endpoint di routing nella nuova posizione.

    * È necessario creare manualmente le nuove risorse nel [portale di Azure](https://portal.azure.com) o tramite l'uso di modelli di gestione risorse. 

    * È necessario rinominare tutte le risorse quando vengono create nella nuova posizione, perché hanno nomi univoci globali. 
     
    * Prima di creare il nuovo hub, è necessario aggiornare i nomi delle risorse e le chiavi delle risorse nel modello del nuovo hub. Quando viene creato il nuovo hub, le risorse devono essere presenti.

1. Non spostare le risorse usate per gli endpoint di routing. Usare "sul posto".

   * Nel passaggio in cui si modifica il modello, sarà necessario recuperare le chiavi per ogni risorsa di routing e inserirle nel modello prima di creare il nuovo hub. 

   * L'hub fa ancora riferimento alle risorse di routing originali e instrada i messaggi a tali risorse come configurato.

   * Si otterrà un lieve impatto sulle prestazioni perché le risorse dell'hub e dell'endpoint di routing non si trovano nella stessa posizione.

> [!NOTE]
> Se l'Hub USA i [miglioramenti dei messaggi](iot-hub-message-enrichments-overview.md), sarà necessario configurarli manualmente nel nuovo hub Internet, perché non vengono esportati con il modello di gestione risorse.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Preparare la migrazione dell'hub a un'altra area

Questa sezione fornisce istruzioni specifiche per la migrazione dell'hub.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Trovare l'Hub originale ed esportarlo in un modello di risorsa.

1. Accedere al [portale di Azure](https://portal.azure.com). 

1. Passare a **gruppi di risorse** e selezionare il gruppo di risorse che contiene l'hub che si vuole spostare. È anche possibile passare a **risorse** e trovare l'hub in questo modo. Selezionare l'hub.

1. Selezionare **Esporta modello** dall'elenco di proprietà e impostazioni per l'hub. 

   ![Screenshot che mostra il comando per esportare il modello per l'hub Internet delle cose.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selezionare **download** per scaricare il modello. Salvare il file in un punto in cui è possibile ritrovarlo. 

   ![Screenshot che mostra il comando per scaricare il modello per l'hub Internet delle cose.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Visualizzare il modello 

1. Passare alla cartella Downloads (o a qualsiasi cartella usata al momento dell'esportazione del modello) e trovare il file zip. Aprire il file zip e trovare il file denominato `template.json`. Selezionarlo, quindi premere CTRL + C per copiare il modello. Passare a una cartella diversa che non si trova nel file zip e incollare il file (CTRL + V). A questo punto è possibile modificarlo.
 
    L'esempio seguente è relativo a un hub generico senza configurazione di routing. Si tratta di un hub di livello S1 (con 1 unità) denominato **ContosoTestHub29358** nell'area **westus**. Ecco il modello esportato.

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

È necessario apportare alcune modifiche prima di poter usare il modello per creare il nuovo hub nella nuova area. Usare [vs code](https://code.visualstudio.com) o un editor di testo per modificare il modello.

#### <a name="edit-the-hub-name-and-location"></a>Modificare il nome e il percorso dell'hub

1. Rimuovere la sezione Parameters nella parte superiore: è molto più semplice usare semplicemente il nome dell'hub perché non ci saranno più parametri. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Modificare il nome in modo da usare il nome effettivo (nuovo) anziché recuperarlo da un parametro (rimosso nel passaggio precedente). 

    Per il nuovo hub, usare il nome dell'Hub originale più il *Clone* di stringa per creare il nuovo nome. Per iniziare, pulire il nome e il percorso dell'hub.
    
    Versione precedente:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nuova versione: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Si noterà quindi che i valori di **path** contengono il nome dell'hub precedente. Modificarli in modo da usare quello nuovo. Questi sono i valori di percorso in **eventHubEndpoints** chiamati **Events** e **OperationsMonitoringEvents**.

    Al termine, la sezione endpoint dell'hub eventi dovrebbe essere simile alla seguente:

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

Quando si esporta il modello di Gestione risorse per un hub con routing configurato, si noterà che le chiavi per tali risorse non sono incluse nel modello esportato, la loro selezione host è indicata da asterischi. Per compilarli, è necessario passare a tali risorse nel portale e recuperare le chiavi **prima** di importare il modello del nuovo hub e creare l'hub. 

1. Recuperare le chiavi necessarie per le risorse di routing e inserirle nel modello. È possibile recuperare la chiave o le chiavi dalla risorsa nel [portale di Azure](https://portal.azure.com). 

   Ad esempio, se si esegue il routing dei messaggi a un contenitore di archiviazione, trovare l'account di archiviazione nel portale. Nella sezione Impostazioni selezionare chiavi di **accesso**e quindi copiare una delle chiavi. Ecco come appare la chiave quando si esporta il modello per la prima volta:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Dopo aver recuperato la chiave dell'account di archiviazione, inserirla nel modello nella clausola `AccountKey=****` al posto degli asterischi. 

1. Per le code del bus di servizio, ottenere la chiave di accesso condivisa corrispondente a SharedAccessKeyName. Ecco la chiave e il `SharedAccessKeyName` nel file JSON:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Lo stesso vale per gli argomenti del bus di servizio e le connessioni a hub eventi.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Creare le nuove risorse di routing nella nuova posizione

Questa sezione si applica solo se si spostano le risorse usate dall'hub per gli endpoint di routing.

Se si desidera spostare le risorse di routing, è necessario configurare manualmente le risorse nella nuova posizione. È possibile creare le risorse di routing utilizzando il [portale di Azure](https://portal.azure.com)oppure esportando il modello di gestione risorse per ciascuna delle risorse utilizzate dal routing dei messaggi, modificarle e importarle. Una volta configurate le risorse, è possibile importare il modello dell'hub, che include la configurazione del routing.

1. Creare ogni risorsa utilizzata dal routing. È possibile eseguire questa operazione manualmente usando il [portale di Azure](https://portal.azure.com)o creare le risorse usando i modelli gestione risorse. Se si desidera utilizzare i modelli, attenersi alla procedura seguente:

    1. Per ogni risorsa utilizzata dal routing, esportarla in un modello di Gestione risorse.
    
    1. Aggiornare il nome e il percorso della risorsa. 

    1. Aggiornare tutti i riferimenti incrociati tra le risorse. Ad esempio, se si crea un modello per un nuovo account di archiviazione, è necessario aggiornare il nome dell'account di archiviazione nel modello e qualsiasi altro modello che vi faccia riferimento. Nella maggior parte dei casi, la sezione di routing nel modello per l'hub è l'unico altro modello che fa riferimento alla risorsa. 

    1. Importare ognuno dei modelli, che distribuisce ogni risorsa.

    Una volta configurate e in esecuzione le risorse utilizzate dal routing, è possibile continuare.

1. Nel modello per l'hub Internet delle cose, modificare il nome di ogni risorsa di routing con il nuovo nome e aggiornare il percorso, se necessario. 

A questo punto si dispone di un modello che creerà un nuovo hub simile a quello dell'hub precedente, a seconda della modalità con cui si è deciso di gestire il routing.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Spostare: creare il nuovo hub nella nuova area caricando il modello

Creare il nuovo hub nella nuova posizione usando il modello. Se si dispone di risorse di routing che saranno spostate, le risorse devono essere configurate nella nuova posizione e i riferimenti nel modello aggiornati per la corrispondenza. Se non si intende trasferire le risorse di routing, è necessario che si trovino nel modello con le chiavi aggiornate.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una risorsa**. 

1. Nella casella di ricerca inserire "distribuzione modello" e premere INVIO.

1. Selezionare **distribuzione modello (Distribuisci usando modelli personalizzati)** . Verrà visualizzata una schermata per la Distribuzione modelli. Selezionare **Create**. Viene visualizzata la schermata seguente:

   ![Screenshot che mostra il comando per la creazione di un modello personalizzato](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Selezionare **Compila un modello personalizzato nell'editor**, che consente di caricare il modello da un file. 

1. Selezionare **Carica file**. 

   ![Screenshot che mostra il comando per il caricamento di un file di modello](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Individuare il nuovo modello modificato e selezionarlo, quindi selezionare **Apri**. Carica il modello nella finestra di modifica. Selezionare **Salva**. 

   ![Screenshot che mostra il caricamento del modello](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Compilare i campi seguenti.

   **Sottoscrizione**: selezionare la sottoscrizione da usare.

   **Gruppo di risorse**: creare un nuovo gruppo di risorse in una nuova posizione. Se è già stato configurato un nuovo, è possibile selezionarlo invece di crearne uno nuovo.

   **Percorso**: se è stato selezionato un gruppo di risorse esistente, questo viene compilato in modo che corrisponda al percorso del gruppo di risorse. Se è stato creato un nuovo gruppo di risorse, questo sarà il percorso.

   Accetto la **casella**di controllo: questo significa che si accetta di pagare per le risorse che si stanno creando.

1. Selezionare il pulsante **Acquista**.

Il portale esegue ora la convalida del modello e distribuisce l'hub clonato. Se sono presenti dati di configurazione del routing, verranno inclusi nel nuovo hub, ma punteranno alle risorse nella posizione precedente.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Gestione dei dispositivi registrati nell'hub Internet

Ora che il clone è attivo e in esecuzione, è necessario copiare tutti i dispositivi dall'hub originale al clone. 

Esistono diversi modi per eseguire questa operazione. Il [servizio Device provisioning (DPS) è stato](/azure/iot-dps/about-iot-dps)originariamente usato per il provisioning dei dispositivi o non è stato fatto. In caso contrario, non è difficile. In caso contrario, può essere molto complicato. 

Se non è stato usato DPS per eseguire il provisioning dei dispositivi, è possibile ignorare la sezione successiva e iniziare a [usare l'importazione/esportazione per spostare i dispositivi nel nuovo hub](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Uso di DPS per eseguire nuovamente il provisioning dei dispositivi nel nuovo hub

Per usare DPS per spostare i dispositivi nella nuova posizione, vedere [come eseguire di nuovo il provisioning dei dispositivi](../iot-dps/how-to-reprovision.md). Al termine, è possibile visualizzare i dispositivi nella [portale di Azure](https://portal.azure.com) e verificare che si trovino nella nuova posizione.

Passare al nuovo hub usando il [portale di Azure](https://portal.azure.com). Selezionare l'hub e quindi selezionare **dispositivi**. Vengono visualizzati i dispositivi di cui è stato eseguito di nuovo il provisioning nell'hub clonato. È anche possibile visualizzare le proprietà dell'hub clonato. 

Se è stato implementato il routing, testare e verificare che i messaggi vengano indirizzati correttamente alle risorse.

### <a name="committing-the-changes-after-using-dps"></a>Commit delle modifiche dopo l'uso di DPS

Il commit della modifica è stato eseguito dal servizio DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Rollback delle modifiche dopo l'uso di DPS. 

Se si vuole eseguire il rollback delle modifiche, eseguire nuovamente il provisioning dei dispositivi dal nuovo hub a quello precedente.

A questo punto è stata completata la migrazione dell'hub e dei relativi dispositivi. È possibile passare alla [pulizia](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Uso di import-export per spostare i dispositivi nel nuovo hub

L'applicazione è destinata a .NET Core, quindi può essere eseguita in Windows o Linux. È possibile scaricare l'esempio, recuperare le stringhe di connessione, impostare i flag per i quali si desidera eseguire il bit ed eseguirlo. Questa operazione può essere eseguita senza mai aprire il codice.

### <a name="downloading-the-sample"></a>Download dell'esempio

1. Usare gli C# esempi di Internet delle cose da questa pagina: [esempi C#di Azure per ](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Scaricare il file con estensione zip e decomprimerlo nel computer. 

1. Il codice pertinente si trova in./iot-hub/Samples/service/ImportExportDevicesSample. Non è necessario visualizzare o modificare il codice per eseguire l'applicazione.

1. Per eseguire l'applicazione, specificare tre stringhe di connessione e cinque opzioni. Questi dati vengono passati come argomenti della riga di comando o vengono usate variabili di ambiente oppure una combinazione dei due. Le opzioni verranno passate come argomenti della riga di comando e le stringhe di connessione come variabili di ambiente. 

   Il motivo è dovuto al fatto che le stringhe di connessione sono lunghe e indesiderate ed è improbabile che si modifichino, ma è possibile modificare le opzioni ed eseguire l'applicazione più volte. Per modificare il valore di una variabile di ambiente, è necessario chiudere la finestra di comando e Visual Studio o VS Code, a seconda del valore utilizzato. 

### <a name="options"></a>Opzioni

Di seguito sono riportate le cinque opzioni specificate durante l'esecuzione dell'applicazione. Questi verranno inseriti nella riga di comando in un minuto.

*   **addDevices** (argomento 1): impostare questo valore su true se si desidera aggiungere i dispositivi virtuali generati automaticamente. Questi vengono aggiunti all'hub di origine. Inoltre, impostare **numToAdd** (argomento 2) per specificare il numero di dispositivi che si desidera aggiungere. Il numero massimo di dispositivi che è possibile registrare in un hub è 1 milione. Lo scopo di questa opzione è per i test: è possibile generare un numero specifico di dispositivi e quindi copiarli in un altro hub.

*   **copyDevices** (argomento 3): impostare su true per copiare i dispositivi da un hub a un altro. 

*   **deleteSourceDevices** (argomento 4): impostare su true per eliminare tutti i dispositivi registrati nell'hub di origine. È consigliabile attendere fino a quando non si è certi che tutti i dispositivi siano stati trasferiti prima di eseguire questa operazione. Quando si eliminano i dispositivi, non è possibile recuperarli.

*   **deleteDestDevices** (argomento 5): impostare su true per eliminare tutti i dispositivi registrati nell'hub di destinazione (clone). Questa operazione può essere eseguita se si desidera copiare i dispositivi più di una volta. 

Il comando di base sarà *DotNet Run* , che indica a .NET di compilare il file csproj locale ed eseguirlo. Aggiungere gli argomenti della riga di comando alla fine prima di eseguirla. 

La riga di comando sarà simile a questi esempi:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Utilizzo delle variabili di ambiente per le stringhe di connessione

1. Per eseguire l'esempio, sono necessarie le stringhe di connessione per gli hub Internet vecchi e nuovi e per un account di archiviazione che è possibile usare per i file di lavoro temporanei. I valori per questi vengono archiviati nelle variabili di ambiente.

1. Per ottenere i valori della stringa di connessione, accedere al [portale di Azure](https://portal.azure.com). 

1. Inserire le stringhe di connessione in un punto in cui è possibile recuperarle, ad esempio Blocco note. Se si copia il codice seguente, è possibile incollare le stringhe di connessione direttamente nel punto in cui passano. Non aggiungere spazi attorno al segno di uguale o modificare il nome della variabile. Non è inoltre necessario racchiudere tra virgolette doppie le stringhe di connessione. Se si inseriscono le virgolette nella stringa di connessione dell'account di archiviazione, non funzionerà.

   Per Windows, questo è il modo in cui si impostano le variabili di ambiente:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Per Linux, questo è il modo in cui definire le variabili di ambiente:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Per le stringhe di connessione dell'hub Internet, passare a ogni hub nel portale. È possibile eseguire ricerche nelle **risorse** per l'hub. Se si conosce il gruppo di risorse, è possibile passare a **gruppi di risorse**, selezionare il gruppo di risorse e quindi selezionare l'hub dall'elenco degli asset nel gruppo di risorse. 

1. Selezionare **criteri di accesso condiviso** dalle impostazioni per l'hub, quindi selezionare **iothubowner** e copiare una delle stringhe di connessione. Eseguire la stessa operazione per l'hub di destinazione. Aggiungerli ai comandi SET appropriati.

1. Per la stringa di connessione dell'account di archiviazione, trovare l'account di archiviazione in **risorse** o nel **gruppo di risorse** e aprirlo. 
   
1. Nella sezione Impostazioni selezionare chiavi di **accesso** e copiare una delle stringhe di connessione. Inserire la stringa di connessione nel file di testo per il comando SET appropriato. 

A questo punto si dispone delle variabili di ambiente in un file con i comandi SET e si conoscono gli argomenti della riga di comando. Eseguire l'esempio.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Esecuzione dell'applicazione di esempio e utilizzo degli argomenti della riga di comando

1. Aprire una finestra del prompt dei comandi. Selezionare Windows e digitare `command prompt` per ottenere la finestra del prompt dei comandi.

1. Copiare i comandi che impostano le variabili di ambiente, una alla volta, e incollarli nella finestra del prompt dei comandi e premere INVIO. Al termine, digitare `SET` nella finestra del prompt dei comandi per visualizzare le variabili di ambiente e i relativi valori. Una volta copiati nella finestra del prompt dei comandi, non è necessario copiarli nuovamente, a meno che non si apra una nuova finestra del prompt dei comandi.

1. Nella finestra del prompt dei comandi modificare le directory fino a quando non ci si trova in./ImportExportDevicesSample (in cui è presente il file ImportExportDevicesSample. csproj). Digitare quindi quanto segue e includere gli argomenti della riga di comando.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Il comando DotNet compila ed esegue l'applicazione. Poiché si passano le opzioni quando si esegue l'applicazione, è possibile modificarne i valori ogni volta che si esegue l'applicazione. Ad esempio, è possibile eseguirlo una volta e creare nuovi dispositivi, quindi eseguirli di nuovo e copiare i dispositivi in un nuovo hub e così via. È anche possibile eseguire tutti i passaggi nella stessa esecuzione, anche se si consiglia di non eliminare i dispositivi fino a quando non si è certi di aver completato la clonazione. Di seguito è riportato un esempio che crea dispositivi 1000 e li copia nell'altro hub.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Dopo aver verificato che i dispositivi siano stati copiati correttamente, è possibile rimuovere i dispositivi dall'hub di origine come segue:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Esecuzione dell'applicazione di esempio con Visual Studio

1. Se si vuole eseguire l'applicazione in Visual Studio, modificare la directory corrente con la cartella in cui risiede il file IoTHubServiceSamples. sln. Eseguire quindi questo comando nella finestra del prompt dei comandi per aprire la soluzione in Visual Studio. È necessario eseguire questa operazione nella stessa finestra di comando in cui si impostano le variabili di ambiente, in modo che tali variabili siano note.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Fare clic con il pulsante destro del mouse sul progetto *ImportExportDevicesSample* e selezionare **Imposta come progetto di avvio**.    
    
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

È possibile visualizzare i dispositivi nella [portale di Azure](https://portal.azure.com) e verificare che si trovino nella nuova posizione.

1. Passare al nuovo hub usando il [portale di Azure](https://portal.azure.com). Selezionare l'hub e quindi selezionare **dispositivi**. Vengono visualizzati i dispositivi appena copiati dall'hub precedente nell'hub clonato. È anche possibile visualizzare le proprietà dell'hub clonato. 

1. Per verificare la presenza di errori di importazione/esportazione, passare all'account di archiviazione di Azure nel [portale di Azure](https://portal.azure.com) e cercare nel contenitore `devicefiles` per il `ImportErrors.log`. Se il file è vuoto (la dimensione è 0), non si sono verificati errori. Se si tenta di importare lo stesso dispositivo più di una volta, il dispositivo viene rifiutato la seconda volta e viene aggiunto un messaggio di errore al file di log.

### <a name="committing-the-changes"></a>Commit delle modifiche 

A questo punto, l'hub è stato copiato nella nuova posizione ed è stata eseguita la migrazione dei dispositivi al nuovo clone. A questo punto è necessario apportare modifiche in modo che i dispositivi funzionino con l'hub clonato.

Per eseguire il commit delle modifiche, ecco i passaggi che è necessario eseguire: 

* Aggiornare ogni dispositivo per modificare il nome host dell'hub Internet per puntare il nome host dell'hub Internet al nuovo hub. È necessario eseguire questa operazione usando lo stesso metodo usato durante il primo provisioning del dispositivo.

* Modificare le applicazioni disponibili che fanno riferimento all'hub precedente per puntare al nuovo hub.

* Al termine, il nuovo hub dovrebbe essere attivo e in esecuzione. L'hub precedente non deve avere dispositivi attivi ed essere in uno stato disconnesso. 

### <a name="rolling-back-the-changes"></a>Rollback delle modifiche

Se si decide di eseguire il rollback delle modifiche, attenersi alla procedura seguente:

* Aggiornare ogni dispositivo per modificare il nome host dell'hub Internet per puntare al nome host dell'hub Internet per l'hub precedente. È necessario eseguire questa operazione usando lo stesso metodo usato durante il primo provisioning del dispositivo.

* Modificare le applicazioni disponibili che fanno riferimento al nuovo hub per puntare all'hub precedente. Se ad esempio si usa Azure Analytics, potrebbe essere necessario riconfigurare l' [input di analisi di flusso di Azure](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Eliminare il nuovo hub. 

* Se si dispone di risorse di routing, la configurazione nell'hub precedente deve ancora puntare alla configurazione di routing corretta e dovrebbe funzionare con tali risorse dopo il riavvio dell'hub.

### <a name="checking-the-results"></a>Verifica dei risultati 

Per controllare i risultati, modificare la soluzione Internet delle cose in modo che punti all'hub nella nuova posizione ed eseguirla. In altre parole, eseguire le stesse azioni con il nuovo hub che è stato eseguito con l'hub precedente e verificare che funzionino correttamente. 

Se è stato implementato il routing, testare e verificare che i messaggi vengano indirizzati correttamente alle risorse.

## <a name="clean-up"></a>Eliminazione

Non pulire fino a quando non si è certi che il nuovo hub è attivo e in esecuzione e i dispositivi funzionano correttamente. Assicurarsi inoltre di testare il routing se si utilizza tale funzionalità. Quando si è pronti, pulire le risorse precedenti attenendosi alla procedura seguente:

* Se non è già stato fatto, eliminare l'hub precedente. Verranno rimossi tutti i dispositivi attivi dall'hub.

* Se si dispone di risorse di routing spostate nella nuova posizione, è possibile eliminare le risorse di routing precedenti.

## <a name="next-steps"></a>Passaggi successivi

È stato clonato un hub Internet in un nuovo hub in una nuova area, completo dei dispositivi. Per altre informazioni sull'esecuzione di operazioni bulk nel registro delle identità in un hub Internet delle cose, vedere [importare ed esportare le identità dei dispositivi dell'hub in blocco](iot-hub-bulk-identity-mgmt.md).

Per altre informazioni sull'hub e sullo sviluppo per l'hub, vedere gli articoli seguenti.

* [Guida per gli sviluppatori dell'hub Internet](iot-hub-devguide.md)

* [Esercitazione sul routing dell'hub Internet](tutorial-routing.md)

* [Panoramica di gestione dei dispositivi dell'hub Internet](iot-hub-device-management-overview.md)

* Per distribuire l'applicazione di esempio, vedere [distribuzione di applicazioni .NET Core](https://docs.microsoft.com/dotnet/core/deploying/index).