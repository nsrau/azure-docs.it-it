---
title: 'Esercitazione: Usare un rilevatore di logo personalizzato per riconoscere i servizi di Azure - Visione personalizzata'
titlesuffix: Azure Cognitive Services
description: In questa esercitazione verrà illustrata un'app di esempio che usa lo strumento Visione personalizzata di Azure in uno scenario di rilevamento di logo. Informazioni su come usare lo strumento Visione personalizzata con altri componenti per realizzare un'applicazione end-to-end.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 259787a90b61b171f391dc02276214f17a57d0d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838817"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Esercitazione: Riconoscere i logo dei servizi di Azure nelle immagini della fotocamera

In questa esercitazione verrà illustrata un'app di esempio che usa lo strumento Visione personalizzata di Azure come parte di uno scenario più ampio. L'app AI Visual Provision, un'app Xamarin.Forms per piattaforme mobili, analizza le immagini della fotocamera dei logo dei servizi di Azure e quindi distribuisce i servizi effettivi all'account Azure dell'utente. Si apprenderà come viene usato lo strumento Visione personalizzata in combinazione con altri componenti per realizzare un'utile applicazione end-to-end. Eseguire l'intero scenario dell'app per sé o limitarsi a completare la parte della configurazione relativa a Visione personalizzata ed esaminare il modo in cui lo strumento viene usato dall'app.

In questa esercitazione verranno illustrate le attività seguenti:

> [!div class="checklist"]
> - Creare un rilevatore di oggetti personalizzato per riconoscere i logo dei servizi di Azure.
> - Connettere l'app a Visione artificiale e Visione personalizzata di Azure.
> - Creare un account dell'entità servizio di Azure per distribuire i servizi di Azure dall'app.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Carico di lavoro Xamarin per Visual Studio. Vedere [Installazione di Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows).
- Un emulatore di iOS o Android per Visual Studio
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (facoltativo)

## <a name="get-the-source-code"></a>Ottenere il codice sorgente

Se si vuole usare l'app Web fornita, clonare o scaricare il codice sorgente dell'app dal repository [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) in GitHub. Aprire il file *Source/VisualProvision.sln* in Visual Studio. In seguito verranno modificati alcuni dei file di progetto per poter eseguire l'app.

## <a name="create-an-object-detector"></a>Creare un rilevatore di oggetti

Accedere al [sito Web Visione personalizzata](https://customvision.ai/) e creare un nuovo progetto. Specificare un progetto di rilevamento di oggetti e usare il dominio Logo. In questo modo il servizio potrà usare un algoritmo ottimizzato per il rilevamento di logo. 

![Finestra di dialogo del nuovo progetto nel sito Web di Visione personalizzata nel browser Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Eseguire quindi il training dell'algoritmo di rilevamento di logo caricando le immagini dei logo dei servizi di Azure e applicando tag alle immagini manualmente. Il repository AIVisualProvision include un set di immagini di training che è possibile usare. Sul sito Web selezionare il pulsante **Add images** (Aggiungi immagini) nella scheda **Training Images** (Immagini di training). Passare quindi alla cartella **Documents/Images/Training_DataSet** del repository. Sarà necessario applicare manualmente tag ai logo in ogni immagine, quindi se si intende eseguire semplicemente un test di questo progetto, è consigliabile caricare solo un sottoinsieme delle immagini. Caricare almeno 15 istanze di ogni tag che si intende usare.

Dopo aver caricato le immagini di training, selezionare la prima visualizzata. Si aprirà la finestra di assegnazione di tag. Disegnare i riquadri e assegnare tag per ogni logo in ogni immagine. 

![Applicazione di tag sul sito Web di Visione personalizzata](media/azure-logo-tutorial/tag-logos.png)

L'app è configurata per funzionare con stringhe di tag specifiche. Vedere le definizioni nel file *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Dopo aver applicato tag a un'immagine, passare a destra per applicare tag alla successiva. Al termine, chiudere la finestra per l'applicazione di tag.

## <a name="train-the-object-detector"></a>Eseguire il training del rilevatore di oggetti

Nel riquadro sinistro impostare l'interruttore **Tags** su **Tagged** (Tag applicato) per visualizzare le immagini. Selezionare quindi il pulsante verde nella parte superiore della pagina per eseguire il training del modello. In questo modo l'algoritmo sarà in grado di riconoscere gli stessi tag nelle nuove immagini. Il modello verrà inoltre testato con alcune immagini esistenti per generare i punteggi di accuratezza.

![Sito Web di Visione personalizzata con la scheda Training Images (Immagini di training). In questa schermata è evidenziato il pulsante per l'esecuzione del training](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Ottenere l'URL di previsione

Dopo aver completato il training del modello, è possibile integrarlo nell'app. A questo scopo, sarà necessario ottenere l'URL dell'endpoint, ossia l'indirizzo del modello, su cui l'app eseguirà una query, e la chiave di previsione, per concedere all'app l'accesso alle richieste di previsione. Nella scheda **Performance** (Prestazioni) fare clic sul pulsante **Prediction URL** (URL di previsione) nella parte superiore della pagina.

![Sito Web di Visione personalizzata con una finestra dell'API Prediction che visualizza un indirizzo URL e una chiave API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copiare l'URL del file di immagine e il valore **Prediction-Key** nei campi appropriati del file *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Esaminare l'utilizzo di Visione personalizzata

Aprire il file *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* per vedere come l'app usa l'URL endpoint e la chiave di Visione personalizzata. Il metodo **PredictImageContentsAsync** acquisisce un flusso di byte di un file di immagine insieme a un token di annullamento (per la gestione di attività asincrone), chiama l'API Prediction di Visione personalizzata e restituisce il risultato della previsione. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Il risultato assume la forma di un'istanza di **PredictionResult**, che contiene un elenco di istanze di **Prediction**. Un'istanza di **Prediction** contiene un tag rilevato e la posizione del relativo rettangolo delimitatore nell'immagine.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Per altre informazioni su come l'app gestisce questi dati, iniziare con il metodo **GetResourcesAsync**. Questo metodo è definito nel file *Source/VisualProvision/Services/Recognition/RecognitionService.cs*.  

## <a name="add-computer-vision"></a>Aggiungere Visione artificiale

La parte dell'esercitazione dedicata a Visione personalizzata è terminata. Se si intende eseguire l'app sarà necessario integrare anche il servizio Visione artificiale. L'app usa la funzionalità di riconoscimento del testo di Visione artificiale per integrare il processo di rilevamento del logo. Un logo di Azure può essere riconosciuto dal suo aspetto *o* dal testo stampato accanto. A differenza dei modelli di Visione personalizzata, il servizio Visione artificiale viene sottoposto a un training preventivo per eseguire per determinate operazioni su immagini o video.

Sottoscrivere il servizio Visione artificiale per ottenere una chiave e un URL endpoint. Per assistenza con questo passaggio, vedere [Come ottenere chiavi di sottoscrizione](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![Servizio Visione artificiale nel portale di Azure con il menu Avvio rapido selezionato. Sono evidenziati un collegamento per le chiavi e l'URL dell'endpoint API](media/azure-logo-tutorial/comvis-keys.png)

Aprire quindi il file *Source\VisualProvision\AppSettings.cs* e popolare le variabili `ComputerVisionEndpoint` e `ComputerVisionKey` con i valori corretti.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]

## <a name="create-a-service-principal"></a>Creare un'entità servizio

L'app richiede un account dell'entità servizio di Azure per distribuire i servizi alla sottoscrizione di Azure. Un'entità servizio consente di delegare specifiche autorizzazione a un'app usando il controllo degli accessi in base al ruolo. Per altre informazioni, vedere la [guida delle entità servizio](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

È possibile creare un'entità servizio usando Azure Cloud Shell o l'interfaccia della riga di comando di Azure, come mostrato qui. Per iniziare, accedere e selezionare la sottoscrizione che si vuole usare.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Creare quindi l'entità servizio. Questo processo può richiedere del tempo.

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Al termine verrà visualizzato l'output JSON seguente, contenente le credenziali necessarie.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Prendere nota dei valori `clientId` e `tenantId`. Aggiungerli ai campi appropriati del file *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Esecuzione dell'app

A questo punto, all'app è stato concesso l'accesso a:

- Un modello di Visione personalizzata sottoposto a training
- Il servizio Visione artificiale
- Un account dell'entità servizio

Per eseguire l'app, procedere come segue:

1. In Esplora soluzioni di Visual Studio, selezionare il progetto **VisualProvision.Android** o il progetto **VisualProvision.iOS**. Scegliere un emulatore o un dispositivo mobile connesso corrispondente dal menu a discesa sulla barra degli strumenti principale. Quindi eseguire l'app.

    > [!NOTE]
    > È necessario un dispositivo MacOS per eseguire un emulatore iOS.

1. Nella prima schermata immettere l'ID client dell'entità servizio, l'ID tenant e la password. Selezionare il pulsante **Login** (Accedi).

    > [!NOTE]
    > In alcuni emulatori è possibile che il pulsante **Login** (Accedi) non sia attivo in questa fase. In questo caso arrestare l'app, aprire il file *Source/VisualProvision/Pages/LoginPage.xaml*, individuare l'elemento `Button` denominato **LOGIN BUTTON**, rimuovere la riga seguente ed eseguire di nuovo l'app.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Schermata dell'app con i campi per le credenziali dell'entità servizio](media/azure-logo-tutorial/app-credentials.png)

1. Nella schermata successiva selezionare la sottoscrizione di Azure dal menu a discesa. Questo menu contiene tutte le sottoscrizioni a cui l'entità servizio può accedere. Fare clic sul pulsante **Continue** (Continua). A questo punto l'app potrebbe chiedere di concedere l'accesso alla fotocamera e all'archivio fotografico del dispositivo. Concedere le autorizzazioni di accesso.

    ![Schermata dell'app con un campo a discesa per la sottoscrizione di Azure di destinazione](media/azure-logo-tutorial/app-az-subscription.png)


1. La fotocamera del dispositivo verrà attivata. Scattare una foto di uno dei logo dei servizi di Azure di cui è stato eseguito il training. Una finestra di distribuzione chiederà di selezionare un'area geografica e un gruppo di risorse per i nuovi servizi, come si farebbe se venissero distribuiti dal portale di Azure. 

    ![Schermata della fotocamera di uno smartphone che inquadra due ritagli di carta dei logo di Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Schermata dell'app con i campi per l'immissione dell'area geografica e del gruppo di risorse per la distribuzione](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se sono stati seguiti tutti i passaggi di questo scenario ed è stata usata l'app per distribuire i servizi di Azure nell'account, passare al [portale di Azure](https://ms.portal.azure.com/) per annullare i servizi che non si vogliono usare.

Se si prevede di creare un progetto personalizzato di rilevamento di oggetti con Visione personalizzata, è consigliabile eliminare il progetto di rilevamento di logo creato in questa esercitazione. La versione di valutazione gratuita di Visione personalizzata consente di creare solo due progetti. Per eliminare il progetto di rilevamento di logo, nel [sito Web di Visione personalizzata](https://customvision.ai) aprire **Projects** (Progetti) e selezionare l'icona del cestino in **My New Project** (Mio nuovo progetto).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata ed esaminata un'app Xamarin.Forms completa di tutte le funzionalità che usa il servizio Visione personalizzata per rilevare i logo nelle immagini acquisite con la fotocamera di un dispositivo mobile. Vedere quindi le procedure consigliate per la creazione di un modello di Visione personalizzata per creare un modello efficace e accurato per la propria app.

> [!div class="nextstepaction"]
> [Come migliorare il classificatore](getting-started-improving-your-classifier.md)
