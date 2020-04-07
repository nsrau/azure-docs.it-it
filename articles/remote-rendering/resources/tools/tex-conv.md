---
title: TexConv - Strumento di conversione texture
description: Manuale dell'utente per lo strumento da riga di comando TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680024"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Strumento di conversione texture

TexConv è uno strumento della riga di comando per elaborare le trame da formati di input tipici come PNG, TGA, JPEG e DDS in formati ottimizzati per l'utilizzo di runtime.
Mentre lo scenario più comune consiste `A.xxx` nel convertire un `B.yyy`singolo file di input in un formato ottimizzato, lo strumento dispone di molte opzioni aggiuntive per gli usi avanzati.

## <a name="command-line-help"></a>Guida della riga di comando

L'esecuzione di TexConv.exe con il `--help` parametro elencherà tutte le opzioni disponibili. Inoltre, TexConv stampa le opzioni utilizzate quando viene eseguito, per aiutare a capire cosa sta facendo. Consultare questo output per i dettagli.

## <a name="general-usage"></a>Utilizzo generale

TexConv produce sempre esattamente un file di **output.** Può utilizzare più file di **input** per assemblare l'output da. Per l'assieme, è necessario anche un **channel mapping**, che indica quale canale (*Rosso, Verde, Blu* o *Alfa*) prendere da quale file di input e spostarlo in quale canale dell'immagine di output.

La riga di comando più semplice è la seguente:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`specifica il file di output e il formato
- `-in0`specifica la prima immagine di input
- `-rgba`indica che l'immagine di output deve utilizzare tutti e quattro i canali e che devono essere presi 1:1 dall'immagine di input

## <a name="multiple-input-files"></a>Più file di input

Per assemblare l'output da più file `-in` di input, specificare ogni file di input utilizzando l'opzione con un numero crescente:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Quando si assembla una mappa cubo da trame `-right` `-left`2D, `-back` è `-px` `-nx`possibile `-py` `-ny`utilizzare `-pz` `-nz`anche , , `-top`, , `-bottom`, `-front`o , , , , , , .

Per eseguire il mapping di questi input al file di output, è necessario un mapping dei canali appropriato.

## <a name="channel-mappings"></a>Mappature dei canali

Le opzioni di mappatura dei canali specificano da quale ingresso riempire i canali di output specificati. È possibile specificare l'ingresso per ogni canale singolarmente in questo modo:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Qui i canali RGB dell'uscita verrebbero riempiti utilizzando la prima immagine di input, ma rosso e blu verranno scambiati. Il canale alfa dell'output verrebbe riempito con i valori del canale rosso della seconda immagine di input.

Specificare il mapping per ogni canale separatamente offre la massima flessibilità. Per comodità lo stesso può essere scritto utilizzando operatori "swizzling":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Canali di uscita

Sono disponibili le seguenti opzioni di mappatura dei canali:

- `-r`, `-g` `-b`, `-a` , : specificano le assegnazioni di un singolo canale
- `-rg`: specificare le assegnazioni dei canali rosso e verde.
- `-rgb`: specificare le assegnazioni dei canali rosso, verde e blu.
- `-rgba`: specifica tutte e quattro le assegnazioni dei canali.

Menzionando solo il canale R, RG o RGB, indica a TexConv di creare un file di output con solo 1, 2 o 3 canali rispettivamente.

### <a name="input-swizzling"></a>Vortice di ingresso

Quando si indica quale trama di input deve riempire quale canale di output, si può swizzll l'ingresso:

- `-rgba in0`equivale a`-rgba in0.rgba`
- `-rgba in0.bgra`swizzera i canali di ingresso
- `-rgb in0.rrr`duplica il canale rosso in tutti i canali

Si può anche riempire i canali con nero o bianco:

- `-rgb in0 -a white`creerà una texture di output a 4 canali ma imposta alfa su completamente opaco
- `-rg black -b white`creerà una texture interamente blu

## <a name="common-options"></a>Opzioni comuni

Le opzioni più interessanti sono elencate di seguito. Altre opzioni sono `TexConv --help`elencate per .

### <a name="output-type"></a>Tipo di output

- `-type 2D`: l'output sarà un'immagine 2D normale.
- `-type Cubemap`: l'output sarà un'immagine della mappa del cubo. Supportato solo per i file di output DDS. Quando questo è specificato, si può assemblare la mappa cubo da 6 immagini di input 2D regolari.

### <a name="image-compression"></a>Compressione delle immagini

- `-compression none`: l'immagine di output verrà decompressa.
- `-compression medium`: se supportata, l'immagine di output utilizzerà la compressione senza sacrificare troppa qualità.
- `-compression high`: se supportata, l'immagine di output utilizzerà la compressione e la qualità del sacrificio a favore di un file più piccolo.

### <a name="mipmaps"></a>Mipmap

Per impostazione predefinita, TexConv genera mipmap quando il formato di output lo supporta.

- `-mipmaps none`: le mipmap non verranno generate.
- `-mipmaps Linear`: se supportato, le mipmap verranno generate utilizzando un filtro box.

### <a name="usage-srgb--gamma-correction"></a>Utilizzo (correzione sRGB/gamma)

L'opzione `-usage` specifica lo scopo dell'output e indica quindi a TexConv se applicare la correzione gamma ai file di input e output. L'utilizzo influisce solo sui canali RGB. Il canale alfa è sempre considerato conterrà valori 'lineari'. Se l'utilizzo non è specificato, la modalità 'auto' tenterà di rilevare l'utilizzo dal formato e dal nome file della prima immagine di input. Ad esempio, i formati di output a canale singolo e doppio sono sempre lineari. Controllare l'output per vedere quale decisione TexConv ha preso.

- `-usage Linear`: l'immagine di output contiene valori che non rappresentano colori. Questo è in genere il caso per le texture metalliche e di rugosità, così come tutti i tipi di maschere.

- `-usage Color`: l'immagine di output rappresenta il colore, ad esempio le mappe diffuse/albedo. Il flag sRGB verrà impostato nell'intestazione DDS di output.

- `-usage HDR`: il file di output deve utilizzare più di 8 bit per pixel per la codifica. Di conseguenza tutti i valori vengono memorizzati nello spazio lineare. Per le trame HDR non importa se i dati rappresentano il colore o altri dati.

- `-usage NormalMap`: l'immagine di output rappresenta una mappa normale con spazio tangente. I valori verranno normalizzati e il calcolo mipmap verrà leggermente ottimizzato.

- `-usage NormalMap_Inverted`: l'output è una mappa normale dello spazio tangente con Y che punta nella direzione opposta a quella dell'ingresso.

### <a name="image-rescaling"></a>Ridimensionamento dell'immagine

- `-minRes 64`: specifica la risoluzione minima dell'output. Se l'immagine di input è più piccola, verrà ridimensionata.
- `-maxRes 1024`: specifica la risoluzione massima dell'output. Se l'immagine di input è più grande, verrà ridimensionata.
- `-downscale 1`: se è maggiore di 0, le immagini di input verranno dimezzate con risoluzione N volte. Utilizzare questa opzione per applicare una riduzione di qualità complessiva.

## <a name="examples"></a>Esempi

### <a name="convert-a-color-texture"></a>Convertire una trama di colore

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Convertire una mappa normale

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Creare una mappa cubo HDRCreate an HDR cubemap

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Un'ottima fonte per i cubemap HDR è [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Cuocere più immagini in una

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Estrarre un singolo canale

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
