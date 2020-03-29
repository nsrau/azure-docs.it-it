---
title: Domande frequenti sull'acustica del progetto
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
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770204"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Domande frequenti sull'acustica del progetto

## <a name="what-is-project-acoustics"></a>Informazioni su Project Acoustics

La suite di plugin Project Acoustics è un sistema acustico che calcola il comportamento delle onde sonore prima del runtime, simile all'illuminazione statica. Il cloud svolge le attività più complesse di calcolo della fisica delle onde, pertanto i costi CPU di runtime sono ridotti.  

## <a name="where-can-i-download-the-plugin"></a>Dove si può scaricare il plug-in?

È possibile scaricare il [plugin Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346) o il plugin Project [Acoustics Unreal](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Project Acoustics &lt;supporta&gt; la piattaforma x?

Il supporto della piattaforma Project Acoustics si evolve in base alle esigenze del cliente. Contattaci sul forum del [problema Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues) per richiedere informazioni sul supporto per piattaforme aggiuntive.

## <a name="is-azure-used-at-runtime"></a>Azure viene usato in fase di esecuzione?

No, l'integrazione cloud viene usata solo durante la fase di pre-calcolo in quanto parte della configurazione della scena.
 
## <a name="what-is-simulation-input"></a>Qual è l'input della simulazione? 

L'input della simulazione è la scena 3D, l'ambiente virtuale o il livello del gioco. Project Acoustics esegue simulazioni volumetriche delle onde in 3D che modellano rigorosamente la fisica dei suoni, comprese diffusione e occlusione morbida.
 
## <a name="what-is-the-runtime-cost"></a>Quali sono i costi di runtime?

Acoustics utilizza circa lo 0,01% della CPU per ogni sorgente per ogni fotogramma. L'utilizzo della RAM dipende dalle dimensioni della scena e può variare da 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>È necessario semplificare la geometria del livello? Controllare il numero di triangoli? Rendere le mesh chiuse?

No. Il sistema inserirà direttamente la geometria dettagliata del livello. Questa sarà voxelizzata per l'elaborazione interna.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Cosa contiene la tabella di ricerca di runtime?

Il file ACE include una tabella di parametri acustici tra numerose coppie di posizione sorgente e listener, nonché la geometria della scena voxelizzata utilizzata per l'interpolazione dei parametri.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Project Acoustics è in grado di gestire le sorgenti in movimento?

Sì, Project Acoustics consulta la tabella di ricerca e aggiorna il DSP audio su ogni tick, in modo che possa gestire le sorgenti in movimento e il listener.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>L'acustica di progetto è in grado di gestire la geometria dinamica? Porte che si chiudono? Muri che esplodono?

No. I parametri acustici vengono pre-calcolati secondo lo stato statico di un livello di gioco. Suggeriamo di uscire dalla geometria della porta dall'acustica e quindi di applicare un'occlusione aggiuntiva in base allo stato degli oggetti di gioco distruttibili e mobili utilizzando tecniche consolidate.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Project Acoustics utilizza materiali acustici?

Sì. I materiali vengono prelevati dai nomi dei materiali fisici nel livello, per gestire l'assorbività.
 
## <a name="what-do-the-probes-represent"></a>Cosa rappresentano i "probe"?

Si tratta di campionamenti di possibili posizioni del giocatore. Ogni probe rappresenta una simulazione delle onde distinta della scena, con origine nella posizione del probe. In fase di esecuzione, i parametri acustici relativi alla posizione dell'ascoltatore vengono interpolati dalle posizioni dei probe nelle vicinanze.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Perché eseguire così tanta elaborazione nel cloud? A cosa serve?

Project Acoustics fornisce parametri acustici accurati e affidabili anche per ambienti virtuali estremamente complessi, tenendo conto di tutti gli aspetti dell'architettura. Fornisce occlusione e ostruzione fluide e variazione di riverbero dinamico senza il lavoro manuale dei volumi di disegno. tutto senza pesare eccessivamente sulla CPU in fase di esecuzione.

## <a name="what-exactly-happens-during-baking"></a>Cosa accade esattamente durante il "baking"?

Un forno è costituito da simulazioni di onde acustiche di regioni di simulazione cuboidi centrate ad ogni sonda dell'ascoltatore.

## <a name="is-my-source-content-secure"></a>Il contenuto di origine è sicuro?

L'acustica del progetto non carica la geometria della scena di origine nel cloud. Invece, la simulazione opera su una voxelizzazione della scena, che viene combinata con i dati sulla posizione della sonda e memorizzata in un formato proprietario.     

## <a name="next-steps"></a>Passaggi successivi
* Prova il [contenuto di esempio di Unità acustica del progetto](unity-quickstart.md) o il contenuto di esempio [Unreal](unreal-quickstart.md)

