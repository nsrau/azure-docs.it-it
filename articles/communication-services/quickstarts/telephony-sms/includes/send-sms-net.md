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
ms.openlocfilehash: b4e085a4c81b44f721ddc8385d2dcd0f4a56f42a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944832"
---
Introduzione a Servizi di comunicazione di Azure, con l'invio di messaggi SMS tramite la libreria client SMS di Servizi di comunicazione di Azure per C#.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- La versione più recente della [libreria client .NET Core](https://dotnet.microsoft.com/download/dotnet-core) per il sistema operativo in uso.
- Una risorsa attiva di Servizi di comunicazione e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono abilitato per gli SMS. [Ottenere un numero di telefono](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In una finestra del terminale o di comando eseguire il comando `dotnet` per verificare se la libreria client .NET è installata.
- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** nel riquadro di spostamento sinistro.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `SmsQuickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Passare alla cartella dell'app appena creata e usare il comando `dotnet build` per compilare l'applicazione.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installare il pacchetto

Rimanendo nella directory dell'applicazione, installare il pacchetto della libreria client SMS di Servizi di comunicazione di Azure per .NET usando il comando `dotnet add package`.

```console
dotnet add package Azure.Communication.Sms
```

Aggiungere una direttiva `using` all'inizio di **Program.cs** per includere lo spazio dei nomi `Azure.Communication`.

```csharp

using Azure.Communication.Sms;

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client SMS di Servizi di comunicazione di Azure per C#.

| Nome                                       | Descrizione                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Questa classe è necessaria per tutte le funzionalità SMS. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per inviare messaggi SMS.                           |
| SendSmsOptions | Questa classe fornisce opzioni per la configurazione dei report di recapito. Se enable_delivery_report è impostato su True, verrà generato un evento quando il recapito ha esito positivo |

## <a name="authenticate-the-client"></a>Autenticare il client

 Aprire **Program.cs** in un editor di testo e sostituire il corpo del metodo `Main` con il codice per inizializzare un oggetto `SmsClient` con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Vedere come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Inviare SMS

Inviare un messaggio SMS chiamando il metodo Send. Aggiungere questo codice alla fine del metodo `Main` in **Program.cs**:

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

È necessario sostituire `<leased-phone-number>` con un numero di telefono abilitato per gli SMS associato alla risorsa di Servizi di comunicazione e `<to-phone-number>` con il numero di telefono a cui inviare un messaggio. Tutti i parametri dei numeri di telefono devono essere conformi allo [standard E.164](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164).

Il parametro `EnableDeliveryReport` è facoltativo ed è possibile usarlo per configurare i report di recapito. È utile per gli scenari in cui si vogliono generare eventi quando vengono recapitati messaggi SMS. Per configurare i report di recapito per i messaggi SMS, vedere la guida di avvio rapido [Gestire gli eventi SMS](../handle-sms-events.md).

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```console
dotnet run
```
