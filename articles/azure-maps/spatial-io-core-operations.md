---
title: Operazioni di i/o Core | Mappe Microsoft Azure
description: Informazioni su come leggere e scrivere in modo efficiente i dati XML e delimitati usando le librerie di base del modulo IO spaziale.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d2a82fd5d9ba958fd6490a83ecbbe0a4bdf820a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370934"
---
# <a name="core-io-operations"></a>Operazioni di i/o Core

Oltre a fornire gli strumenti per leggere i file di dati spaziali, il modulo i/o spaziale espone le librerie principali sottostanti per leggere e scrivere dati XML e delimitati in modo rapido ed efficiente.

Lo spazio dei nomi `atlas.io.core` contiene due classi di basso livello che possono leggere e scrivere rapidamente dati CSV e XML. Queste classi di base potenziano i lettori e i writer dei dati spaziali nel modulo di i/o spaziale. È possibile usarli per aggiungere ulteriore supporto per la lettura e la scrittura di file CSV o XML.
 
## <a name="read-delimited-files"></a>Lettura di file delimitati

La classe `atlas.io.core.CsvReader` legge le stringhe che contengono set di dati delimitati. Questa classe fornisce due metodi per la lettura dei dati:

- Tramite la funzione `read` viene letto il set di dati completo e viene restituita una matrice bidimensionale di stringhe che rappresenta tutte le celle del set di dati delimitato.
- La funzione `getNextRow` legge ogni riga di testo in un set di dati delimitato e restituisce una matrice di stringa che rappresenta tutte le celle della riga del set di dati. L'utente può elaborare la riga ed eliminare la memoria non necessaria da tale riga prima di elaborare la riga successiva. Quindi, la funzione è più efficiente della memoria.

Per impostazione predefinita, il lettore utilizzerà il carattere virgola come delimitatore. Tuttavia, il delimitatore può essere modificato in qualsiasi carattere singolo o impostato su `'auto'`. Se impostato su `'auto'`, il lettore analizzerà la prima riga di testo nella stringa. Quindi seleziona il carattere più comune della tabella seguente da usare come delimitatore.

| | |
| :-- | :-- |
| Virgola | `,` |
| Scheda | `\t` |
| Pipe | `|` |

Questo Reader supporta inoltre i qualificatori di testo usati per gestire le celle che contengono il carattere delimitatore. Il carattere virgolette (`'"'`) è il qualificatore di testo predefinito, ma può essere modificato in qualsiasi carattere singolo.

## <a name="write-delimited-files"></a>Scrivi file delimitati

Il `atlas.io.core.CsvWriter` scrive una matrice di oggetti sotto forma di stringa delimitata. Qualsiasi carattere singolo può essere utilizzato come delimitatore o come qualificatore di testo. Il delimitatore predefinito è virgola (`','`) e il qualificatore di testo predefinito è il carattere virgolette (`'"'`).

Per usare questa classe, attenersi alla procedura seguente:

- Creare un'istanza della classe e, facoltativamente, impostare un delimitatore personalizzato o un qualificatore di testo.
- Scrivere dati nella classe usando la funzione `write` o la funzione `writeRow`. Per la funzione `write`, passare una matrice bidimensionale di oggetti che rappresentano più righe e celle. Per utilizzare la funzione `writeRow`, passare una matrice di oggetti che rappresenta una riga di dati con più colonne.
- Chiamare la funzione `toString` per recuperare la stringa delimitata. 
- È possibile chiamare il metodo `clear` per rendere il writer riutilizzabile e ridurne l'allocazione delle risorse oppure chiamare il metodo `delete` per eliminare l'istanza del writer.

> [!Note]
> Il numero di colonne scritte sarà vincolato al numero di celle nella prima riga dei dati passati al writer.

## <a name="read-xml-files"></a>Lettura di file XML

La classe `atlas.io.core.SimpleXmlReader` è più veloce durante l'analisi dei file XML rispetto a `DOMParser`. Tuttavia, la classe `atlas.io.core.SimpleXmlReader` richiede che i file XML siano formattati correttamente. I file XML che non sono formattati correttamente, ad esempio i tag di chiusura mancanti, probabilmente genereranno un errore.

Il codice seguente illustra come usare la classe `SimpleXmlReader` per analizzare una stringa XML in un oggetto JSON e serializzarla nel formato desiderato.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Scrivere file XML

La classe `atlas.io.core.SimpleXmlWriter` scrive codice XML ben formattato in modo efficiente per la memoria.

Nel codice seguente viene illustrato come utilizzare la classe `SimpleXmlWriter` per generare una stringa XML ben formattata.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Il codice XML generato dal codice precedente avrà un aspetto simile al seguente.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Dettagli del formato dati supportati](spatial-io-supported-data-format-details.md)