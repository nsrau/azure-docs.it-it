---
title: Associazione di servizi cognitivi a un skillt
titleSuffix: Azure Cognitive Search
description: Istruzioni per l'associazione di una sottoscrizione all-in-One di servizi cognitivi a una pipeline di arricchimento di intelligenza artificiale in Azure ricerca cognitiva.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d65b9b60ce93656c9acdc76c77291114468d345a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113938"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Alleghi una risorsa di servizi cognitivi a un Skills in Azure ricerca cognitiva 

Gli algoritmi di intelligenza artificiale consentono di usare le [pipeline di arricchimento](cognitive-search-concept-intro.md) usate per la trasformazione del contenuto in Azure ricerca cognitiva. Questi algoritmi sono basati sulle risorse dei servizi cognitivi di Azure, tra cui [visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/) per l'analisi delle immagini e il riconoscimento ottico dei caratteri (OCR) e [analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per il riconoscimento delle entità, l'estrazione di frasi chiave e altri miglioramenti. Come usato da Azure ricerca cognitiva per scopi di arricchimento dei documenti, gli algoritmi vengono incapsulati all'interno di una *competenza*, inseriti in un *skillt*e a cui fa riferimento un *indicizzatore* durante l'indicizzazione.

Puoi arricchire gratuitamente un numero limitato di documenti. In alternativa, è possibile allungare una risorsa di servizi cognitivi fatturabili a un livello di *competenze* per carichi di lavoro più grandi e più frequenti. In questo articolo si apprenderà come alleghi una risorsa di servizi cognitivi fatturabile per arricchire i documenti durante l' [indicizzazione](search-what-is-an-index.md)ricerca cognitiva di Azure.

> [!NOTE]
> Gli eventi fatturabili includono chiamate a API Servizi cognitivi ed estrazione di immagini come parte della fase di cracking del documento in Azure ricerca cognitiva. Non è previsto alcun addebito per l'estrazione del testo dai documenti o per le competenze che non chiamano servizi cognitivi.
>
> L'esecuzione di competenze fatturabili è il [prezzo con pagamento in base al consumo di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). Per i prezzi di estrazione delle immagini, vedere la [pagina dei prezzi di Azure ricerca cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Requisito della stessa area

È necessario che Azure ricerca cognitiva e servizi cognitivi di Azure esistano nella stessa area. In caso contrario, verrà ricevuto questo messaggio in fase di esecuzione: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Non è possibile spostare un servizio tra le aree. Se si riceve questo errore, è necessario creare una nuova risorsa di servizi cognitivi nella stessa area di Azure ricerca cognitiva.

> [!NOTE]
> Alcune competenze predefinite sono basate su servizi cognitivi non locali (ad esempio, la capacità di [traduzione del testo](cognitive-search-skill-text-translation.md)). Tenere presente che se si aggiunge una di queste competenze al proprio Skills, non è garantito che i dati si trovino nella stessa area della ricerca cognitiva di Azure o della risorsa Servizi cognitivi. Per ulteriori informazioni, vedere la [pagina relativa allo stato del servizio](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Usare risorse gratuite

È possibile usare un'opzione di elaborazione gratuita limitata per completare l'esercitazione di arricchimento di intelligenza artificiale e gli esercizi di avvio rapido.

Le risorse gratuite (arricchimenti limitati) sono limitate a 20 documenti al giorno, per sottoscrizione.

1. Aprire la procedura guidata Importa dati:

   ![Aprire la procedura guidata Importa dati](media/search-get-started-portal/import-data-cmd.png "Aprire la procedura guidata Importa dati")

1. Scegliere un'origine dati e continuare ad **aggiungere l'arricchimento ai (facoltativo)** . Per una procedura dettagliata di questa procedura guidata, vedere [creare un indice nel portale di Azure](search-get-started-portal.md).

1. Espandere **Connetti Servizi cognitivi** e quindi selezionare **gratuito (arricchimenti limitati)** :

   ![Sezione Servizi cognitivi allargati collegati](./media/cognitive-search-attach-cognitive-services/attach1.png "Sezione Servizi cognitivi allargati collegati")

1. È ora possibile continuare con i passaggi successivi, tra cui **aggiungere competenze cognitive**.

## <a name="use-billable-resources"></a>Usare risorse fatturabili

Per i carichi di lavoro che creano più di 20 arricchimenti al giorno, assicurarsi di alleghi una risorsa Servizi cognitivi fatturabile. Si consiglia di alleghi sempre una risorsa di servizi cognitivi fatturabile, anche se non si intende mai chiamare API Servizi cognitivi. Il fissaggio di una risorsa sostituisce il limite giornaliero.

Vengono addebitate solo le competenze che chiamano il API Servizi cognitivi. Non vengono addebitate le [competenze personalizzate](cognitive-search-create-custom-skill-example.md)o le competenze come la [fusione del testo](cognitive-search-skill-textmerger.md), la [barra di divisione del testo](cognitive-search-skill-textsplit.md)e lo [shaper](cognitive-search-skill-shaper.md), che non sono basati su API.

1. Aprire la procedura guidata Importa dati, scegliere un'origine dati e continuare ad **aggiungere l'arricchimento ai (facoltativo)** .

1. Espandere **Connetti Servizi cognitivi** e quindi selezionare **Crea nuova risorsa Servizi cognitivi**. Viene visualizzata una nuova scheda in cui è possibile creare la risorsa:

   ![Creare una risorsa di servizi cognitivi](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Creare una risorsa per Servizi cognitivi")

1. Nell'elenco **location (percorso** ) selezionare l'area in cui si trova il servizio Azure ricerca cognitiva. Assicurarsi di usare questa area per motivi di prestazioni. L'uso di questa area Annulla anche gli addebiti per la larghezza di banda in uscita tra aree.

1. Nell'elenco piano **tariffario** selezionare **S0** per ottenere la raccolta All-in-One delle funzionalità di servizi cognitivi, incluse le funzionalità di visione e linguaggio che riportano le competenze predefinite fornite da Azure ricerca cognitiva.

   Per il livello S0, è possibile trovare le tariffe per carichi di lavoro specifici nella [pagina dei prezzi di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Nell'elenco **Seleziona offerta** verificare che **Servizi cognitivi** sia selezionato.
   + In funzionalità del **linguaggio** le tariffe per **analisi del testo standard** si applicano all'indicizzazione ai.
   + In funzionalità di **visione** , vengono applicate le tariffe per **visione artificiale S1** .

1. Selezionare **Crea** per eseguire il provisioning della nuova risorsa Servizi cognitivi.

1. Tornare alla scheda precedente, che contiene la procedura guidata Importa dati. Selezionare **Aggiorna** per visualizzare la risorsa Servizi cognitivi e quindi selezionare la risorsa:

   ![Selezionare la risorsa Servizi cognitivi](./media/cognitive-search-attach-cognitive-services/attach2.png "Selezionare la risorsa Servizi cognitivi")

1. Espandere la sezione **Aggiungi competenze cognitive** per selezionare le competenze cognitive specifiche che si desidera eseguire sui dati. Completare il resto della procedura guidata.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Collegare un set di competenze esistente a una risorsa Servizi cognitivi

Se si ha un set di competenze esistenti, è possibile collegarlo a una risorsa Servizi cognitivi nuova o diversa.

1. Nella pagina **Panoramica servizio** selezionare **skillsets**:

   ![Scheda skillsets](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Scheda skillsets")

1. Selezionare il nome del file di competenze, quindi selezionare una risorsa esistente o crearne una nuova. Selezionare **OK** per confermare le modifiche.

   ![Elenco delle risorse di competenze](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Elenco delle risorse di competenze")

   Tenere presente che l'opzione **gratuito (arricchimenti limitati)** limita la disponibilità di 20 documenti al giorno e che è possibile usare **Crea nuova risorsa Servizi cognitivi** per eseguire il provisioning di una nuova risorsa fatturabile. Se si crea una nuova risorsa, selezionare **Aggiorna** per aggiornare l'elenco delle risorse Servizi cognitivi, quindi selezionare la risorsa.

## <a name="attach-cognitive-services-programmatically"></a>Collegare Servizi cognitivi a livello di codice

Quando si definisce il set di competenze a livello di codice, aggiungere una sezione `cognitiveServices` al set di competenze. In questa sezione, includere la chiave della risorsa Servizi cognitivi che si desidera associare al proprio insieme di competenze. Tenere presente che la risorsa deve trovarsi nella stessa area della risorsa ricerca cognitiva di Azure. Aggiungere anche `@odata.type` e impostarlo su `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Il formato è indicato nel seguente esempio. Si noti la sezione `cognitiveServices` alla fine della definizione.

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

Per stimare i costi associati all'indicizzazione della ricerca cognitiva, iniziare con un'idea dell'aspetto di un documento medio, in modo da poter eseguire alcuni numeri. Ad esempio, è possibile approssimarsi:

+ 1\.000 PDF.
+ Sei pagine ciascuna.
+ Un'immagine per pagina (6.000 immagini).
+ 3\.000 caratteri per pagina.

Si supponga di avere una pipeline costituita da cracking di documenti di ogni PDF, estrazione di immagini e testo, riconoscimento ottico dei caratteri (OCR) di immagini e riconoscimento di entità delle organizzazioni.

I prezzi indicati in questo articolo sono ipotetici. Sono usati per illustrare il processo di stima. I costi potrebbero essere inferiori. Per i prezzi effettivi delle transazioni, vedere vedere [prezzi di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Per l'individuazione di documenti con contenuto di testo e immagini, attualmente l'estrazione di testo è gratuita. Per le immagini 6.000, presumere $1 per ogni 1.000 immagini estratte. Questo è un costo di $6,00 per questo passaggio.

2. Per il riconoscimento ottico dei caratteri di 6.000 immagini in inglese, la competenza cognitiva OCR usa l'algoritmo migliore (DescribeText). Supponendo un costo di 2,50 dollari per l’analisi di 1.000 immagini, il costo raggiunto in questo passaggio sarebbe di 15 dollari.

3. Per l'estrazione di entità, si otterrebbe un totale di tre record di testo per ogni pagina. (ogni record è composto da 1.000 caratteri). Tre record di testo per pagina moltiplicati per 6.000 pagine sono uguali a 18.000 record di testo. Supponendo un prezzo di 2 dollari ogni 1.000 record di testo, in questo passaggio si raggiungerebbe un costo di 36 dollari.

Riunendola, pagherai circa $57,00 per inserire i documenti PDF 1.000 di questo tipo con le competenze descritte.

## <a name="next-steps"></a>Passaggi successivi
+ [Pagina dei prezzi di Azure ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/)
+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
