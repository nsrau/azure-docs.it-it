---
title: Configurazione di materiali basati su PBR (Physically Based Rendering) in Maya
description: Questa esercitazione spiega come configurare materiali basati su PBR (Physically Based Rendering) in Maya ed esportarli in formato FBX
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977381"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Esercitazione: Configurazione di materiali basati su PBR (Physically Based Rendering) in Maya

## <a name="overview"></a>Panoramica
In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Assegnare materiali con un modello di illuminazione avanzata agli oggetti nella scena.
> * Gestire la creazione di istanze di oggetti e materiali.
> * Esportare una scena in formato FBX selezionando le opzioni appropriate.

La creazione di [materiali PBR (Physically Based Rendering)](../../overview/features/pbr-materials.md) in `Maya` è un'attività relativamente semplice, simile sotto molti aspetti alla configurazione di PBR in altre app per la creazione di contenuti come `3DS Max`. Questa esercitazione è una guida alla configurazione di una funzione shader PBR di base e all'esportazione in formato FBX per i progetti di Rendering remoto di Azure. 

La scena di esempio in questa esercitazione contiene una serie di oggetti `Polygon Box` a cui sono stati assegnati materiali diversi, ossia legno, metallo, metallo verniciato e gomma. In generale, ogni materiale contiene tutte o la maggior parte delle trame seguenti: 

* `Albedo`, che è la mappa colori dei materiali, denominata anche `Diffuse` o `BaseColor`
* `Metalness`, che determina se un materiale è metallico e quali parti sono metalliche. 
* `Roughness`, che determina quanto è ruvida o liscia una superficie, determinando di conseguenza la nitidezza o la sfocatura dei riflessi e delle aree di massima luce sulla superficie stessa.
* `Normals`, che aggiunge dettagli a una superficie, ad esempio erosioni e ammaccature su una superficie metallica o venature su una superficie in legno senza dover aggiungere altri poligoni.
* `Ambient Occlusion`, che viene usata per aggiungere ombreggiature sfumate e ombre da contatto a un modello. Si tratta di una mappa in scala di grigi che indica quali aree di un modello sono totalmente illuminate (bianche) o totalmente in ombra (nere). 

## <a name="prerequisites"></a>Prerequisiti
* `Autodesk Maya 2017` o versione successiva

## <a name="setting-up-materials-in-the-scene"></a>Configurazione dei materiali nella scena
Il processo di configurazione di un materiale PBR in Maya è il seguente:

Per iniziare, come si vedrà nella scena di esempio, sono stati creati alcuni oggetti Box, ognuno dei quali rappresenta un tipo di materiale diverso. Come si può notare nell'immagine seguente, a ognuno di questi oggetti è stato assegnato un nome appropriato. 

> Prima di iniziare a creare asset per Rendering remoto di Azure, vale la pena di sottolineare che questo servizio usa il metro come unità di misura e che la direzione verso l'alto è l'asse y. È quindi consigliabile impostare l'unità di misura della scena in Maya sul metro. Inoltre, ai fini dell'esportazione è consigliabile impostare l'unità di misura sul metro nelle impostazioni di esportazione FBX. 

> [!TIP]
È buona norma assegnare agli asset del modello nomi appropriati, in genere usando il nome della parte o del tipo di materiale pertinente, in quanto i nomi semplificano la navigazione delle scene che contengono molti oggetti.

![Nomi degli oggetti](media/object-names.jpg)

Dopo aver creato o acquisito le trame, a seconda delle esigenze è possibile creare trame esclusive per un modello in un'app come `Quixel Suite`, `Photoshop` o `Substance Suite` oppure usare trame di rivestimento generiche da altre origini e quindi applicarle al modello come indicato di seguito:

* Nel riquadro di visualizzazione della scena fare clic con il pulsante destro del mouse sul modello o sulla geometria. Nel menu visualizzato fare clic su `Assign New Material`.
* Nelle opzioni `Assign New Material` passare a `Maya`>`Stingray PBS`. Questa azione assegna un materiale PBR al modello. 

In `Maya 2020` sono disponibili le funzioni shader PBR `Maya Standard Surface`, `Arnold Standard Surface` e `Stingray PBR`. `Maya Standard Surface Shader` non è ancora esportabile tramite `FBX plugin 2020`, mentre `Arnold Standard Surface Shader` può essere esportata con i file FBX. Per la maggior parte degli altri aspetti è identica a `Maya Standard Surface Shader` ed è analoga a `Physical Material` in `3D Studio Max`.

**`The Stingray PBR Shader`** è compatibile con molte altre applicazioni, soddisfa in gran parte i requisiti di `ARR` ed è supportata a partire da `Maya 2017`. È inoltre utile che questo tipo di materiali venga visualizzato nel riquadro di visualizzazione in modo simile a come verrà visualizzato successivamente in Rendering remoto di Azure.

![Materiale "Stingray"](media/stingray-material.jpg)

Dopo aver assegnato un materiale e un nome appropriato all'asset, è possibile procedere con l'assegnazione delle varie trame. Le immagini seguenti illustrano in dettaglio dove ogni tipo di trama si adatta al materiale PBR. Il materiale `Stingray PBR` consente di selezionare gli attributi che è possibile attivare, quindi prima di poter `plug in` le mappe di trame è necessario attivare gli attributi rilevanti: 

![Configurazione del materiale](media/material-setup.jpg)

> [!TIP]
È consigliabile assegnare ai materiali nomi appropriati, tenendo in considerazione il loro utilizzo e/o il tipo. A un materiale che deve essere usato solo su un'area specifica potrebbe essere assegnato lo stesso nome di quell'area, mentre a un materiale che può essere usato su un numero più ampio di aree può essere assegnato un nome in base alle sue proprietà o al tipo.

Assegnare le trame come indicato di seguito:

![Configurazione della trama](media/texture-setup.jpg)

Una volta creati e configurati i materiali PBR, è opportuno considerare la [creazione di istanze degli oggetti](../../how-tos/conversion/configure-model-conversion.md#instancing) nella scena. La creazione di istanze di oggetti simili nella scena, ad esempio dadi, bulloni e rondelle di viti (essenzialmente, tutti gli oggetti che sono uguali) può contribuire a ridurre notevolmente le dimensioni dei file. A ogni istanza di un oggetto master è possibile assegnare una scala, una rotazione e un set trasformazioni specifiche, in modo da poterla inserire nella scena in base alle esigenze. Il processo di creazione di istanze in Maya è semplice.

* Nel menu `Edit`passare a `Duplicate Special` e aprire `Options`. 
* Nelle opzioni `Duplicate Special` impostare `Geometry Type` da `Copy` a `Instance`. 
* Fare clic su`Duplicate Special`.

![Creazione di istanze](media/instancing.jpg)

Questa azione creerà un'istanza dell'oggetto che può essere spostata, ruotata o ridimensionata indipendentemente dall'oggetto padre e da altre istanze di tale oggetto. 
>Tuttavia, le eventuali modifiche apportate a un'istanza in modalità componente verranno trasmesse a tutte le istanze dell'oggetto, pertanto se si usano istanze di componenti-di oggetti, come vertici o facce di poligoni, assicurarsi innanzitutto di voler effettivamente applicare le modifiche a tutte queste istanze.

Nella scena di esempio è stata creata un'istanza di ogni singolo oggetto Box. Questa azione avrà rilevanza quando si esporterà la scena in formato FBX.

![Panoramica della scena](media/scene-overview.jpg)

>La procedura consigliata per la creazione di istanze nella scena consiste nel crearle man mano che si procede, in quanto la sostituzione di "copie" con istanze degli oggetti in un secondo momento è estremamente difficile. 

## <a name="fbx-export-process"></a>Processo di esportazione FBX

A questo punto è possibile procedere con l'esportazione in formato FBX della scena o dei relativi asset. In generale, nel caso dell'esportazione di asset occorre selezionare solo gli oggetti o gli asset della scena che si vogliono effettivamente esportare. Se una scena contiene 100 oggetti ma se ne vogliono usare solo 30, non ha senso esportare l'intera scena. Pertanto, a meno che non si preferisca esportare tutta la scena, effettuare la selezione e passare a:

* `File` > `Export Selection`, quindi nella parte inferiore della finestra di dialogo di esportazione impostare `Files of Type` su `FBX Export`. Questa finestra contiene le impostazioni di esportazione FBX. Le impostazioni principali per l'esportazione FBX sono evidenziate in rosso nell'immagine seguente.

![Esportazione FBX](media/FBX-exporting.jpg)

A seconda delle esigenze, ad esempio se si vuole inviare un asset a un cliente ma senza inviare un numero elevato di file di trama insieme all'asset, è possibile scegliere di incorporare le trame all'interno del file FBX esportato. Questa opzione prevede la creazione di un pacchetto costituito da un solo file, ma aumenta notevolmente le dimensioni dell'asset FBX. È possibile abilitare l'opzione di incorporamento delle trame attivando l'opzione `Embed Media` come illustrato di seguito.

> [!TIP]
> Come si può notare, in questo caso al file è stato assegnato un nome che riflette questa condizione. È una buona prassi per tenere traccia degli asset. 

Una volta configurata l'esportazione, fare clic sul pulsante "Export Selection" (Esporta selezione) in basso a destra.

![Incorporamento di file multimediali](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusioni

In generale, questo tipo di materiali ha un aspetto più realistico perché è basato sulla fisica della luce. Crea un ulteriore effetto immersivo che fa percepire la scena come realmente esistente nel mondo reale.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata illustrata la funzionalità più importante per assegnare materiali con un modello di illuminazione avanzata agli oggetti di una scena ed esportarla in formato FBX, supportato da Rendering remoto di Azure. Nel passaggio successivo il file FBX verrà convertito e visualizzato in Rendering remoto di Azure.

> [!div class="nextstepaction"]
> [Avvio rapido: Convertire un modello per il rendering](../../quickstarts\convert-model.md)