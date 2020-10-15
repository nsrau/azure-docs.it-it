---
title: "Esercitazione: Ricevere i dati dei dispositivi tramite l'hub IoT di Azure"
description: In questa esercitazione si apprenderà come abilitare il routing dei dati dei dispositivi dall'hub IoT all'API di Azure per FHIR tramite il connettore Azure IoT per FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 3b2e4a1ae5ff43283893b286dafb38491a1181b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308225"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Esercitazione: Ricevere i dati dei dispositivi tramite l'hub IoT di Azure

Il connettore Azure IoT per FHIR* consente di inserire dati da dispositivi IoMT (Internet of Medical Things) nell'API di Azure per FHIR. Nell'argomento di avvio rapido [Distribuire il connettore Azure IoT per FHIR (anteprima) usando il portale di Azure](iot-fhir-portal-quickstart.md) è illustrato un esempio di un dispositivo gestito da Azure IoT Central che [invia dati di telemetria](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) al connettore Azure IoT per FHIR. Il connettore Azure IoT per FHIR può essere usato anche con dispositivi di cui vengono effettuati il provisioning e la gestione tramite l'hub IoT di Azure. Questa esercitazione illustra la procedura per la connessione e l'instradamento dei dati dei dispositivi dall'hub IoT di Azure al connettore Azure IoT per FHIR.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure attiva - [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Un risorsa API di Azure per FHIR con almeno un connettore Azure IoT per FHIR - [Distribuire il connettore Azure IoT per FHIR (anteprima) usando il portale di Azure](iot-fhir-portal-quickstart.md)
- Una risorsa hub IoT di Azure connessa con uno o più dispositivi reali o simulati - [Creare un hub IoT usando il portale di Azure](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Se si usa un'applicazione di dispositivo simulato dell'hub IoT di Azure, è possibile scegliere l'applicazione preferita tra i diversi linguaggi e sistemi supportati.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Ottenere la stringa di connessione per il connettore Azure IoT per FHIR (anteprima)

Per stabilire una connessione sicura con il connettore Azure IoT per FHIR, l'hub IoT di Azure richiede una stringa di connessione. Creare una nuova stringa di connessione per il connettore Azure IoT per FHIR come descritto in [Generare una stringa di connessione](iot-fhir-portal-quickstart.md#generate-a-connection-string). Conservare questa stringa di connessione per usarla nel passaggio successivo.

Il connettore Azure IoT per FHIR usa un'istanza dell'hub eventi di Azure in background per ricevere i messaggi dei dispositivi. La stringa di connessione creata in precedenza è essenzialmente la stringa di connessione a questo hub eventi sottostante.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Connettere l'hub IoT di Azure al connettore Azure IoT per FHIR (anteprima)

L'hub IoT di Azure supporta la funzionalità di [routing dei messaggi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c), che consente di inviare i dati di un dispositivo a vari servizi di Azure come Hub eventi, Account di archiviazione e Bus di servizio. Il connettore Azure IoT per FHIR sfrutta questa funzionalità per connettersi e inviare i dati dei dispositivi dall'hub IoT di Azure all'endpoint dell'hub eventi.

> [!NOTE] 
> Attualmente è possibile usare solo un comando di PowerShell o dell'interfaccia della riga di comando per [creare il routing dei messaggi](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) perché l'hub eventi del connettore Azure IoT per FHIR non è ospitato nella sottoscrizione del cliente, quindi non è visibile tramite il portale di Azure. Una volta però che gli oggetti della route di messaggi sono stati aggiunti tramite PowerShell o l'interfaccia della riga di comando, sono visibili nel portale di Azure e possono essere gestiti da lì.

Per configurare un routing di messaggi sono necessari due passaggi.

### <a name="add-an-endpoint"></a>Aggiungere un endpoint
Questo passaggio definisce un endpoint a cui l'hub IoT deve instradare i dati. Creare questo endpoint usando il comando di PowerShell [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) o il comando dell'interfaccia della riga di comando [az iot hub routing-endpoint create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create), in base alle preferenze.

Ecco l'elenco dei parametri da usare con il comando per creare un endpoint:

|Parametro di PowerShell|Parametro dell'interfaccia della riga di comando|Descrizione|
|---|---|---|
|ResourceGroupName|resource-group|Nome del gruppo di risorse della risorsa hub IoT.|
|Nome|hub-name|Nome della risorsa hub IoT.|
|EndpointName|endpoint-name|Usare il nome che si vuole assegnare all'endpoint che si sta creando.|
|EndpointType|endpoint-type|Tipo di endpoint a cui l'hub IoT deve connettersi. Usare il valore letterale "EventHub" per PowerShell e "eventhub" per l'interfaccia della riga di comando.|
|EndpointResourceGroup|endpoint-resource-group|Nome del gruppo di risorse della risorsa API di Azure per FHIR del connettore Azure IoT per FHIR. Questo valore è disponibile nella pagina Panoramica dell'API di Azure per FHIR.|
|EndpointSubscriptionId|endpoint-subscription-id|ID sottoscrizione della risorsa API di Azure per FHIR del connettore Azure IoT per FHIR. Questo valore è disponibile nella pagina Panoramica dell'API di Azure per FHIR.|
|ConnectionString|connection-string|Stringa di connessione per il connettore Azure IoT per FHIR. Usare il valore ottenuto nel passaggio precedente.|

### <a name="add-a-message-route"></a>Aggiungere una route dei messaggi
Questo passaggio definisce una route dei messaggi usando l'endpoint creato in precedenza. Creare questa route usando il comando di PowerShell [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) o il comando dell'interfaccia della riga di comando [az iot hub route create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create), in base alle preferenze.

Ecco l'elenco dei parametri da usare con il comando per aggiungere una route dei messaggi:

|Parametro di PowerShell|Parametro dell'interfaccia della riga di comando|Descrizione|
|---|---|---|
|ResourceGroupName|g|Nome del gruppo di risorse della risorsa hub IoT.|
|Nome|hub-name|Nome della risorsa hub IoT.|
|EndpointName|endpoint-name|Nome dell'endpoint creato in precedenza.|
|RouteName|route-name|Nome che si vuole assegnare alla route dei messaggi da creare.|
|Source|source-type|Tipo di dati da inviare all'endpoint. Usare il valore letterale "DeviceMessages" per PowerShell e "devicemessages" per l'interfaccia della riga di comando.|

## <a name="send-device-message-to-iot-hub"></a>Inviare il messaggio del dispositivo all'hub IoT

Usare il dispositivo (reale o simulato) per inviare il messaggio di esempio sulla frequenza cardiaca, riportato di seguito, all'hub IoT di Azure. Questo messaggio verrà instradato al connettore Azure IoT per FHIR, dove verrà trasformato in una risorsa di osservazione FHIR e archiviato nell'API di Azure per FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Assicurarsi che il messaggio inviato sia conforme ai [modelli di mapping](iot-mapping-templates.md) configurati con il connettore Azure IoT per FHIR.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Visualizzare i dati del dispositivo nell'API di Azure per FHIR

Per visualizzare le risorse di osservazione FHIR create dal connettore Azure IoT per FHIR nell'API di Azure per FHIR è possibile usare Postman. Configurare [Postman per accedere all'API di Azure per FHIR](access-fhir-postman-tutorial.md) e inviare una richiesta `GET` a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` per visualizzare le risorse di osservazione FHIR con il valore di frequenza cardiaca inviato nel messaggio di esempio precedente.

> [!TIP]
> Verificare che l'utente disponga dell'accesso appropriato al piano dati dell'API di Azure per FHIR. Usare il [controllo degli accessi in base al ruolo di Azure](configure-azure-rbac.md) per assegnare i ruoli del piano dati necessari.


## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato configurato l'hub IoT di Azure per instradare i dati dei dispositivi al connettore Azure IoT per FHIR. Per altre informazioni sul connettore Azure IoT per FHIR, selezionare uno dei passaggi successivi seguenti:

Comprendere le diverse fasi del flusso di dati nel connettore Azure IoT per FHIR.

>[!div class="nextstepaction"]
>[Flusso di dati del connettore Azure IoT per FHIR](iot-data-flow.md)

Informazioni su come configurare il connettore IoT usando un dispositivo e i modelli di mapping FHIR.

>[!div class="nextstepaction"]
>[Modelli di mapping del connettore Azure IoT per FHIR](iot-mapping-templates.md)

*Nel portale di Azure si fa riferimento al Connettore Azure IoT per FHIR come Connettore IoT (anteprima).

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.
