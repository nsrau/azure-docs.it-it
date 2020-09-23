---
title: Configurare la conversione di modelli
description: Descrizione di tutti i parametri per la conversione dei modelli
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: dda2676f258705ed833068c966bcc57115434b0d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967234"
---
# <a name="configure-the-model-conversion"></a>Configurare la conversione di modelli

Questo capitolo documenta le opzioni per la conversione dei modelli.

## <a name="settings-file"></a>File di impostazioni

Se un file chiamato `<modelName>.ConversionSettings.json` viene trovato nel contenitore di input accanto al modello di input `<modelName>.<ext>` , verrà usato per fornire una configurazione aggiuntiva per il processo di conversione del modello.
Ad esempio, viene `box.ConversionSettings.json` usato durante la conversione di `box.gltf` .

Il contenuto del file dovrà soddisfare lo schema JSON seguente:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Un file di esempio `box.ConversionSettings.json` potrebbe essere:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parametri geometrici

* `scaling` - Questo parametro ridimensiona uniformemente un modello. È possibile usare scaling per ingrandire o ridurre le dimensioni di un modello, ad esempio per visualizzare un modello di edificio su un piano.
Il ridimensionamento è importante anche quando un modello è definito in unità diverse dai contatori, perché il motore di rendering prevede contatori.
Se un modello è definito in centimetri, ad esempio, applicando una scala 0,01 si otterrà il rendering del modello nelle dimensioni corrette.
Alcuni formati di dati di origine (ad esempio, FBX) includono un suggerimento di scala per le unità. In questo caso, la conversione ridimensionerà implicitamente il modello in unità metriche. Il ridimensionamento implicito definito dal formato di origine verrà applicato sopra il parametro scaling.
Il fattore di scala finale verrà applicato ai vertici della geometria e alle trasformazioni locali dei nodi del grafico della scena. Il ridimensionamento per la trasformazione dell'entità radice rimarrà invariato.

* `recenterToOrigin` - Indica che un modello dovrà essere convertito in modo che il relativo rettangolo di selezione sia centrato sull'origine.
Se un modello di origine viene spostato lontano dall'origine, i problemi di precisione a virgola mobile possono causare artefatti di rendering.
Il centramento del modello può essere utile in questa situazione.

* `opaqueMaterialDefaultSidedness` - Il motore di rendering presuppone che i materiali opachi siano a doppio lato.
Se il presupposto non è vero per un determinato modello, questo parametro deve essere impostato su "SingleSided". Per ulteriori informazioni, vedere [ :::no-loc text="single sided"::: rendering](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Sostituzioni dei materiali

* `material-override` - Questo parametro consente di [personalizzare l'elaborazione dei materiali durante la conversione](override-materials.md).

### <a name="material-de-duplication"></a>Deduplicazione dei materiali

* `deduplicateMaterials` - Questo parametro abilita o disabilita la deduplicazione automatica dei materiali che condividono le stesse proprietà e le stesse trame. La deduplicazione viene eseguita dopo l'elaborazione delle sostituzioni dei materiali. Questa opzione è abilitata per impostazione predefinita.

* Se anche dopo la deduplicazione di un modello sono presenti più di 65.535 materiali, il servizio tenterà di unire materiali con proprietà simili. Come ultima risorsa, tutti i materiali che superano il limite verranno sostituiti da un materiale di errore rosso.

![Image Mostra due cubi di 68.921 triangoli colorati.](media/mat-dedup.png?raw=true)

Due cubi di 68.921 triangoli colorati. Left: prima della deduplicazione con materiali colore 68.921. Right: dopo la deduplicazione con materiali colore 64.000. Il limite è di 65.535 materiali. (Vedere [limiti](../../reference/limits.md)).

### <a name="color-space-parameters"></a>Parametri di spazio colore

Il motore di rendering prevede che i valori di colore siano nello spazio lineare.
Se un modello è definito con lo spazio gamma, queste opzioni dovranno essere impostate su true.

* `gammaToLinearMaterial` - Converte i colori dei materiali dallo spazio gamma allo spazio lineare.
* `gammaToLinearVertex` -Converte :::no-loc text="vertex"::: i colori dallo spazio gamma allo spazio lineare

> [!NOTE]
> Per i file FBX, queste opzioni sono impostate su `true` per impostazione predefinita. Per tutti gli altri tipi di file, l'impostazione predefinita è `false`.

### <a name="scene-parameters"></a>Parametri della scena

* `sceneGraphMode` - Definisce la modalità di conversione del grafico della scena nel file di origine.
  * `dynamic` (impostazione predefinita): tutti gli oggetti nel file vengono esposti come [entità](../../concepts/entities.md) nell'API e possono essere trasformati in modo indipendente. La gerarchia dei nodi in fase di esecuzione è identica alla struttura nel file di origine.
  * `static`: Tutti gli oggetti vengono esposti nell'API ma non possono essere trasformati in modo indipendente.
  * `none`: il grafico della scena viene compresso in un unico oggetto.

Ogni modalità offre prestazioni diverse in fase di esecuzione. Nella modalità `dynamic`, il costo in termini di prestazioni presenta una scalabilità lineare rispetto al numero di [entità](../../concepts/entities.md) nel grafico, anche quando nessuna parte viene spostata. Usare la `dynamic` modalità solo quando è necessario spostare le parti singolarmente, ad esempio per un'animazione "vista esplosione".

Con la modalità `static` viene esportato l'intero grafico della scena, ma alle parti all'interno del grafico viene applicata una trasformazione costante in relazione alla parte radice. È comunque possibile, tuttavia, spostare, ruotare o ridimensionare il nodo radice dell'oggetto senza un costo significativo in termini di prestazioni. Le [query spaziali](../../overview/features/spatial-queries.md), inoltre, restituiranno singole parti e ogni parte potrà essere modificata tramite [sostituzioni dello stato](../../overview/features/override-hierarchical-state.md). Con questa modalità, il sovraccarico in fase di esecuzione per ogni singolo oggetto è trascurabile. È l'opzione ideale per scene di grandi dimensioni in cui è comunque necessario eseguire l'ispezione per oggetto, ma non modifiche di trasformazione per oggetto.

La modalità `none` comporta il sovraccarico minore in fase di esecuzione e tempi di caricamento leggermente migliori. In questa modalità, l'ispezione o la trasformazione di singoli oggetti non è possibile. I casi d'uso includono, ad esempio, i modelli di fotogrammetria, che non hanno un grafico della scena significativo.

> [!TIP]
> Molte applicazioni caricheranno più modelli. È consigliabile ottimizzare i parametri di conversione per ogni modello in base a come verrà usato. Se si vuole visualizzare il modello di un'auto per consentire all'utente di scomporlo e ispezionarlo in dettaglio, è necessario eseguire la conversione con la modalità `dynamic`. Se si vuole anche inserire l'auto nell'ambiente di uno show room, tuttavia, è possibile convertire il modello con il parametro `sceneGraphMode` impostato su `static` o anche su `none`.

### <a name="physics-parameters"></a>Parametri fisici

* `generateCollisionMesh` - Se è necessario il supporto delle [query spaziali](../../overview/features/spatial-queries.md) su un modello, questa opzione deve essere abilitata. Nel peggiore dei casi, la creazione di una mesh di collisione può raddoppiare il tempo di conversione. I modelli con mesh di collisione richiedono più tempo per il caricamento e, se si usa un grafico della scena `dynamic`, comportano anche un maggiore sovraccarico delle prestazioni in fase di esecuzione. Per prestazioni complessive ottimali, è consigliabile disabilitare questa opzione per tutti i modelli per cui non sono necessarie query spaziali.

### <a name="unlit-materials"></a>Materiali senza illuminazione

* `unlitMaterials` - Per impostazione predefinita, la conversione preferirà creare [materiali PBR](../../overview/features/pbr-materials.md). Questa opzione indica al convertitore di considerare invece tutti i materiali come [materiali a colori](../../overview/features/color-materials.md). Se i dati incorporano già l'illuminazione, come nel caso di modelli creati tramite fotogrammetria, questa opzione consente di applicare rapidamente la conversione corretta per tutti i materiali, senza dover [sostituire ogni materiale](override-materials.md) singolarmente.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Conversione da formati FBX precedenti con modello dei materiali Phong

* `fbxAssumeMetallic` - Le versioni precedenti del formato FBX definiscono i materiali con un modello dei materiali Phong. Il processo di conversione deve dedurre il mapping di questi materiali al [modello PBR](../../overview/features/pbr-materials.md) del renderer. Il funzionamento è in genere corretto, ma potrebbero presentarsi ambiguità se un materiale privo di trame ha valori speculari elevati e colore di albedo diverso da grigio. In questo caso, la conversione deve scegliere tra attribuire priorità ai valori speculari elevati, definendo un materiale metallico altamente riflettente in cui il colore di albedo si dissolve, oppure al colore di albedo, definendo un materiale simile a plastica colorata e lucida. Per impostazione predefinita, in caso di ambiguità il processo di conversione presuppone che valori altamente speculari implichino un materiale metallico. Questo parametro può essere impostato su `false` per passare al comportamento opposto.

### <a name="coordinate-system-overriding"></a>Sostituzione del sistema di coordinate

* `axis` - Per sostituire i vettori unitari del sistema di coordinate. I valori predefiniti sono `["+x", "+y", "+z"]`. In teoria, il formato FBX ha un'intestazione in cui vengono definiti questi vettori e la conversione usa tali informazioni per trasformare la scena. Il formato glTF definisce anche un sistema di coordinate fisso. Nella pratica, alcune risorse contengono informazioni non corrette nell'intestazione oppure sono state salvate con una diversa convenzione del sistema di coordinate. Questa opzione consente di sostituire il sistema di coordinate per compensare. Ad esempio, `"axis" : ["+x", "+z", "-y"]` scambierà l'asse Z e l'asse Y e manterrà la regola della mano del sistema di coordinate invertendo la direzione dell'asse Y.

### <a name="node-meta-data"></a>Meta dati del nodo

* `metadataKeys` : Consente di specificare le chiavi delle proprietà dei metadati del nodo che si desidera memorizzare nel risultato della conversione. È possibile specificare chiavi esatte o chiavi con caratteri jolly. Le chiavi con caratteri jolly sono nel formato "ABC *" e corrispondono a qualsiasi chiave che inizia con "ABC". I tipi di valore di metadati supportati sono `bool` ,, `int` `float` e `string` .

    Per i file GLTF questi dati provengono dall' [oggetto extra nei nodi](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras). Per i file FBX i dati provengono dai `Properties70` dati in `Model nodes` . Per ulteriori informazioni, vedere la documentazione dello strumento asset 3D.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: formato

È possibile modificare il :::no-loc text="vertex"::: formato per una mesh, per ottenere la precisione del risparmio di memoria. Un footprint della memoria inferiore consente di caricare modelli di maggiori dimensioni o di ottenere prestazioni superiori. A seconda dei dati, tuttavia, un formato errato può influire significativamente sulla qualità del rendering.

> [!CAUTION]
> La modifica del :::no-loc text="vertex"::: formato dovrebbe essere l'ultima risorsa quando i modelli non rientrano più nella memoria o quando si ottimizzano per ottenere le migliori prestazioni possibili. Le modifiche possono facilmente determinare artefatti di rendering, sia evidenti che difficili da individuare. A meno che non si sappia a cosa prestare attenzione, è consigliabile non modificare l'impostazione predefinita.

Sono consentite le modifiche seguenti:

* È possibile includere o escludere esplicitamente specifici flussi di dati.
* È possibile ridurre l'accuratezza dei flussi di dati per ridurre il footprint della memoria.

La sezione `vertex` seguente del file con estensione `.json` è facoltativa. Per ogni porzione non specificata in modo esplicito, il servizio di conversione userà l'impostazione predefinita.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Forzando l'impostazione di un componente su `NONE`, è certo che la mesh di output non includerà il rispettivo flusso.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Formati di componente per :::no-loc text="vertex"::: flusso

Per i rispettivi componenti sono consentiti i formati seguenti:

| Componente :::no-loc text="Vertex"::: | Formati supportati (grassetto = impostazione predefinita) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, NONE |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **NONE**|
|normale| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|tangent| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, NONE |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NONE |

#### <a name="supported-component-formats"></a>Formati dei componenti supportati

Di seguito sono riportati i footprint della memoria dei formati:

| Format | Descrizione | Byte per :::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|Precisione piena a virgola mobile, due componenti|8
|16_16_FLOAT|Mezza precisione a virgola mobile, due componenti|4
|32_32_32_FLOAT|Precisione piena a virgola mobile, tre componenti|12
|16_16_16_16_FLOAT|Mezza precisione a virgola mobile, quattro componenti|8
|8_8_8_8_UNSIGNED_NORMALIZED|Byte normalizzato sull'intervallo `[0; 1]`, quattro componenti|4
|8_8_8_8_SIGNED_NORMALIZED|Byte normalizzato sull'intervallo `[-1; 1]`, quattro componenti|4

#### <a name="best-practices-for-component-format-changes"></a>Procedure consigliate per le modifiche al formato dei componenti

* `position`: raramente è sufficiente un'accuratezza ridotta. **16_16_16_16_FLOAT** introduce artefatti di quantizzazione evidenti, anche per modelli di piccole dimensioni.
* `normal`, `tangent`, `binormal`: questi valori vengono in genere modificati insieme. A meno che non siano presenti artefatti di illuminazione evidenti derivanti dalla quantizzazione normale, non esiste alcun motivo per aumentare l'accuratezza. In alcuni casi, tuttavia, è possibile impostare questi componenti su **NONE**:
  * `normal`, `tangent` e `binormal` sono necessari solo quando almeno un materiale nel modello deve essere illuminato. In Rendering remoto di Azure ciò avviene quando nel modello viene usato in qualsiasi momento un [materiale PBR](../../overview/features/pbr-materials.md).
  * `tangent` e `binormal` sono necessari solo quando uno dei materiali illuminati usa una trama di mapping normale.
* `texcoord0` e `texcoord1`: le coordinate delle trame possono usare un'accuratezza ridotta (**16_16_FLOAT**) quando i relativi valori rimangono nell'intervallo `[0; 1]` e quando le trame gestite hanno una dimensione massima di 2048 x 2048 pixel. Se vengono superati questi limiti, la qualità del texture mapping risulta ridotta.

#### <a name="example"></a>Esempio

Si supponga di avere un modello di fotogrammetria in cui l'illuminazione è integrata nelle trame. Tutto ciò che serve per eseguire il rendering del modello sono le :::no-loc text="vertex"::: posizioni e le coordinate di trama.

Per impostazione predefinita, il convertitore deve presupporre che a un certo punto in un modello si vorranno usare materiali PBR, pertanto genererà automaticamente i dati `normal`, `tangent` e `binormal`. Di conseguenza, l'utilizzo della memoria per vertice sarà `position` (12 byte) + `texcoord0` (8 byte) + `normal` (4 byte) + `tangent` (4 byte) + `binormal` (4 byte) = 32 byte. I modelli più grandi di questo tipo possono avere con facilità molti milioni di :::no-loc text="vertices"::: modelli che possono richiedere più gigabyte di memoria. Quantità di dati così elevate influiranno sulle prestazioni e potrebbero esaurire la memoria.

Sapendo che non è mai necessaria un'illuminazione dinamica sul modello e che tutte le coordinate di trama sono `[0; 1]` comprese nell'intervallo, è possibile impostare `normal` , `tangent` e `binormal` su `NONE` e `texcoord0` su Half Precision ( `16_16_FLOAT` ), ottenendo solo 16 byte per :::no-loc text="vertex"::: . Il dimezzamento dei dati della mesh consente di caricare modelli di maggiori dimensioni e migliorare potenzialmente le prestazioni.

## <a name="memory-optimizations"></a>Ottimizzazioni della memoria

Il consumo di memoria del contenuto caricato può diventare un collo di bottiglia nel sistema di rendering. Se il payload di memoria diventa troppo grande, può compromettere le prestazioni di rendering o fare in modo che il modello non venga caricato completamente. Questo paragrafo illustra alcune importanti strategie per ridurre il footprint di memoria.

### <a name="instancing"></a>Instancing

La creazione di istanze è un concetto in cui le mesh vengono riutilizzate per le parti con trasformazioni spaziali distinte, anziché ogni parte che fa riferimento alla propria geometria univoca. La creazione di istanze ha un impatto significativo sul footprint di memoria.
Casi d'uso di esempio per la creazione di istanze sono le viti in un modello di motore o poltrone in un modello di architettura.

> [!NOTE]
> Le istanze possono migliorare significativamente il consumo di memoria (e quindi i tempi di caricamento), tuttavia i miglioramenti apportati al lato prestazioni di rendering non sono significativi.

Il servizio di conversione rispetta le istanze se le parti vengono contrassegnate di conseguenza nel file di origine. Tuttavia, la conversione non esegue un'analisi approfondita aggiuntiva dei dati mesh per identificare le parti riutilizzabili. Lo strumento di creazione del contenuto e la relativa pipeline di esportazione rappresentano quindi i criteri decisivi per la configurazione delle istanze appropriate.

Un modo semplice per verificare se le informazioni sulle istanze vengono mantenute durante la conversione consiste nell'esaminare le [statistiche di output](get-information.md#example-info-file), in particolare il `numMeshPartsInstanced` membro. Se il valore di `numMeshPartsInstanced` è maggiore di zero, indica che le mesh sono condivise tra le istanze.

#### <a name="example-instancing-setup-in-3ds-max"></a>Esempio: configurazione di istanze in 3ds Max

[Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max) presenta diverse modalità di clonazione di oggetti denominata **`Copy`** , **`Instance`** , e si **`Reference`** comportano in modo diverso rispetto alle istanze nel file esportato `.fbx` .

![Clonazione in 3ds Max](./media/3dsmax-clone-object.png)

* **`Copy`** : In questa modalità la mesh viene clonata, quindi non viene utilizzata alcuna istanza ( `numMeshPartsInstanced` = 0).
* **`Instance`** : I due oggetti condividono la stessa mesh, quindi viene usata l'istanza ( `numMeshPartsInstanced` = 1).
* **`Reference`** : I modificatori Distinct possono essere applicati alle geometrie, pertanto l'utilità di esportazione sceglie un approccio conservativo e non usa le istanze ( `numMeshPartsInstanced` = 0).


### <a name="depth-based-composition-mode"></a>Modalità di composizione basata sulla profondità

Se la memoria costituisce un problema, configurare il renderer con la [modalità di composizione basata sulla profondità](../../concepts/rendering-modes.md#depthbasedcomposition-mode). In questa modalità il payload GPU viene distribuito tra più GPU.

### <a name="decrease-vertex-size"></a>Riduci dimensioni vertice

Come illustrato nella sezione [procedure consigliate per le modifiche al formato dei componenti](configure-model-conversion.md#best-practices-for-component-format-changes) , la modifica del formato del vertice può ridurre il footprint di memoria. Questa opzione, tuttavia, deve essere l'ultima risorsa.

### <a name="texture-sizes"></a>Dimensioni trama

A seconda del tipo di scenario, la quantità di dati di trama può superare la memoria usata per i dati di rete. I modelli fotogrammetria sono candidati.
La configurazione della conversione non fornisce un modo per ridimensionare automaticamente le trame. Se necessario, il ridimensionamento della trama deve essere eseguito come passaggio di pre-elaborazione lato client. Il passaggio di conversione tuttavia sceglie un [formato di compressione della trama](https://docs.microsoft.com/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)appropriato:

* `BC1` per le trame colore opaco
* `BC7` per le trame colore di origine con canale alfa

Poiché `BC7` il formato ha due volte il footprint di memoria rispetto a `BC1` , è importante assicurarsi che le trame di input non forniscano inutilmente un canale alfa.

## <a name="typical-use-cases"></a>Casi d'uso tipici

Trovare le impostazioni di importazione valide per uno specifico caso d'uso può essere un processo tedioso. D'altra parte, le impostazioni di conversione possono avere un impatto significativo sulle prestazioni in fase di esecuzione.

Esistono alcune classi di casi d'uso idonee per specifiche ottimizzazioni. Di seguito sono riportati alcuni esempi.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Caso d'uso: visualizzazione di architetture/mappe esterne di grandi dimensioni

* Questi tipi di scene tendono a essere statici e non necessitano quindi di parti mobili. Di conseguenza, è possibile impostare `sceneGraphMode` su `static` o anche su `none`, migliorando così le prestazioni in fase di esecuzione. Con la modalità `static`, è comunque possibile spostare, ruotare e ridimensionare il nodo radice della scena, ad esempio per passare dinamicamente dalla scala 1:1 (per la visualizzazione in prima persona) a una visualizzazione su piano.

* Quando si devono spostare le parti, in genere è necessario anche supportare operazioni di ray casting o altre [query spaziali](../../overview/features/spatial-queries.md) per poter in primo luogo selezionare tali parti. Se invece non si intende spostare le parti, è molto probabile che non sia necessario includerle nelle query spaziali e che sia quindi possibile disattivare il flag `generateCollisionMesh`. Questa opzione ha un impatto significativo sui tempi di conversione e di caricamento, nonché sui costi di aggiornamento per fotogramma in fase di esecuzione.

* Se l'applicazione non usa [piani di taglio](../../overview/features/cut-planes.md), il flag `opaqueMaterialDefaultSidedness` dovrebbe essere disattivato. Il miglioramento delle prestazioni è in genere del 20-30%. Sarà comunque possibile usare piani di taglio, ma, in modo controintuitivo, non saranno presenti facce posteriori quando si esaminano le parti interne degli oggetti. Per ulteriori informazioni, vedere [ :::no-loc text="single sided"::: rendering](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Caso d'uso: modelli di fotogrammetria

In caso di rendering di modelli di fotogrammetria, in genere non è necessario un grafico della scena ed è pertanto possibile impostare `sceneGraphMode` su `none`. Dato che questi modelli raramente contengono inizialmente un grafico della scena complesso, l'impatto di questa opzione dovrebbe tuttavia essere irrilevante.

Non è necessaria alcuna illuminazione dinamica perché l'illuminazione è già integrata nelle trame. Di conseguenza:

* Impostare il flag `unlitMaterials` su `true` per trasformare tutti i materiali in [materiali a colori](../../overview/features/color-materials.md) senza illuminazione.
* Rimuovere i dati non necessari dal formato dei vertici. Vedere l'[esempio](#example) precedente.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Caso d'uso: visualizzazione di macchine compatte e così via

In questi casi d'uso, i modelli hanno spesso un livello di dettaglio elevato in un volume ridotto. Il renderer è altamente ottimizzato per gestire in modo efficiente questi casi. La maggior parte delle ottimizzazioni indicate nei casi d'uso precedenti, tuttavia, non è applicabile:

* Le singole parti dovranno essere selezionabili e mobili, quindi il parametro `sceneGraphMode` deve rimanere impostato su `dynamic`.
* Le operazioni di ray casting sono in genere parte integrante dell'applicazione, pertanto è necessario generare mesh di collisione.
* L'aspetto dei piani di taglio è migliore con il flag `opaqueMaterialDefaultSidedness` abilitato.

## <a name="deprecated-features"></a>Funzionalità deprecate

La fornitura di impostazioni tramite il nome file non specifico del modello `conversionSettings.json` è ancora supportata ma deprecata.
Usare invece il nome file specifico del modello `<modelName>.ConversionSettings.json` .

## <a name="next-steps"></a>Passaggi successivi

* [Conversione di modelli](model-conversion.md)
* [Materiali a colori](../../overview/features/color-materials.md)
* [Materiali PBR](../../overview/features/pbr-materials.md)
* [Sostituire i materiali durante la conversione di modelli](override-materials.md)
