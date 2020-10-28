---
title: Requisiti del pacchetto di disegni in Creator di Mappe di Azure
description: Informazioni sui requisiti del pacchetto di disegno per convertire i file di progettazione della struttura per eseguire il mapping dei dati
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 2c3e46bf386e70cbe35d96728ede896d6bf0dc7d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895835"
---
# <a name="drawing-package-requirements"></a>Requisiti del pacchetto di disegni

È possibile convertire i pacchetti di disegno caricati in dati della mappa usando il [servizio di conversione mappe di Azure](/rest/api/maps/conversion). Questo articolo descrive i requisiti del pacchetto di disegni per l'API di conversione. Per altre informazioni, è possibile scaricare il [pacchetto di disegni](https://github.com/Azure-Samples/am-creator-indoor-data-examples) di esempio.

## <a name="prerequisites"></a>Prerequisiti

Il pacchetto di disegno include i disegni salvati in formato DWG, che è il formato di file nativo per il software AutoCAD® di Autodesk.

È possibile scegliere qualsiasi software CAD per produrre i disegni nel pacchetto di disegno.  

Il [servizio di conversione di Mappe di Azure](/rest/api/maps/conversion) converte il pacchetto di disegni in dati di mappe. Il servizio di conversione funziona con il formato di file DWG di AutoCAD. `AC1032` è la versione del formato interno per i file DWG ed è consigliabile selezionare `AC1032` la versione del formato di file DWG interno.  

## <a name="glossary-of-terms"></a>Glossario dei termini

Per un riferimento semplice, di seguito sono riportati alcuni termini e definizioni importanti durante la lettura di questo articolo.

| Termine  | Definizione |
|:-------|:------------|
| Livello | Un livello DWG di AutoCAD.|
| Level | Un'area di un edificio a un'elevazione impostata, ad esempio il piano. |
| Xref  |Un file in formato DWG di AutoCAD (. dwg), associato al disegno primario come riferimento esterno.  |
| Funzionalità | Un oggetto che combina una figura geometrica con informazioni aggiuntive di metadati. |
| Classi di funzionalità | Un progetto comune per le caratteristiche. Ad esempio, un' *unità* è una classe di funzionalità e un *ufficio* è una funzionalità. |

## <a name="drawing-package-structure"></a>Struttura del pacchetto di disegno

Un pacchetto di disegni è un archivio ZIP che contiene i file seguenti:

* File DWG in formato DWG di AutoCAD.
* Un file _manifest.json_ per una singola struttura.

È possibile organizzare i file DWG in qualsiasi modo all'interno della cartella, ma il file manifesto deve risiedere nella directory radice della cartella. È necessario comprimere la cartella in un unico file di archivio con estensione zip. Le sezioni successive illustrano in dettaglio i requisiti per i file DWG, il file manifesto e il contenuto di questi file.  

## <a name="dwg-files-requirements"></a>Requisiti dei file DWG

È necessario un singolo file DWG per ogni livello della struttura. I dati del livello devono essere contenuti in un singolo file DWG. Qualsiasi riferimento esterno ( _xref_ ) deve essere associato al disegno padre. Inoltre, ogni file DWG:

* Deve definire i livelli _Exterior_ e _Unit_ . Facoltativamente, può definire i livelli facoltativi seguenti: _Wall_ , _door_ , _UnitLabel_ , _zone_ e _ZoneLabel_ .
* Non deve contenere caratteristiche di più livelli.
* Non deve contenere caratteristiche di più strutture.

Il [servizio di conversione di Mappe di Azure](/rest/api/maps/conversion) può estrarre le classi di caratteristiche seguenti da un file DWG:

* Levels
* Unità
* Zone
* Aperture
* Pareti
* Penetrazioni verticali

Tutti i processi di conversione generano un set minimo di categorie predefinite: room, structure.wall, opening.door, zone e facility. Le categorie aggiuntive sono relative a ogni nome di categoria a cui fanno riferimento gli oggetti.  

Un livello DWG deve contenere le caratteristiche di una singola classe. Le classi non devono condividere un livello. Ad esempio, le unità e le pareti non possono condividere un livello.

I livelli DWG devono inoltre rispettare i criteri seguenti:

* Le origini dei disegni per tutti i file DWG devono essere allineate alla stessa latitudine e alla stessa longitudine.
* Ogni livello deve avere lo stesso orientamento degli altri.
* I poligoni ad intersezione automatica vengono corretti automaticamente e il servizio di [conversione mappe di Azure](/rest/api/maps/conversion) genera un avviso. È necessario controllare manualmente i risultati corretti, perché potrebbero non corrispondere ai risultati previsti.

Tutte le entità livello devono essere di uno dei tipi seguenti: linea, polilinea, poligono, arco circolare, cerchio o testo (riga singola). Qualsiasi altro tipo di entità viene ignorato.

Nella tabella seguente vengono descritti i tipi di entità supportati e le funzionalità supportate per ogni livello. Se un livello contiene tipi di entità non supportati, il [servizio di conversione mappe di Azure](/rest/api/maps/conversion) ignora tali entità.  

| Livello | Tipi di entità | Funzionalità |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Poligono, polilinea (chiusa), cerchio | Levels
| [Unità](#unit-layer) |  Poligono, polilinea (chiusa), cerchio | Penetrazioni verticali, unità
| [Wall](#wall-layer)  | Poligono, polilinea (chiusa), cerchio | Non applicabile. Per altre informazioni, vedere il [livello Wall](#wall-layer).
| [Door](#door-layer) | Poligono, polilinea, linea, arco circolare, cerchio | Aperture
| [Zona](#zone-layer) | Poligono, polilinea (chiusa), cerchio | Zona
| [UnitLabel](#unitlabel-layer) | Testo (riga singola) | Non applicabile. Questo livello può aggiungere proprietà solo alle caratteristiche di unità del livello Unit. Per altre informazioni, vedere il [livello UnitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Testo (riga singola) | Non applicabile. Questo livello può aggiungere proprietà solo alle caratteristiche di zona del livello ZonesLayer. Per ulteriori informazioni, vedere il [livello ZoneLabel](#zonelabel-layer).

Le sezioni successive illustrano in dettaglio i requisiti per ogni livello.

### <a name="exterior-layer"></a>Livello Exterior

Il file DWG per ogni livello deve contenere un livello per definirne il perimetro. Questo livello viene definito livello *esterno* . Se ad esempio una struttura contiene due livelli, dovrà avere due file DWG, con un livello di esterni per ognuno.

Indipendentemente dal numero di disegni di entità presenti nel livello esterno, il [set di dati di struttura risultante](tutorial-creator-indoor-maps.md#create-a-feature-stateset) conterrà solo una funzionalità di livello per ogni file DWG. Inoltre:

* Gli esterni devono essere disegnati come poligono, polilinea (chiusa) o cerchio.
* Gli esterni possono sovrapporsi, ma vengono risolti in un'unica geometria.

Se il livello contiene più polilinee sovrapposte, le polilinee vengono dissolte in una funzionalità a un solo livello. In alternativa, se il livello contiene più polilinee non sovrapposte, la funzionalità del livello risultante avrà una rappresentazione multipoligonale.

È possibile vedere un esempio di livello esterno come livello struttura nel [pacchetto di disegno di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Livello Unit

Il file DWG per ogni livello definisce un livello contenente unità. Le unità sono spazi esplorabili nell'edificio, ad esempio uffici, corridoi, scale e ascensori. Il livello unità dovrà rispettare i requisiti seguenti:

* Le unità devono essere disegnate come poligono, polilinea (chiusa) o cerchio.
* Le unità devono rientrare nei limiti del perimetro esterno della struttura.
* Le unità non devono sovrapporsi parzialmente.
* Le unità non devono contenere figure geometriche che si auto-intersecano.

Assegnare un nome a un'unità creando un oggetto testo nel livello UnitLabel, quindi inserire l'oggetto all'interno dei limiti dell'unità. Per altre informazioni, vedere il [livello UnitLabel](#unitlabel-layer).

È possibile vedere un esempio del livello unità nel pacchetto di [disegno di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Livello Wall

Il file DWG per ogni livello può contenere un livello che definisce gli extent fisici di muri, colonne e altre strutture di compilazione.

* I muri devono essere disegnati come poligono, polilinea (chiusa) o cerchio.
* Il livello parete o i livelli devono contenere solo la geometria interpretata come struttura di compilazione.

È possibile vedere un esempio di livello muri nel pacchetto di [disegno di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Livello Door

È possibile includere un livello DWG che contiene porte. Ogni sportello deve sovrapporsi al bordo di un'unità dal livello unità.

Gli sportelli aperti in un set di dati di Maps di Azure sono rappresentati come segmento a riga singola che si sovrappone a più limiti di unità. Le immagini seguenti illustrano come convertire la geometria nel livello della porta per aprire le funzionalità in un set di dati.

![Quattro grafici che illustrano i passaggi per generare le aperture](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Livello Zone

Il file DWG per ogni livello può contenere un livello di zona che definisce gli extent fisici delle zone. Una zona può essere uno spazio vuoto interno o un cortile.

* Le zone devono essere disegnate come poligono, polilinea (chiusa) o cerchio.
* Le zone possono sovrapporsi.
* Le zone possono rientrare o essere esterne al perimetro esterno della struttura.

Assegnare un nome a una zona creando un oggetto testo nel livello ZoneLabel e inserendo l'oggetto testo all'interno dei limiti della zona. Per altre informazioni, vedere il [livello ZoneLabel](#zonelabel-layer).

È possibile vedere un esempio del livello zona nel pacchetto di [disegno di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>Livello UnitLabel

Il file DWG per ogni livello può contenere un livello UnitLabel. Il livello UnitLabel aggiunge una proprietà Name alle unità estratte dal livello unità. Per le unità con una proprietà name possono essere specificati dettagli aggiuntivi nel file manifesto.

* Le etichette di unità devono essere entità di testo a riga singola.
* Le etichette di unità devono rientrare nei limiti della rispettiva unità.
* Le unità non devono contenere più entità di testo nel livello UnitLabel.

È possibile vedere un esempio del livello UnitLabel nel pacchetto di [disegno di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>Livello ZoneLabel

Il file DWG per ogni livello può contenere un livello ZoneLabel. Questo livello aggiunge una proprietà name alle zone estratte dal livello Zone. Per le zone con una proprietà name possono essere specificati dettagli aggiuntivi nel file manifesto.

* Le etichette di zone essere entità di testo a riga singola.
* Le etichette di zone devono rientrare nei limiti della rispettiva zona.
* Le zone non devono contenere più entità di testo nel livello ZoneLabel.

È possibile vedere un esempio del livello ZoneLabel nel pacchetto di [disegno di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Requisiti dei file manifesto

La cartella ZIP deve contenere un file manifesto al livello radice della directory, che deve essere denominato **manifest.json** . Questo file descrive i file DWG per consentire al [servizio di conversione di Mappe di Azure](/rest/api/maps/conversion) di analizzarne il contenuto. Vengono inseriti solo i file identificati dal manifesto. I file che si trovano nella cartella zip, ma non sono elencati correttamente nel manifesto, vengono ignorati.

I percorsi dei file nell' `buildingLevels` oggetto del file manifesto devono essere relativi alla radice della cartella zip. Il nome del file DWG deve corrispondere esattamente al nome del livello della struttura. Ad esempio, un file DWG per il livello "seminterrato" è "seminterrato. dwg". Un file DWG per il livello 2 è denominato "level_2. dwg". Se il nome del livello include uno spazio, usare un carattere di sottolineatura.

Sebbene esistano requisiti quando si utilizzano gli oggetti manifesto, non tutti gli oggetti sono obbligatori. La tabella seguente Mostra gli oggetti obbligatori e facoltativi per la versione 1,1 del [servizio di conversione mappe di Azure](/rest/api/maps/conversion).

| Oggetto | Obbligatoria | Descrizione |
| :----- | :------- | :------- |
| `version` | True |Versione dello schema del manifesto. Attualmente è supportata solo la versione 1,1.|
| `directoryInfo` | true | Descrive le informazioni geografiche e di contatto della struttura. Può anche essere usato per descrivere informazioni geografiche e di contatto di un occupante. |
| `buildingLevels` | true | Specifica i livelli degli edifici e i file che contengono la progettazione dei livelli. |
| `georeference` | true | Contiene informazioni geografiche numeriche per il disegno della struttura. |
| `dwgLayers` | true | Elenca i nomi dei livelli e ogni livello elenca i nomi delle rispettive caratteristiche. |
| `unitProperties` | false | Può essere usato per inserire metadati aggiuntivi per le caratteristiche delle unità. |
| `zoneProperties` | false | Può essere usato per inserire metadati aggiuntivi per le caratteristiche delle zone. |

Le sezioni successive illustrano in dettaglio i requisiti per ogni oggetto.

### `directoryInfo`

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
| `name`      | string | true   |  Nome dell'edificio. |
| `streetAddress`|    string |    false    | Indirizzo dell'edificio. |
|`unit`     | string    |  false    |  Unità dell'edificio. |
| `locality` |    string |    false |    Nome di un'area, un quartiere o un'area geografica. Ad esempio, "Overlake" o "Central District". La località non fa parte dell'indirizzo postale. |
| `adminDivisions` |    Matrice di stringhe JSON |    false     | Una matrice contenente le designazioni degli indirizzi (paese, provincia, città) o (paese, regione, città metropolitana, provincia). Usare i codici paese ISO 3166 e i codici stato/territorio ISO 3166-2. |
| `postalCode` |    string    | false    | Il codice di smistamento postale. |
| `hoursOfOperation` |    string |     false | Conforme al formato degli orari di apertura [OSM Opening Hours](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification). |
| `phone`    | string |    false |    Numero di telefono associato all'edificio. Deve includere il codice paese. |
| `website`    | string |    false    | Sito Web associato all'edificio. Deve iniziare con http o HTTPS. |
| `nonPublic` |    bool    | false | Flag che specifica se l'edificio è aperto al pubblico. |
| `anchorLatitude` | NUMERIC |    false | Latitudine di un ancoraggio di struttura (puntina da disegno). |
| `anchorLongitude` | NUMERIC |    false | Longitudine di un ancoraggio di struttura (puntina da disegno). |
| `anchorHeightAboveSeaLevel`  | NUMERIC | false | Altezza del pianterreno della struttura sul livello del mare, in metri. |
| `defaultLevelVerticalExtent` | NUMERIC | false | Altezza predefinita (spessore) di un livello di questa struttura da usare quando la proprietà `verticalExtent` di un livello non è definita. |

### `buildingLevels`

L'oggetto `buildingLevels` contiene una matrice JSON di livelli di edifici.

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
|`levelName`    |string    |true |    Nome descrittivo del livello. Ad esempio: piano 1, lobby, parcheggio blu o seminterrato.|
|`ordinal` | integer |    true | Determina l'ordine verticale dei livelli. Ogni struttura deve avere un livello con ordinal 0. |
|`heightAboveFacilityAnchor` | NUMERIC | false |    Altezza del livello superiore all'ancoraggio in metri. |
| `verticalExtent` | NUMERIC | false | Altezza da piano a soffitto (spessore) del livello in metri. |
|`filename` |    string |    true |    Percorso del file system del disegno CAD per un livello dell'edificio. Deve essere relativo alla radice del file ZIP dell'edificio. |

### `georeference`

| Proprietà  | Type | Obbligatoria | Description |
|-----------|------|----------|-------------|
|`lat`    | NUMERIC |    true |    Rappresentazione decimale della latitudine in gradi in corrispondenza dell'origine del disegno della struttura. Le coordinate dell'origine devono essere espresse in WGS84 Web Mercator (`EPSG:3857`).|
|`lon`    |NUMERIC|    true|    Rappresentazione decimale della longitudine in gradi in corrispondenza dell'origine del disegno della struttura. Le coordinate dell'origine devono essere espresse in WGS84 Web Mercator (`EPSG:3857`). |
|`angle`|    NUMERIC|    true|   Angolo in senso orario, in gradi, tra il vero nord e l'asse verticale (Y) del disegno.   |

### `dwgLayers`

| Proprietà  | Type | Obbligatoria | Description |
|-----------|------|----------|-------------|
|`exterior`    |matrice di stringhe|    True|    Nomi dei livelli che definiscono il profilo di compilazione esterno.|
|`unit`|    matrice di stringhe|    True|    Nomi dei livelli che definiscono le unità.|
|`wall`|    matrice di stringhe    |false|    Nomi dei livelli che definiscono i muri.|
|`door`    |matrice di stringhe|    false   | Nomi dei livelli che definiscono le porte.|
|`unitLabel`    |matrice di stringhe|    false    |Nomi dei livelli che definiscono i nomi delle unità.|
|`zone` | matrice di stringhe    | false    | Nomi dei livelli che definiscono le zone.|
|`zoneLabel` | matrice di stringhe |     false |    Nomi dei livelli che definiscono i nomi delle zone.|

### `unitProperties`

L'oggetto `unitProperties` contiene una matrice JSON di proprietà delle unità.

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
|`unitName`    |string    |true    |Nome dell'unità da associare al record `unitProperty`. Questo record è valido solo quando `unitName` viene trovata una corrispondenza tra etichette nei `unitLabel` livelli. |
|`categoryName`|    string|    false    |Nome della categoria. Per un elenco completo di categorie, vedere [Categorie](https://aka.ms/pa-indoor-spacecategories). |
|`navigableBy`| matrice di stringhe |    false    |Indica i tipi di agenti mobili che possono attraversare l'unità. Questa proprietà informa le funzionalità wayfinding. I valori consentiti sono:,,, `pedestrian` `wheelchair` `machine` `bicycle` , `automobile` , `hiredAuto` , `bus` , `railcar` , `emergency` , `ferry` , `boat` e `disallowed` .|
|`routeThroughBehavior`|    string|    false    |Il comportamento del percorso attraverso l'unità. I calori consentiti sono `disallowed`, `allowed` e `preferred`. Il valore predefinito è `allowed`.|
|`occupants`    |matrice di oggetti directoryInfo |false    |Elenco di occupanti per l'unità. |
|`nameAlt`|    string|    false|    Nome alternativo dell'unità. |
|`nameSubtitle`|    string    |false|    Sottotitolo dell'unità. |
|`addressRoomNumber`|    string|    false|    Camera, unità, Apartment o numero di gruppo dell'unità.|
|`verticalPenetrationCategory`|    string|    false| Quando questa proprietà è definita, la funzionalità risultante è una penetrazione verticale (VRT) anziché un'unità. È possibile usare rotonde per passare ad altre funzionalità di VRT nei livelli superiori o inferiori. La penetrazione verticale è un nome di [categoria](https://aka.ms/pa-indoor-spacecategories) . Se questa proprietà è definita, la `categoryName` proprietà viene sottoposta a override con `verticalPenetrationCategory` . |
|`verticalPenetrationDirection`|    string|    false    |Se la proprietà `verticalPenetrationCategory` è definita, facoltativamente definire la direzione valida dello spostamento. I valori consentiti sono: `lowToHigh` ,, `highToLow` `both` e `closed` . Il valore predefinito è `both`.|
| `nonPublic` | bool | false | Indica se l'unità è aperta al pubblico. |
| `isRoutable` | bool | false | Quando questa proprietà è impostata su `false` , non è possibile passare all'unità. Il valore predefinito è `true`. |
| `isOpenArea` | bool | false | Consente all'agente di esplorazione di accedere all'unità senza la necessità di un'apertura collegata all'unità. Per impostazione predefinita, questo valore è impostato su `true` per le unità senza aperture e `false` per le unità con aperture. L'impostazione `isOpenArea` manuale `false` su su un'unità senza aperture genera un avviso. Ciò è dovuto al fatto che l'unità risultante non sarà raggiungibile da un agente navigante.|

### `zoneProperties`

L'oggetto `zoneProperties` contiene una matrice JSON di proprietà delle zone.

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
|zoneName        |string    |true    |Nome della zona da associare al record `zoneProperty`. Questo record è valido solo quando nel livello `zoneLabel` della zona è disponibile un'etichetta corrispondente `zoneName`.  |
|categoryName|    string|    false    |Nome della categoria. Per un elenco completo di categorie, vedere [Categorie](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    string|    false    |Nome alternativo della zona.  |
|zoneNameSubtitle|    string |    false    |Sottotitolo della zona. |
|zoneSetId|    string |    false    | Impostare ID per stabilire una relazione tra più zone in modo da consentirne la query o selezionarla come gruppo. Ad esempio, zone che si estendono su più livelli. |

### <a name="sample-drawing-package-manifest"></a>Manifesto del pacchetto di disegni di esempio

Di seguito è riportato un file manifesto di esempio per il pacchetto di disegno di esempio. Per scaricare l'intero pacchetto, vedere [esempio di pacchetto di disegno](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>File manifesto

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Quando il pacchetto di disegno soddisfa i requisiti, è possibile usare il [servizio di conversione mappe di Azure](/rest/api/maps/conversion) per convertire il pacchetto in un set di dati della mappa. Quindi, è possibile usare il set di dati per generare una mappa interna usando il modulo Maps indoor.

> [!div class="nextstepaction"]
>[Creator per piante di interni](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Esercitazione: Creare una pianta di interni di Creator](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Stile dinamico delle mappe interne](indoor-map-dynamic-styling.md)