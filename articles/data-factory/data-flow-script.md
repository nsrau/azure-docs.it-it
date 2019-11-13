---
title: Script del flusso di dati Azure Data Factory mapping
description: Panoramica del linguaggio code-behind degli script del flusso di dati Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/10/2019
ms.openlocfilehash: 4ff5a05fd40ef086c1f2332443ca03d5e872e9a8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010151"
---
# <a name="data-flow-script-dfs"></a>Script del flusso di dati (DFS)

Lo script del flusso di dati (DFS) è costituito dai metadati sottostanti, analogamente a un linguaggio di codifica, utilizzato per eseguire le trasformazioni incluse in un flusso di dati di mapping. Ogni trasformazione è rappresentata da una serie di proprietà che forniscono le informazioni necessarie per eseguire correttamente il processo. Lo script è visibile e modificabile da ADF facendo clic sul pulsante "script" nella barra multifunzione superiore dell'interfaccia utente del browser.

![Pulsante script](media/data-flow/scriptbutton.png "Pulsante script")

Ad esempio, `allowSchemaDrift: true,` in una trasformazione origine indica al servizio di includere tutte le colonne del set di dati di origine nel flusso di dati anche se non sono incluse nella proiezione dello schema.

## <a name="use-cases"></a>Casi d'uso
Il DFS viene prodotto automaticamente dall'interfaccia utente. È possibile fare clic sul pulsante script per visualizzare e personalizzare lo script. È anche possibile generare script all'esterno dell'interfaccia utente di ADF, quindi passarli al cmdlet di PowerShell. Quando si esegue il debug di flussi di dati complessi, può risultare più semplice analizzare il code-behind dello script anziché analizzare la rappresentazione grafica dei flussi dell'interfaccia utente.

Di seguito sono riportati alcuni casi d'uso di esempio:
- A livello produce molti flussi di dati abbastanza simili, ad esempio i flussi di dati di "contrassegno".
- Espressioni complesse difficili da gestire nell'interfaccia utente o che causano problemi di convalida.
- Debug e migliore comprensione di diversi errori restituiti durante l'esecuzione.

Quando si compila uno script del flusso di dati da usare con PowerShell o un'API, è necessario comprimere il testo formattato in una sola riga. È possibile tenere sotto controllo le schede e le nuove righe come caratteri di escape. Ma il testo deve essere formattato per adattarsi a una proprietà JSON. È presente un pulsante nell'interfaccia utente dell'editor di script nella parte inferiore che formatterà lo script come una singola riga.

![Pulsante copia](media/data-flow/copybutton.png "Pulsante Copia")

## <a name="how-to-add-transforms"></a>Come aggiungere trasformazioni
Per aggiungere trasformazioni sono necessari tre passaggi di base: aggiungere i dati di trasformazione principali, reindirizzare il flusso di input e quindi reindirizzare il flusso di output. Questa operazione può essere considerata più semplice in un esempio.
Si inizierà con una semplice origine per eseguire il sink di flusso di dati come il seguente:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Se si decide di aggiungere una trasformazione derivazione, è necessario innanzitutto creare il testo della trasformazione principale, che include un'espressione semplice per aggiungere una nuova colonna maiuscola denominata `upperCaseTitle`:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Quindi, si accetta il DFS esistente e si aggiunge la trasformazione:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

A questo punto, il flusso in ingresso viene reindirizzato identificando la trasformazione che si vuole venga successiva alla nuova trasformazione (in questo caso, `source1`) e copiando il nome del flusso nella nuova trasformazione:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Infine, identifichiamo la trasformazione che desideriamo dopo la nuova trasformazione e sostituisco il relativo flusso di input (in questo caso, `sink1`) con il nome del flusso di output della nuova trasformazione:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Nozioni fondamentali su DFS
Il DFS è costituito da una serie di trasformazioni connesse, tra cui origini, sink e diverse altre, che possono aggiungere nuove colonne, filtrare dati, unire dati e molto altro ancora. In genere, lo script inizia con una o più origini seguite da numerose trasformazioni e termina con uno o più sink.

Le origini hanno tutti la stessa costruzione di base:
```
source(
  source properties
) ~> source_name
```

Ad esempio, un'origine semplice con tre colonne (movieId, title, genres) sarà:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Tutte le trasformazioni diverse dalle origini hanno la stessa costruzione di base:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Ad esempio, una semplice trasformazione derivata che accetta una colonna (titolo) e la sovrascrive con una versione maiuscola è la seguente:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

E un sink senza schema sarebbe semplicemente:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Passaggi successivi

Esplorare i flussi di dati iniziando dall' [articolo Panoramica dei flussi di dati](concepts-data-flow-overview.md)
