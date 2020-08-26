---
title: Come certificare i dispositivi Plug and Play Microsoft Docs
description: Come generatore di dispositivi, informazioni su come eseguire i test e inviare un dispositivo per la certificazione
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: eaae6d90799c11d2475105a30f830db8dfae7fcf
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861212"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>Come certificare i dispositivi Plug and Play

Il programma di certificazione dei dispositivi Plug and Play è costituito da strumenti per verificare che un dispositivo soddisfi i requisiti di certificazione Plug and Play. Gli strumenti consentono inoltre alle organizzazioni di comprendere la disponibilità dei dispositivi Plug and Play. Questi dispositivi certificati sono personalizzati per le soluzioni Internet e consentono di ridurre il time-to-Market.

Questo articolo illustra come:

- Installare l'estensione dello strumento da riga di comando Azure Internet per l'interfaccia della riga di comando di Azure
- Eseguire i test del Plug and Play per convalidare l'applicazione del dispositivo durante la fase di sviluppo  
- Usare il portale per dispositivi certificati di Azure per convalidare l'applicazione del dispositivo

## <a name="prepare-your-device"></a>Preparare il dispositivo

Il codice dell'applicazione in esecuzione nel Plug and Play dell'utente deve:

- Connettersi all'hub di Azure per le cose usando il [servizio Device provisioning (DPS)](../iot-dps/about-iot-dps.md).
- Segui le [convenzioni di gioco](concepts-convention.md) per implementare i dati di telemetria, le proprietà e i comandi.

L'applicazione è un software installato separatamente dal sistema operativo o fornito con il sistema operativo in un'immagine del firmware con Flash per il dispositivo.

Il processo di certificazione verifica che il dispositivo sia Plug and Play compatibile convalidando che l'implementazione del dispositivo corrisponda ai dati di telemetria, alle proprietà e ai comandi definiti nel modello di dispositivo [DTDL (Digital Twin Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) e che il modello sia disponibile nel [repository del modello pubblico di Azure](concepts-model-repository.md).

Per soddisfare i requisiti di certificazione, il dispositivo deve:

- Si connette all'hub Azure Internet con il [DPS](../iot-dps/about-iot-dps.md).
- Implementare i dati di telemetria, le proprietà o i comandi dopo la convenzione Plug and Play.
- Descrivere le interazioni del dispositivo con un modello [DTDL V2](https://aka.ms/dtdl) .
- Pubblicare il modello e tutte le interfacce necessarie nel repository del [modello pubblico di Azure](https://devicemodels.azureiotsolutions.com/) .
- Inviare l'ID modello durante la [registrazione DPS](concepts-developer-guide.md#dps-payload) nel payload di provisioning di DPS.
- Annunciare l'ID modello durante la [connessione MQTT](/concepts-developer-guide.md#model-id-announcement).

## <a name="test-with-the-azure-iot-extension-cli"></a>Eseguire test con l'interfaccia della riga di comando dell'estensione Azure

L' [estensione dell'interfaccia](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/product?view=azure-cli-latest) della riga di comando di Azure è in grado di verificare che l'implementazione del dispositivo corrisponda al modello prima di inviare il dispositivo per la certificazione tramite il portale per i dispositivi Azure Certified

La procedura seguente illustra come preparare ed eseguire i test di certificazione usando l'interfaccia della riga di comando:

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Installare l'estensione Azure per l'interfaccia della riga di comando di Azure

Vedere le istruzioni di installazione per configurare l' [interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) della riga di comando di Azure nell'ambiente in uso.

Per installare l'estensione Azure, eseguire il comando seguente:

```azurecli
az extension add --name azure-iot
```

Per altre informazioni, vedere l' [interfaccia della riga di comando di Azure per Azure](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot?view=azure-cli-latest).

### <a name="create-a-new-product-test"></a>Crea nuovo test del prodotto

Il comando seguente crea un test usando DPS con un metodo di attestazione della chiave simmetrica:

- Crea un nuovo prodotto da testare e genera una configurazione di test. L'output Visualizza le informazioni DPS che il dispositivo deve usare per il provisioning: la chiave primaria, l'ID dispositivo e l'ambito ID.
- Specifica la cartella con i file DTDL che descrivono il modello.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> Quando si usa l'interfaccia della riga di comando, è necessario [accedere](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) alla sottoscrizione.

L'output JSON del comando contiene `primaryKey` , `registrationId` e `scopeID` da usare quando si connette il dispositivo.

Output previsto:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>Connettere il dispositivo

Usare l'output delle informazioni DPS del comando precedente per connettere il dispositivo all'istanza dell'hub del test.

### <a name="manage-and-configure-the-product-tests"></a>Gestire e configurare i test del prodotto

Quando il dispositivo è connesso e pronto per interagire con l'hub Internet, generare un file di configurazione di test del prodotto. Per creare il file:

- Usare il test `id` dall'output del comando precedente.
- Usare il `--wait` parametro per ottenere il test case.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Output previsto:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

È possibile utilizzare il `az iot product test case update` comando per modificare il file di configurazione di test.

### <a name="run-the-tests"></a>Eseguire i test

Dopo aver generato la configurazione di test, il passaggio successivo consiste nell'eseguire i test. Usare lo stesso `devicetestId` comando dei comandi precedenti come parametro per eseguire i test. Controllare i risultati del test per assicurarsi che tutti i test abbiano uno stato `Passed` .

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Esempio di output dell'esecuzione di test

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Eseguire test tramite il portale per i dispositivi Azure Certified

I passaggi seguenti illustrano come usare il [portale per dispositivi Azure Certified](https://aka.ms/acdp) per l'onboarding, registrare i dettagli del prodotto, inviare una guida introduttiva ed eseguire i test di certificazione.

> [!NOTE]
> Al momento della stesura di questo documento, il portale non supporta la pubblicazione nel [Catalogo dei dispositivi Azure per](https://aka.ms/devicecatalog)l'it.

### <a name="onboarding"></a>Onboarding

Per usare il [portale di certificazione](https://aka.ms/acdp), è necessario usare un Azure Active Directory dal tenant aziendale o dell'Istituto di istruzione.

Per pubblicare i modelli nel repository del modello pubblico di Azure, l'account deve essere un membro del [Microsoft Partner Network](https://partner.microsoft.com). Il sistema verifica che l'ID Microsoft Partner Network esista e che l'account sia completamente controllato prima della pubblicazione nel catalogo dei dispositivi.

### <a name="company-profile"></a>Profilo della società

È possibile gestire il profilo aziendale dal menu di spostamento a sinistra. Il profilo della società include l'URL aziendale, l'indirizzo di posta elettronica e il logo della società. Prima di eseguire qualsiasi operazione di certificazione, è necessario accettare il contratto di programma in questa pagina.

Le informazioni sul profilo aziendale vengono usate nella descrizione del dispositivo mostrata nel catalogo dei dispositivi.

### <a name="create-new-project"></a>Creare un nuovo progetto

Per certificare un dispositivo, è prima necessario creare un nuovo progetto.

Passare al [portale di certificazione](https://aka.ms/acdp). Nella pagina **progetti** selezionare *+ Crea nuovo progetto*. Immettere quindi un nome per il progetto, il nome del dispositivo e selezionare una classe del dispositivo.

Le informazioni sul prodotto fornite durante il processo di certificazione rientrano in quattro categorie:

- Informazioni sul dispositivo. Raccoglie informazioni sul dispositivo, ad esempio il nome, la descrizione, le certificazioni e il sistema operativo.
- Guida **introduttiva** . È necessario inviare la guida come documento PDF che verrà approvata dall'amministratore di sistema prima di pubblicare il dispositivo.
- Dettagli di marketing. Fornire informazioni di marketing pronte per il cliente per il dispositivo. Le informazioni di marketing includono come descrizione, una foto e i distributori.
- Certificazioni di settore aggiuntive. Questa sezione facoltativa consente di fornire informazioni aggiuntive sulle altre certificazioni ottenute dal dispositivo.

### <a name="connect-and-test"></a>Connetti e test

Il passaggio Connetti e test verifica che il dispositivo soddisfi i requisiti di certificazione Plug and Play.

È necessario completare tre passaggi:

1. Connetti e individua le interfacce. Il dispositivo deve connettersi al servizio Azure Azure per la certificazione tramite DPS. Scegliere il metodo di autenticazione (certificato X. 509, chiavi simmetriche o Trusted Platform Module) per usare e aggiornare l'applicazione del dispositivo con le informazioni DPS.
1. Esaminare le interfacce. Esaminare l'interfaccia e verificare che ognuno disponga di input di payload che abbiano senso per i test.
1. Eseguire il test. Il sistema testa ogni modello di dispositivo per verificare che i dati di telemetria, le proprietà e i comandi descritti nel modello seguano le convenzioni Plug and Play. Al termine del test, selezionare il collegamento **Visualizza log** per visualizzare i dati di telemetria dal dispositivo e i dati non elaborati inviati alle proprietà del dispositivo gemello dell'hub Internet.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'invio del dispositivo è stato completato, è possibile contattare il team di certificazione del dispositivo all'indirizzo [iotcert@microsoft.com](mailto:iotcert@microsoft.com) per continuare con i passaggi successivi, che includono Microsoft Partner Network convalida dell'appartenenza e una revisione delle guide introduttive. Quando tutti i requisiti sono soddisfatti, è possibile scegliere di includere il dispositivo nel catalogo dei [dispositivi Certified for Azure](https://aka.ms/devicecatalog).
