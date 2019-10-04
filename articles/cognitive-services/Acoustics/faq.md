---
title: Domande frequenti sui progetti acustici
titlesuffix: Azure Cognitive Services
description: Questa pagina fornisce risposte alle domande frequenti su Project Acoustics, incluse istruzioni per il download e il processo di bake.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: f0484fc9fc2af5514ba0f5b61277146a51757057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855036"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Domande frequenti sui progetti acustici

## <a name="what-is-project-acoustics"></a>Informazioni su Project Acoustics

Il progetto Acoustics suite di plug-in è un sistema acustico che calcola il comportamento delle frequenze acustiche prima del runtime, analogamente all'illuminazione statica. Il cloud svolge le attività più complesse di calcolo della fisica delle onde, pertanto i costi CPU di runtime sono ridotti.  

## <a name="where-can-i-download-the-plugin"></a>Dove si può scaricare il plug-in?

È possibile scaricare il [plug](https://www.microsoft.com/download/details.aspx?id=57346) -in del progetto Acoustics Unity o il plug-in [Acoustics Unreal del progetto](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Il progetto Acoustics supporta &lt;la&gt; piattaforma x?

Il supporto della piattaforma acustica del progetto si evolve in base alle esigenze dei clienti. Per ulteriori informazioni sul supporto di piattaforme aggiuntive, contattare [Microsoft nel forum del problema](https://github.com/microsoft/ProjectAcoustics/issues) relativo all'acustica del progetto.

## <a name="is-azure-used-at-runtime"></a>Azure viene usato in fase di esecuzione?

No, l'integrazione cloud viene usata solo durante la fase di pre-calcolo in quanto parte della configurazione della scena.
 
## <a name="what-is-simulation-input"></a>Qual è l'input della simulazione? 

L'input della simulazione è la scena 3D, l'ambiente virtuale o il livello del gioco. Project Acoustics esegue simulazioni volumetriche delle onde in 3D che modellano rigorosamente la fisica dei suoni, comprese diffusione e occlusione morbida.
 
## <a name="what-is-the-runtime-cost"></a>Quali sono i costi di runtime?

Acoustics utilizza circa lo 0,01% della CPU per ogni sorgente per ogni fotogramma. L'utilizzo della RAM dipende dalle dimensioni della scena e può variare da 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>È necessario semplificare la geometria del livello? Controllare il numero di triangoli? Rendere le mesh chiuse?

No. Il sistema inserirà direttamente la geometria dettagliata del livello. Questa sarà voxelizzata per l'elaborazione interna.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Cosa contiene la tabella di ricerca di runtime?

Il file ACE include è una tabella di parametri acustici tra numerose coppie di posizioni di origine e di listener, nonché la geometria della scena voxelized usata per l'interpolazione dei parametri.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Il progetto Acoustics può gestire le origini mobili?

Sì, il progetto Acoustics consulta la tabella di ricerca e aggiorna il DSP audio a ogni ciclo, in modo da poter gestire le origini e il listener in stato di trasferimento.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>I progetti acustici possono gestire la geometria dinamica? Porte che si chiudono? Muri che esplodono?

No. I parametri acustici vengono pre-calcolati secondo lo stato statico di un livello di gioco. È consigliabile lasciare la geometria dello sportello fuori dall'acustica e quindi applicare un'occlusione aggiuntiva in base allo stato degli oggetti del gioco distruttibili e mobili usando tecniche stabilite.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Il progetto Acoustics usa materiali acustici?

Sì. I materiali vengono prelevati dai nomi dei materiali fisici nel livello, per gestire l'assorbività.
 
## <a name="what-do-the-probes-represent"></a>Cosa rappresentano i "probe"?

Si tratta di campionamenti di possibili posizioni del giocatore. Ogni probe rappresenta una simulazione delle onde distinta della scena, con origine nella posizione del probe. In fase di esecuzione, i parametri acustici relativi alla posizione dell'ascoltatore vengono interpolati dalle posizioni dei probe nelle vicinanze.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Perché eseguire così tanta elaborazione nel cloud? A cosa serve?

Project Acoustics fornisce parametri acustici accurati e affidabili anche per ambienti virtuali estremamente complessi, tenendo conto di tutti gli aspetti dell'architettura. Fornisce un'occlusione uniforme e un'ostruzione e una variazione dinamica dei riverberi senza il lavoro manuale di creazione di volumi. tutto senza pesare eccessivamente sulla CPU in fase di esecuzione.

## <a name="what-exactly-happens-during-baking"></a>Cosa accade esattamente durante il "baking"?

Un Bake è costituito da simulazioni Wave acustiche di aree di simulazione Cuboid centrate su ogni Probe del listener.

## <a name="next-steps"></a>Passaggi successivi
* Provare il [contenuto di esempio del progetto Acoustics Unity](unity-quickstart.md) o un [contenuto di esempio non reale](unreal-quickstart.md)

