---
title: Aggiungere Servizi cognitivi al proprio set di competenze - Ricerca di Azure
description: Istruzioni per collegare una sottoscrizione di Servizi cognitivi tutto in uno a un set di competenze cognitive
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317237"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Associare la risorsa Servizi cognitivi a un set di competenze 

> [!NOTE]
> A partire dal 21 dicembre 2018 è possibile associare una risorsa dei Servizi cognitivi a un set di competenze della Ricerca di Azure. In questo modo sarà possibile iniziare ad addebitare per l'esecuzione del set di competenze. In questa data avrà inizio anche l'addebito dell'estrazione delle immagini come parte della fase di individuazione dei documenti. L'estrazione del testo dai documenti continuerà a essere offerta gratuitamente.
>
> L'esecuzione delle competenze predefinite verrà addebitata in base ai[prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). Per l'estrazione delle immagini verranno applicati i prezzi di anteprima, come illustrato nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


La ricerca cognitiva estrae e arricchisce i dati per consentirne la ricerca in Ricerca di Azure. I passaggi di estrazione e arricchimento vengono definiti *competenze cognitive*, che vengono chiamate durante l'indicizzazione del contenuto nell'ambito di un *set di competenze*. Un set di competenze può usare [competenze predefinite](cognitive-search-predefined-skills.md) o competenze personalizzate. Per altre informazioni, vedere [Example: create a custom skill](cognitive-search-create-custom-skill-example.md) (Esempio: creare una competenza personalizzata).

In questo articolo viene illustrato come associare la risorsa [Servizi cognitivi ](https://azure.microsoft.com/services/cognitive-services/) al set di competenze cognitive.

La risorsa Servizi cognitivi selezionata attiverà le competenze cognitive predefinite e verrà usata anche per la fatturazione. Eventuali arricchimenti apportati usando le competenze cognitive predefinite verranno fatturati sulla risorsa Servizi cognitivi selezionata. In particolare, verranno fatturati alla stessa tariffa che verrebbe usata se fosse stata eseguita la stessa attività usando una risorsa Servizi cognitivi. Vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Limiti se non viene selezionata una risorsa Servizi cognitivi
A partire dal 1 febbraio 2019, se al proprio set di conoscenze non è associata una sottoscrizione di Servizi cognitivi, sarà possibile arricchire gratuitamente solo un numero ridotto di documenti (20 documenti al giorno). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Associazione di una risorsa Servizi cognitivi a un nuovo set di competenze

1. Nell'ambito del processo di *importazione dei dati*, dopo essersi connessi all'origine dati si passerà al passaggio facoltativo *Aggiungi ricerca cognitiva*. 

1. Espandere la sezione *Collega Servizi cognitivi*. Verranno visualizzate eventuali risorse Servizi cognitivi presenti nella stessa area geografica del servizio di ricerca. 
![Sezione Collega Servizi cognitivi espansa](./media/cognitive-search-attach-cognitive-services/attach1.png "Sezione Collega Servizi cognitivi espansa")

1. Selezionare una risorsa Servizi cognitivi esistente oppure *creare una nuova risorsa Servizi cognitivi*. Se si seleziona l'opzione *Gratuito (miglioramenti limitati)*, sarà possono arricchire gratuitamente solo un numero ridotto di documenti (20 documenti al giorno). Se fa clic su *Crea una nuova risorsa Servizi cognitivi*, verrà aperta una nuova scheda che consente di creare la risorsa Servizi cognitivi. 

1. Se è già stata creata una nuova risorsa, fare clic su *Aggiorna* per aggiornare l'elenco delle risorse Servizi cognitivi e selezionare la risorsa. 
![Risorsa Servizi cognitivi selezionata](./media/cognitive-search-attach-cognitive-services/attach2.png "Risorsa Servizi cognitivi selezionata")

1. Al termine di questa operazione sarà possibile espandere la sezione *Aggiungi arricchimenti* per selezionare le competenze cognitive specifiche che si intende eseguire sui dati e continuare con il resto del flusso.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Associazione di una risorsa Servizi cognitivi a un set di competenze esistente

1. Nella pagina Panoramica del servizio selezionare la scheda *Set di competenze*. ![Scheda Set di competenze](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Scheda Set di competenze")

1. *Fare clic* sul set di competenze che si vuole modificare. Verrà visualizzato un pannello da cui è possibile modificare il set di competenze.

1. Selezionare una risorsa Servizi cognitivi esistente oppure *creare una nuova risorsa Servizi cognitivi*. Se si seleziona l'opzione *Gratuito (miglioramenti limitati)*, sarà possono arricchire gratuitamente solo un numero ridotto di documenti (20 documenti al giorno). Se fa clic su *Crea una nuova risorsa Servizi cognitivi*, verrà aperta una nuova scheda che consente di creare la risorsa Servizi cognitivi. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Se è già stata creata una nuova risorsa, fare clic su *Aggiorna* per aggiornare l'elenco delle risorse Servizi cognitivi e selezionare la risorsa.
1. Fare clic su *OK* per confermare le modifiche.

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Associazione di una risorsa Servizi cognitivi a livello di codice

Quando si definisce il set di competenze a livello di codice, aggiungere una sezione `cognitiveServices`, in cui deve essere inclusa la chiave della risorsa Servizi cognitivi che si vuole associare al set di competenze, oltre a @odata.type, che deve essere impostato su "#Microsoft.Azure.Search.CognitiveServicesByKey". Questo modello è illustrato nell'esempio seguente.

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>Esempio: Stima del costo per l'individuazione e l'arricchimento di documenti
È possibile che si voglia stimare il costo necessario per arricchire un determinato tipo di documento. L'esercizio seguente è solo un esempio, ma può essere di grande aiuto.

Si supponga di avere 1000 file PDF, costituiti mediamente da sei pagine ciascuno, e che ogni pagina contenga un'immagine e sia costituita in media da 3.000 caratteri. 

Si supponga quindi di voler seguire la procedura seguente nell'ambito del processo di arricchimento:
1. Estrarre il contenuto e le immagini dal documento per consentirne l'individuazione.
1. Per la fase di arricchimento, applicare il riconoscimento ottico dei caratteri alle pagine estratte, combinare il testo di tutte le pagine e quindi estrarre ognuna delle organizzazioni presenti nel testo combinato di tutte le immagini.

Si stimerà ora quanto costerebbe inserire questi documenti simulando ogni passaggio necessario.

Per i 1.000 documenti:

1. Individuazione dei documenti: si estrae un numero combinato di 6.000 immagini. Presupponendo 1 dollaro per ogni 1.000 immagini estratte, si giungerebbe a 6 dollari.

2. Si estrae quindi il testo da ognuna delle 6.000 immagini. In inglese, la competenza cognitiva OCR usa l'algoritmo migliore (DescribeText). Supponendo un costo di 2,50 dollari per 1.000 immagini da analizzare, il costo raggiunto in questo passaggio sarebbe di 15 dollari.

3. Per l'estrazione delle entità, si ha un totale di 3 record di testo per ogni pagina (ogni record è composto da 1000 caratteri). 3 record di testo/pagina * 6.000 pagine = 18.000 record di testo. Supponendo un prezzo di 2 dollari ogni 1000 record di testo, in questo passaggio si raggiungerebbe un costo di 36 dollari.

Unendo i due passaggi, si arriverebbe a pagare 57 dollari per inserire 1.000 documenti PDF di questo tipo con il set di competenze descritto.  In questo esercizio è stato preso in considerazione il prezzo più alto per ogni transazione, che avrebbe potuto essere inferiore in caso di prezzo scaglionato. Vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Passaggi successivi
+ [Pagina Prezzi di Ricerca di Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
