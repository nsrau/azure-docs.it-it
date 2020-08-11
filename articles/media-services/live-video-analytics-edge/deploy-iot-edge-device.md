---
title: Distribuire analisi video in tempo reale in un dispositivo IoT Edge-Azure
description: Questo articolo elenca i passaggi che consentono di distribuire analisi video in tempo reale sul dispositivo IoT Edge. Questa operazione può essere eseguita, ad esempio, se si ha accesso a un computer Linux locale e/o in precedenza è stato creato un account di servizi multimediali di Azure.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: f031f679d8fe8e1c14b6a4086f5e1c37f15c7855
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067905"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Distribuire analisi video in tempo reale in un dispositivo IoT Edge

Questo articolo elenca i passaggi che consentono di distribuire analisi video in tempo reale sul dispositivo IoT Edge. Questa operazione può essere eseguita, ad esempio, se si ha accesso a un computer Linux locale e/o in precedenza è stato creato un account di servizi multimediali di Azure.


## <a name="prerequisites"></a>Prerequisiti

* Un computer Linux che soddisfa i vincoli HW/SW per analisi video in tempo reale
* Sottoscrizione di Azure a cui si dispone dei [privilegi di proprietario](../../role-based-access-control/built-in-roles.md#owner)
* [Creare e configurare l'hub Internet](../../iot-hub/iot-hub-create-through-portal.md)
* [Registrare IoT Edge dispositivo](../../iot-edge/how-to-register-device.md)
* [Installare il runtime Azure IoT Edge in sistemi Linux basati su Debian](../../iot-edge/how-to-install-iot-edge-linux.md)
* [Creare un account di Servizi multimediali di Azure](../latest/create-account-howto.md)
    * Usare una di queste aree: Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali, Giappone orientale, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Canada orientale, Regno Unito meridionale, Francia centrale, Francia meridionale, Svizzera settentrionale, Svizzera occidentale e Giappone occidentale.
    * Si consiglia di usare gli account di archiviazione GPv2 (General-Purpose v2)

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Configurazione delle risorse di Azure per l'uso di analisi video in tempo reale

### <a name="create-custom-azure-resource-manager-role"></a>Creare un ruolo personalizzato di Azure Resource Manager

Vedere [creare un ruolo di Azure Resource Manager personalizzato](create-custom-azure-resource-manager-role-how-to.md) e assegnarlo a un'entità servizio per l'uso di analisi video in tempo reale.

### <a name="set-up-a-premium-streaming-endpoint"></a>Configurare un endpoint di streaming Premium

Se si intende usare analisi video in tempo reale per registrare i video in modo continuo nel cloud e successivamente usare le [API di query](playback-recordings-how-to.md#query-api) prima di riprodurli, è consigliabile aggiornare il servizio multimediale per usare un [endpoint di streaming Premium](../latest/streaming-endpoint-concept.md#types).  

Si tratta di un passaggio facoltativo. A tale scopo, è possibile usare questo comando dell'interfaccia della riga di comando di Azure:

```azure-cli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

È possibile usare questo comando per avviare l'endpoint di streaming 

> [!IMPORTANT]
> A questo punto, la sottoscrizione inizierà a essere fatturata.

```azure-cli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Eseguire la procedura descritta in questo articolo per ottenere le credenziali per accedere alle API del servizio multimediale: [accedere alle API del servizio multimediale](../latest/access-api-howto.md#use-the-azure-portal).

## <a name="create-and-use-local-user-account-for-deployment"></a>Creare e usare un account utente locale per la distribuzione
Per eseguire l'analisi video in tempo reale sul modulo IoT Edge creare un account utente locale con il minor numero di privilegi possibile. Eseguire ad esempio i comandi seguenti nel computer Linux:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Concessione di autorizzazioni per l'archiviazione del dispositivo

Ora che è stato creato un account utente locale, 

* Sarà necessaria una cartella locale per archiviare i dati di configurazione dell'applicazione. Creare una cartella e concedere le autorizzazioni all'account LocalUser scrivere in tale cartella usando i comandi seguenti:

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* Sarà necessaria anche una cartella per [registrare i video in un file locale](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Usare i comandi seguenti per creare una cartella locale per la stessa:

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Distribuire il modulo Edge di analisi video in tempo reale

<!-- (To JuliaKo: this is similar to https://docs.microsoft.com/azure/iot-edge/how-to-deploy-blob)-->
Il video live Analytics in IoT Edge espone le proprietà del modulo gemello documentate nello [schema di configurazione del modulo gemello](module-twin-configuration-schema.md). 

### <a name="deploy-using-the-azure-portal"></a>Distribuire tramite il portale di Azure

Il portale di Azure illustra la creazione di un manifesto di distribuzione e il push della distribuzione in un dispositivo IoT Edge.  
#### <a name="select-your-device-and-set-modules"></a>Selezionare il dispositivo e impostare i moduli

1. Accedere al [portale di Azure](https://ms.portal.azure.com/) e passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu.
1. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.
1. Selezionare **imposta moduli**.

#### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure dispone di una procedura guidata che illustra la creazione di un manifesto di distribuzione. Include tre passaggi organizzati in schede: **moduli**, **Route**e **Revisione + creazione**.

#### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **moduli IOT Edge** della pagina fare clic sull'elenco a discesa **aggiungi** e selezionare **IOT Edge modulo** per visualizzare la pagina **Aggiungi IOT Edge modulo** .
1. Nella scheda **Impostazioni modulo** immettere un nome per il modulo e quindi specificare l'URI dell'immagine del contenitore:   
    Esempi:
    
    * **Nome del modulo IOT Edge**: lvaEdge
    * **URI immagine**: MCR.Microsoft.com/media/Live-Video-Analytics:1.0    
    
    ![Aggiunta](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Non selezionare **Aggiungi** fino a quando non sono stati specificati i valori nelle schede **Impostazioni modulo**, **Opzioni di creazione contenitori**e **modulo gemello** , come descritto in questa procedura.
    
    > [!WARNING]
    > Azure IoT Edge distingue tra maiuscole e minuscole quando si effettuano chiamate ai moduli. Prendere nota della stringa esatta usata come nome del modulo.

1. Aprire la scheda **variabili di ambiente** .
   
   Aggiungere i valori seguenti nelle caselle di input in cui vengono visualizzate le ![ variabili di ambiente](./media/deploy-iot-edge-device/environment-variables.png) 

1. Aprire la scheda **Opzioni di creazione del contenitore** .

    ![Opzioni di creazione del contenitore](./media/deploy-iot-edge-device/container-create-options.png)
 
    Copiare e incollare il codice JSON seguente nella casella per limitare le dimensioni dei file di log generati dal modulo.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   La sezione "binds" nel file JSON include 2 voci:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": viene usato per associare i dati di configurazione dell'applicazione permanenti dal contenitore e archiviarli sul dispositivo perimetrale.
   1. "/var/media:/var/media": associa le cartelle multimediali tra il dispositivo perimetrale e il contenitore. Viene usato per archiviare le registrazioni video quando si esegue una topologia del grafico multimediale che supporta l'archiviazione di clip video sul dispositivo perimetrale.
   
1. Nella scheda **Impostazioni dispositivi gemelli del modulo** copiare il codice JSON seguente e incollarlo nella casella.
 
    ![Impostazioni del dispositivo gemello](./media/deploy-iot-edge-device/twin-settings.png)

    L'analisi video in tempo reale su IoT Edge richiede un set di proprietà di dispositivi gemelli obbligatori per l'esecuzione, come elencato nello [schema di configurazione del modulo gemello](module-twin-configuration-schema.md).  

    Il codice JSON che è necessario immettere nella casella di modifica delle impostazioni del modulo gemello sarà simile al seguente:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Queste sono le proprietà **obbligatorie** e per il codice JSON precedente,  
    * {subscriptionID}: ID sottoscrizione di Azure
    * {resourceGroupName}: il gruppo di risorse a cui appartiene l'account del servizio multimediale
    * {AMS-account-name}-questo è il nome dell'account di servizi multimediali
    
    Per ottenere gli altri valori, vedere [accedere all'API di servizi multimediali di Azure](../latest/access-api-howto.md#use-the-azure-portal).  
    * aadTenantId: ID del tenant ed è uguale a "AadTenantId" dal collegamento precedente.
    * aadServicePrincipalAppId: ID app dell'entità servizio per l'account del servizio multimediale ed è uguale a "AadClientId" dal collegamento precedente.
    * aadServicePrincipalSecret: si tratta della password dell'entità servizio ed è uguale a "AadSecret" dal collegamento precedente.

    Di seguito sono riportate alcune proprietà **consigliate** aggiuntive che possono essere aggiunte al file JSON e che consentono di monitorare il modulo. Per ulteriori informazioni, vedere [monitoraggio e registrazione](monitoring-logging.md):
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    Di seguito sono riportate alcune proprietà **facoltative** che è possibile aggiungere in JSON:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```
   > [!Note]
   > La proprietà del dispositivo gemello **allowUnsecuredEndpoints** è impostata su true per lo scopo delle esercitazioni e delle guide introduttive.   
   È necessario impostare questa proprietà su **false** durante l'esecuzione nell'ambiente di produzione. In questo modo, l'applicazione bloccherà tutti gli endpoint non protetti e per eseguire le topologie del grafico saranno necessarie le credenziali di connessione valide.  
   
    Selezionare Aggiungi per aggiungere le proprietà del modulo gemello.
1. Selezionare **Avanti: Route** per passare alla sezione route.
    Specificare le route.

Mantenere le route predefinite e selezionare **Avanti: rivedere + crea** per passare alla sezione revisione.

#### <a name="review-deployment"></a>Verificare la distribuzione

La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Sono stati dichiarati anche due moduli che non sono stati aggiunti: $edgeAgent e $edgeHub. Questi due moduli costituiscono il runtime di IoT Edge e sono impostazioni predefinite obbligatorie in ogni distribuzione.

Esaminare le informazioni di distribuzione e quindi selezionare Crea.

### <a name="verify-your-deployment"></a>Verificare la distribuzione

Dopo aver creato la distribuzione, tornare alla pagina IoT Edge dell'hub Internet.

1.  Selezionare il dispositivo IoT Edge impostato come destinazione della distribuzione per aprirne i dettagli.
2.  Nei dettagli del dispositivo verificare che il modulo di archiviazione BLOB sia elencato sia come **Specificato nella distribuzione che come Segnalato dal dispositivo**.

Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.
Codice di stato: 200 – OK significa che [il runtime di IOT Edge](../../iot-edge/iot-edge-runtime.md) è integro e funziona correttamente.

![Stato](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Richiamare un metodo diretto

Successivamente, è possibile testare l'esempio richiamando un metodo diretto. Leggi i [metodi diretti per analisi video in tempo reale su IOT Edge](direct-methods.md) per comprendere tutti i metodi diretti forniti dal modulo lvaEdge.

1. Facendo clic sul modulo perimetrale creato, verrà visualizzata la pagina di configurazione.  

    ![Moduli](./media/deploy-iot-edge-device/modules.png)
1. Fare clic sull'opzione di menu Metodo diretto.

    > [!NOTE] 
    > È necessario aggiungere un valore nelle sezioni della stringa di connessione, come si può vedere nella pagina corrente. Non è necessario nascondere o modificare alcun elemento nella sezione **Nome impostazione** . È accettabile lasciarlo impostato su pubblico.

    ![Metodo diretto](./media/deploy-iot-edge-device/module-details.png)
1. Immettere quindi "GraphTopologyList" nella casella nome metodo.
1. Quindi, copiare e incollare il payload JSON seguente nella casella payload.
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. Fare clic sull'opzione "Richiama metodo" nella parte superiore della pagina

    ![Metodi diretti](./media/deploy-iot-edge-device/direct-method.png)
1. Nella casella risultati dovrebbe essere visualizzato un messaggio di stato 200

    ![Messaggio di stato 200](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Passaggi successivi
Esercitazione [introduttiva: introduzione ad analisi video in tempo reale su IOT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Nel comando verrà eseguito il comando `device-id` anziché il valore predefinito `lva-sample-device` .
