---
title: Domande frequenti su acustica progetto
titlesuffix: Azure Cognitive Services
description: Questa pagina fornisce risposte alle domande frequenti su Project Acoustics, incluse istruzioni per il download e il processo di bake.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: c43c81d42a39bda504b02eb6c053a16a2cf53aec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138049"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Domande frequenti su acustica progetto

## <a name="what-is-project-acoustics"></a>Informazioni su Project Acoustics

Il gruppo di progetto acustica dei plug-in è un sistema acustica che calcola il comportamento di audio wave prima della fase di esecuzione, simile a illuminazione statico. Il cloud svolge le attività più complesse di calcolo della fisica delle onde, pertanto i costi CPU di runtime sono ridotti.  

## <a name="where-can-i-download-the-plugin"></a>Dove si può scaricare il plug-in?

È possibile scaricare progetto acustica dal [pagina dell'area Download di progetto acustica](https://www.microsoft.com/en-us/download/details.aspx?id=57346).

## <a name="does-project-acoustics-support-x-platform"></a>Supporta progetti acustica <x> piattaforma?

Supporto della piattaforma acustica progetto si evolve in base alle esigenze dei clienti. Contattaci sul [forum di Project acustica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) per richiedere informazioni sul supporto per altre piattaforme.

## <a name="is-azure-used-at-runtime"></a>Azure viene usato in fase di esecuzione?

No, l'integrazione cloud viene usata solo durante la fase di pre-calcolo in quanto parte della configurazione della scena.
 
## <a name="what-is-simulation-input"></a>Qual è l'input della simulazione? 

L'input della simulazione è la scena 3D, l'ambiente virtuale o il livello del gioco. Project Acoustics esegue simulazioni volumetriche delle onde in 3D che modellano rigorosamente la fisica dei suoni, comprese diffusione e occlusione morbida.
 
## <a name="what-is-the-runtime-cost"></a>Quali sono i costi di runtime?

Acoustics utilizza circa lo 0,01% della CPU per ogni sorgente per ogni fotogramma. L'utilizzo della RAM dipende dalle dimensioni della scena e può variare da 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>È necessario semplificare la geometria del livello? Controllare il numero di triangoli? Rendere le mesh chiuse?

 No. Il sistema inserirà direttamente la geometria dettagliata del livello. Questa sarà voxelizzata per l'elaborazione interna.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Cosa contiene la tabella di ricerca di runtime?

Include il file di voce ACE è una tabella di parametri di modello acustici tra numerosi origine e le coppie di percorso del listener, nonché geometria scena voxelized usata per l'interpolazione di parametro.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Progetto acustica può gestire lo spostamento di origini?

Sì, progetto acustica consulta la tabella di ricerca e aggiorna il DSP audio su ciascun tick, in modo che possa gestire lo spostamento di origini e il listener.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Progetto acustica gestibili geometry dinamica? Porte che si chiudono? Muri che esplodono?

 No. I parametri acustici vengono pre-calcolati secondo lo stato statico di un livello di gioco. È consigliabile lasciando lo sportello della geometria da acustica e applicando quindi aggiuntive relative all'occlusione basata sullo stato di distruttibile e gli oggetti gioco mobili usando stabilita tecniche.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Progetto acustica Usa materiali acustici?

Sì. I materiali vengono prelevati dai nomi dei materiali fisici nel livello, per gestire l'assorbività.
 
## <a name="what-do-the-probes-represent"></a>Cosa rappresentano i "probe"?

Si tratta di campionamenti di possibili posizioni del giocatore. Ogni probe rappresenta una simulazione delle onde distinta della scena, con origine nella posizione del probe. In fase di esecuzione, i parametri acustici relativi alla posizione dell'ascoltatore vengono interpolati dalle posizioni dei probe nelle vicinanze.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Perché eseguire così tanta elaborazione nel cloud? A cosa serve?

Project Acoustics fornisce parametri acustici accurati e affidabili anche per ambienti virtuali estremamente complessi, tenendo conto di tutti gli aspetti dell'architettura. Offre smooth occlusione e ostruzione e variazione riverbero dinamico senza interventi manuali dei volumi di disegno. tutto senza pesare eccessivamente sulla CPU in fase di esecuzione.

## <a name="what-exactly-happens-during-baking"></a>Cosa accade esattamente durante il "baking"?

Un bake è costituito da simulazioni wave acustico delle aree di simulazione cuboid centrate al probe ogni listener.

## <a name="next-steps"></a>Passaggi successivi
* Provare il [contenuto di esempio di progetto acustica Unity](unity-quickstart.md) o [contenuto di esempio Unreal](unreal-quickstart.md)

