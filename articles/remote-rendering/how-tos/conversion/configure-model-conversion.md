---
title: Configurare la conversione del modello
description: Descrizione di tutti i parametri di conversione del modello
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681571"
---
# <a name="configure-the-model-conversion"></a>Configurare la conversione del modello

In questo capitolo vengono documentate le opzioni per la conversione del modello.

## <a name="settings-file"></a>File di impostazioni

Se un `ConversionSettings.json` file chiamato viene trovato nel contenitore di input accanto al modello di input, viene usato per fornire una configurazione aggiuntiva per il processo di conversione del modello.

Il contenuto del file deve soddisfare il seguente schema json:

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
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

Un `ConversionSettings.json` file di esempio potrebbe essere:An example file might be:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parametri della geometria

* `scaling`- Questo parametro ridimensiona un modello in modo uniforme. Il ridimensionamento può essere utilizzato per aumentare o ridurre un modello, ad esempio per visualizzare un modello di edificio su un piano di tabella. Poiché il motore di rendering prevede lunghezze da specificare in metri, un altro utilizzo importante di questo parametro si verifica quando un modello è definito in unità diverse. Ad esempio, se un modello è definito in centimetri, l'applicazione di una scala pari a 0,01 deve eseguire il rendering del modello con le dimensioni corrette.
Alcuni formati di dati di origine (ad esempio .fbx) forniscono un hint di ridimensionamento dell'unità, nel qual caso la conversione scala implicitamente il modello in unità di misuramento. Il ridimensionamento implicito fornito dal formato di origine verrà applicato sopra il parametro di ridimensionamento.
Il fattore di scala finale viene applicato ai vertici della geometria e alle trasformazioni locali dei nodi del grafico della scena. Il ridimensionamento per la trasformazione dell'entità radice rimane invariato.

* `recenterToOrigin`- Indica che un modello deve essere convertito in modo che il relativo riquadro di delimitazione sia centrato sull'origine.
Il centramento è importante se il modello di origine è spostato lontano dall'origine, poiché in tal caso problemi di precisione a virgola mobile possono causare artefatti di rendering.

* `opaqueMaterialDefaultSidedness`- Il motore di rendering presuppone che i materiali opachi siano fronte/retro.
Se questo non è il comportamento previsto, questo parametro deve essere impostato su "SingleSided". Per ulteriori informazioni, consultate [Rendering su lato singolo](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Sostituzioni di materiale

* `material-override`- Questo parametro consente di personalizzare la lavorazione dei materiali durante la [conversione.](override-materials.md)

### <a name="color-space-parameters"></a>Parametri dello spazio colore

Il motore di rendering prevede che i valori di colore si trovarano nello spazio lineare.
Se un modello è definito utilizzando lo spazio gamma, queste opzioni devono essere impostate su true.

* `gammaToLinearMaterial`- Convertire i colori del materiale dallo spazio gamma allo spazio lineare
* `gammaToLinearVertex`- Convertire i colori dei vertici dallo spazio gamma allo spazio lineare

> [!NOTE]
> Per i file FBX `true` queste impostazioni sono impostate su per impostazione predefinita. Per tutti gli altri tipi `false`di file, il valore predefinito è .

### <a name="scene-parameters"></a>Parametri della scena

* `sceneGraphMode`- Definisce la modalità di conversione del grafico della scena nel file di origine:
  * `dynamic`(impostazione predefinita): tutti gli oggetti nel file vengono esposti come [entità](../../concepts/entities.md) nell'API e possono essere trasformati in modo indipendente. La gerarchia dei nodi in fase di esecuzione è identica alla struttura nel file di origine.
  * `static`: tutti gli oggetti vengono esposti nell'API ma non possono essere trasformati in modo indipendente.
  * `none`: il grafico della scena viene compresso in un unico oggetto.

Ogni modalità ha prestazioni di runtime diverse. In `dynamic` modalità, il costo delle prestazioni viene scalato in modo lineare con il numero di [entità](../../concepts/entities.md) nel grafico, anche quando non viene spostata alcuna parte. Deve essere utilizzato solo quando lo spostamento di parti singolarmente è necessario per l'applicazione, ad esempio per un'animazione di "vista esplosione".

La `static` modalità esporta il grafico completo della scena, ma le parti all'interno di questo grafico hanno una trasformazione costante rispetto alla sua parte radice. Il nodo radice dell'oggetto, tuttavia, può comunque essere spostato, ruotato o ridimensionato senza costi significativi delle prestazioni. Inoltre, [le query spaziali](../../overview/features/spatial-queries.md) restituiranno singole parti e ogni parte può essere modificata tramite [sostituzioni di stato.](../../overview/features/override-hierarchical-state.md) Con questa modalità, l'overhead di runtime per oggetto è trascurabile. È ideale per scene di grandi dimensioni in cui è ancora necessaria l'ispezione per oggetto, ma non le modifiche di trasformazione per oggetto.

La `none` modalità ha il minor sovraccarico di runtime e anche tempi di caricamento leggermente migliori. L'ispezione o la trasformazione di singoli oggetti non è possibile in questa modalità. I casi d'uso sono, ad esempio, modelli di fotogrammetria che non hanno un grafico di scena significativo in primo luogo.

> [!TIP]
> Molte applicazioni caricheranno più modelli. È consigliabile ottimizzare i parametri di conversione per ogni modello in base a come verrà utilizzato. Ad esempio, se si desidera visualizzare il modello di un'auto che l'utente può `dynamic` smontare e ispezionare in dettaglio, è necessario convertirlo con la modalità. Tuttavia, se si desidera inoltre posizionare l'auto in un `sceneGraphMode` ambiente `static` show `none`room, tale modello può essere convertito con impostato su o anche .

### <a name="physics-parameters"></a>Parametri fisici

* `generateCollisionMesh`- Se è necessario il supporto per [le query spaziali](../../overview/features/spatial-queries.md) su un modello, questa opzione deve essere abilitata.- If you need support for spatial queries on a model, this option has to be enabled. Nel peggiore dei casi, la creazione di una mesh di collisione può raddoppiare il tempo di conversione. I modelli con dimensioni di collisione richiedono `dynamic` più tempo per il caricamento e quando si usa un grafico di scena, hanno anche un sovraccarico delle prestazioni di runtime più elevato. Per ottenere prestazioni ottimali complessive, è consigliabile disabilitare questa opzione in tutti i modelli in cui non sono necessarie query spaziali.

### <a name="unlit-materials"></a>Materiali non riapplicati

* `unlitMaterials`- Per impostazione predefinita la conversione preferirà creare [materiali PBR](../../overview/features/pbr-materials.md). Questa opzione indica al convertitore di trattare tutti i materiali come [materiali di colore.](../../overview/features/color-materials.md) Se si dispone di dati che incorporano già l'illuminazione, ad esempio i modelli creati tramite fotogrammetria, questa opzione consente di applicare rapidamente la conversione corretta per tutti i materiali, senza la necessità di [sostituire ogni singolo materiale.](override-materials.md)

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Conversione da vecchi formati FBX, con un modello di materiale Phong

* `fbxAssumeMetallic`- Le versioni precedenti del formato FBX definiscono i loro materiali utilizzando un modello di materiale Phong. Il processo di conversione deve dedurre il modo in cui questi materiali vengono mappati al [modello PBR](../../overview/features/pbr-materials.md)del renderer. Di solito questo funziona bene, ma un'ambiguità può sorgere quando un materiale non ha texture, alti valori speculari e un colore albedo non grigio. In questa circostanza, la conversione deve scegliere tra dare priorità agli alti valori speculari, definendo un materiale metallico altamente riflettente in cui il colore albedo si dissolve, o dando priorità al colore albedo, definendo qualcosa come una plastica colorata brillante. Per impostazione predefinita, il processo di conversione presuppone che valori altamente speculari implichino un materiale metallico nei casi in cui si applica l'ambiguità. Questo parametro può `false` essere impostato su per passare al contrario.

### <a name="coordinate-system-overriding"></a>Sostituzione del sistema di coordinate

* `axis`- Per sostituire i vettori di unità del sistema di coordinate. I valori `["+x", "+y", "+z"]`predefiniti sono . In teoria, il formato FBX ha un'intestazione in cui tali vettori sono definiti e la conversione utilizza tali informazioni per trasformare la scena. Il formato glTF definisce anche un sistema di coordinate fisse. In pratica, alcune risorse hanno informazioni non corrette nell'intestazione o sono state salvate con una convenzione di sistema di coordinate diversa. Questa opzione consente di sostituire il sistema di coordinate per compensare. Ad esempio: `"axis" : ["+x", "+z", "-y"]` scambierà l'asse z e l'asse Y e manterrerà la mano del sistema di coordinate invertendo la direzione dell'asse Y.

### <a name="vertex-format"></a>Formato vertice

È possibile regolare il formato del vertice per una mesh, per scambiare precisione per risparmiare memoria. Un footprint di memoria inferiore consente di caricare modelli più grandi o di ottenere prestazioni migliori. Tuttavia, a seconda dei dati, il formato errato può influire in modo significativo sulla qualità del rendering.

> [!CAUTION]
> La modifica del formato dei vertici dovrebbe essere l'ultima risorsa quando i modelli non si adattano più alla memoria o quando si ottimizzano per ottenere le migliori prestazioni possibili. Le modifiche possono facilmente introdurre artefatti di rendering, sia quelli ovvi che quelli sottili. A meno che non si sa cosa guardare fuori per, non si dovrebbe cambiare l'impostazione predefinita.

Queste regolazioni sono possibili:

* Flussi di dati specifici possono essere inclusi o esclusi in modo esplicito.
* La precisione dei flussi di dati può essere ridotta per ridurre il footprint di memoria.

La `vertex` sezione seguente `.json` del file è facoltativa. Per ogni parte non specificata in modo esplicito, il servizio di conversione esegue il fallback all'impostazione predefinita.

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

Forzando un componente `NONE`a , è garantito che la mesh di output non abbia il rispettivo flusso.

#### <a name="component-formats-per-vertex-stream"></a>Formati dei componenti per flusso di vertici

Questi formati sono consentiti per i rispettivi componenti:

| Componente vertice | Formati supportati (grassetto e predefinito) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT 32_32_32_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, NESSUNO |
|colore1| 8_8_8_8_UNSIGNED_NORMALIZED, **NONE**|
|normale| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|tangente| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|binormale| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, NONE |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NONE |

#### <a name="supported-component-formats"></a>Formati dei componenti supportati

I requisiti di memoria dei formati sono i seguenti:

| Format | Descrizione | Byte per vertice |
|:-------|:------------|:---------------|
|32_32_FLOAT|precisione a virgola mobile completa a due componenti|8
|16_16_FLOAT|precisione a metà virgola mobile a due componenti|4
|32_32_32_FLOAT|tre componenti precisione a virgola mobile|12
|16_16_16_16_FLOAT|precisione a quattro componenti a metà virgola mobile|8
|8_8_8_8_UNSIGNED_NORMALIZED|byte a quattro componenti, `[0; 1]` normalizzato all'intervallo|4
|8_8_8_8_SIGNED_NORMALIZED|byte a quattro componenti, `[-1; 1]` normalizzato all'intervallo|4

#### <a name="best-practices-for-component-format-changes"></a>Procedure consigliate per le modifiche al formato dei componenti

* `position`: è raro che la precisione sia sufficiente. **16_16_16_16_FLOAT** introduce notevoli artefatti di quantizzazione, anche per modelli di piccole dimensioni.
* `normal`, `tangent` `binormal`, , : in genere questi valori vengono modificati insieme. A meno che non vi siano artefatti di illuminazione evidenti derivanti dalla normale quantizzazione, non vi è alcun motivo per aumentarne la precisione. In alcuni casi, tuttavia, questi componenti possono essere impostati su **NONE**:
  * `normal`, `tangent`e `binormal` sono necessari solo quando deve essere illuminato almeno un materiale nel modello. In ARR questo caso quando un [materiale PBR](../../overview/features/pbr-materials.md) viene utilizzato sul modello in qualsiasi momento.
  * `tangent`e `binormal` sono necessari solo quando uno qualsiasi dei materiali applicati utilizza una normale texture mappa.
* `texcoord0`, `texcoord1` : le coordinate delle trame possono utilizzare `[0; 1]` una precisione ridotta (**16_16_FLOAT**) quando i loro valori rimangono nell'intervallo e quando le trame indirizzate hanno una dimensione massima di 2048 x 2048 pixel. Se tali limiti vengono superati, la qualità della mappatura delle trame ne risentirà.

#### <a name="example"></a>Esempio

Si supponga di disporre di un modello di fotogrammetria, che presenta un'illuminazione fissata nelle trame. Tutto ciò che è necessario per eseguire il rendering del modello sono le posizioni dei vertici e le coordinate della trama.

Per impostazione predefinita, il convertitore deve presupporre che è possibile utilizzare materiali `normal` `tangent`PBR `binormal` su un modello in un secondo momento, in modo che generi , e i dati per l'utente. Di conseguenza, l'utilizzo `position` della memoria per `texcoord0` vertice è `normal` (12 byte) - (8 byte) - (4 byte) - `tangent` (4 byte) - `binormal` (4 byte) - 32 byte. I modelli più grandi di questo tipo possono facilmente avere molti milioni di vertici, generando modelli che possono occupare più gigabyte di memoria. Tali grandi quantità di dati influiranno sulle prestazioni e si potrebbe anche esaurire la memoria.

Sapendo che non è mai necessaria l'illuminazione dinamica sul `[0; 1]` modello e `normal`sapendo `tangent`che `binormal` `NONE` tutte `texcoord0` le coordinate`16_16_FLOAT`di trama sono in intervallo, è possibile impostare , , e a metà precisione ( ), ottenendo solo 16 byte per vertice. Il taglio dei dati mesh a metà consente di caricare modelli più grandi e di migliorare potenzialmente le prestazioni.

## <a name="typical-use-cases"></a>Casi d'uso tipici

Trovare buone impostazioni di importazione per un determinato caso d'uso può essere un processo noioso. D'altra parte, le impostazioni di conversione possono avere un impatto significativo sulle prestazioni di runtime.

Esistono alcune classi di casi d'uso che si qualificano per ottimizzazioni specifiche. Di seguito sono riportati alcuni esempi.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Caso d'uso: visualizzazione architettonica / grandi mappe esterne

* Questi tipi di scene tendono ad essere statici, il che significa che non hanno bisogno di parti mobili. Di conseguenza, l'oggetto `sceneGraphMode` può essere impostato su `static` o anche `none`, che migliora le prestazioni di runtime. Con `static` la modalità, il nodo radice della scena può ancora essere spostato, ruotato e ridimensionato, ad esempio per passare dinamicamente tra la scala 1:1 (per la visualizzazione in prima persona) e una vista dall'alto della tabella.

* Quando è necessario spostare le parti, ciò significa anche che è necessario il supporto per raycast o altre [query spaziali,](../../overview/features/spatial-queries.md)in modo da poter selezionare tali parti in primo luogo. D'altra parte, se non si intende spostare qualcosa, è probabile che non è necessario che anche per `generateCollisionMesh` partecipare a query spaziali e quindi può disattivare il flag. Questa opzione ha un impatto significativo sui tempi di conversione, sui tempi di caricamento e sui costi di aggiornamento runtime per frame.

* Se l'applicazione non utilizza piani `opaqueMaterialDefaultSidedness` di [taglio](../../overview/features/cut-planes.md), il flag deve essere disattivato. Il miglioramento delle prestazioni è in genere del 20%-30%. I piani di taglio possono ancora essere utilizzati, ma non ci saranno facce posteriori quando si esaminano le parti interne degli oggetti, il che sembra contro-intuitivo. Per ulteriori informazioni, consultate [Rendering su lato singolo](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Caso d'uso: modelli di fotogrammetria

Quando si esegue il rendering di modelli di fotogrammetria, `sceneGraphMode` in `none`genere non è necessario un grafico di scena, pertanto è possibile impostare su . Poiché tali modelli raramente contengono un grafico di scena complesso per cominciare, l'impatto di questa opzione dovrebbe essere insignificante, però.

Poiché l'illuminazione è già cotta nelle texture, non è necessaria alcuna illuminazione dinamica. Di conseguenza:

* Impostare `unlitMaterials` la `true` bandiera per trasformare tutti i materiali in materiali di [colore](../../overview/features/color-materials.md)non illuminati .
* Rimuovere i dati non necessari dal formato del vertice. Vedere [l'esempio](#example) precedente.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Caso d'uso: visualizzazione di macchine compatte, ecc.

In questi casi d'uso, i modelli hanno spesso dettagli molto elevati all'interno di un piccolo volume. Il renderer è fortemente ottimizzato per gestire bene questi casi. Tuttavia, la maggior parte delle ottimizzazioni menzionate nel caso di utilizzo precedente non si applicano qui:

* Le singole parti devono essere selezionabili e mobili, quindi la `sceneGraphMode` deve essere lasciata a `dynamic`.
* I cast a raggi sono in genere parte integrante dell'applicazione, pertanto le maglie di collisione devono essere generate.
* I piani di taglio `opaqueMaterialDefaultSidedness` hanno un aspetto migliore con il flag abilitato.

## <a name="next-steps"></a>Passaggi successivi

* [Conversione del modello](model-conversion.md)
* [Materiali colore](../../overview/features/color-materials.md)
* [Materiali PBR](../../overview/features/pbr-materials.md)
* [Sostituzione dei materiali durante la conversione del modello](override-materials.md)
