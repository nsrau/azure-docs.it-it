---
title: includere file
description: includere file
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: ad8266d936c272ee2f6bad254738622c3f81bf03
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757146"
---
Iniziare a usare Servizi di comunicazione di Azure usando la libreria client SMS JavaScript di Servizi di comunicazione per inviare messaggi SMS.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versioni Active LTS e Maintenance LTS (8.11.1 e 10.14.1 consigliate).
- Una risorsa di Servizi di comunicazione attiva e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Numero di telefono abilitato per SMS. [Ottenere un numero di telefono](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In un terminale o una finestra di comando eseguire `node --version` per verificare che Node.js sia installato.
- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** dal riquadro di spostamento a sinistra.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

Aprire prima di tutto il terminale o la finestra di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Eseguire `npm init -y` per creare un file **package.json** con le impostazioni predefinite.

```console
npm init -y
```

Usare un editor di testo per creare un file denominato **send-sms.js** nella directory radice del progetto. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

### <a name="install-the-package"></a>Installare il pacchetto

Usare il comando `npm install` per installare la libreria client SMS di Servizi di comunicazione di Azure per JavaScript.

```console
npm install @azure/communication-sms --save
```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client SMS di Servizi di comunicazione di Azure per Node.js.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Questa classe è necessaria per tutte le funzionalità SMS. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per inviare messaggi SMS. |
| SendSmsOptions | Questa interfaccia fornisce opzioni per la configurazione della creazione di report di recapito. Se `enable_delivery_report` è impostato su `true`, verrà generato un evento quando il recapito ha esito positivo. |
| SendMessageRequest | Questa interfaccia è il modello per la creazione della richiesta SMS, ad esempio configurare i numeri di telefono del mittente e del destinatario e il contenuto dell'SMS. |

## <a name="authenticate-the-client"></a>Autenticare il client

Importare **SmsClient** dalla libreria client e crearne un'istanza con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Informazioni su come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).

Aggiungere il codice seguente al file **send-sms.js**:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Inviare SMS

Inviare un messaggio SMS chiamando il metodo `send`. Aggiungere questo codice alla fine del file **send-sms.js**:

```javascript
async function main() {
  await smsClient.send({
    from: "<leased-phone-number>",
    to: ["<to-phone-number>"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true //Optional parameter
  });
}

main();
```

È necessario sostituire `<leased-phone-number>` con un numero di telefono abilitato per SMS associato alla risorsa di Servizi di comunicazione e `<to-phone-number>` con il numero di telefono a cui si desidera inviare un messaggio.

Il parametro `enableDeliveryReport` è facoltativo ed è possibile usarlo per configurare i report di recapito. È utile per gli scenari in cui si vogliono generare eventi quando vengono recapitati messaggi SMS. Per configurare i report di recapito per i messaggi SMS, vedere la guida di avvio rapido [Gestire gli eventi SMS](../handle-sms-events.md).

## <a name="run-the-code"></a>Eseguire il codice

Usare il comando `node` per eseguire il codice aggiunto al file **send-sms.js**.

```console

node ./send-sms.js

```
