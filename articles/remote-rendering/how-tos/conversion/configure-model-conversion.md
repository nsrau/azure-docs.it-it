---
title: Configurare la conversione di modelli
description: Descrizione di tutti i parametri di conversione del modello
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681571"
---
# <a name="configure-the-model-conversion"></a>Configurare la conversione di modelli

In questo capitolo vengono documentate le opzioni per la conversione del modello.

## <a name="settings-file"></a>File di impostazioni

Se un file chiamato `ConversionSettings.json` viene trovato nel contenitore di input accanto al modello di input, viene usato per fornire una configurazione aggiuntiva per il processo di conversione del modello.

Il contenuto del file deve soddisfare lo schema JSON seguente:

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

Un file `ConversionSettings.json` di esempio potrebbe essere:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parametri Geometry

* `scaling`-Questo parametro consente di ridimensionare un modello in modo uniforme. La scalabilità può essere utilizzata per aumentare o ridurre un modello, ad esempio per visualizzare un modello di compilazione in una tabella in alto. Poiché il motore di rendering prevede che la lunghezza venga specificata in metri, un altro importante utilizzo di questo parametro si verifica quando un modello viene definito in unità diverse. Se, ad esempio, un modello è definito in centimetri, l'applicazione di una scala 0,01 deve eseguire il rendering del modello con le dimensioni corrette.
Alcuni formati dei dati di origine (ad esempio. fbx) forniscono un hint di ridimensionamento unità, nel qual caso la conversione scala in modo implicito il modello in unità di misura. Il ridimensionamento implicito fornito dal formato di origine verrà applicato al di sopra del parametro di ridimensionamento.
Il fattore di scala finale viene applicato ai vertici di geometria e alle trasformazioni locali dei nodi del grafico della scena. Il ridimensionamento per la trasformazione dell'entità radice rimane invariato.

* `recenterToOrigin`-Indica che è necessario convertire un modello in modo che il relativo rettangolo di delimitazione sia centrato all'origine.
Il centramento è importante se il modello di origine viene spostato lontano dall'origine, poiché in questo caso i problemi di precisione a virgola mobile possono causare artefatti di rendering.

* `opaqueMaterialDefaultSidedness`-Il motore di rendering presuppone che i materiali opachi siano a doppio lato.
Se questo non è il comportamento previsto, questo parametro deve essere impostato su "SingleSided". Per ulteriori informazioni, vedere [rendering a lato singolo](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Sostituzioni materiali

* `material-override`-Questo parametro consente l'elaborazione dei materiali da [personalizzare durante la conversione](override-materials.md).

### <a name="color-space-parameters"></a>Parametri dello spazio colore

Il motore di rendering prevede che i valori dei colori siano nello spazio lineare.
Se un modello viene definito usando lo spazio gamma, queste opzioni devono essere impostate su true.

* `gammaToLinearMaterial`-Converte i colori del materiale dallo spazio gamma allo spazio lineare
* `gammaToLinearVertex`-Convertire i colori dei vertici dallo spazio gamma allo spazio lineare

> [!NOTE]
> Per i file FBX queste impostazioni sono impostate `true` su per impostazione predefinita. Per tutti gli altri tipi di file, il `false`valore predefinito è.

### <a name="scene-parameters"></a>Parametri della scena

* `sceneGraphMode`-Definisce la modalità di conversione del grafico della scena nel file di origine:
  * `dynamic`(impostazione predefinita): tutti gli oggetti nel file vengono esposti come [entità](../../concepts/entities.md) nell'API e possono essere trasformati in modo indipendente. La gerarchia del nodo in fase di esecuzione è identica alla struttura nel file di origine.
  * `static`: Tutti gli oggetti vengono esposti nell'API ma non possono essere trasformati in modo indipendente.
  * `none`: Il grafico della scena viene compresso in un unico oggetto.

Ogni modalità presenta prestazioni di runtime diverse. In `dynamic` modalità il costo delle prestazioni viene scalato in modo lineare con il numero di [entità](../../concepts/entities.md) nel grafico, anche quando nessuna parte viene spostata. Deve essere usato solo quando lo stato di trasferimento delle parti singolarmente è necessario per l'applicazione, ad esempio per un'animazione "vista esplosione".

La `static` modalità Esporta il grafico della scena completa, ma le parti all'interno di questo grafico hanno una trasformazione costante rispetto alla relativa parte radice. Il nodo radice dell'oggetto, tuttavia, può comunque essere spostato, ruotato o ridimensionato senza costi significativi per le prestazioni. Inoltre, le [query spaziali](../../overview/features/spatial-queries.md) restituiscono singole parti e ogni parte può essere modificata tramite [sostituzioni di stato](../../overview/features/override-hierarchical-state.md). Con questa modalità, il sovraccarico di runtime per oggetto è trascurabile. È ideale per le grandi scene in cui è ancora necessario l'ispezione per ogni oggetto, ma nessuna trasformazione per oggetto.

La `none` modalità ha il minor sovraccarico in fase di esecuzione e tempi di caricamento leggermente migliori. L'ispezione o la trasformazione di singoli oggetti non è possibile in questa modalità. I casi di utilizzo sono, ad esempio, i modelli fotogrammetria che non dispongono di un grafico di scena significativo nella prima posizione.

> [!TIP]
> In molte applicazioni vengono caricati più modelli. È consigliabile ottimizzare i parametri di conversione per ogni modello a seconda di come verrà usato. Se, ad esempio, si desidera visualizzare il modello di un'automobile in modo che l'utente abbia a che fare con `dynamic` l'analisi dettagliata, è necessario convertirlo in modalità. Tuttavia, se si desidera inserire l'auto in un ambiente di visualizzazione della stanza, il modello può essere convertito con `sceneGraphMode` impostato su `static` o anche `none`.

### <a name="physics-parameters"></a>Parametri di fisica

* `generateCollisionMesh`-Se è necessario il supporto per le [query spaziali](../../overview/features/spatial-queries.md) su un modello, questa opzione deve essere abilitata. Nel peggiore dei casi, la creazione di una mesh di collisione può raddoppiare il tempo di conversione. I modelli con mesh collisione impiegano più tempo per il `dynamic` caricamento e quando si usa un grafico della scena, hanno anche un sovraccarico delle prestazioni di runtime superiore. Per ottenere prestazioni ottimali complessive, è consigliabile disabilitare questa opzione per tutti i modelli in cui non sono necessarie query spaziali.

### <a name="unlit-materials"></a>Materiale spento

* `unlitMaterials`Per impostazione predefinita, la conversione preferisce creare [materiali PBR](../../overview/features/pbr-materials.md). Questa opzione indica al convertitore di considerare in alternativa tutti i materiali come [materiali colorati](../../overview/features/color-materials.md) . Se sono presenti dati che incorporano già l'illuminazione, ad esempio i modelli creati tramite fotogrammetria, questa opzione consente di applicare rapidamente la conversione corretta per tutti i materiali, senza dover [sostituire ogni singolo materiale](override-materials.md) .

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Conversione da formati FBX precedenti, con un modello di materiale Phong

* `fbxAssumeMetallic`-Le versioni precedenti del formato FBX definiscono i materiali usando un modello di materiale Phong. Il processo di conversione deve dedurre il mapping di questi materiali al [modello PBR](../../overview/features/pbr-materials.md)del renderer. In genere questa operazione funziona correttamente, ma è possibile che si verifichi un'ambiguità quando un materiale non ha trame, valori speculari elevati e un colore di albedo non grigio. In questo caso, la conversione deve scegliere tra la priorità dei valori speculari elevati, definendo un materiale metallico altamente riflettente, in cui il colore dell'albedo si dissolve o assegnando la priorità al colore dell'albedo, definendo qualcosa di simile a una plastica colorata brillante. Per impostazione predefinita, il processo di conversione presuppone che i valori altamente speculari implichino un materiale metallico nei casi in cui si applica l'ambiguità. Questo parametro può essere impostato su `false` per passare al contrario.

### <a name="coordinate-system-overriding"></a>Override del sistema di coordinate

* `axis`-Per eseguire l'override di vettori di unità di sistema di coordinate. I valori predefiniti `["+x", "+y", "+z"]`sono. In teoria, il formato FBX ha un'intestazione in cui vengono definiti questi vettori e la conversione usa tali informazioni per trasformare la scena. Il formato glTF definisce anche un sistema di coordinate fisso. In pratica, alcuni asset contengono informazioni non corrette nell'intestazione o sono stati salvati con una convenzione del sistema di coordinate diversa. Questa opzione consente di eseguire l'override del sistema di coordinate per compensare. Ad esempio `"axis" : ["+x", "+z", "-y"]` , scambierà l'asse Z e l'asse y e manterrà la manualità del sistema di coordinate invertendo la direzione dell'asse y.

### <a name="vertex-format"></a>Formato vertici

È possibile modificare il formato del vertice per una mesh, in modo da ottenere la precisione per il risparmio di memoria. Un footprint di memoria inferiore consente di caricare modelli più grandi o ottenere prestazioni migliori. Tuttavia, a seconda dei dati, il formato errato può influire significativamente sulla qualità del rendering.

> [!CAUTION]
> La modifica del formato del vertice dovrebbe essere l'ultima risorsa quando i modelli non rientrano più nella memoria o quando si ottimizzano per ottenere le migliori prestazioni possibili. Le modifiche possono introdurre facilmente elementi di rendering, sia quelli evidenti che quelli sottili. A meno che non si conoscano gli elementi da cercare, è consigliabile non modificare il valore predefinito.

Queste rettifiche sono possibili:

* I flussi di dati specifici possono essere inclusi o esclusi in modo esplicito.
* L'accuratezza dei flussi di dati può essere ridotta per ridurre il footprint di memoria.

La sezione `vertex` seguente del `.json` file è facoltativa. Per ogni parte non specificata in modo esplicito, il servizio di conversione esegue il fallback all'impostazione predefinita.

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

Forzando un componente su `NONE`, si garantisce che la mesh di output non disponga del rispettivo flusso.

#### <a name="component-formats-per-vertex-stream"></a>Formati di componente per flusso di vertici

Questi formati sono consentiti per i rispettivi componenti:

| Componente vertex | Formati supportati (Bold = default) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, nessuno |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **nessuno**|
|normale| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, None |
|tangente| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, None |
|binormale| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, None |
|TEXCOORD0| **32_32_FLOAT**, 16_16_FLOAT, None |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, None |

#### <a name="supported-component-formats"></a>Formati di componenti supportati

I footprint di memoria dei formati sono i seguenti:

| Format | Descrizione | Byte per vertice |
|:-------|:------------|:---------------|
|32_32_FLOAT|precisione a virgola mobile completa a due componenti|8
|16_16_FLOAT|precisione a virgola mobile a due componenti|4
|32_32_32_FLOAT|precisione a virgola mobile completa a tre componenti|12
|16_16_16_16_FLOAT|precisione a virgola mobile a quattro componenti|8
|8_8_8_8_UNSIGNED_NORMALIZED|byte a quattro componenti, normalizzato nell' `[0; 1]` intervallo|4
|8_8_8_8_SIGNED_NORMALIZED|byte a quattro componenti, normalizzato nell' `[-1; 1]` intervallo|4

#### <a name="best-practices-for-component-format-changes"></a>Procedure consigliate per le modifiche al formato dei componenti

* `position`: È raro che sia sufficiente un'accuratezza ridotta. **16_16_16_16_FLOAT** introduce elementi di quantizzazione evidenti, anche per i modelli di piccole dimensioni.
* `normal`, `tangent`, `binormal`: Questi valori vengono in genere modificati insieme. A meno che non siano presenti artefatti di illuminazione evidenti derivanti dalla normale quantizzazione, non c'è motivo di aumentarne la precisione. In alcuni casi, tuttavia, questi componenti possono essere impostati su **None**:
  * `normal`, `tangent`e `binormal` sono necessari solo quando è necessario illuminare almeno un materiale nel modello. In ARR questo è il caso in cui un [materiale PBR](../../overview/features/pbr-materials.md) viene usato per il modello in qualsiasi momento.
  * `tangent`e `binormal` sono necessari solo quando uno dei materiali illuminati usa una trama normale della mappa.
* `texcoord0`, `texcoord1` : Le coordinate di trama possono usare un'accuratezza ridotta (**16_16_FLOAT**) quando i `[0; 1]` relativi valori sono compresi nell'intervallo e quando le trame con indirizzo hanno una dimensione massima di 2048 x 2048 pixel. Se questi limiti vengono superati, la qualità del mapping della trama risentirà.

#### <a name="example"></a>Esempio

Si supponga di avere un modello fotogrammetria, in cui l'illuminazione è cotta nelle trame. Tutto ciò che serve per eseguire il rendering del modello sono le posizioni dei vertici e le coordinate di trama.

Per impostazione predefinita, il convertitore deve presupporre che sia necessario usare i materiali PBR in un modello in un determinato momento, quindi genera `normal`automaticamente `tangent`i dati `binormal` , e. Di conseguenza, l'utilizzo della memoria per `position` vertice è (12 byte `texcoord0` ) + (8 byte `normal` ) + (4 byte `tangent` ) + (4 byte `binormal` ) + (4 byte) = 32 byte. I modelli più grandi di questo tipo possono avere facilmente molti milioni di vertici, ottenendo modelli che possono richiedere più gigabyte di memoria. Una quantità elevata di dati influirà sulle prestazioni e potrebbe anche esaurire la memoria.

Sapendo che non è mai necessaria un'illuminazione dinamica sul modello e che tutte le coordinate di trama sono `[0; 1]` comprese nell'intervallo, è `normal`possibile `tangent`impostare, `binormal` e `NONE` su `texcoord0` e su Half Precision`16_16_FLOAT`(), ottenendo solo 16 byte per vertice. Il taglio dei dati di rete a metà consente di caricare modelli di dimensioni maggiori e potenzialmente migliorare le prestazioni.

## <a name="typical-use-cases"></a>Casi d'uso tipici

Trovare le impostazioni di importazione valide per un caso d'uso specifico può essere un processo noioso. D'altra parte, le impostazioni di conversione possono avere un impatto significativo sulle prestazioni di Runtime.

Esistono alcune classi di casi d'uso che sono idonee per specifiche ottimizzazioni. Di seguito sono riportati alcuni esempi.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Caso d'uso: visualizzazione architettura/mappe esterne di grandi dimensioni

* Questi tipi di scene tendono a essere statici, vale a dire che non necessitano di parti mobili. Di conseguenza, `sceneGraphMode` può essere impostato su `static` o addirittura `none`, che migliora le prestazioni di Runtime. Con `static` la modalità, è ancora possibile spostare, ruotare e ridimensionare il nodo radice della scena, ad esempio per passare dinamicamente dalla scala 1:1 (per la visualizzazione della prima persona) a una visualizzazione superiore della tabella.

* Quando è necessario spostare parti, questo in genere significa anche che è necessario il supporto per raycasts o altre [query spaziali](../../overview/features/spatial-queries.md), in modo che sia possibile scegliere le parti in primo luogo. D'altra parte, se non si intende spostare un elemento, è probabile che non sia necessario partecipare alle query spaziali e che quindi possa disabilitare il `generateCollisionMesh` flag. Questo commutatore ha un impatto significativo sui tempi di conversione, i tempi di caricamento e anche i costi di aggiornamento per fotogramma del runtime.

* Se l'applicazione non usa i [piani taglia](../../overview/features/cut-planes.md), il `opaqueMaterialDefaultSidedness` flag deve essere disattivato. Il miglioramento delle prestazioni è in genere 20%-30%. I piani tagliati possono comunque essere utilizzati, ma non vi saranno back-visi quando si esaminano le parti interne degli oggetti, che sembrano non intuitive. Per ulteriori informazioni, vedere [rendering a lato singolo](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Caso d'uso: modelli fotogrammetria

Quando si esegue il rendering dei modelli fotogrammetria, in genere non è necessario un grafico della `sceneGraphMode` scena, quindi `none`è possibile impostare su. Poiché i modelli contengono raramente un grafico della scena complessa da cui iniziare, l'impatto di questa opzione dovrebbe essere irrilevante.

Poiché l'illuminazione è già stata preparata alle trame, non è necessaria alcuna illuminazione dinamica. Di conseguenza:

* Impostare il `unlitMaterials` flag su `true` per trasformare tutti i materiali in [materiali di colore](../../overview/features/color-materials.md)spento.
* Rimuovere i dati non necessari dal formato vertice. Vedere l' [esempio](#example) precedente.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Caso d'uso: visualizzazione di macchine compatte e così via.

In questi casi d'uso, i modelli spesso hanno un numero molto elevato di dettagli in un volume ridotto. Il renderer è molto ottimizzato per la gestione di questi casi. Tuttavia, la maggior parte delle ottimizzazioni indicate nel caso di utilizzo precedente non si applica qui:

* Le `sceneGraphMode` singole parti devono essere selezionabili e mobili, quindi è necessario lasciarle `dynamic`a.
* I cast dei raggi sono in genere parte integrante dell'applicazione, pertanto è necessario generare mesh di collisione.
* I piani tagliati sembrano migliori `opaqueMaterialDefaultSidedness` con il flag abilitato.

## <a name="next-steps"></a>Passaggi successivi

* [Conversione di modelli](model-conversion.md)
* [Materiali a colori](../../overview/features/color-materials.md)
* [Materiali PBR](../../overview/features/pbr-materials.md)
* [Sostituire i materiali durante la conversione di modelli](override-materials.md)
