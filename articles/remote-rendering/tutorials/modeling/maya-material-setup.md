---
title: Configurare materiali basati su PBR (Physically Based Rendering) in Maya
description: Questa esercitazione spiega come configurare materiali basati su PBR (Physically Based Rendering) in Maya ed esportarli in formato FBX.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 72742ff4f6aa19fda092b44d8d2237e7d49dd816
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85373243"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>Esercitazione: Configurare materiali basati su PBR (Physically Based Rendering) in Maya

## <a name="overview"></a>Panoramica
In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Assegnare materiali con illuminazione avanzata agli oggetti nella scena.
> * Gestire la creazione di istanze di oggetti e materiali.
> * Esportare una scena in formato FBX e selezionare opzioni importanti.

La creazione di [materiali basati su PBR (Physically Based Rendering)](../../overview/features/pbr-materials.md) in Maya è un'attività relativamente semplice. È simile sotto molti aspetti alla configurazione di materiali basati su PBR in altre app per la creazione di contenuti come 3DS Max. Questa esercitazione è una guida alla configurazione di una funzione shader PBR di base e all'esportazione in formato FBX per i progetti di Rendering remoto di Azure. 

La scena di esempio dell'esercitazione contiene una serie di oggetti poligono a cui sono assegnati materiali diversi, ossia legno, metallo, metallo verniciato, plastica e gomma. In generale, ogni materiale contiene tutte o la maggior parte delle trame seguenti:

* **Albedo**, che è la mappa colori dei materiali, denominata anche **Diffuse** o **BaseColor**.
* **Metalness**, che determina se un materiale è metallico e quali parti sono metalliche. 
* **Roughness**, che determina quanto è ruvida o liscia una superficie, determinando di conseguenza la nitidezza o la sfocatura dei riflessi e delle aree di massima luce sulla superficie stessa.
* **Normal**, che aggiunge dettagli a una superficie senza bisogno di aggiungere altri poligoni. Esempi di dettaglio possono essere erosioni e ammaccature su una superficie metallica o venature su una superficie in legno.
* **Ambient Occlusion**, che viene usata per aggiungere ombreggiature sfumate e ombre da contatto a un modello. Si tratta di una mappa in scala di grigi che indica quali aree di un modello sono totalmente illuminate (bianche) o totalmente in ombra (nere). 

## <a name="prerequisites"></a>Prerequisiti
* Autodesk Maya 2017 o versioni successive

## <a name="set-up-materials-in-the-scene"></a>Configurare i materiali nella scena
Ecco come configurare un materiale PBR in Maya.

Come si vedrà nella scena di esempio, sono stati creati alcuni oggetti scatola. Ogni oggetto rappresenta un tipo di materiale diverso. Come si può notare nell'immagine, a ognuno di questi oggetti è stato assegnato un nome appropriato.

Rendering remoto di Azure usa il metro per la misurazione e la direzione verso l'alto è l'asse Y. Prima di iniziare a creare risorse, è consigliabile impostare le unità della scena in Maya sul metro. Per l'esportazione, impostare le unità sul metro nelle impostazioni di esportazione FBX.

> [!TIP]
> Assegnare agli asset del modello nomi appropriati in base al nome della parte o del tipo di materiale pertinente. I nomi significativi semplificano la navigazione delle scene che contengono molti oggetti.

![Nomi degli oggetti](media/object-names.jpg)

Dopo aver creato o acquisito alcune trame, è anche possibile crearne di personalizzate. A questo scopo è possibile usare app come Quixel Suite, PhotoShop o Substance Suite oppure ottenere trame di rivestimento generiche da altre origini.

Per applicare trame al modello:

1. Nel riquadro di visualizzazione della scena fare clic con il pulsante destro del mouse sul modello o sulla geometria. Nel menu visualizzato scegliere **Assign New Material** (Assegna nuovo materiale).
1. Nella finestra di dialogo **Assign New Material** passare a **Maya** > **Stingray PBS**. Questa azione assegna un materiale PBR al modello. 

In Maya 2020 sono disponibili diversi shader PBR, tra cui **Maya Standard Surface**, **Arnold Standard Surface** e **Stingray PBR**. Lo **shader Maya Standard Surface** non è ancora esportabile tramite il plug-in FBX 2020. Lo **shader Arnold Standard Surface** può essere esportato con i file FBX. Per la maggior parte degli altri aspetti, è identico allo **shader Maya Standard Surface**. È analogo a **Physical Material** in 3D Studio Max.

Lo **shader Stingray PBR** è compatibile con molte altre applicazioni e soddisfa in gran parte i requisiti di Rendering remoto di Azure. È supportato a partire da Maya 2017. Questo tipo di materiale viene visualizzato nel riquadro di visualizzazione in modo simile a come apparirà successivamente in Rendering remoto di Azure.

![Materiale Stingray](media/stingray-material.jpg)

Dopo aver assegnato un materiale e un nome appropriato all'asset, è possibile assegnare le varie trame. Le immagini seguenti mostrano dove ogni tipo di trama si adatta al materiale PBR. Il materiale Stingray PBR consente di selezionare gli attributi che è possibile attivare. Prima di inserire le mappe delle trame, è necessario attivare gli attributi pertinenti.

![Configurazione del materiale](media/material-setup.jpg)

Assegnare ai materiali nomi appropriati tenendo in considerazione il loro utilizzo o il tipo. Un materiale usato esclusivamente in un'area potrebbe prendere il nome di quell'area. Un materiale usato in una varietà di aree potrebbe prendere il nome di una sua proprietà o del tipo.

Assegnare le trame come illustrato nell'immagine.

![Configurazione della trama](media/texture-setup.jpg)

Una volta creati e configurati i materiali PBR, considerare la [creazione di istanze degli oggetti](../../how-tos/conversion/configure-model-conversion.md#instancing) nella scena. La creazione di istanze di oggetti simili nella scena, ad esempio dadi, bulloni, viti e rondelle, può contribuire a ridurre notevolmente le dimensioni dei file. A ogni istanza di un oggetto master è possibile assegnare una scala, una rotazione e un set di trasformazioni specifiche, in modo da poterla inserire nella scena in base alle esigenze. 

Il processo di creazione di istanze in Maya è semplice.

1. Nel menu **Edit** (Modifica) passare a **Duplicate Special** (Duplica speciali) per aprire le opzioni.
1. Nella finestra di dialogo **Duplicate Special Options** (Duplica opzioni speciali) per **Geometry type** (Tipo di geometria) selezionare l'opzione **Instance** (Istanza). 
1. Selezionare **Duplicate Special** (Duplica speciali).

   ![Creazione di istanze](media/instancing.jpg)

Verrà creata un'istanza dell'oggetto. È possibile spostarla, ruotarla o ridimensionarla indipendentemente dall'oggetto padre e da altre istanze di tale oggetto padre. 

Le eventuali modifiche apportate a un'istanza in modalità componente verranno trasmesse a tutte le istanze dell'oggetto, pertanto se si usano istanze di componenti dell'oggetto, come vertici o facce di poligoni, assicurarsi di voler effettivamente applicare le modifiche a tutte queste istanze. 

Nella scena di esempio è stata creata un'istanza di ogni singolo oggetto scatola. Questa azione avrà rilevanza quando si esporterà la scena in formato FBX.

![Panoramica della scena](media/scene-overview.jpg)

> [!TIP]
> Creare le istanze man mano che si procede, in quanto la sostituzione delle copie con istanze degli oggetti in un secondo momento è estremamente difficile. 

## <a name="fbx-export-process"></a>Processo di esportazione FBX

Si procederà ora con l'esportazione in formato FBX della scena o dei relativi asset. È opportuno selezionare nella scena solo gli oggetti o gli asset che si vogliono esportare. Se ad esempio una scena contiene 100 oggetti ma se ne vogliono usare solo 30, non ha senso esportare l'intera scena. 

Per effettuare la selezione:

1. Passare a **File** > **Export Selection** (Esporta selezione) per aprire la finestra di dialogo **Export Selection**.
1. Nella casella **Files of type** (Tipo di file) selezionare **FBX export** (Esportazione FBX) per visualizzare le impostazioni di esportazione FBX. Le impostazioni principali per l'esportazione FBX sono evidenziate in rosso nell'immagine.

   ![Esportazione FBX](media/FBX-exporting.jpg)

A seconda delle esigenze, ad esempio se si vuole inviare un asset a un cliente ma senza inviare un numero elevato di file di trama insieme all'asset, è possibile scegliere di incorporare le trame all'interno del file FBX esportato. Questa opzione prevede la creazione di un pacchetto costituito da un solo file, ma le dimensioni dell'asset FBX aumentano notevolmente. È possibile abilitare l'opzione di incorporamento delle trame selezionando l'opzione **Embed Media** (Incorpora file multimediali) come illustrato.

> [!TIP]
> In questo esempio al file è stato assegnato un nome che riflette questa condizione. Questo stile di denominazione è un modo efficace per tenere traccia degli asset. 

Dopo aver impostato la configurazione per l'esportazione, selezionare **Export Selection** (Esporta selezione) in basso a destra.

![Incorporamento di file multimediali](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusioni

In generale, questo tipo di materiale ha un aspetto più realistico perché è basato sulla fisica della luce. Crea un ulteriore effetto immersivo che fa percepire la scena come realmente esistente nel mondo reale.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come configurare materiali con illuminazione avanzata per gli oggetti di una scena e come esportare gli oggetti nel formato FBX, che è supportato da Rendering remoto di Azure. Il passaggio successivo consiste nel convertire il file FBX e visualizzarlo in Rendering remoto di Azure.

> [!div class="nextstepaction"]
> [Avvio rapido: Convertire un modello per il rendering](../../quickstarts\convert-model.md)