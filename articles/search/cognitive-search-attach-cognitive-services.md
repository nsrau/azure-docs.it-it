---
title: Associare la risorsa Servizi cognitivi a un set di competenze in Ricerca di Azure
description: Istruzioni per collegare una sottoscrizione di Servizi cognitivi tutto in uno a un set di competenze cognitive in Ricerca di Azure
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 52efa685bba330879365f56e547881d62a52a185
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000156"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Associare la risorsa Servizi cognitivi a un set di competenze in Ricerca di Azure 

La ricerca cognitiva estrae e arricchisce i dati per consentirne la ricerca in Ricerca di Azure. I passaggi di estrazione e arricchimento vengono definiti *competenze cognitive*, che vengono chiamate durante l'indicizzazione del contenuto nell'ambito di un *set di competenze*. Un set di competenze può usare [competenze predefinite](cognitive-search-predefined-skills.md) o personalizzate. Per altre informazioni, vedere [Esempio: creare una competenza personalizzata](cognitive-search-create-custom-skill-example.md).

In questo articolo viene illustrato come associare una risorsa [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/) al set di competenze cognitive.

La risorsa Servizi cognitivi selezionata attiverà le competenze cognitive predefinite. e verrà usata anche per la fatturazione. Eventuali arricchimenti apportati usando le competenze cognitive predefinite verranno fatturati sulla risorsa Servizi cognitivi selezionata. In particolare, verranno fatturati alla stessa tariffa che verrebbe usata se fosse stata eseguita la stessa attività usando una risorsa Servizi cognitivi. Vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> Dal 21 dicembre 2018 è possibile associare una risorsa dei Servizi cognitivi a un set di competenze della Ricerca di Azure. Ciò ci consente di addebitare i costi per l'esecuzione di set di competenze. In questa data è iniziato anche l'addebito dell'estrazione delle immagini come parte della fase di individuazione dei documenti. L'estrazione del testo dai documenti continua a essere offerta gratuitamente.
>
> L'esecuzione delle competenze predefinite viene addebitata in base ai [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). Per l'estrazione delle immagini vengono applicati i prezzi di anteprima, come illustrato nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Limiti se non viene selezionata una risorsa Servizi cognitivi
A partire dal 1 febbraio 2019, se al proprio set di conoscenze non è associata una sottoscrizione di Servizi cognitivi, sarà possibile arricchire gratuitamente solo un numero ridotto di documenti (20 documenti al giorno). 

## <a name="associate-a-cognitive-services-resource-with-a-new-skillset"></a>Associare una risorsa Servizi cognitivi a un nuovo set di competenze

1. Nell'ambito della procedura guidata di [importazione dei dati](search-import-data-portal.md), dopo essersi connessi all'origine dati, passare all’operazione facoltativa **Aggiungi ricerca cognitiva**. Questo è il secondo passaggio della procedura guidata.

1. Espandere la sezione **Collega Servizi cognitivi**. Questo passaggio consente di visualizzare eventuali risorse Servizi cognitivi presenti nella stessa area geografica del servizio Ricerca di Azure.

   ![Sezione Collega Servizi cognitivi espansa](./media/cognitive-search-attach-cognitive-services/attach1.png "Sezione Collega Servizi cognitivi espansa")

1. Selezionare una risorsa Servizi cognitivi esistente oppure **Crea nuova risorsa Servizi cognitivi**. Se si seleziona **Gratuito (miglioramenti limitati)**, sarà possono arricchire gratuitamente solo un numero ridotto di documenti (20 documenti al giorno). Se si seleziona **Crea nuova risorsa Servizi cognitivi**, verrà aperta una nuova scheda in cui è possibile creare la risorsa. 

1. Se è già stata creata una nuova risorsa, selezionare **Aggiorna** per aggiornare l'elenco delle risorse Servizi cognitivi, quindi selezionare la risorsa. 

   ![Risorsa Servizi cognitivi selezionata](./media/cognitive-search-attach-cognitive-services/attach2.png "Risorsa Servizi cognitivi selezionata")

1. Espandere la sezione **Aggiungi arricchimenti** per selezionare le competenze cognitive specifiche che si intende eseguire sui dati e continuare con il resto del flusso.

## <a name="associate-a-cognitive-services-resource-with-an-existing-skillset"></a>Associare una risorsa Servizi cognitivi a un set di competenze esistente

1. Nella pagina **Panoramica del servizio**, selezionare la scheda **Set di competenze**.

   (./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Scheda Set di competenze")Scheda Set di competenze

1. Selezionare il set di competenze da modificare. Questa operazione consente di aprire un pannello in cui è possibile modificare un set di competenze.

1. Selezionare una risorsa Servizi cognitivi esistente oppure **Crea nuova risorsa Servizi cognitivi**. Se si seleziona **Gratuito (miglioramenti limitati)**, sarà possono arricchire gratuitamente solo un numero ridotto di documenti (20 documenti al giorno). Se si seleziona **Crea nuova risorsa Servizi cognitivi**, verrà aperta una nuova scheda in cui è possibile creare la risorsa.

   <n/> 
   <img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Se è già stata creata una nuova risorsa, selezionare **Aggiorna** per aggiornare l'elenco delle risorse Servizi cognitivi, quindi selezionare la risorsa.

1. Selezionare **OK** per confermare le modifiche.

## <a name="associate-a-cognitive-services-resource-programmatically"></a>Associare una risorsa Servizi cognitivi a livello di codice

Quando si definisce il set di competenze a livello di codice, aggiungere una sezione `cognitiveServices`. Nella sezione, includere la chiave della risorsa Servizi cognitivi da associare al set di competenze. Aggiungere anche `@odata.type` e impostarlo su `#Microsoft.Azure.Search.CognitiveServicesByKey`. Il formato è indicato nel seguente esempio.

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
        "key": "your key goes here"
    }
}
```
## <a name="example-estimate-the-cost-of-document-cracking-and-enrichment"></a>Esempio: Stimare il costo per l'individuazione e l'arricchimento di documenti
È possibile che si voglia stimare il costo necessario per arricchire un tipo di documento. L'esercizio seguente è solo un esempio, ma potrebbe essere di grande aiuto.

Supponiamo di avere 1.000 documenti PDF. Stimiamo che, in media, le pagine di ciascun documento sono 6. In ciascuna pagina è presente 1 immagine. In media, in ogni pagina sono presenti 3.000 caratteri. 

Supponiamo quindi di voler seguire la procedura seguente nell'ambito del processo di arricchimento:
1. Estrarre il contenuto e le immagini dal documento per consentirne l'individuazione.
1. Per la fase di arricchimento, applicare il riconoscimento ottico dei caratteri (OCR) a tutte le pagine estratte, combinare il testo di tutte le pagine e quindi estrarre ognuna delle organizzazioni presenti nel testo combinato di tutte le immagini.

Stimiamo ora quanto costerebbe inserire questi 1.000 documenti simulando ogni passaggio necessario:

1. Per l’individuazione dei documenti è necessario estrarre un numero combinato di 6.000 immagini. Presupponendo 1 dollaro per ogni 1,000 immagini estratte, giungeremmo a 6 dollari.

2. Estraiamo quindi il testo da ognuna delle 6.000 immagini. In inglese, la competenza cognitiva OCR usa l'algoritmo migliore (DescribeText). Supponendo un costo di 2,50 dollari per l’analisi di 1.000 immagini, il costo raggiunto in questo passaggio sarebbe di 15 dollari.

3. Per l'estrazione delle entità, abbiamo un totale di 3 record di testo per ogni pagina (ogni record è composto da 1.000 caratteri). Tre record di testo per pagina * 6.000 pagine = 18.000 record di testo. Supponendo un prezzo di 2 dollari ogni 1.000 record di testo, in questo passaggio si raggiungerebbe un costo di 36 dollari.

Unendo i due passaggi, arriveremmo a pagare 57 dollari per inserire 1.000 documenti PDF di questo tipo con il set di competenze descritto. In questo esercizio, abbiamo utilizzato il prezzo più alto per ogni transazione. Potrebbe essere inferiore a causa dello scaglionamento dei prezzi. Vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Passaggi successivi
+ [Pagina Prezzi di Ricerca di Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
