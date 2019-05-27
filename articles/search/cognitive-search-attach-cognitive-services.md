---
title: Allegare la risorsa Servizi cognitivi a un set di competenze - Ricerca di Azure
description: Istruzioni per collegare una sottoscrizione di servizi cognitivi all-in-one per una pipeline di arricchimento cognitive in ricerca di Azure.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f9a1e82cb60bf0ec32165294e7f4af3e93d042b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158532"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Allegare la risorsa Servizi cognitivi a un set di competenze - Ricerca di Azure 

Unità di algoritmi di intelligenza artificiale per il [pipeline di indicizzazione cognitive](cognitive-search-concept-intro.md) usato per abilitare l'arricchimento dei documenti in ricerca di Azure. Questi algoritmi sono basati sulle risorse di servizi cognitivi di Azure, includendo [visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/) per l'analisi delle immagini e riconoscimento ottico dei caratteri (OCR) e [Analitica testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per il riconoscimento di entità, estrazione di frasi chiave e altri miglioramenti. Usato da ricerca di Azure per scopi di arricchimento dei documenti, gli algoritmi vengono incapsulati all'interno di un *competenza*, inserito in un *insieme di competenze*e referenziati da un' *indicizzatore* durante l'indicizzazione.

È possibile arricchire gratuitamente un numero limitato di documenti oppure si può collegare una risorsa di Servizi cognitivi fatturabile per carichi di lavoro più grandi e più frequenti. In questo articolo si apprenderà come collegare una risorsa di servizi cognitivi fatturabile con le tue competenze cognitive per arricchire i documenti durante [indicizzazione della ricerca di Azure](search-what-is-an-index.md).

> [!NOTE]
> Eventi fatturabili includono chiamate all'API servizi cognitivi e l'immagine estrazione come parte della fase decifrazione del documento in ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti o per acquisire le competenze che non chiamano servizi cognitivi.
>
> L'esecuzione delle competenze fatturabili si trova il [servizi cognitivi pay-capacità di passare prezzo](https://azure.microsoft.com/pricing/details/cognitive-services/). Per l'estrazione di immagini sui prezzi, vedere la [pagina dei prezzi di ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Requisito stessa area

È necessario che esistano ricerca di Azure e servizi cognitivi di Azure nella stessa area. In caso contrario, si riceverà questo messaggio in fase di esecuzione: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` Non è possibile spostare un servizio in aree geografiche. Se si verifica questo errore, è necessario creare un nuovo servizio nella stessa area e ripubblicare gli indici di conseguenza.

## <a name="use-free-resources"></a>Usare risorse gratuite

È possibile usare un'opzione di elaborazione limitata e gratuita per eseguire gli esercizi di esercitazione e l'avvio rapido di ricerca cognitiva.

Gratuito (miglioramenti limitati) risorse sono limitate a 20 documenti al giorno, per ogni sottoscrizione.

1. Aprire l'importazione guidata dei dati:

   ![Aprire il procedura guidata Importa dati](media/search-get-started-portal/import-data-cmd2.png "aprire importazione guidata dati")

1. Scegliere un'origine dati e continuare a **Aggiungi ricerca cognitiva (facoltativo)**. Per una procedura dettagliata di questa procedura guidata, vedere [importazione, l'indice e query usando gli strumenti del portale](search-get-started-portal.md).

1. Espandere **collegare servizi cognitivi** e quindi selezionare **gratuito (miglioramenti limitati)**:

   ![Collegare servizi cognitivi sezione espansa](./media/cognitive-search-attach-cognitive-services/attach1.png "sezione espansa collegare servizi cognitivi")

1. Continuare al passaggio successivo **miglioramenti aggiungere**. Per una descrizione di competenze disponibile nel portale, vedere [passaggio 2: Aggiungi competenze cognitive](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) nella Guida introduttiva ricerca cognitiva.

## <a name="use-billable-resources"></a>Usare risorse fatturabili

Per i carichi di lavoro che creano più di 20 miglioramenti al giorno, assicurarsi di collegare una risorsa di servizi cognitivi fatturabile. È consigliabile sempre collegare una risorsa di servizi cognitivi fatturabile, anche se non si prevede di chiamare le API servizi cognitivi. Collegare una risorsa sostituisce il limite giornaliero.

Ti viene addebitata solo per le competenze che chiamano le API servizi cognitivi. Non sarà addebitato [competenze personalizzate](cognitive-search-create-custom-skill-example.md), o, ad esempio le competenze [fusione di testo](cognitive-search-skill-textmerger.md), [splitter testo](cognitive-search-skill-textsplit.md), e [shaper](cognitive-search-skill-shaper.md), che non sono basate su API.

1. Aprire l'importazione guidata dei dati, scegliere un'origine dati e continuare a **Aggiungi ricerca cognitiva (facoltativo)**.

1. Espandere **collegare servizi cognitivi** e quindi selezionare **Crea nuova risorsa di servizi cognitivi**. Apre una nuova scheda in modo che sia possibile creare la risorsa:

   ![Crea una risorsa di servizi cognitivi](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "crea una risorsa di servizi cognitivi")

1. Nel **posizione** elencare, selezionare l'area in cui si trova il servizio di ricerca di Azure. Assicurarsi di usare quest'area per motivi di prestazioni. Tramite quest'area void anche i costi della larghezza di banda in uscita tra aree.

1. Nel **piano tariffario** elenco, selezionare **S0** per ottenere la raccolta all-in-one delle funzionalità di servizi cognitivi, incluse le funzionalità di visione artificiale e linguaggio che supportano le competenze predefinite usate dalla ricerca di Azure.

   Per il livello S0, è possibile trovare tariffe per carichi di lavoro specifici nel [pagina dei prezzi di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Nel **seleziona offrono** assicurarsi che sia selezionato **servizi cognitivi** sia selezionata.
   + Sotto **Language** funzionalità, le tariffe per **testo Analitica Standard** si applicano all'indicizzazione di intelligenza artificiale.
   + Sotto **visione** funzionalità, le tariffe per **Computer Vision S1** applicare.

1. Selezionare **Create** per effettuare il provisioning della nuova risorsa di servizi cognitivi.

1. Tornare alla scheda precedente, che contiene l'importazione guidata dei dati. Selezionare **Aggiorna** per mostrare la risorsa di servizi cognitivi e quindi selezionare la risorsa:

   ![Selezionare la risorsa di servizi cognitivi](./media/cognitive-search-attach-cognitive-services/attach2.png "selezionare la risorsa di servizi cognitivi")

1. Espandere la **miglioramenti aggiungere** sezione per selezionare le competenze cognitive specifiche che si desidera eseguire sui dati. Completare il resto della procedura guidata. Per una descrizione di competenze disponibile nel portale, vedere [passaggio 2: Aggiungi competenze cognitive](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) nella Guida introduttiva ricerca cognitiva.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Collegare un set di competenze esistente a una risorsa Servizi cognitivi

Se si ha un set di competenze esistenti, è possibile collegarlo a una risorsa Servizi cognitivi nuova o diversa.

1. Nel **Panoramica del servizio** pagina, selezionare **competenze**:

   ![Scheda Set di competenze](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Scheda Set di competenze")

1. Selezionare il nome del set di competenze e quindi selezionare una risorsa esistente o crearne uno nuovo. Selezionare **OK** per confermare le modifiche.

   ![Elenco di risorse set di competenze](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Elenco di risorse set di competenze")

   Tenere presente che il **gratuito (miglioramenti limitati)** opzione è limitato a 20 documenti ogni giorno e che è possibile usare **Crea nuova risorsa di servizi cognitivi** effettuare il provisioning di una nuova risorsa fatturabile. Se si crea una nuova risorsa, selezionare **Aggiorna** per aggiornare l'elenco delle risorse Servizi cognitivi, quindi selezionare la risorsa.

## <a name="attach-cognitive-services-programmatically"></a>Collegare Servizi cognitivi a livello di codice

Quando si definisce il set di competenze a livello di codice, aggiungere una sezione `cognitiveServices` al set di competenze. In questa sezione includono la chiave della risorsa che si desidera associare l'insieme di competenze di servizi cognitivi. Tenere presente che la risorsa deve essere nella stessa area come risorsa di ricerca di Azure. Aggiungere anche `@odata.type` e impostarlo su `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Il formato è indicato nel seguente esempio. Si noti che il `cognitiveServices` sezione alla fine della definizione.

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

## <a name="example-estimate-costs"></a>Esempio: Stimare i costi

Per stimare i costi relativi all'indicizzazione di ricerca cognitiva, iniziare con un'idea di un documento Media come appare in modo che è possibile eseguire alcuni numeri. Ad esempio, potrebbe simulare:

+ 1.000 documenti PDF.
+ Sei pagine ogni.
+ Un'immagine per ogni pagina (6.000 immagini).
+ 3000 caratteri per ogni pagina.

Si presuppone una pipeline costituita da decifrazione del documento di ogni estrazione PDF, immagine e testo, riconoscimento ottico dei caratteri (OCR) di immagini e riconoscimento di entità delle organizzazioni.

I prezzi indicati in questo articolo sono ipotetici. Vengono utilizzati per illustrare il processo di stima. I costi può essere inferiori. Per il prezzo effettivo delle transazioni, vedere [prezzi di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Per l'individuazione di documenti con contenuto di testo e immagini, attualmente l'estrazione di testo è gratuita. 6.000 immagini, presuppone che $1 per ogni 1.000 immagini estratte. Che rappresenta un costo di $6.00 per questo passaggio.

2. Per il riconoscimento ottico dei caratteri di 6.000 immagini in inglese, la competenza cognitiva OCR usa l'algoritmo migliore (DescribeText). Supponendo un costo di 2,50 dollari per l’analisi di 1.000 immagini, il costo raggiunto in questo passaggio sarebbe di 15 dollari.

3. Per l'estrazione di entità, è necessario un totale di tre record di testo per ogni pagina. (ogni record è composto da 1.000 caratteri). È uguale a tre record di testo per ogni pagina moltiplicato per le 6.000 pagine 18.000 dei record di testo. Supponendo un prezzo di 2 dollari ogni 1.000 record di testo, in questo passaggio si raggiungerebbe un costo di 36 dollari.

Riassumendo, dovrai quindi pagare circa 57,00 $ per l'inserimento di 1.000 documenti PDF di questo tipo con il set di competenze descritto.

## <a name="next-steps"></a>Passaggi successivi
+ [Pagina Prezzi di Ricerca di Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
