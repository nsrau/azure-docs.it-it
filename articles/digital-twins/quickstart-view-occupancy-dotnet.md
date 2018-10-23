---
title: Trovare stanze disponibili con Gemelli digitali di Azure (C#) | Microsoft Docs
description: In questa guida introduttiva si eseguono due applicazioni .NET Core di esempio per inviare dati di telemetria simulati relativi a movimento ed emissioni di anidride carbonica in uno spazio in Gemelli digitali di Azure. L'obiettivo è trovare le stanze disponibili con aria pulita dalle API di gestione dopo l'elaborazione nel cloud.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/02/2018
ms.author: alinast
ms.openlocfilehash: e0b47b1322a520ad8b09fd2fe2967e628b5e4e03
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322878"
---
# <a name="quickstart-find-available-rooms-using-azure-digital-twins"></a>Guida introduttiva: Trovare le stanze disponibili usando Gemelli digitali di Azure

Il servizio Gemelli digitali di Azure consente di ricreare un'immagine digitale dell'ambiente fisico. È quindi possibile ricevere notifiche in base agli eventi che si verificano nell'ambiente e personalizzare le risposte agli eventi. 

Questa guida introduttiva usa [due esempi .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) per digitalizzare un edificio di uffici immaginario e mostra come trovare le stanze disponibili nell'edificio. Con Gemelli digitali è possibile associare più sensori all'ambiente. Oltre che la disponibilità di stanze, è possibile scoprire se la qualità dell'aria nella stanza disponibile è ottimale, con l'aiuto di un sensore simulato di anidride carbonica. Una delle applicazioni di esempio genererà dati del sensore casuali per visualizzare più facilmente questo scenario.

## <a name="prerequisites"></a>Prerequisiti

1. Se non si ha un account Azure, [crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

1. Le due applicazioni console eseguite in questa guida introduttiva sono scritte in C#. Sarà necessario installare [.NET Core SDK versione 2.1.403 o successiva](https://www.microsoft.com/net/download) nel computer di sviluppo. Se .NET Core SDK è già installato, è possibile verificare la versione corrente di C# nel computer di sviluppo eseguendo `dotnet --version` in un prompt dei comandi.

1. Scaricare il [progetto C# di esempio](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) ed estrarre l'archivio digital-twins-samples-csharp-master.zip. 


## <a name="create-a-digital-twins-instance"></a>Creare un'istanza di Gemelli digitali

Creare una nuova istanza di Gemelli digitali nel [portale](https://portal.azure.com) seguendo i passaggi di questa sezione.

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]

## <a name="set-permissions-for-your-app"></a>Impostare le autorizzazioni per l'app

In questa sezione, l'applicazione di esempio viene registrata in Azure Active Directory (AAD) in modo che possa accedere all'istanza di Gemelli digitali. Se si ha già un'app registrata in AAD, è possibile riutilizzarla per l'esempio, assicurandosi che sia configurata come indicato in questa sezione. 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>Compilare l'applicazione

Compilare l'applicazione di occupazione seguendo questa procedura:

1. Aprire un prompt dei comandi e passare alla cartella in cui sono stati estratti i file dell'archivio digital-twins-samples-csharp-master.zip.
1. Eseguire `cd occupancy-quickstart/src`.
1. Eseguire `dotnet restore`.
1. Modificare *appSettings.json* per aggiornare le variabili seguenti:
    - *ClientId*: immettere l'*ID applicazione* dell'app registrata in AAD, annotato nella sezione precedente.
    - *Tenant*: immettere l'*ID directory* del tenant di AAD, anche questo annotato nella sezione precedente.
    - *BaseUrl*: URL dell'*API di gestione* dell'istanza di Gemelli digitali, che sarà nel formato seguente: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Sostituire i segnaposto in questo URL con i valori per l'istanza dalla sezione precedente.

## <a name="provision-graph"></a>Effettuare il provisioning del grafico

In questo passaggio viene effettuato il provisioning del grafico spaziale di Gemelli digitali con vari spazi, un dispositivo, due sensori, una funzione personalizzata e un'assegnazione di ruolo. Il provisioning del grafico spaziale verrà effettuato usando il file [*provisionSample.yaml*](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Eseguire `dotnet run ProvisionSample`.
    >[!NOTE]
    >Per autenticare l'utente in Azure AD viene usato lo strumento Accesso dispositivo dell'interfaccia della riga di comando di Azure. L'utente deve immettere un codice specifico per eseguire l'autenticazione tramite la pagina di [accesso Microsoft](https://microsoft.com/devicelogin). Dopo l'inserimento del codice, seguire la procedura per l'autenticazione. L'utente deve eseguire l'autenticazione ogni volta che viene eseguito lo strumento.
    
    >[!TIP]
    > Se viene visualizzato l'errore seguente quando si esegue questo passaggio, verificare di aver copiato correttamente le variabili. 
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. Il passaggio di provisioning potrebbe richiedere alcuni minuti. Effettuerà anche il provisioning di un hub IoT nell'istanza di Gemelli digitali e si ripeterà in ciclo finché lo stato di IoTHub non è `Running`.

    ![Effettuare il provisioning dell'esempio][4]

1. Al termine dell'esecuzione, copiare il valore `ConnectionString` del dispositivo per usarlo nell'esempio del simulatore di dispositivo. Copiare solo la stringa evidenziata nell'immagine seguente:

    ![Effettuare il provisioning dell'esempio][1]

## <a name="send-sensor-data"></a>Inviare i dati dei sensori

È possibile compilare ed eseguire l'applicazione del simulatore di sensori attenendosi alla procedura seguente:

1. Aprire un nuovo prompt dei comandi e passare al progetto scaricato, nella cartella digital-twins-samples-csharp-master.
1. Eseguire `cd device-connectivity`.
1. Eseguire `dotnet restore`.
1. Modificare *appsettings.json* per aggiornare *DeviceConnectionString* con il valore `ConnectionString` precedente.
1. Eseguire `dotnet run` per iniziare a inviare i dati dei sensori. Dovrebbero essere inviati al servizio Gemelli digitali come illustrato nell'immagine seguente:

     ![Connettività dei dispositivi][2]

1. Mantenere il simulatore in esecuzione in modo da poter visualizzare i risultati affiancati con l'azione del passaggio successivo. Questa finestra mostrerà i dati dei sensori simulati inviati a Gemelli digitali e il passaggio successivo eseguirà query in tempo reale per trovare le stanze disponibili con aria pulita.

    >[!TIP]
    > Se viene visualizzato l'errore seguente quando si esegue questo passaggio, verificare di aver copiato correttamente il valore `DeviceConnectionString`.  
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Trovare gli spazi disponibili con aria pulita

Il sensore di esempio sta simulando valori di dati casuali per due sensori, di movimento e di anidride carbonica. Nell'esempio, gli spazi disponibili con aria pulita sono definiti come privi di occupanti e con un livello di anidride carbonica inferiore a 1000 ppm. Se la condizione non viene soddisfatta, lo spazio non è disponibile o la qualità dell'aria non è soddisfacente.

1. Aprire il prompt dei comandi usato per eseguire il passaggio di provisioning precedente.
1. Eseguire `dotnet run GetAvailableAndFreshSpaces`.
1. Esaminare fianco a fianco questo prompt dei comandi e il prompt dei comandi dei dati dei sensori come indicato di seguito. 
1. Un prompt dei comandi invia i dati simulati relativi a movimento e anidride carbonica a Gemelli digitali ogni 5 secondi. L'altro prompt legge in tempo reale il grafico per trovare le stanze disponibili con aria pulita in base ai dati simulati casuali. Mostrerà una delle condizioni seguenti in tempo quasi reale, in base agli ultimi dati dei sensori inviati:
    - Stanze disponibili con aria pulita.
    - Occupata o con qualità dell'aria non soddisfacente.

     ![Ottenere gli spazi disponibili con aria pulita][3]

Per comprendere cosa è accaduto in questa guida introduttiva e quali API sono state chiamate, aprire [Visual Studio Code](https://code.visualstudio.com/Download) con il progetto di codice dell'area di lavoro trovato in digital-twins-samples-csharp (vedere il comando riportato di seguito). Le esercitazioni successive approfondiscono l'esame del codice e illustrano come modificare i dati di configurazione e quali sono le API chiamate. Per maggiori informazioni sulle API di gestione, passare alla pagina Swagger di Gemelli digitali `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net//management/swagger` oppure sfogliare [Swagger per Gemelli digitali](https://docs.westcentralus.azuresmartspaces.net/management/swagger). 

```
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

## <a name="clean-up-resources"></a>Pulire le risorse

Le esercitazioni analizzano in dettaglio come compilare un'applicazione che i facility manager possono usare per aumentare la produttività degli occupanti e gestire l'edificio in modo più efficiente.

Se si prevede di continuare con le esercitazioni, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare i passaggi seguenti per eliminare tutte le risorse create da questa guida introduttiva:

1. Eliminare la cartella che è stata creata durante il download del repository di esempio.
1. Nel menu a sinistra del [portale di Azure](http://portal.azure.com) fare clic su **Tutte le risorse** e quindi selezionare la risorsa Gemelli digitali. Nella parte superiore del riquadro **Tutte le risorse** fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Questa guida introduttiva ha offerto una panoramica di un semplice scenario per la ricerca di stanze con condizioni lavorative ottimali. Per un'analisi più approfondita di questo scenario, passare a questa esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire Gemelli digitali di Azure e configurare un grafico spaziale](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
