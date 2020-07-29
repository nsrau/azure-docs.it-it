---
title: TexConv-strumento di conversione della trama
description: Manuale dell'utente per lo strumento da riga di comando TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80680024"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv-strumento di conversione della trama

TexConv è uno strumento da riga di comando per elaborare trame da formati di input tipici come PNG, TGA, JPEG e DDS in formati ottimizzati per l'utilizzo in fase di esecuzione.
Sebbene lo scenario più comune consiste nel convertire un singolo file di input `A.xxx` in un formato ottimizzato `B.yyy` , lo strumento offre molte opzioni aggiuntive per gli utilizzi avanzati.

## <a name="command-line-help"></a>Guida della riga di comando

Eseguendo TexConv.exe con il `--help` parametro, vengono elencate tutte le opzioni disponibili. Inoltre, TexConv stampa le opzioni usate al momento dell'esecuzione, per facilitare la comprensione del funzionamento. Per informazioni dettagliate, vedere questo output.

## <a name="general-usage"></a>Utilizzo generale

TexConv produce sempre **esattamente un** file di output. Può usare più file di **input** per assemblare l'output da. Per l'assembly, è necessario anche un **mapping del canale**, che indica quale canale (*rosso, verde, blu* o *alfa*) deve prendere da quale file di input e spostarlo nel canale dell'immagine di output.

La riga di comando più diretta è la seguente:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`Specifica il formato e il file di output
- `-in0`Specifica la prima immagine di input
- `-rgba`indica che l'immagine di output deve usare tutti e quattro i canali e che devono essere presi 1:1 dall'immagine di input

## <a name="multiple-input-files"></a>Più file di input

Per assemblare l'output da più file di input, specificare ogni file di input usando l' `-in` opzione con un numero crescente:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Quando si assembla un mappa cubi da trame 2D, è possibile usare anche `-right` ,, `-left` `-top` , `-bottom` , o,,, `-front` `-back` `-px` `-nx` `-py` `-ny` , `-pz` , `-nz` .

Per eseguire il mapping di questi input al file di output, è necessario un mapping del canale appropriato.

## <a name="channel-mappings"></a>Mapping dei canali

Le opzioni di mapping del canale specificano da quale input inserire i canali di output specificati. È possibile specificare l'input per ogni canale singolarmente, come indicato di seguito:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Qui i canali RGB dell'output verrebbero riempiti usando la prima immagine di input, ma il rosso e il blu verranno scambiati. Il canale alfa dell'output verrebbe riempito con i valori del canale rosso della seconda immagine di input.

La specifica del mapping per ogni canale garantisce la massima flessibilità. Per praticità, lo stesso può essere scritto usando gli operatori "swizzling":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Canali di output

Sono disponibili le seguenti opzioni di mapping dei canali:

- `-r`, `-g` , `-b` , `-a` : Specificano assegnazioni a canale singolo
- `-rg`: Specificare le assegnazioni di canale rosso e verde.
- `-rgb`: Specificare le assegnazioni di canale rosso, verde e blu.
- `-rgba`: Specifica tutte e quattro le assegnazioni di canale.

Per citare solo il canale R, RG o RGB, indica a TexConv di creare un file di output con soli 1, 2 o 3 canali rispettivamente.

### <a name="input-swizzling"></a>Swizzling di input

Quando si indica quale trama di input deve riempire il canale di output, è possibile swizzle l'input:

- `-rgba in0`equivale a`-rgba in0.rgba`
- `-rgba in0.bgra`swizzle i canali di input
- `-rgb in0.rrr`il canale rosso viene duplicato in tutti i canali

Uno può anche riempire i canali con il nero o il bianco:

- `-rgb in0 -a white`creerà una trama di output a 4 canali, ma imposterà Alpha su completamente opaco
- `-rg black -b white`creerà una trama completamente blu

## <a name="common-options"></a>Opzioni comuni

Le opzioni più interessanti sono elencate di seguito. Altre opzioni sono elencate da `TexConv --help` .

### <a name="output-type"></a>Tipo di output

- `-type 2D`: L'output sarà un'immagine 2D normale.
- `-type Cubemap`: L'output sarà un'immagine mappa cubi. Supportato solo per i file di output DDS. Quando si specifica questa impostazione, è possibile assemblare mappa cubi da 6 immagini di input 2D regolari.

### <a name="image-compression"></a>Compressione immagini

- `-compression none`: L'immagine di output verrà decompressa.
- `-compression medium`: Se supportato, l'immagine di output utilizzerà la compressione senza sacrificare una quantità eccessiva di qualità.
- `-compression high`: Se supportato, l'immagine di output utilizzerà la compressione e sacrifica la qualità a favore di un file più piccolo.

### <a name="mipmaps"></a>Mipmap

Per impostazione predefinita, TexConv genera mipmap quando il formato di output lo supporta.

- `-mipmaps none`: Mipmap non verrà generato.
- `-mipmaps Linear`: Se supportato, mipmap verrà generato utilizzando un filtro Box.

### <a name="usage-srgb--gamma-correction"></a>Utilizzo (correzione RGB/gamma)

L' `-usage` opzione specifica lo scopo dell'output e quindi indica a TexConv se applicare la correzione gamma ai file di input e di output. L'utilizzo influiscono solo sui canali RGB. Il canale alfa viene sempre considerato contenere valori "Linear". Se non si specifica Usage, la modalità "auto" tenterà di rilevare l'utilizzo dal formato e dal nome file della prima immagine di input. Ad esempio, i formati di output single e Dual Channel sono sempre lineari. Controllare l'output per verificare quale decisione ha TexConv.

- `-usage Linear`: L'immagine di output contiene valori che non rappresentano colori. Si tratta in genere del caso di trame metalliche e di rugosità, nonché di tutti i tipi di maschere.

- `-usage Color`: L'immagine di output rappresenta il colore, ad esempio mappe diffuse/albedo. Il flag sRGB verrà impostato nell'intestazione DDS di output.

- `-usage HDR`: Il file di output deve usare più di 8 bit per pixel per la codifica. Di conseguenza, tutti i valori vengono archiviati nello spazio lineare. Per le trame HDR non è rilevante se i dati rappresentano il colore o altri dati.

- `-usage NormalMap`: L'immagine di output rappresenta una mappa normale dello spazio tangente. I valori verranno normalizzati e il calcolo mipmap verrà ottimizzato leggermente.

- `-usage NormalMap_Inverted`: L'output è una mappa normale dello spazio tangente con Y che punta alla direzione opposta rispetto all'input.

### <a name="image-rescaling"></a>Ridimensionamento dell'immagine

- `-minRes 64`: Specifica la risoluzione minima dell'output. Se l'immagine di input è più piccola, verrà ridimensionata.
- `-maxRes 1024`: Specifica la risoluzione massima dell'output. Se l'immagine di input è più grande, otterrà downscaling.
- `-downscale 1`: Se è maggiore di 0, le immagini di input saranno dimezzate nella risoluzione N volte. Usare questa applicazione per applicare una riduzione complessiva della qualità.

## <a name="examples"></a>Esempi

### <a name="convert-a-color-texture"></a>Convertire una trama colori

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Convertire una mappa normale

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Creare un mappa cubi HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Un'ottima fonte di CubeMaps HDR è [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Cuocere più immagini in un'unica

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Estrarre un canale singolo

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
