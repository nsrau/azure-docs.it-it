---
title: Collegare Servizi cognitivi a un set di competenze
titleSuffix: Azure Cognitive Search
description: Istruzioni per il collegamento di una sottoscrizione all-in-one di Servizi cognitivi a una pipeline di arricchimento dell'io in Ricerca cognitiva di Azure.Instructions for attaching a Cognitive Services all-in-one subscription to an AI enrichment pipeline in Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472452"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Collegare una risorsa di Servizi cognitivi a un set di competenze in Ricerca cognitiva di AzureAttach a Cognitive Services resource to a skillset in Azure Cognitive Search 

Quando si configura una pipeline di arricchimento in Ricerca cognitiva di Azure, è possibile arricchire gratuitamente un numero limitato di documenti. Per carichi di lavoro più grandi e più frequenti, è necessario collegare una risorsa servizi cognitivi fatturabili.

In questo articolo verrà illustrato come associare una risorsa assegnando una chiave a un set di competenze che definisce una pipeline di arricchimento.

## <a name="resources-used-during-enrichment"></a>Risorse utilizzate durante l'arricchimento

Ricerca cognitiva di Azure ha una dipendenza dai servizi cognitivi, tra cui [Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/) per l'analisi delle immagini e il riconoscimento ottico dei caratteri (OCR), analisi [del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per l'elaborazione del linguaggio naturale e altri arricchimenti come la [traduzione del](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)testo . Nel contesto dell'arricchimento in Ricerca cognitiva di Azure, questi algoritmi di iA vengono racchiusi all'interno di una *competenza,* inserita in un set di *competenze*e a cui fa riferimento un *indicizzatore* durante l'indicizzazione.

## <a name="how-billing-works"></a>Modalità di funzionamento della fatturazione

+ Ricerca cognitiva di Azure usa la chiave di risorsa Servizi cognitivi fornita in un set di competenze per fatturare l'arricchimento di immagini e testo. L'esecuzione delle competenze fatturabili è al [prezzo pay-as-you go](https://azure.microsoft.com/pricing/details/cognitive-services/)dei Servizi cognitivi.

+ L'estrazione delle immagini è un'operazione di Ricerca cognitiva di Azure che si verifica quando i documenti vengono violati prima dell'arricchimento. L'estrazione dell'immagine è fatturabile. Per i prezzi per l'estrazione delle immagini, vedere la [pagina dei prezzi di Ricerca cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400)di Azure.For image extraction pricing, see the Azure Cognitive Search pricing page .

+ L'estrazione del testo si verifica anche durante la frase di cracking del documento. Non è fatturabile.

+ Le competenze che non chiamano servizi cognitivi, incluse le competenze Condizionale, Shaper, Unione testo e Suddivisione testo, non sono fatturabili.

## <a name="same-region-requirement"></a>Requisito della stessa area

È necessario che Ricerca cognitiva di Azure e Servizi cognitivi di Azure esistano all'interno della stessa area. In caso contrario, verrà visualizzato questo messaggio in fase di esecuzione:Otherwise, you will get this message at run time:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Non è possibile spostare un servizio tra aree diverse. Se viene visualizzato questo errore, è necessario creare una nuova risorsa Servizi cognitivi nella stessa area di Ricerca cognitiva di Azure.If you get this error, you should create a new Cognitive Services resource in the same region as Azure Cognitive Search.

> [!NOTE]
> Alcune competenze incorporate si basano su servizi cognitivi non regionali (ad esempio, la competenza di traduzione del [testo).](cognitive-search-skill-text-translation.md) L'uso di una competenza non regionale significa che la richiesta potrebbe essere gestita in un'area diversa dall'area Ricerca cognitiva di Azure.Using a non-regional skill means that your request might be serviced in a region other than the Azure Cognitive Search region. Per altre informazioni sui servizi non regionali, vedere la pagina [Prodotto servizi cognitivi per area geografica.](https://aka.ms/allinoneregioninfo)

## <a name="use-free-resources"></a>Usare risorse gratuite

È possibile utilizzare un'opzione di elaborazione gratuita limitata per completare il tutorial di arricchimento AI e gli esercizi di avvio rapido.

Le risorse gratuite (arricchitori limitati) sono limitate a 20 documenti al giorno, per indicizzatore. È possibile eliminare e ricreare l'indicizzatore per reimpostare il contatore.

1. Aprire l'Importazione guidata dati:

   ![Aprire l'Importazione guidata dati](media/search-get-started-portal/import-data-cmd.png "Aprire l'Importazione guidata dati")

1. Scegliere un'origine dati e continuare con **Aggiungi arricchimento aico (facoltativo).** Per una procedura dettagliata di questa procedura guidata, vedere Creare un indice nel portale di Azure.For a step-by-step walkthrough of this wizard, see [Create an index in the Azure portal.](search-get-started-portal.md)

1. Espandere **Collega servizi cognitivi** e quindi selezionare **Gratuito (arricchimenti limitati)**:

   ![Sezione Collegamento cognitivo espansa](./media/cognitive-search-attach-cognitive-services/attach1.png "Sezione Collegamento cognitivo espansa")

1. È ora possibile continuare con i passaggi successivi, tra cui **Aggiungi competenze cognitive**.

## <a name="use-billable-resources"></a>Usare risorse fatturabili

Per i carichi di lavoro che creano più di 20 arricchimenti al giorno, assicurarsi di collegare una risorsa di Servizi cognitivi fatturabile. È consigliabile collegare sempre una risorsa Servizi cognitivi fatturabile, anche se non si intende mai chiamare le API di Servizi cognitivi. L'associazione di una risorsa ha la precedenza sul limite giornaliero.

Ti vengono addebitati solo le competenze che chiamano le API di Servizi cognitivi. Non vengono fatturate [competenze personalizzate](cognitive-search-create-custom-skill-example.md)o competenze come [la fusione](cognitive-search-skill-textmerger.md)del testo, il [separatore](cognitive-search-skill-textsplit.md)di testo e [lo shaper](cognitive-search-skill-shaper.md), che non sono basate su API.

1. Aprire l'Importazione guidata dati, scegliere un'origine dati e continuare con **Aggiungi arricchimento aido (facoltativo).**

1. Espandere **Collega servizi cognitivi** e quindi selezionare **Crea nuova risorsa Servizi cognitivi**. Viene aperta una nuova scheda in cui è possibile creare la risorsa:A new tab opens so that you can create the resource:

   ![Creare una risorsa per Servizi cognitivi](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Creare una risorsa per Servizi cognitivi")

1. Nell'elenco Posizione selezionare l'area in cui si trova il servizio Ricerca cognitiva di Azure.In the **Location** list, select the region where your Azure Cognitive Search service is located. Assicurarsi di utilizzare questa area per motivi di prestazioni. L'utilizzo di questa area annulla anche i costi della larghezza di banda in uscita tra le aree.

1. Nell'elenco **Livello dei prezzi** selezionare S0 per ottenere la raccolta all-in-one di funzionalità di Servizi cognitivi, incluse le funzionalità di visione e linguaggio che supportano le competenze predefinite fornite da Ricerca cognitiva di Azure.In the Pricing tier list, select **S0** to get the all-in-one collection of Cognitive Services features, including the Vision and Language features that back the built-in skills provided by Azure Cognitive Search.

   Per il livello S0, è possibile trovare le tariffe per carichi di lavoro specifici nella pagina dei prezzi di [Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Nell'elenco **Seleziona offerta** verificare che **l'opzione Servizi cognitivi** sia selezionata.
   + In **Funzionalità del linguaggio,** le tariffe per **Text Analytics Standard** si applicano all'indicizzazione AI.
   + In **Funzionalità di visione,** si applicano le tariffe per **Visione computerizzata S1.**

1. Selezionare **Crea** per eseguire il provisioning della nuova risorsa Servizi cognitivi.

1. Tornare alla scheda precedente, che contiene l'Importazione guidata dati. Selezionare Aggiorna per visualizzare la risorsa Servizi cognitivi e quindi selezionare la risorsa:Select **Refresh** to show the Cognitive Services resource, and then select the resource:

   ![Selezionare la risorsa Servizi cognitivi](./media/cognitive-search-attach-cognitive-services/attach2.png "Selezionare la risorsa Servizi cognitivi")

1. Espandere la sezione **Aggiungi competenze cognitive** per selezionare le competenze cognitive specifiche che si desidera eseguire sui dati. Completare il resto della procedura guidata.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Collegare un set di competenze esistente a una risorsa Servizi cognitivi

Se si ha un set di competenze esistenti, è possibile collegarlo a una risorsa Servizi cognitivi nuova o diversa.

1. Nella pagina **Panoramica del servizio** selezionare **Set di competenze:**

   ![Scheda Competenze](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Scheda Competenze")

1. Selezionare il nome del set di competenze, quindi selezionare una risorsa esistente o crearne una nuova. Selezionare **OK** per confermare le modifiche.

   ![Elenco risorse set di competenze](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Elenco risorse set di competenze")

   Tenere presente che l'opzione **Gratuito (arricchimenti limitati)** limita a 20 documenti al giorno e che è possibile usare **Crea nuova risorsa Servizi cognitivi** per eseguire il provisioning di una nuova risorsa fatturabile. Se si crea una nuova risorsa, selezionare **Aggiorna** per aggiornare l'elenco delle risorse Servizi cognitivi, quindi selezionare la risorsa.

## <a name="attach-cognitive-services-programmatically"></a>Collegare Servizi cognitivi a livello di codice

Quando si definisce il set di competenze a livello di codice, aggiungere una sezione `cognitiveServices` al set di competenze. In questa sezione includere la chiave della risorsa Servizi cognitivi che si desidera associare al set di competenze. Tenere presente che la risorsa deve trovarsi nella stessa area della risorsa Ricerca cognitiva di Azure.Remember that the resource must be in the same region as your Azure Cognitive Search resource. Aggiungere anche `@odata.type` e impostarlo su `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Il formato è indicato nel seguente esempio. Si `cognitiveServices` noti la sezione alla fine della definizione.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Esempio: stimare i costi

Per stimare i costi associati all'indicizzazione della ricerca cognitiva, iniziare con un'idea dell'aspetto medio di un documento in modo da poter eseguire alcuni numeri. Ad esempio, è possibile approssimare:For example, you might approximate:

+ 1.000 PDF.
+ Sei pagine ciascuna.
+ Un'immagine per pagina (6.000 immagini).
+ 3.000 caratteri per pagina.

Si supponga una pipeline che consiste nel cracking di documenti di ogni PDF, estrazione di immagini e testo, riconoscimento ottico dei caratteri (OCR) delle immagini e riconoscimento di entità delle organizzazioni.

I prezzi indicati in questo articolo sono ipotetici. Sono usati per illustrare il processo di stima. I costi potrebbero essere inferiori. Per i prezzi effettivi delle transazioni, vedere Prezzi dei [servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Per l'individuazione di documenti con contenuto di testo e immagini, attualmente l'estrazione di testo è gratuita. Per 6.000 immagini, assumere 1 USD per ogni 1.000 immagini estratte. Questo è un costo di 6,00 dollari per questo passaggio.

2. Per il riconoscimento ottico dei caratteri di 6.000 immagini in inglese, la competenza cognitiva OCR usa l'algoritmo migliore (DescribeText). Supponendo un costo di 2,50 dollari per l’analisi di 1.000 immagini, il costo raggiunto in questo passaggio sarebbe di 15 dollari.

3. Per l'estrazione di entità, si avrebbe un totale di tre record di testo per pagina. (ogni record è composto da 1.000 caratteri). Tre record di testo per pagina moltiplicati per 6.000 pagine equivale a 18.000 record di testo. Supponendo un prezzo di 2 dollari ogni 1.000 record di testo, in questo passaggio si raggiungerebbe un costo di 36 dollari.

Mettendo tutto insieme, pagheresti circa 57,00 dollari per ingerire 1.000 documenti PDF di questo tipo con il set di competenze descritto.

## <a name="next-steps"></a>Passaggi successivi
+ [Pagina dei prezzi di Ricerca cognitiva di AzureAzure Cognitive Search pricing page](https://azure.microsoft.com/pricing/details/search/)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
