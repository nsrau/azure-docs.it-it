---
title: Ripetere il training di un servizio Web classico | Documentazione Microsoft
description: Informazioni su come ripetere il training di un modello a livello di codice e aggiornare il servizio Web per l'uso del modello appena sottoposto a training in Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: aashishb
ms.author: aashishb
manager: hjerez
editor: ''
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 7adc5995b830d049c8dd6412415bd5987e60fe08
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="retrain-a-classic-web-service"></a>Ripetere il training di un servizio Web classico
Il servizio Web predittivo distribuito è l'endpoint dei punteggi predefinito. Gli endpoint predefiniti vengono mantenuti sincronizzati con gli esperimenti di training e di assegnazione dei punteggi di origine, quindi il modello con training per l'endpoint predefinito non può essere sostituito. Per ripetere il training del servizio Web è necessario aggiungere un nuovo endpoint al servizio Web. 

## <a name="prerequisites"></a>prerequisiti
È necessario impostare un esperimento di training e un esperimento predittivo come illustrato in [Ripetere il training dei modelli di Machine Learning in modo programmatico](retrain-models-programmatically.md). 

> [!IMPORTANT]
> L'esperimento predittivo deve essere distribuito come servizio Web di Machine Learning classico. 
> 
> 

Per altre informazioni sulla distribuzione di servizi Web, vedere [Distribuire un servizio Web di Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Aggiungere un nuovo endpoint
Il servizio Web predittivo distribuito contiene un endpoint di assegnazione dei punteggi predefinito che viene mantenuto sincronizzato con il modello con training originale con esperimenti di training e di assegnazione dei punteggi. Per aggiornare il servizio Web con un nuovo modello con training, è necessario creare un nuovo endpoint di assegnazione dei punteggi. 

Per creare un nuovo endpoint dei punteggi, nel servizio Web predittivo che può essere aggiornato con il modello con training:

> [!NOTE]
> Assicurarsi di aggiungere l'endpoint al servizio Web predittivo, non al servizio Web di training. Se sono stati distribuiti correttamente sia un servizio Web di training che uno predittivo, verranno visualizzati due servizi Web elencati separatamente. Il servizio Web predittivo deve terminare con "[predictive exp.]".
> 
> 

Esistono due modi per aggiungere un nuovo endpoint a un servizio Web:

1. A livello di codice
2. Usare il portale dei servizi Web di Microsoft Azure

### <a name="programmatically-add-an-endpoint"></a>Aggiungere un endpoint a livello di codice
È possibile aggiungere endpoint di assegnazione dei punteggi usando il codice di esempio disponibile in questo [repository GitHub](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Usare il portale dei servizi Web di Microsoft Azure per aggiungere un endpoint
1. In Machine Learning Studio fare clic su Web Services (Servizi Web) nella colonna di spostamento a sinistra.
2. Nella parte inferiore del dashboard dei servizi Web, fare clic su **Manage endpoints preview** (Gestisci anteprima endpoint).
3. Fare clic su **Aggiungi**.
4. Immettere un nome e una descrizione per il nuovo endpoint. Selezionare il livello di registrazione e indicare se i dati di esempio sono abilitati. Per altre informazioni sulla registrazione, vedere [Abilitare la registrazione per i servizi Web di Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aggiornare il modello con training dell'endpoint aggiunto
Per completare il processo di ripetizione del training, è necessario aggiornare il modello con training del nuovo endpoint aggiunto.

Se l'endpoint è stato aggiunto usando il codice di esempio, è inclusa la posizione dell'URL della guida identificato dal valore *HelpLocationURL* nell'output.

Per recuperare l'URL del percorso:

1. Copiare e incollare l'URL nel browser.
2. Fare clic sul collegamento Aggiorna risorsa.
3. Copiare l'URL POST della richiesta PATCH. Ad esempio: 
   
     URL DELLA PATCH: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

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
2. Nel menu a sinistra fare clic su **Machine Learning**.
3. In Nome fare clic sull'area di lavoro e quindi su **Servizi Web**.
4. In Nome fare clic su **Census Model [predictive exp.]**.
5. Fare clic sul nuovo endpoint aggiunto.
6. Nel dashboard dell'endpoint fare clic su **Aggiorna risorsa**.
7. Nella pagina della documentazione sull'aggiornamento dell'API di risorsa per il servizio Web è possibile trovare **Nome risorsa** in **Risorse aggiornabili**.

Se il token di firma di accesso condiviso scade prima di avere terminato l'aggiornamento dell'endpoint, è necessario eseguire un'operazione GET con l'ID processo per ottenere un nuovo token.

Al termine dell'esecuzione del codice, il nuovo endpoint verrà avviato con il modello di cui è stato ripetuto il training in circa 30 secondi.

## <a name="summary"></a>Summary
Usando le API per la ripetizione del training, è possibile aggiornare il modello con training di un servizio Web predittivo abilitando scenari come:

* Ripetizione periodica del training del modello con nuovi dati.
* Distribuzione di un modello ai clienti per fare in modo che possano ripetere il training del modello con i propri dati.

## <a name="next-steps"></a>Passaggi successivi
[Risoluzione dei problemi relativi alla ripetizione del training di un servizio Web classico di Azure Machine Learning](troubleshooting-retraining-models.md)

