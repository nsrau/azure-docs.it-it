---
title: Input e output di riferimento in pipeline di ricerca cognitiva - Ricerca di Azure
description: Vengono illustrati l’annotazione di sintassi e le modalità di riferimento a un'annotazione in input e output di un insieme di competenze in una pipeline di ricerca cognitiva in Ricerca di Azure.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 40559744f0650c64afb1dc63c38f56efaa0219d7
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265543"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Come fare riferimento alle annotazioni in un insieme di competenze di ricerca cognitiva

In questo articolo si comprenderà come fare riferimento alle annotazioni nelle definizioni delle competenze, utilizzando esempi per illustrare diversi scenari. Poiché il contenuto di un documento illustra un set di competenze, vengono aggiunte alcune annotazioni. Le annotazioni possono essere utilizzate come input per un'ulteriore arricchimento di downstream o mappate a un campo di output in un indice. 
 
Gli esempi in questo articolo si basano sul campo *contenuto* generato automaticamente da [indicizzatori BLOB di Azure](search-howto-indexing-azure-blob-storage.md) in quanto parte della fase di decifrazione del documento. Quando si fa riferimento ai documenti da un contenitore BLOB, utilizzare un formato, ad esempio `"/document/content"`, dove il campo *contenuto* fa parte del *documento*. 

## <a name="background-concepts"></a>Concetti di base

Prima di esaminare la sintassi, è opportuno rivedere alcuni concetti importanti per comprendere meglio gli esempi forniti più avanti in questo articolo.

| Nome | Descrizione |
|------|-------------|
| Documento arricchito | Un documento arricchito è una struttura interna creata e utilizzata dalla pipeline per contenere tutte le annotazioni correlate a un documento. Un documento arricchito può essere paragonato a una struttura di annotazioni. In genere, un'annotazione creata da un'annotazione precedente diventa il corrispettivo elemento figlio.<p/>I documenti arricchiti esistono solo per la durata dell'esecuzione di un insieme di competenze. Dopo che il contenuto è stato mappato all'indice di ricerca, il documento arricchito non è più necessario. Anche se si non interagisce direttamente con i documenti arricchiti, è utile disporre di un modello mentale dei documenti quando si crea un insieme di competenze. |
| Contesto di arricchimento | Il contesto in cui avviene l'arricchimento, in riferimento ai termini con cui esso è stato arricchito. Per impostazione predefinita, il contesto di arricchimento è impostato al `"/document"` livello e definito per l'ambito dei singoli documenti. Quando viene eseguita una competenza, gli output di tale competenza diventano [proprietà del contesto definito](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Esempio 1: Riferimento di annotazione semplice

Nell'archivio BLOB di Azure, si supponga di disporre di un'ampia gamma di file contenenti riferimenti ai nomi di persone che si desidera estrarre utilizzando il riconoscimento delle entità. Nella definizione di competenza riportata di seguito, `"/document/content"` è la rappresentazione testuale dell'intero documento e "persone" è l'estrazione di nomi e cognome per le entità identificate come persone.

Poiché il contesto predefinito è `"/document"`, è ora possibile fare riferimento all'elenco delle persone come `"/document/people"`. In questo caso specifico `"/document/people"` è un'annotazione, che potrebbe ora essere mappata a un campo in un indice o utilizzata in un'altra competenza nello stesso insieme di competenze.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Esempio 2 Riferimento a una matrice all'interno di un documento

Questo esempio si basa su quello precedente e mostra come richiamare più volte un'operazione di arricchimento tramite lo stesso documento. Supponiamo che nell'esempio precedente viene generata una matrice di stringhe con i nomi di 10 persone da un singolo documento. Un passaggio successivo congruo potrebbe essere un secondo arricchimento che estrae il cognome da nome e cognome. Poiché sono presenti 10 nomi, questo passaggio deve essere chiamato 10 volte in questo documento, una volta per ogni persona. 

Per richiamare il corretto numero di iterazioni, impostare il contesto come `"/document/people/*"`, in cui l'asterisco (`"*"`) rappresenta tutti i nodi nel documento arricchito come discendenti di `"/document/people"`. Sebbene questa competenza venga definita solo una volta nella matrice competenze, viene chiamata per ogni membro all'interno del documento fino a quando non vengono elaborati tutti i membri.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Quando le annotazioni sono matrici o raccolte di stringhe, è possibile fare riferimento a membri specifici anziché alla matrice nel suo complesso. L'esempio precedente genera un'annotazione denominata `"last"` in ogni nodo rappresentato dal contesto. Se si desidera fare riferimento a questa famiglia di annotazioni, è possibile utilizzare la sintassi `"/document/people/*/last"`. Se si desidera fare riferimento a un'annotazione particolare, è possibile utilizzare un indice esplicito: `"/document/people/1/last`"per fare riferimento al cognome del primo utente identificato nel documento. Si noti che in questa sintassi le matrici sono "indicizzate a 0".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Esempio 3: Riferimento a membri all'interno di una matrice

In alcuni casi è necessario raggruppare tutte le annotazioni di un determinato tipo per trasmetterle a una determinata competenza. Prendere in considerazione un’ipotetica competenza personalizzata che identifica il cognome più comune da tutti i cognomi estratti nell'esempio 2. Per fornire alla competenza personalizzata solo i cognomi, specificare il contesto come `"/document"` e l'input come `"/document/people/*/lastname"`.

Si noti che la cardinalità `"/document/people/*/lastname"` di è maggiore di quella del documento. Potrebbero essere presenti 10 nodi dei cognomi mentre esserci un solo nodo del documento per questo documento. In tal caso, verranno automaticamente creati una matrice di `"/document/people/*/lastname"` contenente tutti gli elementi nel documento.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Vedere anche
+ [Come integrare una competenza personalizzata in una pipeline di arricchimento](cognitive-search-custom-skill-interface.md)
+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
+ [Creare un insieme di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire la mappatura dei campi arricchiti a un indice](cognitive-search-output-field-mapping.md)
