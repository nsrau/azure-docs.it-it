---
title: Errori e avvisi di conversione dei disegni in Mappe di Azure
description: Informazioni sugli errori e gli avvisi di conversione che possono verificarsi durante l'uso del servizio di conversione Mappe di Azure. Leggere i suggerimenti su come risolvere gli errori e gli avvisi, con alcuni esempi.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 14cf5238d29ede1ea229604316eee875b417e50e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361535"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Errori e avvisi di conversione dei disegni

Il [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) consente di convertire i pacchetti di disegno caricati in dati di mappe. I pacchetti di disegno devono essere conformi ai [requisiti del pacchetto di disegno](drawing-requirements.md). Se uno o più requisiti non sono soddisfatti, il servizio di conversione restituirà errori o avvisi. Questo articolo elenca i codici degli errori e degli avvisi di conversione, con suggerimenti su come risolverli. Inoltre fornisce alcuni esempi di disegni che possono causare la restituzione di questi codici da parte del servizio di conversione.

Il servizio di conversione avrà esito positivo se sono presenti avvisi di conversione. Tuttavia, è consigliabile esaminare e risolvere tutti gli avvisi. Un avviso indica che parte della conversione è stata ignorata o corretta automaticamente. La mancata risoluzione degli avvisi può causare errori nei processi successivi.

## <a name="general-warnings"></a>Avvisi generali

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*Descrizione per geometryWarning*

Un avviso **geometryWarning** si verifica quando il disegno contiene un'entità non valida. Un'entità non valida è un'entità non è conforme a vincoli geometrici. Esempi di entità non valide sono un poligono che si auto-interseca o una polilinea non chiusa in un livello che supporta solo la geometria chiusa.

Il servizio di conversione non è in grado di creare una funzionalità di mappa da un'entità non valida e la ignora.

#### <a name="examples-for-geometrywarning"></a>*Esempi per geometryWarning*

* Le due immagini seguenti mostrano esempi di poligoni che si auto-intersecano.

     ![Esempio di poligono con intersezione automatica, ad esempio uno.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Esempio di poligono con intersezione automatica, esempio due.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Di seguito è riportata un'immagine che mostra una polilinea non chiusa. Si supponga che il livello supporti solo la geometria chiusa.

    ![Esempio di una polilinea non chiusa](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*Come correggere geometryWarning*

Esaminare il **geometryWarning** per ogni entità per verificare che segua i vincoli geometrici.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Descrizione per unexpectedGeometryInLayer*

Un avviso **unexpectedGeometryInLayer** si verifica quando il disegno contiene una geometria non compatibile con il tipo di geometria previsto per un livello specifico. Quando il servizio di conversione restituisce un avviso **unexpectedGeometryInLayer**, ignorerà tale geometria.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Esempio per unexpectedGeometryInLayer*

L'immagine seguente mostra una polilinea non chiusa. Si supponga che il livello supporti solo la geometria chiusa.

![Esempio di una polilinea non chiusa](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*Come correggere unexpectedGeometryInLayer*

Controllare ogni avviso **unexpectedGeometryInLayer** e spostare la geometria non compatibile in un livello compatibile. Se non è compatibile con nessuno degli altri livelli, è necessario rimuoverla.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Descrizione per unsupportedFeatureRepresentation*

L'avviso **unsupportedFeatureRepresentation** si verifica quando il disegno contiene un tipo di entità non supportato.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Esempio per unsupportedFeatureRepresentation*

L'immagine seguente illustra un tipo di entità non supportato come oggetto testo multilinea su un livello di etichetta.
  
![Esempio di oggetto testo multilinea sul livello etichetta](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*Come correggere unsupportedFeatureRepresentation*

Assicurarsi che i file DWG contengano solo i tipi di entità supportati. I tipi supportati sono elencati nella sezione [Requisiti dei file di disegno nell'articolo Requisiti del pacchetto di disegno](drawing-requirements.md#drawing-package-requirements).

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*Descrizione per automaticRepairPerformed*

L'avviso **automaticRepairPerformed** si verifica quando il servizio di conversione ripara automaticamente la geometria non valida.

#### <a name="examples-for-automaticrepairperformed"></a>*Esempi per automaticRepairPerformed*

* L'immagine seguente illustra il modo in cui il servizio di conversione ha riparato un poligono che si auto-interseca in una geometria valida.

    ![Esempio di poligono che si auto-interseca riparato](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* L'immagine seguente illustra il modo in cui il servizio di conversione ha allineato il primo e l'ultimo vertice di una polilinea non chiusa per creare una polilinea chiusa, dove il primo e l'ultimo vertice erano a una distanza inferiore a 1 mm.  

    ![Esempio di polilinea allineata](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* L'immagine seguente illustra come, in un livello che supporta solo polilinee chiuse, il servizio di conversione ha riparato più polilinee non chiuse. Per evitare l'eliminazione delle polilinee non chiuse, il servizio le ha combinate in un'unica polilinea chiusa.

    ![Esempio di polilinee non chiuse combinate in una singola polilinea chiusa](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Come correggere automaticRepairPerformed*

Per correggere un avviso **automaticRepairPerformed**, eseguire le azioni seguenti:

1. Controllare la geometria di ogni avviso e il testo dell'avviso specifico.
2. Determinare se la riparazione automatizzata è corretta.
3. Se la riparazione è corretta, continuare. In caso contrario, passare al file di progettazione e risolvere l'avviso manualmente.

>[!TIP]
>Per eliminare un avviso in futuro, apportare modifiche al disegno originale in modo che corrisponda al disegno riparato.

## <a name="manifest-warnings"></a>Avvisi del manifesto

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*Descrizione per redundantAttribution*

L'avviso **redundantAttribution** si verifica quando il manifesto contiene proprietà dell'oggetto ridondanti o in conflitto.

#### <a name="examples-for-redundantattribution"></a>*Esempi per redundantAttribution*

* Il frammento di codice JSON seguente contiene due o più oggetti `unitProperties` con la stessa `name`.

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* Nel frammento di codice JSON seguente due o più oggetti `zoneProperties` hanno lo stesso `name`.

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*Come correggere redundantAttribution*

Per correggere un avviso **redundantAttribution*, rimuovere le proprietà dell'oggetto ridondanti o in conflitto.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*Descrizione per manifestWarning*

Un **manifestWarning** si verifica quando il manifesto contiene oggetti unitProperties o zoneProperties non usati durante la conversione.

#### <a name="examples-for-manifestwarning"></a>*Esempi per manifestWarning*

* Il manifesto contiene un oggetto `unitProperties` con un `unitName` senza etichetta corrispondente in un livello `unitLabel`.

* Il manifesto contiene un oggetto `zoneProperties` con un `zoneName` senza etichetta corrispondente in un livello `zoneLabel`.

#### <a name="how-to-fix-manifestwarning"></a>*Come correggere manifestWarning*

Per correggere **manifestWarning**, rimuovere l'oggetto `unitProperties` o `zoneProperties` non usato dal manifesto oppure aggiungere un'etichetta di unità/zona al disegno, in modo che l'oggetto proprietà venga usato durante la conversione.

## <a name="wall-warnings"></a>Avvisi muro

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Descrizione per wallOutsideLevel*

L'avviso **wallOutsideLevel** si verifica quando il disegno contiene una geometria muro all'esterno dei limiti di una struttura di livello.

#### <a name="example-for-walloutsidelevel"></a>*Esempio per wallOutsideLevel*

* L'immagine seguente mostra un muro interno, in rosso, all'esterno del limite del livello giallo.

    ![Esempio di muro interno all'esterno del limite del livello](./media/drawing-conversion-error-codes/wall-outside-level.png)

* L'immagine seguente mostra un muro esterno, in rosso, all'esterno del limite del livello giallo.

    ![Esempio di muro esterno all'esterno del limite del livello](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Come correggere wallOutsideLevel*

Per correggere un avviso **wallOutsideLevel**, espandere la geometria del livello per includere tutti i muri. In alternativa, modificare i limiti del muro per adattarli al limite del livello.

## <a name="unit-warnings"></a>Avvisi unità

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Descrizione per unitOutsideLevel*

Un avviso **wallOutsideLevel** si verifica quando il disegno contiene una geometria unità all'esterno dei limiti di un contorno di livello.

#### <a name="example-for-unitoutsidelevel"></a>*Esempio per unitOutsideLevel*

 Nell'immagine seguente la geometria dell'unità, in rosso, supera i limiti del limite del livello giallo.

 ![Esempio di unità che supera il limite del livello](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Come correggere unitOutsideLevel*

Per correggere un avviso **unitOutsideLevel**, espandere il limite del livello per includere tutte le unità. In alternativa, modificare la geometria dell'unità per adattarla al limite del livello.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*Descrizione per partiallyOverlappingUnit*

Un avviso **partiallyOverlappingUnit** si verifica quando il disegno contiene una geometria di unità parzialmente sovrapposta a un'altra geometria di unità. Il servizio di conversione ignora le unità sovrapposte.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Scenari di esempio partiallyOverlappingUnit*

Nell'immagine seguente l'unità sovrapposta è evidenziata in rosso. `UNIT110` e `HALLWAY` sono ignorati.

![Esempio di unità sovrapposte](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*Come correggere partiallyOverlappingUnit*

Per correggere un avviso **partiallyOverlappingUnit**, ridisegnare ogni unità parzialmente sovrapposta in modo che non si sovrapponga ad altre unità.

## <a name="door-warnings"></a>Avvisi porta

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Descrizione per doorOutsideLevel*

Un avviso **doorOutsideLevel** si verifica quando il disegno contiene una geometria porta all'esterno dei limiti di una geometria di livello.

#### <a name="example-for-dooroutsidelevel"></a>*Esempio per doorOutsideLevel*

Nell'immagine seguente la geometria della porta, evidenziata in rosso, si sovrappone al limite del livello giallo.

![Esempio di porta sovrapposta a un limite di livello](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*Come correggere doorOutsideLevel*

Per correggere un avviso **doorOutsideLevel**, ridisegnare la geometria della porta in modo che si trovi all'interno dei limiti del livello.

## <a name="zone-warnings"></a>Avvisi zona

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*Descrizione per zoneWarning*

L'avviso **zoneWarning** si verifica quando una zona non contiene un'etichetta. Il servizio di conversione elimina una zona che non è un'etichetta.

#### <a name="example-for-zonewarning"></a>*Esempio per zoneWarning*

La figura seguente mostra una zona che non contiene un'etichetta.

![L'esempio di una zona non contiene un'etichetta](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Come correggere zoneWarning*

Per correggere un avviso **zoneWarning**, verificare che ogni zona abbia un'etichetta singola.

## <a name="label-warnings"></a>Avvisi etichetta

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*Descrizione per labelWarning*

L'avviso **labelWarning** si verifica quando il disegno contiene funzionalità di etichette ambigue o contraddittorie.

Un avviso **labelWarning** si verifica a causa di uno o più dei motivi seguenti:

* Un'etichetta di unità non è presente in nessuna unità.
* Un'etichetta di zona non è presente in nessuna zona.
* Un'etichetta di zona si trova all'interno di due o più zone.

#### <a name="example-for-labelwarning"></a>*Esempio per labelWarning*

L'immagine seguente illustra un'etichetta che si trova all'interno di due zone.

![Esempio di etichetta all'interno di due zone ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*Come correggere labelWarning*

Per correggere un avviso **labelWarning**, verificare quanto segue:

* Tutte le etichette di unità si trovano all'interno di unità.
* Tutte le etichette di zona si trovano all'interno di zone.
* Tutte le etichette di zona si trovano in una e una sola zona.

## <a name="drawing-package-errors"></a>Errori del pacchetto di disegno

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*Descrizione per invalidArchiveFormat*

Un errore **invalidArchiveFormat** si verifica quando il pacchetto di disegno è in un formato di archivio non valido, ad esempio GZIP o 7-zip. È supportato solo il formato di archivio ZIP.

Un errore **invalidArchiveFormat** si verifica anche quando l'archivio ZIP è vuoto.

#### <a name="how-to-fix-invalidarchiveformat"></a>*Come correggere invalidArchiveFormat*

Per correggere un errore **invalidArchiveFormat**, verificare che:

* Il nome del file di archivio termina con _.zip_.
* L'archivio ZIP contiene dati.
* È possibile aprire l'archivio ZIP.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*Descrizione per invalidUserData*

Un errore **invalidUserData** si verifica quando il servizio di conversione non è in grado di leggere un oggetto dati utente dall'archivio.

#### <a name="example-scenario-for-invaliduserdata"></a>*Scenario di esempio per invalidUserData*

Si è tentato di caricare un pacchetto di disegno con un parametro `udid` errato.

#### <a name="how-to-fix-invaliduserdata"></a>*Come correggere invalidUserData*

Per correggere un errore **invalidUserData**, verificare che:

* È stato fornito un `udid` corretto per il pacchetto caricato.
* Azure Maps Creator è stato abilitato per l'account Mappe di Azure usato per il caricamento del pacchetto di disegno.
* La richiesta API al servizio di conversione contiene la chiave di sottoscrizione per l'account Mappe di Azure usato per il caricamento del pacchetto di disegno.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Descrizione per dwgError*

Un errore **dwgError** si verifica quando il pacchetto di disegno contiene un problema con uno o più file DWG nell'archivio ZIP caricato.

L'errore **dwgError** si verifica quando il pacchetto di disegno contiene un file DWG che non può essere aperto perché non è valido o è danneggiato.

* Un file DWG non è un disegno con formato di file DWG di AutoCAD valido.
* Un file DWG è danneggiato.
* Un file DWG è elencato nel file _manifest.json_, ma manca nell'archivio ZIP.

#### <a name="how-to-fix-dwgerror"></a>*Come correggere dwgError*

Per correggere un **dwgError**, esaminare il file _manifest.json_ per confermare che:

* Tutti i file DWG nell'archivio ZIP sono disegni con formato DWG di AutoCAD valido, aperti ognuno in AutoCAD. Rimuovere o correggere tutti i disegni non validi.
* L'elenco di file DWG nel file _manifest.json_ corrisponde ai file DWG nell'archivio ZIP.

## <a name="manifest-errors"></a>Errori del manifesto

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Descrizione per invalidJsonFormat

Un errore **invalidJsonFormat** si verifica quando non è possibile leggere il file _manifest.json_.

Non è possibile leggere il file _manifest.json_file a causa di errori di formattazione JSON o di sintassi. Per altre informazioni sul formato JSON e sulla sintassi, vedere [JavaScript Object Notation (JSON) Data Interchange Format](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*Come correggere invalidJsonFormat*

Per correggere un errore **invalidJsonFormat**, usare un linter JSON per rilevare e risolvere eventuali errori JSON.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Descrizione per missingRequiredField*

Un errore **missingRequiredField** si verifica quando nel file _manifest.json_ mancano i dati necessari.

#### <a name="how-to-fix-missingrequiredfield"></a>*Come correggere missingRequiredField*

Per correggere un errore **missingRequiredField**, verificare che il manifesto contenga tutte le proprietà necessarie. Per un elenco completo degli oggetti manifesto necessari, vedere la [sezione manifest nei requisiti del pacchetto di disegno](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*Descrizione per missingManifest*

L'errore **missingManifest** si verifica quando il file _manifest.json_ non è presente nell'archivio ZIP.

L'errore **missingManifest** si verifica a causa di uno o più dei motivi seguenti:

* Il file _manifest.json_ non è stato digitato correttamente.
* Manca il file _manifest.json_.
* Il file _manifest.json_ non si trova nella cartella radice dell'archivio ZIP.

#### <a name="how-to-fix-missingmanifest"></a>*Come correggere missingManifest*

Per correggere un errore **missingManifest**, verificare che l'archivio includa un file denominato _manifest.json_ al livello radice dell'archivio ZIP.

### <a name="conflict"></a>**conflict**

#### <a name="description-for-conflict"></a>*Descrizione per conflict*

L'errore **conflict** si verifica quando il file _manifest.json_ contiene informazioni in conflitto.

#### <a name="example-scenario-for-conflict"></a>*Scenario di esempio per conflict*

Il servizio di conversione restituirà un errore **conflict** quando viene definito più di un livello con lo stesso numero ordinale di livello. Il frammento di codice JSON seguente mostra due livelli definiti con lo stesso numero ordinale.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Come correggere conflict*

Per correggere un errore **conflict**, esaminare il file _manifest.json_ e rimuovere eventuali informazioni in conflitto.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Descrizione per invalidGeoreference*

L'errore **invalidGeoreference** si verifica quando un file _manifest.json_ contiene una georeferenziazione non valida.

L'errore **invalidGeoreference** si verifica a causa di uno o più dei motivi seguenti:

* L'utente fa riferimento geografico a un valore di latitudine o longitudine non compreso nell'intervallo.
* L'utente fa riferimento geografico a un valore della rotazione non compreso nell'intervallo.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Scenario di esempio per invalidGeoreference*

Nel frammento di codice JSON seguente la latitudine è superiore al limite massimo.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Come correggere invalidGeoreference*

Per correggere un errore **invalidGeoreference**, verificare che i valori georeferenziati siano compresi nell'intervallo.

>[!IMPORTANT]
>In GeoJSON, l'ordine delle coordinate è longitudine e latitudine. Se non si usa l'ordine corretto, è possibile fare riferimento accidentalmente a un valore di latitudine o longitudine non compreso nell'intervallo.

## <a name="wall-errors"></a>Errori muro

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Descrizione per wallError*

L'errore **wallError** si verifica quando il disegno contiene un errore durante il tentativo di creazione di una funzionalità muro.

#### <a name="example-scenario-for-wallerror"></a>*Scenario di esempio per wallError*

L'immagine seguente visualizza una funzionalità muro che non si sovrappone ad alcuna unità.

![Esempio di funzionalità muro che non si sovrappone ad alcuna unità](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*Come correggere wallError*

Per correggere un errore **wallError**, ridisegnare il muro in modo che si sovrapponga ad almeno un'unità. In alternativa, creare una nuova unità che si sovrappone al muro.

## <a name="vertical-penetration-errors"></a>Errori penetrazione verticale

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*Descrizione per verticalPenetrationError*

L'errore **verticalPenetrationError** si verifica quando il disegno contiene una funzionalità di penetrazione verticale ambigua.

L'errore **verticalPenetrationError** si verifica a causa di uno o più dei motivi seguenti:

* Il disegno contiene un'area di penetrazione verticale senza aree di penetrazione verticale sovrapposte a tutti i livelli superiori o inferiori.
* Il pacchetto di disegno contiene un livello con due o più funzionalità di penetrazione verticale che si sovrappongono a una funzionalità di penetrazione verticale singola a un altro livello direttamente superiore o inferiore.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Scenario di esempio per verticalPenetrationError*

L'immagine seguente mostra un'area di penetrazione verticale priva di aree di penetrazione verticale sovrapposte ai livelli superiori o inferiori.

![Esempio di penetrazione verticale 1](./media/drawing-conversion-error-codes/vrt-2.png)

L'immagine seguente illustra un'area di penetrazione verticale che si sovrappone a più di un'area di penetrazione verticale a un livello adiacente.

![Esempio di penetrazione verticale 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Come correggere verticalPenetrationError

Per correggere un errore **verticalPenetrationError**, leggere come usare una funzionalità di penetrazione verticale nell'articolo [Requisiti del pacchetto di disegno](drawing-requirements.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come usare il visualizzatore errori di disegno di Mappe di Azure](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Autore per mapping di interni](creator-indoor-maps.md)