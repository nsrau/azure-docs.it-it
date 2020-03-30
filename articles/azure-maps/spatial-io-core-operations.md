---
title: Operazioni di I/O di base Mappe di Microsoft Azure
description: Informazioni su come leggere e scrivere in modo efficiente XML e dati delimitati utilizzando le librerie di base dal modulo i/O spaziale.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371448"
---
# <a name="core-io-operations"></a>Operazioni di I/O di base

Oltre a fornire strumenti per leggere i file di dati spaziali, il modulo I/O spaziale espone le librerie sottostanti di base per leggere e scrivere XML e dati delimitati in modo rapido ed efficiente.

Lo `atlas.io.core` spazio dei nomi contiene due classi di basso livello in grado di leggere e scrivere rapidamente dati CSV e XML. Queste classi di base alimentano i lettori e i writer di dati spaziali nel modulo I/O spaziale. Sentiti libero di usarli per aggiungere ulteriore supporto di lettura e scrittura per i file CSV o XML.
 
## <a name="read-delimited-files"></a>Lettura di file delimitati

La `atlas.io.core.CsvReader` classe legge le stringhe che contengono set di dati delimitati. Questa classe fornisce due metodi per la lettura dei dati:This class provides two methods for reading data:

- La `read` funzione leggerà il set di dati completo e restituirà una matrice bidimensionale di stringhe che rappresentano tutte le celle del set di dati delimitato.
- La `getNextRow` funzione legge ogni riga di testo in un set di dati delimitato e restituisce una matrice di stringhe che rappresenta tutte le celle in tale riga del set di dati. L'utente può elaborare la riga ed eliminare la memoria non necessaria da tale riga prima di elaborare la riga successiva. Quindi, la funzione è più efficiente in termini di memoria.

Per impostazione predefinita, il lettore utilizzerà il carattere virgola come delimitatore. Tuttavia, il delimitatore può essere modificato `'auto'`in qualsiasi singolo carattere o impostato su . Se impostato `'auto'`su , il lettore analizzerà la prima riga di testo nella stringa. Quindi, selezionerà il carattere più comune dalla tabella seguente da utilizzare come delimitatore.

| | |
| :-- | :-- |
| Virgola | `,` |
| Scheda | `\t` |
| Pipe | `|` |

Questo lettore supporta anche i qualificatori di testo utilizzati per gestire le celle che contengono il carattere delimitatore. Il carattere virgolette (`'"'`) è il qualificatore di testo predefinito, ma può essere modificato in qualsiasi carattere singolo.

## <a name="write-delimited-files"></a>Scrivere file delimitati

Il `atlas.io.core.CsvWriter` scrive una matrice di oggetti come stringa delimitata. Qualsiasi carattere singolo può essere utilizzato come delimitatore o qualificatore di testo. Il delimitatore predefinito`','`è la virgola ( )`'"'`e il qualificatore di testo predefinito è il carattere virgolette ( ).

Per utilizzare questa classe, attenersi alla seguente procedura:

- Creare un'istanza della classe e, facoltativamente, impostare un delimitatore personalizzato o un qualificatore di testo.
- Scrivere dati nella classe `write` utilizzando `writeRow` la funzione o la funzione . Per `write` la funzione, passare una matrice bidimensionale di oggetti che rappresentano più righe e celle. Per utilizzare `writeRow` la funzione, passare una matrice di oggetti che rappresentano una riga di dati con più colonne.
- Chiamare `toString` la funzione per recuperare la stringa delimitata. 
- Facoltativamente, chiamare `clear` il metodo per rendere riutilizzabile il writer `delete` e ridurne l'allocazione delle risorse oppure chiamare il metodo per eliminare l'istanza del writer.

> [!Note]
> Il numero di colonne scritte sarà vincolato al numero di celle nella prima riga dei dati passati al writer.

## <a name="read-xml-files"></a>Leggere i file XML

La `atlas.io.core.SimpleXmlReader` classe è più veloce nell'analisi di file XML rispetto `DOMParser`a . Tuttavia, `atlas.io.core.SimpleXmlReader` la classe richiede che i file XML siano ben formattati. I file XML che non sono ben formattati, ad esempio i tag di chiusura mancanti, genereranno probabilmente un errore.

Il codice seguente illustra `SimpleXmlReader` come usare la classe per analizzare una stringa XML in un oggetto JSON e serializzarla nel formato desiderato.

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

La `atlas.io.core.SimpleXmlWriter` classe scrive XML in formato ben formattato in modo efficiente dalla memoria.

Nel codice seguente viene `SimpleXmlWriter` illustrato come utilizzare la classe per generare una stringa XML ben formattata.

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

Il codice XML generato dal codice precedente sarà simile al seguente.

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
> [CsvReader (informazioni in uso)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader (informazioni in base al tazz](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter (scrittura di SimpleXmlWriter)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Dettagli sul formato dati supportati](spatial-io-supported-data-format-details.md)