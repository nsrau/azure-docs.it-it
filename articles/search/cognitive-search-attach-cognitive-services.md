---
title: Allegare la risorsa Servizi cognitivi a un set di competenze - Ricerca di Azure
description: Istruzioni per collegare una sottoscrizione di Servizi cognitivi tutto in uno a una pipeline di arricchimento cognitivo in Ricerca di Azure.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bad64f439d45581f8f4b55ea1ac849db1e27cb76
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024592"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Allegare la risorsa Servizi cognitivi a un set di competenze - Ricerca di Azure 

Unità di algoritmi di intelligenza artificiale il [pipeline di indicizzazione cognitive](cognitive-search-concept-intro.md) utilizzate per l'elaborazione dati non strutturati in ricerca di Azure. Questi algoritmi sono basati sulle [risorse di Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/), tra cui [Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/) per l'analisi delle immagini e il riconoscimento ottico dei caratteri (OCR) e [Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per il riconoscimento di entità, l'estrazione di frasi chiave e altri arricchimenti.

È possibile arricchire gratuitamente un numero limitato di documenti oppure è possibile collegare una risorsa di Servizi cognitivi fatturabile per carichi di lavoro più grandi e più frequenti. L'articolo illustra come associare una risorsa di Servizi cognitivi al set di competenze cognitive per arricchire i dati durante l'[indicizzazione di Ricerca di Azure](search-what-is-an-index.md).

Se la pipeline è costituita da competenze non correlate alle API Servizi cognitivi, è comunque necessario collegare una risorsa Servizi cognitivi. In questo modo, si esegue l'override della risorsa **Gratuito**, che consente solo un numero ridotto di arricchimenti al giorno. Non sono previsti addebiti per le competenze non associate alle API Servizi cognitivi, tra cui: [competenze personalizzate](cognitive-search-create-custom-skill-example.md), [di unione testo](cognitive-search-skill-textmerger.md), [di divisione testo](cognitive-search-skill-textsplit.md) e [di shaping](cognitive-search-skill-shaper.md).

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario collegare una risorsa fatturabile di Servizi cognitivi. Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze incorporate verrà addebitato alla esistente [servizi cognitivi pay-capacità di passare prezzo](https://azure.microsoft.com/pricing/details/cognitive-services/). Prezzi di estrazione di immagini è descritta nel [pagina dei prezzi di ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="use-free-resources"></a>Usare risorse gratuite

È possibile usare un'opzione di elaborazione limitata e gratuita per eseguire gli esercizi di esercitazione e l'avvio rapido di ricerca cognitiva. 

**Gratuito (miglioramenti limitati)** sono limitate a 20 documenti al giorno, per ogni sottoscrizione.

1. Aprire la procedura guidata **Importa dati**.

   ![Comando Importa dati](media/search-get-started-portal/import-data-cmd2.png "Comando Importa dati")

1. Scegliere un'origine dati e passare a **Aggiungi ricerca cognitiva (facoltativo)**. Per una procedura dettagliata su questa procedura guidata, vedere [Usare gli strumenti predefiniti del portale per importazione, indicizzazione e query in Ricerca di Azure](search-get-started-portal.md).

1. Espandere **Collega Servizi cognitivi** e selezionare **Gratuito (miglioramenti limitati)**.

   ![Sezione Collega Servizi cognitivi espansa](./media/cognitive-search-attach-cognitive-services/attach1.png "Sezione Collega Servizi cognitivi espansa")

Continuare con il passaggio successivo, **Aggiungi arricchimenti**. Per una descrizione delle competenze disponibili nel portale, vedere ["Passaggio 2: Aggiungere competenze cognitive"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) nell'argomento di avvio rapido sulla ricerca cognitiva.

## <a name="use-billable-resources"></a>Usare risorse fatturabili

Per carichi di lavoro di oltre 20 arricchimenti al giorno, è necessario collegare una risorsa Servizi cognitivi fatturabile. 

Vengono addebitate solo le competenze che chiamano le API Servizi cognitivi. Le competenze non basate sull'API, ad esempio [competenze personalizzate](cognitive-search-create-custom-skill-example.md), [di unione testo](cognitive-search-skill-textmerger.md), [di divisione testo](cognitive-search-skill-textsplit.md) e [di shaping](cognitive-search-skill-shaper.md) non vengono addebitate.

1. Aprire il **Importa dati** procedura guidata, scegliere un'origine dati e continuare a **Aggiungi ricerca cognitiva (facoltativo)**. 

1. Espandere **collegare servizi cognitivi** e quindi selezionare **Crea nuova risorsa di servizi cognitivi**. Verrà visualizzata una nuova scheda in modo che sia possibile creare la risorsa. 

   ![Crea una risorsa di servizi cognitivi](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "crea una risorsa di servizi cognitivi")

1. Nel percorso, scegliere la stessa area di ricerca di Azure per evitare costi di larghezza di banda in uscita tra aree.

1. Nel piano tariffario, scegliere **S0** per ottenere la raccolta all-in-one delle funzionalità di servizi cognitivi, incluse le funzionalità di visione artificiale e linguaggio che supportano le competenze predefinite usate dalla ricerca di Azure. 

   Per il livello S0, è possibile trovare tariffe per carichi di lavoro specifici nel [pagina dei prezzi di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Nelle **seleziona offrono**, assicurarsi che *servizi cognitivi* sia selezionata.
   + In funzionalità del linguaggio, le tariffe per la *testo Analitica Standard* si applicano all'indicizzazione di intelligenza artificiale. 
   + In funzionalità di visione artificiale, le tariffe per la *Computer Vision S1* vengono applicati.

1. Fare clic su **Crea** per effettuare il provisioning della nuova risorsa Servizi cognitivi. 

1. Tornare alla scheda precedente contenente la procedura guidata **Importa dati**. Fare clic su **Aggiorna** per visualizzare la risorsa Servizi cognitivi e quindi selezionare la risorsa.

   ![Risorsa Servizi cognitivi selezionata](./media/cognitive-search-attach-cognitive-services/attach2.png "Risorsa Servizi cognitivi selezionata")

1. Espandere la sezione **Aggiungi arricchimenti** per selezionare le competenze cognitive specifiche che si intende eseguire sui dati e continuare con il resto del flusso. Per una descrizione delle competenze disponibili nel portale, vedere ["Passaggio 2: Aggiungere competenze cognitive"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) nell'argomento di avvio rapido sulla ricerca cognitiva.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Collegare un set di competenze esistente a una risorsa Servizi cognitivi

Se si ha un set di competenze esistenti, è possibile collegarlo a una risorsa Servizi cognitivi nuova o diversa.

1. Nella pagina **Panoramica del servizio** fare clic su **Set di competenze**.

   ![Scheda Set di competenze](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Scheda Set di competenze")

1. Fare clic sul nome del set di competenze e quindi selezionare una risorsa esistente o crearne una nuova. Fare clic su **OK** per confermare le modifiche. 

   ![Elenco di risorse set di competenze](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Elenco di risorse set di competenze")

Ricordare che **Gratuito (miglioramenti limitati)** è limitato a 20 documenti ogni giorno e che **Crea nuova risorsa Servizi cognitivi** viene usato per effettuare il provisioning di una nuova risorsa fatturabile. Se si crea una nuova risorsa, selezionare **Aggiorna** per aggiornare l'elenco delle risorse Servizi cognitivi, quindi selezionare la risorsa.

## <a name="attach-cognitive-services-programmatically"></a>Collegare Servizi cognitivi a livello di codice

Quando si definisce il set di competenze a livello di codice, aggiungere una sezione `cognitiveServices` al set di competenze. Nella sezione, includere la chiave della risorsa Servizi cognitivi da associare al set di competenze. Ricordare che la risorsa deve essere nella stessa area di Ricerca di Azure. Aggiungere anche `@odata.type` e impostarlo su `#Microsoft.Azure.Search.CognitiveServicesByKey`. 

Il formato è indicato nel seguente esempio. Si noti la sezione cognitiveServices in fondo alla definizione

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

Per stimare i costi associati all'indicizzazione di ricerca cognitiva, iniziare basandosi su un documento medio, in modo da poter inserire qualche cifra. Ad esempio, a scopo di stima, si potrebbero inserire i valori approssimativi:

+ 1.000 file PDF
+ Sei pagine ognuno
+ Un'immagine per pagina (6.000 immagini)
+ 3.000 caratteri per pagina

Si presuppone una pipeline costituita da decifrazione del documento di ogni file PDF con estrazione di testo e immagini, riconoscimento ottico dei caratteri (OCR) di immagini e riconoscimento di entità delle organizzazioni. 

In questo esercizio viene usato il prezzo più alto per ogni transazione. I costi effettivi possono essere inferiori vista l'applicazione di prezzi progressivi. Vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Per l'individuazione di documenti con contenuto di testo e immagini, attualmente l'estrazione di testo è gratuita. Per 6.000 immagini, si supponga un costo di 1 dollaro per ogni 1.000 immagini estratte, con un costo di 6 dollari per questo passaggio.

2. Per il riconoscimento ottico dei caratteri di 6.000 immagini in inglese, la competenza cognitiva OCR usa l'algoritmo migliore (DescribeText). Supponendo un costo di 2,50 dollari per l’analisi di 1.000 immagini, il costo raggiunto in questo passaggio sarebbe di 15 dollari.

3. Per l'estrazione delle entità, abbiamo un totale di 3 record di testo per ogni pagina (ogni record è composto da 1.000 caratteri). Tre record di testo per pagina * 6.000 pagine = 18.000 record di testo. Supponendo un prezzo di 2 dollari ogni 1.000 record di testo, in questo passaggio si raggiungerebbe un costo di 36 dollari.

Unendo i due passaggi, si arriverebbe a pagare circa 57 dollari per inserire 1.000 documenti PDF di questo tipo con il set di competenze descritto. 

## <a name="next-steps"></a>Passaggi successivi
+ [Pagina Prezzi di Ricerca di Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
