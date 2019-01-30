---
title: Allegare la risorsa Servizi cognitivi a un set di competenze - Ricerca di Azure
description: Istruzioni per collegare una sottoscrizione di Servizi cognitivi tutto in uno a una pipeline di arricchimento cognitivo in Ricerca di Azure.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bfa9bbb9816148182b79a8231f2ddb3e46433804
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413244"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Allegare la risorsa Servizi cognitivi a un set di competenze - Ricerca di Azure 

Gli algoritmi di intelligenza artificiale guidano le [pipeline di ricerca cognitiva](cognitive-search-concept-intro.md) usate per l'elaborazione di dati non strutturati in un'operazione di indicizzazione di Ricerca di Azure. Questi algoritmi sono basati sulle [risorse di Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/), tra cui [Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/) per l'analisi delle immagini e il riconoscimento ottico dei caratteri (OCR) e [Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per il riconoscimento di entità, l'estrazione di frasi chiave e altri arricchimenti.

È possibile arricchire gratuitamente un numero limitato di documenti oppure è possibile collegare una risorsa di Servizi cognitivi fatturabile per carichi di lavoro più grandi e più frequenti. L'articolo illustra come associare una risorsa di Servizi cognitivi al set di competenze cognitive per arricchire i dati durante l'[indicizzazione di Ricerca di Azure](search-what-is-an-index.md).

Se la pipeline è costituita da competenze non correlate alle API Servizi cognitivi, è comunque necessario collegare una risorsa Servizi cognitivi. In questo modo, si esegue l'override della risorsa **Gratuito**, che consente solo un numero ridotto di arricchimenti al giorno. Non sono previsti addebiti per le competenze non associate alle API Servizi cognitivi, tra cui: [competenze personalizzate](cognitive-search-create-custom-skill-example.md), [di unione testo](cognitive-search-skill-textmerger.md), [di divisione testo](cognitive-search-skill-textsplit.md) e [di shaping](cognitive-search-skill-shaper.md).

> [!NOTE]
> Dal 21 dicembre 2018 è possibile associare una risorsa dei Servizi cognitivi a un set di competenze della Ricerca di Azure. Ciò ci consente di addebitare i costi per l'esecuzione di set di competenze. In questa data è iniziato anche l'addebito dell'estrazione delle immagini come parte della fase di individuazione dei documenti. L'estrazione di testo dai documenti continua a essere offerta gratuitamente.
>
> L'esecuzione delle [competenze cognitive predefinite](cognitive-search-predefined-skills.md) viene addebitata in base ai [prezzi con pagamento a consumo di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services), alla stessa tariffa che verrebbe usata se fosse stata eseguita l'attività direttamente. L'estrazione di immagini è un addebito previsto in Ricerca di Azure, attualmente offerto al prezzo di anteprima. Per informazioni dettagliate, vedere la [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400) oppure [Come funziona la fatturazione](search-sku-tier.md#how-billing-works).


## <a name="use-free-resources"></a>Usare risorse gratuite

È possibile usare un'opzione di elaborazione limitata e gratuita per eseguire gli esercizi di esercitazione e l'avvio rapido di ricerca cognitiva. 

> [!Important]
> A partire dal 1° febbraio 2019, l'opzione **Gratuito (miglioramenti limitati)** è limitata a 20 documenti al giorno. 

1. Aprire la procedura guidata **Importa dati**.

   ![Comando Importa dati](media/search-get-started-portal/import-data-cmd2.png "Comando Importa dati")

1. Scegliere un'origine dati e passare a **Aggiungi ricerca cognitiva (facoltativo)**. Per una procedura dettagliata su questa procedura guidata, vedere [Usare gli strumenti predefiniti del portale per importazione, indicizzazione e query in Ricerca di Azure](search-get-started-portal.md).

1. Espandere **Collega Servizi cognitivi** e selezionare **Gratuito (miglioramenti limitati)**.

   ![Sezione Collega Servizi cognitivi espansa](./media/cognitive-search-attach-cognitive-services/attach1.png "Sezione Collega Servizi cognitivi espansa")

Continuare con il passaggio successivo, **Aggiungi arricchimenti**. Per una descrizione delle competenze disponibili nel portale, vedere ["Passaggio 2: Aggiungere competenze cognitive"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) nell'argomento di avvio rapido sulla ricerca cognitiva.

## <a name="use-billable-resources"></a>Usare risorse fatturabili

Per carichi di lavoro di oltre 20 arricchimenti al giorno, è necessario collegare una risorsa Servizi cognitivi fatturabile. 

Vengono addebitate solo le competenze che chiamano le API Servizi cognitivi. Le competenze non basate sull'API, ad esempio [competenze personalizzate](cognitive-search-create-custom-skill-example.md), [di unione testo](cognitive-search-skill-textmerger.md), [di divisione testo](cognitive-search-skill-textsplit.md) e [di shaping](cognitive-search-skill-shaper.md) non vengono addebitate.

1. Nella procedura guidata **Importa dati**, in **Collega Servizi cognitivi** selezionare una risorsa esistente oppure fare clic su **Crea nuova risorsa Servizi cognitivi**.

1. Selezionando **Crea nuova risorsa Servizi cognitivi** viene aperta una nuova scheda in cui è possibile creare la risorsa. Assegnare alla risorsa un nome univoco.

1. Scegliere la stessa località di Ricerca di Azure. Attualmente l'indicizzazione con competenze cognitive è supportata nelle aree seguenti:

  * Stati Uniti centro-occidentali
  * Stati Uniti centro-meridionali
  * Stati Uniti orientali
  * Stati Uniti orientali 2
  * Stati Uniti occidentali 2
  * Canada centrale
  * Europa occidentale
  * Regno Unito meridionale
  * Europa settentrionale
  * Brasile meridionale
  * Asia sud-orientale
  * India centrale
  * Australia orientale

1. Scegliere il piano tariffario tutto in uno, **S0**. Questo piano offre le funzionalità di Visione artificiale e Lingua che supportano le competenze predefinite nella ricerca cognitiva.

    ![Creare una nuova risorsa Servizi cognitivi](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Creare una nuova risorsa Servizi cognitivi")

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
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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

+ 1000 file PDF
+ Sei pagine ognuno
+ Un'immagine per pagina (6000 immagini)
+ 3000 caratteri per pagina

Si supponga di avere una pipeline costituita dall'individuazione del documento di ogni file PDF con estrazione di testo e immagini, riconoscimento ottico dei caratteri (OCR) delle immagini e riconoscimento di entità denominate delle organizzazioni. 

In questo esercizio viene usato il prezzo più alto per ogni transazione. I costi effettivi possono essere inferiori vista l'applicazione di prezzi progressivi. Vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Per l'individuazione di documenti con contenuto di testo e immagini, attualmente l'estrazione di testo è gratuita. Per 6000 immagini, si supponga un costo di 1 dollaro per ogni 1000 immagini estratte, con un costo di 6 dollari per questo passaggio.

2. Per il riconoscimento ottico dei caratteri di 6000 immagini in inglese, la competenza cognitiva OCR usa l'algoritmo migliore (DescribeText). Supponendo un costo di 2,50 dollari per l’analisi di 1000 immagini, il costo raggiunto in questo passaggio sarebbe di 15 dollari.

3. Per l'estrazione delle entità, abbiamo un totale di 3 record di testo per ogni pagina (ogni record è composto da 1000 caratteri). Tre record di testo per pagina * 6000 pagine = 18.000 record di testo. Supponendo un prezzo di 2 dollari ogni 1000 record di testo, in questo passaggio si raggiungerebbe un costo di 36 dollari.

Unendo i due passaggi, si arriverebbe a pagare circa 57 dollari per inserire 1000 documenti PDF di questo tipo con il set di competenze descritto. 

## <a name="next-steps"></a>Passaggi successivi
+ [Pagina Prezzi di Ricerca di Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
