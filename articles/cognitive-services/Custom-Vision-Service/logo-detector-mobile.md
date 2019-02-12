---
title: 'Esercitazione: Usare un rilevatore di logo personalizzato per riconoscere i servizi di Azure - Visione personalizzata'
titlesuffix: Azure Cognitive Services
description: In questa esercitazione verrà illustrata un'app di esempio che usa lo strumento Visione personalizzata di Azure in uno scenario di rilevamento di logo. Informazioni su come usare lo strumento Visione personalizzata con altri componenti per realizzare un'applicazione end-to-end.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 520e1981544f6ad0a6f1412a47dc8ea50fdd53a5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771435"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Esercitazione: Riconoscere i logo dei servizi di Azure nelle immagini della fotocamera

In questa esercitazione verrà illustrata un'app di esempio che usa lo strumento Visione personalizzata di Azure come parte di uno scenario più ampio. L'app AI Visual Provision, un'app Xamarin.Forms per piattaforme mobili, analizza le immagini della fotocamera dei logo dei servizi di Azure e quindi distribuisce i servizi effettivi all'account Azure dell'utente. Verrà mostrato come viene usato lo strumento Visione personalizzata in coordinamento con altri componenti per realizzare un'utile applicazione end-to-end. È possibile scegliere di eseguire l'intera app autonomamente o limitarsi a completare la parte relativa allo strumento Visione personalizzata della configurazione per scoprire come viene usato dall'app.

In questa esercitazione verranno illustrate le attività seguenti:

> [!div class="checklist"]
> - Creare un rilevatore di oggetti personalizzato per riconoscere i logo dei servizi di Azure
> - Connettere l'app a Visione artificiale e Visione personalizzata di Azure
> - Creare un account dell'entità servizio di Azure per distribuire i servizi di Azure dall'app

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Carico di lavoro Xamarin per Visual Studio (vedere [Installazione di Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Un emulatore di iOS o Android per Visual Studio
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (facoltativo)

## <a name="get-the-source-code"></a>Ottenere il codice sorgente

Se si vuole usare l'app Web fornita, clonare o scaricare il codice sorgente dell'app dal repository [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) in GitHub. Aprire il file *Source/VisualProvision.sln* in Visual Studio. Più avanti, si dovranno apportare le modifiche necessarie ad alcuni file di progetto per poter eseguire l'app.

## <a name="create-an-object-detector"></a>Creare un rilevatore di oggetti

Accedere al [sito Web Visione personalizzata](https://customvision.ai/) e creare un nuovo progetto. Specificare un progetto di rilevamento di oggetti e usare il dominio Logo. In questo modo il servizio potrà usare un algoritmo ottimizzato per il rilevamento di logo. 

![Finestra di dialogo Nuovo progetto nel sito Web Visione personalizzata nel browser Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

A questo punto, è necessario eseguire il training dell'algoritmo di rilevamento di logo caricando le immagini dei logo dei servizi di Azure e contrassegnandole manualmente. Il repository AIVisualProvision include un set di immagini di training che è possibile usare. Nel sito Web selezionare il pulsante **Add images** (Aggiungi immagini) nella scheda **Training Images** (Immagini di training) e quindi passare alla cartella **Documents/Images/Training_DataSet** del repository. Sarà necessario contrassegnare manualmente i logo in ogni immagine, quindi se si intende eseguire semplicemente un test di questo progetto, è consigliabile caricare solo un sottoinsieme delle immagini. È necessario caricare almeno 15 istanze di ogni tag che si prevede di usare.

Dopo aver caricato le immagini di training, selezionare la prima visualizzata. Si aprirà la finestra di assegnazione di tag. Disegnare i riquadri e assegnare tag per ogni logo in ogni immagine. 

![Immagine dei logo con i tag applicati nel sito Web Visione personalizzata](media/azure-logo-tutorial/tag-logos.png)

L'app è configurata per l'uso di specifiche stringhe di tag. Vedere le definizioni nel file *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Dopo aver contrassegnato un'immagine, passare a destra per contrassegnare quella successiva. Al termine, uscire dalla finestra di assegnazione di tag.

## <a name="train-the-object-detector"></a>Eseguire il training del rilevatore di oggetti

Nel riquadro sinistro impostare l'interruttore **Tag** su **Tagged** (Tag applicato). Dovrebbero essere visualizzate tutte le immagini. Quindi fare clic sul pulsante verde nella parte superiore della pagina per eseguire il training del modello. In questo modo l'algoritmo sarà in grado di riconoscere gli stessi tag nelle nuove immagini. Il modello verrà inoltre testato con alcune immagini esistenti per generare i punteggi di accuratezza.

![Sito Web Visione personalizzata con la scheda Training Images e il pulsante Train evidenziato](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Ottenere l'URL di previsione

Dopo aver completato il training del modello, è possibile integrarlo nell'app. A questo scopo, sarà necessario ottenere l'URL dell'endpoint, ossia l'indirizzo del modello, su cui l'app eseguirà una query, e la chiave di previsione, per concedere all'app l'accesso alle richieste di previsione. Nella scheda **Performance** (Prestazioni) fare clic sul pulsante **Prediction URL** (URL di previsione) nella parte superiore della pagina.

![Sito Web Visione personalizzata con una schermata dell'API Prediction che mostra un indirizzo URL e una chiave dell'API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copiare l'URL del file di immagine e il valore `Prediction-key` nei campi appropriati del file *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Esaminare l'utilizzo di Visione personalizzata

Aprire il file *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* per vedere come vengono usati l'URL dell'endpoint e la chiave di Visione personalizzata nell'app. Il metodo **PredictImageContentsAsync** acquisisce un flusso di byte di un file di immagine insieme a un token di annullamento (per la gestione di attività asincrone), chiama l'API Prediction di Visione personalizzata e restituisce il risultato della previsione. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Il risultato assume la forma di un'istanza di **PredictionResult**, che contiene un elenco di istanze di **Prediction**. Un'istanza di **Prediction** contiene un tag rilevato e la posizione del relativo rettangolo delimitatore nell'immagine.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Per altre informazioni su come vengono gestiti questi dati nell'app, avviare il metodo **GetResourcesAsync** definito nel file *Source/VisualProvision/Services/Recognition/RecognitionService.cs*. 

## <a name="add-computer-vision"></a>Aggiungere Visione artificiale

La parte dell'esercitazione relativa a Visione personalizzata è stata completata, ma se si vuole eseguire l'app, sarà necessario integrare anche il servizio Visione artificiale. L'app usa la funzionalità di riconoscimento del testo di Visione artificiale per completare il processo di rilevamento dei logo. Un logo di Azure si può riconoscere dall'aspetto _oppure_ dal testo stampato accanto. A differenza dei modelli di Visione personalizzata, il servizio Visione artificiale viene sottoposto a un training preventivo per eseguire per determinate operazioni su immagini o video.

È sufficiente sottoscrivere il servizio Visione artificiale per ottenere una chiave e un URL dell'endpoint. Per assistenza con questo passaggio, vedere [Come ottenere chiavi di sottoscrizione](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![Il servizio Visione artificiale nel portale di Azure, con il menu Avvio rapido selezionato; un collegamento per le chiavi è evidenziato, così come l'URL dell'endpoint dell'API.](media/azure-logo-tutorial/comvis-keys.png)

Quindi, aprire il file *Source\VisualProvision\AppSettings.cs* e popolare le variabili `ComputerVisionEndpoint` e `ComputerVisionKey` con i valori corretti.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Creare un'entità servizio

L'app richiede un account dell'entità servizio di Azure per distribuire i servizi alla sottoscrizione di Azure. Un'entità servizio consente di delegare specifiche autorizzazione a un'app usando il controllo degli accessi in base al ruolo. Per altre informazioni, vedere la [guida delle entità servizio](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

È possibile creare un'entità servizio usando Azure Cloud Shell o l'interfaccia della riga di comando di Azure, come segue. Prima di tutto, accedere e selezionare la sottoscrizione da usare.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Quindi creare l'entità servizio. Tenere presente che questa operazione potrebbe richiedere del tempo.

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Al termine, dovrebbe essere visualizzato l'output JSON seguente, contenente le credenziali necessarie.

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
* Un modello di Visione personalizzata con training
* Il servizio Visione artificiale
* Un account dell'entità servizio 

Per eseguire l'app, procedere come segue:

1. In Esplora soluzioni di Visual Studio selezionare il progetto VisualProvision.Android o VisualProvision.iOS e scegliere un emulatore corrispondente o un dispositivo mobile connesso dal menu a discesa della barra degli strumenti principale (per eseguire un emulatore iOS, è necessario un dispositivo MacOS). Quindi eseguire l'app.

1. Nella prima schermata caricata immettere l'ID client dell'entità servizio, l'ID tenant e la password. Fare clic sul pulsante **Login** (Accedi).

    > [!NOTE]
    > In alcuni emulatori è possibile che il pulsante **Login** non venga attivato in questo passaggio. In questo caso, interrompere l'app, aprire il file _Source/VisualProvision/Pages/LoginPage.xaml_, individuare l'elemento `Button` denominato LOGIN BUTTON e rimuovere la riga:
      ```xaml
      IsEnabled="{Binding IsValid}"
      ```
    
    > Quindi eseguire di nuovo l'app.

    ![Schermata dell'app con i campi per le credenziali dell'entità servizio](media/azure-logo-tutorial/app-credentials.png)

1. Nella schermata successiva selezionare la propria sottoscrizione di Azure dal menu a discesa, che dovrebbe contenere tutte le sottoscrizioni a cui ha accesso l'entità servizio. Fare clic sul pulsante **Continue** (Continue).

    ![Schermata dell'app con un campo a discesa per la sottoscrizione di Azure di destinazione](media/azure-logo-tutorial/app-az-subscription.png)

    A questo punto l'app potrebbe richiedere di concedere ai dispositivi l'accesso alla fotocamera e all'archivio di foto. Accettare queste autorizzazioni.

1. Quindi, la fotocamera del dispositivo verrà attivata. Acquisire una foto di uno dei logo dei servizi di Azure di cui è stato eseguito il training. In una finestra di dialogo di distribuzione verrà visualizzata la richiesta di selezionare un'area geografica e un gruppo di risorse per i nuovi servizi, come si farebbe se venissero distribuiti dal portale di Azure. 

    ![Schermata della fotocamera di uno smartphone con due ritagli di carta dei logo di Azure visualizzati](media/azure-logo-tutorial/app-camera-capture.png)

    ![Schermata dell'app con i campi per l'immissione dell'area geografica di distribuzione e il gruppo di risorse](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Pulire le risorse 

Se sono stati seguiti tutti i passaggi di questo scenario ed è stata usata l'app per distribuire i servizi di Azure nell'account, assicurarsi di accedere al [portale di Azure](https://ms.portal.azure.com/) al termine e di annullare i servizi che non si vogliono usare.

Inoltre, se si prevede di creare un progetto personalizzato di rilevamento di oggetti con Visione personalizzata in futuro, è consigliabile eliminare il progetto di rilevamento di logo creato in questa esercitazione. La versione di valutazione gratuita di Visione personalizzata consente di creare due progetti. Nel [sito Web di Visione personalizzata](https://customvision.ai) passare a **Projects** (Progetti) e selezionare l'icona del cestino sotto **My New Project** (Mio nuovo progetto).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata ed esplorata un'app Xamarin.Forms completa di tutte le funzionalità che usa il servizio Visione personalizzata per rilevare i logo nelle immagini delle fotocamere di dispositivi mobili. Quindi, leggere le procedure consigliate per creare un modello di Visione personalizzata in modo da poterne creare uno efficace ed accurato per una propria app.

> [!div class="nextstepaction"]
> [Come migliorare il classificatore](getting-started-improving-your-classifier.md)