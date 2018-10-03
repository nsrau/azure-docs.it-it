---
title: Domande frequenti su Acoustics - Servizi cognitivi
description: Questa pagina fornisce risposte alle domande frequenti su Project Acoustics, incluse istruzioni per il download e il processo di bake.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: a71e867bd23cf64b2ac7fc8cd1c54c55d92ce924
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431789"
---
# <a name="frequently-asked-questions"></a>Domande frequenti

## <a name="what-is-project-acoustics"></a>Informazioni su Project Acoustics

Il plug-in per Unity Project Acoustics è un sistema di acustica che calcola il comportamento delle onde sonore prima della fase di esecuzione, come se si trattasse di illuminazione statica. Il cloud svolge le attività più complesse di calcolo della fisica delle onde, pertanto i costi CPU di runtime sono ridotti.  

## <a name="where-can-i-download-the-plugin"></a>Dove si può scaricare il plug-in?

Se si è interessati a valutare il plug-in di acustica, registrarsi [qui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) per partecipare alla Designer Preview.

## <a name="is-azure-used-at-runtime"></a>Azure viene usato in fase di esecuzione?

No, l'integrazione cloud viene usata solo durante la fase di pre-calcolo in quanto parte della configurazione della scena.
 
## <a name="what-is-simulation-input"></a>Qual è l'input della simulazione? 

L'input della simulazione è la scena 3D, l'ambiente virtuale o il livello del gioco. Project Acoustics esegue simulazioni volumetriche delle onde in 3D che modellano rigorosamente la fisica dei suoni, comprese diffusione e occlusione morbida.
 
## <a name="what-is-the-runtime-cost"></a>Quali sono i costi di runtime?

Acoustics utilizza circa lo 0,01% della CPU per ogni sorgente per ogni fotogramma. L'utilizzo della RAM dipende dalle dimensioni della scena e può variare da 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>È necessario semplificare la geometria del livello? Controllare il numero di triangoli? Rendere le mesh chiuse?

No. Il sistema inserirà direttamente la geometria dettagliata del livello. Questa sarà voxelizzata per l'elaborazione interna.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Cosa contiene la tabella di ricerca di runtime?

Il file ACE è una tabella di parametri acustici tra numerose coppie di posizioni della sorgente e dell'ascoltatore
 
## <a name="can-it-handle-moving-sources"></a>È possibile gestire le sorgenti in movimento?

Sì, il plug-in di spazializzazione di Unity **Microsoft Acoustics** consulta la tabella di ricerca in ogni tick di elaborazione audio confrontandola con le posizioni correnti della sorgente e dell'ascoltatore. Il DSP dello spazializzatore aggiorna senza soluzione di continuità i parametri di elaborazione dell'acustica in ogni tick.
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>È possibile gestire geometria dinamica? Porte che si chiudono? Muri che esplodono?

No. I parametri acustici vengono pre-calcolati secondo lo stato statico di un livello di gioco. È consigliabile escludere la geometria delle porte dall'acustica e quindi applicare ulteriore occlusione in base allo stato degli oggetti di gioco mobili e distruttibili usando tecniche collaudate.
 
## <a name="does-it-handle-materials"></a>Gestisce i materiali?

Sì. I materiali vengono prelevati dai nomi dei materiali fisici nel livello, per gestire l'assorbività.
 
## <a name="what-do-the-probes-represent"></a>Cosa rappresentano i "probe"?

Si tratta di campionamenti di possibili posizioni del giocatore. Ogni probe rappresenta una simulazione delle onde distinta della scena, con origine nella posizione del probe. In fase di esecuzione, i parametri acustici relativi alla posizione dell'ascoltatore vengono interpolati dalle posizioni dei probe nelle vicinanze.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Perché eseguire così tanta elaborazione nel cloud? A cosa serve?

Project Acoustics fornisce parametri acustici accurati e affidabili anche per ambienti virtuali estremamente complessi, tenendo conto di tutti gli aspetti dell'architettura. Fornisce occlusione/ostruzione morbide senza bisogno di operazioni manuali e variazione dinamica del riverbero senza bisogno di disegnare i volumi, tutto senza pesare eccessivamente sulla CPU in fase di esecuzione.

## <a name="what-exactly-happens-during-baking"></a>Cosa accade esattamente durante il "baking"?

Il sistema considera le potenziali posizioni del giocatore per generare un set di posizioni di campionamento dei probe distribuite in modo uniforme. Il bake di un livello è costituito da attività indipendenti per ogni probe: il sistema prende in considerazione un cuboide "area di simulazione" con al centro il probe ed esegue una simulazione dettagliata delle onde all'interno di tale area con risoluzione fino a 25 cm.

## <a name="next-steps"></a>Passaggi successivi
* Esplorare la [scena di esempio](sample-walkthrough.md)

