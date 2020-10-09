---
title: Videocamera depth di Azure Kinect DK
description: Informazioni sui principi operativi e sulle funzionalità chiave della fotocamera depth in Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, depth fotocamera, TOF, principi, prestazioni, invalidamento
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276700"
---
# <a name="azure-kinect-dk-depth-camera"></a>Videocamera depth di Azure Kinect DK

Questa pagina illustra come usare la videocamera depth in Azure Kinect DK. La videocamera Depth è la seconda delle due fotocamere. Come spiegato nelle sezioni precedenti, l'altra fotocamera è la fotocamera RGB.  

## <a name="operating-principles"></a>Principi operativi

La videocamera Azure Kinect DK Depth implementa il principio AMCW (Time-of-Flight) con modulazione dell'ampiezza (ToF). La fotocamera proietta l'illuminazione modulata nello spettro near-IR (NIR) sulla scena. Viene quindi registrata una misura indiretta del tempo necessario per il trasferimento dalla fotocamera alla scena e viceversa.

Queste misurazioni vengono elaborate per generare una mappa di profondità. Una mappa di profondità è un set di valori di coordinata Z per ogni pixel dell'immagine, misurato in unità di millimetri.

Insieme a una mappa di profondità, viene anche ottenuta una cosiddetta lettura IR pulita. Il valore di pixel nella lettura IR pulita è proporzionale alla quantità di luce restituita dalla scena. L'immagine ha un aspetto simile a un'immagine IR normale. Nella figura seguente è illustrata una mappa di profondità di esempio (a sinistra) e un'immagine IR pulita corrispondente (a destra).

![Profondità e IR side-by-side](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>Funzionalità principali

Le caratteristiche tecniche della fotocamera Depth includono:

- chip di imaging ToF da 1 megapixel con tecnologia pixel avanzata che consente frequenze di modulazione più elevate e precisione della profondità.
- Due diodi laser NIR che abilitano le modalità di profondità dei campi di visualizzazione (FoV) near e Wide.
- Il più piccolo pixel ToF del mondo, a 3,5 μm di 3,5 μm.
- Selezione automatica per ogni pixel che consente l'acquisizione di un intervallo dinamico di grandi dimensioni che consente l'acquisizione pulita degli oggetti vicini e lontani.
- Otturatore globale che consente di migliorare le prestazioni in luce solare.
- Metodo di calcolo della profondità a più fasi che consente un'accuratezza affidabile anche in presenza di chip, laser e variazione di alimentazione.
- Errori casuali e sistematici ridotti.

![Modulo Depth](./media/concepts/depth-camera-depth-module.jpg)

La fotocamera di profondità trasmette immagini IR modulate non elaborate al PC host. Sul PC, il software del motore di profondità accelerata GPU converte il segnale non elaborato in mappe di profondità.La videocamera Depth supporta diverse modalità. Le modalità **di visualizzazione dei campi (FOV) ristrette** sono ideali per le scene con extent più piccoli nelle dimensioni X e Y, ma con extent più grandi nella dimensione Z. Se la scena ha extent X e Y di grandi dimensioni, ma intervalli Z più piccoli, le **modalità FOV** sono più adatte.

La videocamera Depth supporta le modalità di suddivisione in **contenitori 2x2** per estendere l'intervallo Z rispetto alle modalità non in **contenitori**corrispondenti. La creazione di contenitori viene eseguita a scapito della riduzione della risoluzione delle immagini. Tutte le modalità possono essere eseguite fino a 30 frame al secondo (fps), ad eccezione della modalità 1 megapixel (MP) che viene eseguita con una frequenza massima dei fotogrammi di 15 fps. La videocamera Depth fornisce anche una **modalità IR passiva**. In questa modalità gli illuminatori della fotocamera non sono attivi e viene osservata solo l'illuminazione ambientale.

## <a name="camera-performance"></a>Prestazioni della fotocamera

Le prestazioni della fotocamera vengono misurate come errori sistematici e casuali.

### <a name="systematic-error"></a>Errore sistematico

L'errore sistematico viene definito come la differenza tra la profondità misurata dopo la rimozione del rumore e la profondità corretta (base di verità). La media temporale viene calcolata su molti frame di una scena statica per eliminare il più possibile il rumore di profondità. Più precisamente, l'errore sistematico viene definito come segue:

![Errore sistematico di profondità](./media/concepts/depth-camera-systematic-error.png)

Dove *d<sub>t</sub> * denota la profondità della misura al momento *t*, *N* è il numero di fotogrammi usati nella procedura di media e *d<sub>gt</sub> * è la profondità della verità di base.

La specifica di errore sistematico della fotocamera è esclusa l'interferenza a percorsi multipli (MPI). MPI si verifica quando un pixel del sensore integra la luce riflessa da più di un oggetto. MPI è parzialmente mitigato nella nostra fotocamera di profondità usando frequenze di modulazione più elevate, oltre all'invalidamento della profondità, che verrà introdotto in un secondo momento.

### <a name="random-error"></a>Errore casuale

Si supponga di prendere le immagini 100 dello stesso oggetto senza dover passare alla fotocamera. La profondità dell'oggetto sarà leggermente diversa in ognuna delle immagini 100. Questa differenza è causata da un rumore di scatto. Il rumore di colpo è il numero di fotoni che raggiungono il sensore variano in base a un fattore casuale nel tempo. Questo errore casuale viene definito in una scena statica come deviazione standard della profondità nel tempo calcolato come:

![Errore casuale di profondità](./media/concepts/depth-camera-random-error.png)

Dove *N* indica il numero di misurazioni di profondità, *d<sub>t</sub> * rappresenta la misurazione della profondità all'ora *t* e *d* denota il valore medio calcolato su tutte le misurazioni di profondità *d<sub>t</sub>*.

## <a name="invalidation"></a>Invalidamento

In alcuni casi, è possibile che la fotocamera di profondità non fornisca valori corretti per alcuni pixel. In questi casi, i pixel di profondità vengono invalidati. I pixel non validi sono indicati dal valore di profondità uguale a 0. I motivi per cui il motore di profondità non è in grado di produrre valori corretti includono:

- Al di fuori della maschera di illuminazione IR attiva
- Segnale IR saturato
- Segnale IR basso
- Outlier filtro
- Interferenza a percorsi multipli

### <a name="illumination-mask"></a>Maschera di illuminazione

I pixel vengono invalidati quando sono all'esterno della maschera di illuminazione IR attiva. Non è consigliabile usare il segnale di tali pixel per calcolare la profondità. La figura seguente mostra l'esempio di invalidamento per maschera di illuminazione. I pixel invalidati sono i pixel di colore nero all'esterno del cerchio nelle modalità FoV (a sinistra) e l'esagono nelle modalità FoV Narrow (Right).

![Invalidamento esterno alla maschera di illuminazione](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>Attendibilità del segnale

I pixel vengono invalidati quando contengono un segnale IR saturato. Quando i pixel sono saturi, le informazioni sulle fasi vengono perse. L'immagine seguente mostra l'esempio di invalidamento di un segnale IR saturato. Vedere frecce puntate ai pixel di esempio nelle immagini Depth e IR.

![Saturazione di invalidamento](./media/concepts/depth-camera-invalidation-saturation.png)

L'invalidamento può verificarsi anche quando il segnale IR non è sufficientemente forte per generare la profondità. La figura seguente mostra l'esempio di invalidamento di un segnale IR basso. Vedere le frecce che puntano ad esempio pixel nelle immagini Depth e IR.

![Segnale basso per l'invalidamento](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>Profondità ambigua

I pixel possono anche essere invalidati se hanno ricevuto segnali da più di un oggetto nella scena. Un caso comune in cui questo tipo di invalidamento può essere visualizzato è negli angoli.  A causa della geometria della scena, la luce IR dalla fotocamera si riflette su una parete e sull'altra. Questa luce riflessa causa ambiguità nella profondità misurata del pixel. I filtri nell'algoritmo Depth rilevano questi segnali ambigui e invalidano i pixel.

Le figure seguenti illustrano esempi di invalidamento per il rilevamento di più percorsi. È anche possibile vedere in che modo la stessa area di attacco che è stata invalidata da una visualizzazione della fotocamera (riga superiore) può apparire nuovamente da una diversa visualizzazione della fotocamera (riga inferiore). Questa immagine dimostra che le superfici invalidate da una prospettiva possono essere visibili da un'altra.

![Invalidamento a percorsi multipli-angolo](./media/concepts/depth-camera-invalidation-multipath.png)

Un altro caso comune di percorsi multipli è costituito da pixel che contengono il segnale misto da primo piano e sfondo, ad esempio attorno ai bordi degli oggetti. Durante il movimento veloce, è possibile visualizzare più pixel invalidati intorno ai bordi. I pixel aggiuntivi invalidati sono dovuti all'intervallo di esposizione dell'acquisizione di profondità non elaborata,

![Invalidamento a percorsi multipli-bordi](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>Passaggi successivi

[Sistemi di coordinate](coordinate-systems.md)
