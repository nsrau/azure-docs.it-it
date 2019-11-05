---
title: Ripetere il training e distribuire un servizio Web classico
titleSuffix: Azure Machine Learning Studio (classic)
description: Informazioni su come ripetere il training di un modello e aggiornare un servizio Web classico per usare il modello appena sottoposto a training in Azure Machine Learning Studio (classico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 477e099bb759e09402a245b693d95f3b3980699e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480197"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Ripetere il training e distribuire un servizio Web classico in studio (classico)

La ripetizione del training dei modelli di Machine Learning è un modo per garantire che rimangano accurati e si basino sui dati più rilevanti disponibili. In questo articolo viene illustrato come ripetere il training di un servizio Web classico di studio (classico). Per una guida su come ripetere il training di un nuovo servizio Web di studio (classico), [vedere questo articolo sulle procedure.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si disponga già di un esperimento di ripetizione del training e di un esperimento predittivo. La procedura è illustrata in [Ripetere il training e distribuire un modello di Machine Learning](/azure/machine-learning/studio/retrain-machine-learning-model). Tuttavia, invece di distribuire il modello di Machine Learning come nuovo servizio Web, si distribuirà l'esperimento predittivo come servizio Web classico.
     
## <a name="add-a-new-endpoint"></a>Aggiungere un nuovo endpoint

Il servizio Web predittivo distribuito contiene un endpoint di assegnazione dei punteggi predefinito che viene mantenuto sincronizzato con il modello con training originale con esperimenti di training e di assegnazione dei punteggi. Per aggiornare il servizio Web con un nuovo modello con training, è necessario creare un nuovo endpoint di assegnazione dei punteggi.

Esistono due modi per aggiungere un nuovo endpoint a un servizio Web:

* A livello di codice
* Tramite il portale dei servizi Web di Azure

> [!NOTE]
> Assicurarsi di aggiungere l'endpoint al servizio Web predittivo, non al servizio Web di training. Se sono stati distribuiti correttamente sia un servizio Web di training che uno predittivo, verranno visualizzati due servizi Web elencati separatamente. Il servizio Web predittivo deve terminare con "[predictive exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Aggiungere un endpoint a livello di codice

È possibile aggiungere endpoint di assegnazione dei punteggi usando il codice di esempio disponibile in questo [repository GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Usare il portale dei servizi Web di Azure per aggiungere un endpoint

1. In Machine Learning Studio (classico), nella colonna di spostamento a sinistra fare clic su servizi Web.
1. Nella parte inferiore del dashboard dei servizi Web, fare clic su **Manage endpoints preview** (Gestisci anteprima endpoint).
1. Fare clic su **Aggiungi**.
1. Immettere un nome e una descrizione per il nuovo endpoint. Selezionare il livello di registrazione e indicare se i dati di esempio sono abilitati. Per altre informazioni sulla registrazione, vedere [Abilitare la registrazione per i servizi Web di Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aggiornare il modello con training dell'endpoint aggiunto

### <a name="retrieve-patch-url"></a>Recuperare l'URL PATCH

### <a name="option-1-programmatically"></a>Opzione 1: a livello di codice

Per ottenere l'URL PATCH corretto a livello di codice, seguire questa procedura:

1. Eseguire il codice di esempio [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
1. Dall'output di AddEndpoint, trovare il valore *HelpLocation* e copiare l'URL.

   ![HelpLocation nell'output dell'esempio addEndpoint.](./media/retrain-classic/addEndpoint-output.png)
1. Incollare l'URL in un browser per passare a una pagina contenente i collegamenti della guida per il servizio Web.
1. Fare clic sul collegamento **Aggiorna risorsa** per aprire la pagina della guida per l'applicazione di patch.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Opzione 2: usare il portale dei servizi Web Azure Machine Learning

Seguire questa procedura per ottenere l'URL PATCH corretto tramite il portale Web:

1. Accedere al [portale dei servizi Web di Azure Machine Learning](https://services.azureml.net/).
1. Fare clic su **Servizi Web** o **Servizi Web classici** nella parte superiore.
1. Fare clic sul servizio Web di assegnazione dei punteggi in uso (se il nome predefinito del servizio Web non è stato modificato, terminerà in "[Scoring Exp.]").
1. Fare clic su **+Nuovo**.
1. Una volta aggiunto l'endpoint, fare clic sul nome dell'endpoint.
1. Sotto all'URL **patch** fare clic su **API Help** (Guida API) per aprire la pagina della Guida relativa all'applicazione di patch.

> [!NOTE]
> Se l'endpoint è stato aggiunto al servizio Web di training invece che al servizio Web predittivo, si riceverà l'errore seguente quando si fa clic sul collegamento **Aggiorna risorsa** : "si è verificato un errore, ma questa funzionalità non è supportata o non è disponibile in questo contesto. Questo servizio Web non dispone di alcuna risorsa aggiornabile. Ci scusiamo per l'inconveniente e stiamo lavorando per migliorare questo flusso di lavoro.
>

La pagina della guida di PATCH contiene l'URL PATCH da usare e fornisce il codice di esempio che è possibile usare per chiamarlo.

![URL della patch.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Aggiornare l'endpoint

Ora è possibile usare il modello con training per aggiornare l'endpoint dei punteggi creato prima.

Il codice di esempio seguente mostra come usare *BaseLocation*, *RelativeLocation*, *SasBlobToken* e l'URL PATCH per aggiornare l'endpoint.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

I valori di *apiKey* e *endpointUrl* per la chiamata possono essere ottenuti dal dashboard dell'endpoint.

Il valore del parametro *Name* in *Resources* deve corrispondere al nome della risorsa del modello sottoposto a training nell'esperimento predittivo. Per ottenere il nome della risorsa:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu a sinistra fare clic su **Machine Learning**.
1. In Nome fare clic sull'area di lavoro e quindi su **Servizi Web**.
1. In Nome fare clic su **Census Model [predictive exp.]** .
1. Fare clic sul nuovo endpoint aggiunto.
1. Nel dashboard dell'endpoint fare clic su **Aggiorna risorsa**.
1. Nella pagina della documentazione sull'aggiornamento dell'API di risorsa per il servizio Web è possibile trovare **Nome risorsa** in **Risorse aggiornabili**.

Se il token di firma di accesso condiviso scade prima di avere terminato l'aggiornamento dell'endpoint, è necessario eseguire un'operazione GET con l'ID processo per ottenere un nuovo token.

Al termine dell'esecuzione del codice, il nuovo endpoint verrà avviato con il modello di cui è stato ripetuto il training in circa 30 secondi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come gestire i servizi Web o tenere traccia di più esecuzioni degli esperimenti, vedere gli articoli seguenti:

* [Esplorare il portale dei servizi Web](manage-new-webservice.md)
* [Gestire le iterazioni dell'esperimento](manage-experiment-iterations.md)