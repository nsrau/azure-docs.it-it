---
title: 'Avvio rapido: Distribuire IoT Connector (anteprima) con il portale di Azure'
description: Questa guida di avvio rapido illustra come distribuire, configurare e usare la funzionalità IoT Connector dell'API di Azure per FHIR usando il portale di Azure.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: 95f5b5f13401c224ccf67c5f013deedf00379de7
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446811"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>Avvio rapido: Distribuire IoT Connector (anteprima) con il portale di Azure

IoT Connector è una funzionalità facoltativa dell'API di Azure per FHIR che consente di inserire dati da dispositivi IoMT (Internet of Medical Things). Durante la fase di anteprima, la funzionalità IoT Connector è disponibile gratuitamente. In questa guida introduttiva si apprende come:
- Distribuire e configurare IoT Connector usando il portale di Azure
- Usare un dispositivo simulato per inviare dati a IoT Connector
- Visualizzare le risorse create da IoT Connector per l'API di Azure per FHIR

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure attiva - [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Risorsa API di Azure per FHIR - [Distribuire l'API di Azure per FHIR con il portale di Azure](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Passare alla risorsa API di Azure per FHIR

Aprire il [portale di Azure](https://portal.azure.com) e passare alla risorsa **API di Azure per FHIR** per cui si vuole creare la funzionalità IoT Connector.

[![Risorsa API di Azure per FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

Nel menu di spostamento a sinistra fare clic su **IoT Connector (anteprima)** nella sezione **Componenti aggiuntivi** per aprire la pagina dei **connettori IoT**.

[![Funzionalità IoT Connector (anteprima)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>Creare un nuovo IoT Connector (anteprima)

Fare clic sul pulsante **Aggiungi** per aprire la pagina **Create IoT Connector** (Crea IoT Connector).

[![Aggiungere IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Immettere le impostazioni per il nuovo IoT Connector. Fare clic sul pulsante **Crea** e attendere la distribuzione di IoT Connector.

> [!NOTE]
> È necessario selezionare **Crea** come valore per l'elenco a discesa **Tipo di risoluzione** per questa installazione. 

[![Creare IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Impostazione|valore|Descrizione |
|---|---|---|
|Nome del connettore|Un nome univoco|Immettere un nome per identificare IoT Connector. Questo nome deve essere univoco all'interno di una risorsa API di Azure per FHIR. Il nome può contenere solo lettere minuscole, numeri e il segno meno (-). Deve iniziare e terminare con una lettera o un numero e deve avere una lunghezza compresa tra 3 e 24 caratteri.|
|Tipo di risoluzione|Cerca o Crea|Selezionare **Cerca** se si dispone di un processo fuori banda per creare le risorse FHIR [Dispositivo](https://www.hl7.org/fhir/device.html) e [Paziente](https://www.hl7.org/fhir/patient.html) nell'API di Azure per FHIR. IoT Connector userà un riferimento a queste risorse quando viene creata una risorsa FHIR [Osservazione](https://www.hl7.org/fhir/observation.html) per rappresentare i dati del dispositivo. Selezionare **Crea** quando si vuole che IoT Connector crei risorse Dispositivo e Paziente essenziali nell'API di Azure per FHIR usando i rispettivi valori degli identificatori presenti nei dati del dispositivo.|

Al termine dell'installazione, l'istanza di IoT Connector appena creata verrà visualizzata nella pagina dei **connettori IoT**.

[![Creazione di IoT Connector completata](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>Configurare IoT Connector (anteprima)

IoT Connector necessita di due modelli di mapping per trasformare i messaggi del dispositivo in risorse di osservazione basate su FHIR: **mapping dispositivo** e **mapping FHIR**. IoT Connector non è completamente operativo fino a quando non vengono caricati questi mapping.

[![Mapping di IoT Connector mancanti](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Per caricare i modelli di mapping, fare clic sull'istanza di IoT Connector appena distribuita per passare alla pagina **IoT Connector**.

[![Clic su IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Mapping dispositivo

Il modello di mapping del dispositivo trasforma i dati del dispositivo in uno schema normalizzato. Nella pagina **IoT Connector** fare clic sul pulsante **Configure device mapping** (Configura mapping dispositivo) per passare alla pagina **Mapping dispositivo**. 

[![Clic su Configura mapping dispositivo in IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Nella pagina **Mapping dispositivo** aggiungere lo script seguente all'editor JSON e fare clic su **Salva**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![Mapping dispositivo di IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)


#### <a name="fhir-mapping"></a>Mapping FHIR

Il modello di mapping FHIR trasforma un messaggio normalizzato in una risorsa di osservazione basata su FHIR. Nella pagina **IoT Connector** fare clic sul pulsante **Configure FHIR mapping** (Configura mapping FHIR) per passare alla pagina **Mapping FHIR**.  

[![Clic su Configura mapping FHIR in IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

Nella pagina **Mapping FHIR** aggiungere lo script seguente all'editor JSON e fare clic su **Salva**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![Mapping FHIR in IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Generare una stringa di connessione

Il dispositivo IoMT richiede una stringa di connessione per la connessione e l'invio di messaggi a IoT Connector. Nella pagina **IoT Connector** per l'istanza di IoT Connector appena distribuita selezionare il pulsante **Manage client connections** (Gestisci connessioni client). 

[![Clic su Gestisci connessioni client in IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

Nella pagina **Connessioni** fare clic sul pulsante **Aggiungi** per creare una nuova connessione. 

[![Connessioni di IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Specificare un nome descrittivo per la connessione nella finestra sovrapposta e selezionare il pulsante **Crea**.

[![Nuova connessione per IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Selezionare la connessione appena creata dalla pagina **Connessioni** e copiare il valore del campo **Stringa di connessione primaria** dalla finestra sovrapposta a destra.

[![Stringa di connessione di IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Conservare questa stringa di connessione, perché sarà necessaria in un passaggio successivo. 

## <a name="connect-your-devices-to-iot"></a>Connettere i dispositivi a IoT

Azure offre una vasta gamma di prodotti IoT per la connessione e la gestione dei dispositivi IoT. È possibile creare una soluzione personalizzata basata su PaaS usando Hub IoT di Azure o iniziare con una piattaforma per app IoT gestita con Azure IoT Central. Per questa esercitazione si userà Azure IoT Central, che include modelli di soluzioni per settori specifici che consentono di iniziare subito a usare il servizio.

Distribuire il [modello di applicazione di monitoraggio pazienti continuo](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template). Questo modello include due dispositivi simulati che generano dati in tempo reale che consentono di iniziare a usare il servizio: **Benda intelligente per i segni vitali** e **Ginocchiera intelligente**.

> [!NOTE]
> Ogni volta che i dispositivi reali sono pronti, è possibile usare la stessa applicazione IoT Central per l'[onboarding dei dispositivi](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template) e sostituire i simulatori di dispositivo. Il flusso dei dati del dispositivo verrà automaticamente indirizzato anche a FHIR. 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>Connettere i dati IoT a IoT Connector (anteprima)
Una volta distribuita l'applicazione IoT Central, i due dispositivi simulati predefiniti inizieranno a generare dati di telemetria. Per questa esercitazione, verranno inseriti i dati di telemetria dal simulatore *Benda intelligente per i segni vitali* in FHIR tramite IoT Connector. Per esportare i dati IoT in IoT Connector, è opportuno [configurare l'esportazione dati continua in IoT Central](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export). Nella pagina Esportazione dati continua:
- Selezionare *Hub eventi di Azure* come destinazione dell'esportazione.
- Selezionare il valore *Usa stringa di connessione* per il campo **Spazio dei nomi di Hub eventi**.
- Nel campo **Stringa di connessione** specificare la stringa di connessione di IoT Connector ottenuta in un passaggio precedente.
- Lasciare l'opzione **Telemetria** impostata su *Sì* per il campo **Dati da esportare**.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Visualizzare i dati del dispositivo nell'API di Azure per FHIR

Per visualizzare le risorse di osservazione basate su FHIR create da IoT Connector nell'API di Azure per FHIR è possibile usare Postman. Configurare [Postman per accedere all'API di Azure per FHIR](access-fhir-postman-tutorial.md) e inviare una richiesta `GET` a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` per visualizzare le risorse di osservazione FHIR con il valore di frequenza cardiaca. 

> [!TIP]
> Verificare che l'utente disponga dell'accesso appropriato al piano dati dell'API di Azure per FHIR. Usare il [controllo degli accessi in base al ruolo di Azure](configure-azure-rbac.md) per assegnare i ruoli del piano dati necessari.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessaria, è possibile eliminare un'istanza di IoT Connector rimuovendo il gruppo di risorse associato, il servizio API di Azure per FHIR associato o l'istanza di IoT Connector stessa. 

Per rimuovere direttamente un'istanza di IoT Connector, selezionare l'istanza dalla pagina dei **connettori IoT** per passare alla pagina **IoT Connector** e fare clic sul pulsante **Elimina**. Selezionare **Sì** quando viene richiesta la conferma. 

[![Eliminare l'istanza di IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata distribuita la funzionalità IoT Connector nella risorsa API di Azure per FHIR. Per altre informazioni sul connettore IoT, selezionare uno dei passaggi successivi seguenti:

Comprendere le diverse fasi del flusso di dati nel connettore IoT.

>[!div class="nextstepaction"]
>[Flusso di dati del connettore IoT](iot-data-flow.md)

Informazioni su come configurare il connettore IoT usando un dispositivo e i modelli di mapping FHIR.

>[!div class="nextstepaction"]
>[Modelli di mapping del connettore IoT](iot-mapping-templates.md)

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.
