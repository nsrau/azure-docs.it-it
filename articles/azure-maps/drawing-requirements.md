---
title: Requisiti del pacchetto di disegni in Creator di Mappe di Azure
description: Informazioni sui requisiti del pacchetto di disegni per convertire i file della progettazione di strutture in dati di mappa usando il servizio di conversione di Mappe di Azure
author: anastasia-ms
ms.author: v-stharr
ms.date: 5/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: c0c81f529dfc959916ff7c102b2b903a808b9672
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681896"
---
# <a name="drawing-package-requirements"></a>Requisiti del pacchetto di disegni

Il [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) consente di convertire i pacchetti di disegni caricati in dati di mappe. Questo articolo descrive i requisiti del pacchetto di disegni per l'API di conversione. Per altre informazioni, è possibile scaricare il [pacchetto di disegni](https://github.com/Azure-Samples/am-creator-indoor-data-examples) di esempio.

## <a name="prerequisites"></a>Prerequisiti

Il pacchetto di disegni include i disegni salvati in formato DWG, ovvero il formato di file nativo per il software AutoCAD® di Autodesk, un [marchio di Autodesk, Inc](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12).

È possibile scegliere qualsiasi software CAD per produrre i disegni del pacchetto di disegni.  

Il [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) converte il pacchetto di disegni in dati di mappe.  Il servizio di conversione è stato sviluppato e testato usando il formato di file DWG di AutoCAD. `AC1032` è la versione interna del formato per i file DWG. È consigliabile selezionare `AC1032` per la versione interna del formato di file DWG.  

Glossario dei termini usati in questo documento.

| Termine  | Definizione |
|:-------|:------------|
| Livello | Un livello DWG di AutoCAD.|
| Level | Un'area di un edificio a un'elevazione impostata, ad esempio il piano. |
| Xref  |Un file in formato DWG di AutoCAD (con estensione dwg) collegato al disegno primario come riferimento esterno.  |
| Funzionalità | Un oggetto che combina una figura geometrica con informazioni aggiuntive di metadati. |
| Classi di caratteristiche | Un progetto comune per le caratteristiche. Ad esempio, un'unità è una classe di caratteristiche e un ufficio è una caratteristica. |

## <a name="drawing-package-structure"></a>Struttura del pacchetto di disegni

Un pacchetto di disegni è un archivio ZIP che contiene i file seguenti:

* File DWG in formato DWG di AutoCAD.
* Un file _manifest.json_ per una singola struttura.

I file DWG possono essere organizzati in qualsiasi modo all'interno della cartella, ma il file manifesto deve risiedere nella directory radice della cartella. La cartella deve essere compressa in un singolo file di archivio con estensione zip. Le sezioni successive illustrano in dettaglio i requisiti per i file DWG, il file manifesto e il contenuto di questi file.  

## <a name="dwg-files-requirements"></a>Requisiti dei file DWG

È necessario un singolo file DWG per ogni livello della struttura. I dati del livello devono essere contenuti in un singolo file DWG. Qualsiasi riferimento esterno (_xref_) deve essere associato al disegno padre. Inoltre, ogni file DWG:

* Deve definire i livelli _Exterior_ e _Unit_. Facoltativamente, può definire i livelli seguenti: _Wall_, _Door_, _UnitLabel_, _Zone_ e _ZoneLabel_.
* Non deve contenere caratteristiche di più livelli.
* Non deve contenere caratteristiche di più strutture.

Il [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) può estrarre le classi di caratteristiche seguenti da un file DWG:

* Levels
* Unità
* Zone
* Aperture
* Pareti
* Penetrazioni verticali

Tutti i processi di conversione generano un set minimo di categorie predefinite: room, structure.wall, opening.door, zone e facility. Per ogni nome di categoria a cui gli oggetti fanno riferimento sono disponibili categorie aggiuntive.  

Un livello DWG deve contenere le caratteristiche di una singola classe. Le classi non devono condividere un livello. Ad esempio, le unità e le pareti non possono condividere un livello.

I livelli DWG devono inoltre rispettare i criteri seguenti:

* Le origini dei disegni per tutti i file DWG devono essere allineate alla stessa latitudine e alla stessa longitudine.
* Ogni livello deve avere lo stesso orientamento degli altri.
* I poligoni che si auto-intersecano verranno corretti automaticamente e il [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) genererà un avviso. È consigliabile controllare manualmente i risultati corretti perché potrebbero non corrispondere a quelli previsti.

Tutte le entità dei livelli devono essere di uno dei tipi seguenti: linea, polilinea, poligono, arco circolare, cerchio, testo (riga singola). Tutti gli altri tipi di entità verranno ignorati.

La tabella seguente illustra i tipi di entità supportati e le caratteristiche supportate per ogni livello. Se un livello contiene tipi di entità non supportati, il [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) li ignorerà.  

| Livello | Tipi di entità | Funzionalità |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Poligono, polilinea (chiusa), cerchio | Levels
| [Unità](#unit-layer) |  Poligono, polilinea (chiusa), cerchio | Penetrazioni verticali, unità
| [Wall](#wall-layer)  | Poligono, polilinea (chiusa), cerchio | Non applicabile. Per altre informazioni, vedere il [livello Wall](#wall-layer).
| [Door](#door-layer) | Poligono, polilinea, linea, arco circolare, cerchio | Aperture
| [Zona](#zone-layer) | Poligono, polilinea (chiusa), cerchio | Zona
| [UnitLabel](#unitlabel-layer) | Testo (riga singola) | Non applicabile. Questo livello può aggiungere proprietà solo alle caratteristiche di unità del livello Unit. Per altre informazioni, vedere il [livello UnitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Testo (riga singola) | Non applicabile. Questo livello può aggiungere proprietà solo alle caratteristiche di zona del livello ZonesLayer. Per altre informazioni, vedere il [livello ZoneLabel](#zonelabel-layer)

Le sezioni successive illustrano in dettaglio i requisiti per ogni livello.

### <a name="exterior-layer"></a>Livello Exterior

Il file DWG per ogni livello deve contenere un livello per definirne il perimetro. Questo livello è definito Exterior, ossia esterni. Se ad esempio una struttura contiene due livelli, dovrà avere due file DWG, con un livello di esterni per ognuno.

Indipendentemente dal numero di disegni di entità inclusi nel livello esterni, il [set di dati della struttura risultante](tutorial-creator-indoor-maps.md#create-a-feature-stateset) conterrà solo **una** caratteristica di livello per ogni file DWG. Inoltre:

* Gli esterni devono essere disegnati come poligono, polilinea (chiusa), cerchio.

* Gli esterni possono sovrapporsi, ma verranno risolti in un'unica figura geometrica.

Se il livello contiene più polilinee sovrapposte, le polilinee verranno risolte in una singola caratteristica di livello. In alternativa, se il livello contiene più polilinee non sovrapposte, la caratteristica di livello risultante avrà una rappresentazione multipoligonale.

Un esempio di livello esterni può essere considerato il livello OUTLINE del [pacchetto di disegni di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Livello Unit

Il file DWG per ogni livello deve definire un livello contenente Unit, ovvero unità.  Le unità sono spazi esplorabili nell'edificio, ad esempio uffici, corridoi, scale e ascensori. Il livello unità dovrà rispettare i requisiti seguenti:

* Le unità devono essere disegnate come poligono, polilinea (chiusa), cerchio.
* Le unità devono rientrare nei limiti del perimetro esterno della struttura.
* Le unità non devono sovrapporsi parzialmente.
* Le unità non devono contenere figure geometriche che si auto-intersecano.

 Assegnare un nome a un'unità creando un oggetto testo nel livello _unitLabel_, quindi inserirlo entro i limiti dell'unità. Per altre informazioni, vedere il [livello UnitLabel](#unitlabel-layer).

Un esempio di livello unità può essere considerato il livello UNITS del [pacchetto di disegni di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Livello Wall

Il file DWG per ogni livello può contenere un livello Wall, ovvero parete, che definisce le estensioni fisiche di pareti, colonne e altre strutture dell'edificio.

* Le pareti devono essere disegnate come poligono, polilinea (chiusa), cerchio.
* I livelli di pareti possono contenere solo figure geometriche interpretate come strutture dell'edificio.

Un esempio di livello parete può essere considerato il livello WALLS del [pacchetto di disegni di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Livello Door

È possibile includere un livello DWG contenente Door, ovvero porte. Ogni porta deve sovrapporsi al bordo di un'unità del livello Unit.

Le aperture delle porte in un set di dati di Mappe di Azure sono rappresentate da un segmento di linea singola che si sovrappone a più limiti di unità. Per convertire le figure geometriche del livello porte in caratteristiche di apertura in un set di dati, seguire questa procedura.

![Procedura per generare le aperture](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Livello Zone

Il file DWG per ogni livello può contenere un livello Zone che definisce le estensioni fisiche delle zone. Una zona può essere uno spazio vuoto interno o un cortile.

* Le zone devono essere disegnate come poligono, polilinea (chiusa), cerchio.
* Le zone possono sovrapporsi.
* Le zone possono essere interne o esterne al perimetro esterno della struttura.

Assegnare un nome a una zona creando un oggetto testo nel livello _zoneLabel_ e inserendolo entro i limiti della zona. Per altre informazioni, vedere il [livello ZoneLabel](#zonelabel-layer).

Un esempio di livello zona può essere considerato il livello ZONES del [pacchetto di disegni di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>Livello UnitLabel

Il file DWG per ogni livello può contenere un livello UnitLabel, ovvero etichetta di unità. Il livello etichette di unità aggiunge una proprietà name alle unità estratte dal livello Unit. Per le unità con una proprietà name possono essere specificati dettagli aggiuntivi nel file manifesto.

* Le etichette di unità devono essere entità di testo a riga singola.
* Le etichette di unità devono rientrare nei limiti della rispettiva unità.
* Le unità non devono contenere più entità di testo nel livello etichette di unità.

Un esempio di livello UnitLabel può essere considerato il livello UNITLABELS del [pacchetto di disegni di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>Livello ZoneLabel

Il file DWG per ogni livello può contenere un livello ZoneLabel, ovvero etichetta di zona. Questo livello aggiunge una proprietà name alle zone estratte dal livello Zone. Per le zone con una proprietà name possono essere specificati dettagli aggiuntivi nel file manifesto.

* Le etichette di zone essere entità di testo a riga singola.
* Le etichette di zone devono rientrare nei limiti della rispettiva zona.
* Le zone non devono contenere più entità di testo nel livello etichette di zone.

Un esempio di livello Zonelabel può essere considerato il livello ZONELABELS del [pacchetto di disegni di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Requisiti dei file manifesto

La cartella ZIP deve contenere un file manifesto al livello radice della directory, che deve essere denominato **manifest.json**. Questo file descrive i file DWG per consentire al [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) di analizzarne il contenuto. Verranno inseriti solo i file identificati dal manifesto. I file inclusi nella cartella ZIP ma che non sono correttamente elencati nel manifesto verranno ignorati.

I percorsi dei file, nell'oggetto **buildingLevels** del file manifesto, devono essere relativi alla radice della cartella ZIP. Il nome del file DWG deve corrispondere esattamente al nome del livello della struttura. Ad esempio, un file DWG per il livello "seminterrato" si chiamerà "seminterrato.dwg". Un file DWG per il livello 2 sarà denominato "livello_2.dwg". Se il nome del livello include uno spazio, usare un carattere di sottolineatura. 

Anche se sono previsti requisiti per l'uso degli oggetti del manifesto, non tutti gli oggetti sono obbligatori. La tabella seguente mostra gli oggetti obbligatori e facoltativi per la versione 1.1 del [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion).

| Oggetto | Obbligatoria | Descrizione |
| :----- | :------- | :------- |
| directoryInfo | true | Descrive le informazioni geografiche e di contatto della struttura. Può anche essere usato per descrivere informazioni geografiche e di contatto di un occupante. |
| buildingLevels | true | Specifica i livelli degli edifici e i file che contengono la progettazione dei livelli. |
| georeference | true | Contiene informazioni geografiche numeriche per il disegno della struttura. |
| dwgLayers | true | Elenca i nomi dei livelli e ogni livello elenca i nomi delle rispettive caratteristiche. |
| unitProperties | false | Può essere usato per inserire metadati aggiuntivi per le caratteristiche delle unità. |
| zoneProperties | false | Può essere usato per inserire metadati aggiuntivi per le caratteristiche delle zone. |

Le sezioni successive illustrano in dettaglio i requisiti per ogni oggetto.

### <a name="directoryinfo"></a>directoryInfo

| Proprietà  | type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
| name      | string/int | true   |  Nome dell'edificio. |
| streetAddress|    string/int |    false    | Indirizzo dell'edificio. |
|unit     | string/int    |  false    |  Unità dell'edificio. |
| locality |    string/int |    false |    Nome di un'area, un quartiere o un'area geografica. Ad esempio, "Overlake" o "Central District". La località non fa parte dell'indirizzo postale. |
| adminDivisions |    Matrice di stringhe JSON |    false     | Una matrice contenente le designazioni degli indirizzi (paese, provincia, città) o (paese, regione, città metropolitana, provincia). Usare i codici paese ISO 3166 e i codici stato/territorio ISO 3166-2. |
| postalCode |    string/int    | false    | Il codice di smistamento postale. |
| hoursOfOperation |    string |     false | Conforme al formato degli orari di apertura [OSM Opening Hours](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification). |
| phone    | string/int |    false |    Numero di telefono associato all'edificio. Deve includere il codice paese. |
| sito Web    | string |    false    | Sito Web associato all'edificio. Può iniziare con HTTP o HTTPS. |
| nonPublic |    bool    | false | Flag che specifica se l'edificio è aperto al pubblico. |
| anchorLatitude | NUMERIC |    false | Latitudine di un ancoraggio di struttura (puntina da disegno). |
| anchorLongitude | NUMERIC |    false | Longitudine di un ancoraggio di struttura (puntina da disegno). |
| anchorHeightAboveSeaLevel  | NUMERIC | false | Altezza del pianterreno della struttura sul livello del mare, in metri. |
| defaultLevelVerticalExtent | NUMERIC | false | Altezza predefinita (spessore) di un livello di questa struttura da usare quando la proprietà `verticalExtent` di un livello non è definita. |

### <a name="buildinglevels"></a>buildingLevels

L'oggetto `buildingLevels` contiene una matrice JSON di livelli di edifici.

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
|levelName    |string/int    |true |    Nome descrittivo del livello. Ad esempio: primo piano, atrio, parcheggio blu, seminterrato e così via.|
|ordinal | integer |    true | Viene usato per determinare l'ordine verticale dei livelli. Ogni struttura deve avere un livello con ordinal 0. |
|heightAboveFacilityAnchor | NUMERIC |    false |    Altezza del livello rispetto al pianoterra, in metri. |
| verticalExtent | NUMERIC | false | Altezza da pavimento a soffitto (spessore) del livello, in metri. |
|filename |    string/int |    true |    Percorso del file system del disegno CAD per un livello dell'edificio. Deve essere relativo alla radice del file ZIP dell'edificio. |

### <a name="georeference"></a>georeference

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
|lat    | NUMERIC |    true |    Rappresentazione decimale della latitudine in gradi in corrispondenza dell'origine del disegno della struttura. Le coordinate dell'origine devono essere espresse in WGS84 Web Mercator (`EPSG:3857`).|
|lon    |NUMERIC|    true|    Rappresentazione decimale della longitudine in gradi in corrispondenza dell'origine del disegno della struttura. Le coordinate dell'origine devono essere espresse in WGS84 Web Mercator (`EPSG:3857`). |
|angle|    NUMERIC|    true|   L'angolo in senso orario, in gradi, tra il nord geografico e l'asse verticale (Y) del disegno.   |

### <a name="dwglayers"></a>dwgLayers

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
|exterior    |Matrice di string/int|    true|    Nomi dei livelli che definiscono il profilo della facciata dell'edificio.|
|unit|    Matrice di string/int|    true|    Nomi dei livelli che definiscono le unità.|
|wall|    Matrice di string/int    |false|    Nomi dei livelli che definiscono le pareti.|
|door    |Matrice di string/int|    false   | Nomi dei livelli che definiscono le porte.|
|unitLabel    |Matrice di string/int|    false    |Nomi dei livelli che definiscono i nomi delle unità.|
|zona | Matrice di string/int    | false    | Nomi dei livelli che definiscono le zone.|
|zoneLabel | Matrice di string/int |     false |    Nomi dei livelli che definiscono i nomi delle zone.|

### <a name="unitproperties"></a>unitProperties

L'oggetto `unitProperties` contiene una matrice JSON di proprietà delle unità.

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
|unitName    |string/int    |true    |Nome dell'unità da associare al record `unitProperty`. Questo record è valido solo quando nei livelli `unitLabel` è disponibile un'etichetta corrispondente `unitName`. |
|categoryName|    string/int|    false    |Nome della categoria. Per un elenco completo di categorie, vedere [Categorie](https://aka.ms/pa-indoor-spacecategories). |
|navigableBy| Matrice di stringhe |    false    |Indica i tipi di agenti mobili che possono attraversare l'unità. Ad esempio, "pedone". Questa proprietà informa le funzionalità di orientamento.  I valori consentiti sono `pedestrian`, `wheelchair`, `machine`, `bicycle`, `automobile`, `hiredAuto`, `bus`, `railcar`, `emergency`, `ferry`, `boat` e `disallowed`.|
|routeThroughBehavior|    string|    false    |Il comportamento del percorso attraverso l'unità. I calori consentiti sono `disallowed`, `allowed` e `preferred`. Il valore predefinito è `allowed`.|
|occupants    |Matrice di oggetti directoryInfo |false    |Elenco di occupanti per l'unità. |
|nameAlt|    string/int|    false|    Nome alternativo dell'unità. |
|nameSubtitle|    string/int    |false|    Sottotitolo dell'unità. |
|addressRoomNumber|    string/int|    false|    Numero di camera/unità/appartamento/suite dell'unità.|
|verticalPenetrationCategory|    string/int|    false| Quando questa proprietà è definita, la caratteristica risultante sarà una penetrazione verticale invece di un'unità. Le penetrazioni verticali possono essere usate per passare ad altre penetrazioni verticali nei livelli al di sopra o al di sotto. La penetrazione verticale è un nome di [categoria](https://aka.ms/pa-indoor-spacecategories). Se questa proprietà è definita, la proprietà categoryName viene sostituita da verticalPenetrationCategory. |
|verticalPenetrationDirection|    string|    false    |Se la proprietà `verticalPenetrationCategory` è definita, facoltativamente definire la direzione valida dello spostamento. I valori consentiti sono `lowToHigh`, `highToLow`, `both` e `closed`. Il valore predefinito è `both`.|
| nonPublic | bool | false | Indica se l'unità è aperta al pubblico. |
| isRoutable | bool | false | Se è impostata su `false`, non è possibile entrare nell'unità né attraversarla. Il valore predefinito è `true`. |
| isOpenArea | bool | false | Consente l'accesso dell'agente mobile all'unità senza la necessità di un'apertura. Per impostazione predefinita, questo valore è impostato su `true`, a meno che l'unità non abbia un'apertura. |

### <a name="the-zoneproperties-object"></a>Oggetto zoneProperties

L'oggetto `zoneProperties` contiene una matrice JSON di proprietà delle zone.

| Proprietà  | Type | Obbligatoria | Descrizione |
|-----------|------|----------|-------------|
|zoneName        |string/int    |true    |Nome della zona da associare al record `zoneProperty`. Questo record è valido solo quando nel livello `zoneLabel` della zona è disponibile un'etichetta corrispondente `zoneName`.  |
|categoryName|    string/int|    false    |Nome della categoria. Per un elenco completo di categorie, vedere [Categorie](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    string/int|    false    |Nome alternativo della zona.  |
|zoneNameSubtitle|    string/int |    false    |Sottotitolo della zona. |

### <a name="sample-drawing-package-manifest"></a>Manifesto del pacchetto di disegni di esempio

Di seguito è riportato un file manifesto di esempio per il pacchetto di disegni di esempio. Per scaricare l'intero pacchetto, fare clic su [esempio di pacchetto di disegni](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>File manifesto

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddresss": "Contoso Way", 
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
            "nonWheelchairAccessible": false, 
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

Quando il pacchetto di disegni soddisfa i requisiti, è possibile usare il [servizio di conversione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) per convertirlo in un set di dati di mappa. È quindi possibile usare il set di dati per generare una pianta di interni usando il modulo corrispondente. Per altre informazioni sull'uso del modulo di piante di interni, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
>[Creator per piante di interni](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Esercitazione: Creare una pianta di interni di Creator](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Stili dinamici per le piante di interni](indoor-map-dynamic-styling.md)
