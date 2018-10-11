---
title: Informazioni su Project Acoustics
titlesuffix: Azure Cognitive Services
description: Il plug-in per Unity Project Acoustics fornisce occlusione, riverbero e spazializzazione per i progetti destinati a schermi VR e tradizionali.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 6a3c97ead40cfb24604edac0624e38a9b0713fc0
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901025"
---
# <a name="what-is-project-acoustics"></a>Informazioni su Project Acoustics
Il plug-in per Unity Project Acoustics fornisce occlusione, riverbero e spazializzazione per i progetti destinati a schermi VR e tradizionali. Offre un modo per progettare l'acustica dei giochi adattando l'intento dei progettisti a una simulazione delle onde basata sulla fisica.

## <a name="why-use-acoustics-in-virtual-environments"></a>Perché usare l'acustica negli ambienti virtuali?
Per comprendere il mondo che li circonda, gli esseri umani usano stimoli audiovisivi. Nei mondi virtuali, la combinazione di audio spaziale e acustica aumenta l'immersività dell'esperienza. Lo strumento per l'acustica descritto in questo articolo analizza i mondi virtuali per creare una simulazione acustica realistica e supporta un processo di progettazione post-simulazione. L'analisi include sia la geometria che i materiali per ogni superficie del mondo. La simulazione include parametri come direzione di arrivo (portaling, generazione di portali), potenza del riverbero, tempi di decadimento ed effetti di occlusione e ostruzione.

## <a name="how-does-this-approach-to-acoustics-work"></a>Come funziona questo approccio all'acustica?
Il sistema si basa su un'elaborazione offline del mondo virtuale, che consente una simulazione più complessa rispetto all'esecuzione dell'analisi in fase di esecuzione. L'elaborazione offline produce una tabella di ricerca di parametri acustici. Il progettista specifica le regole applicate ai parametri in fase di esecuzione. Ottimizzando queste regole è possibile ottenere effetti iper-realistici per un elevato impatto emotivo o scene ipo-realistiche per una maggiore presenza dei suoni di sottofondo.

## <a name="design-process-comparison"></a>Confronto del processo di progettazione
Il plug-in Project Acoustics supporta un nuovo processo di progettazione per l'acustica nelle scene di Unity. Per illustrare questo nuovo processo di progettazione, è possibile confrontarlo con uno degli approcci all'acustica attualmente più diffusi.

### <a name="typical-approach-to-acoustics-today"></a>Approccio attuale tipico all'acustica
In un approccio tipico all'acustica, oggi si disegnano i volumi di riverbero:

![Visualizzazione progettazione](media/reverbZonesAltSPace2.png)

Quindi si regolano i parametri per ogni zona:

![Visualizzazione progettazione](media/TooManyReverbParameters.png)

Infine, si aggiunge la logica di ray tracing per ottenere i filtri giusti per occlusioni e ostruzioni in tutta la scena e la logica di ricerca del percorso per la generazione di portali. Questo codice può accrescere i costi in fase di esecuzione. Inoltre, presenta problemi con lo smussamento degli angoli e casi limite nelle scene di forma irregolare.

### <a name="an-alternative-approach-with-physics-based-design"></a>Un approccio alternativo con la progettazione basata sulla fisica
Con l'approccio offerto dal plug-in per Unity Project Acoustics, si forniscono la forma e i materiali per una scena statica. Poiché la scena è voxelizzata e il processo non usa il ray tracing, non è necessario specificare una mesh acustica semplificata o chiusa. Inoltre non è necessario specificare i volumi di riverbero per la scena. Il plug-in carica la scena nel cloud, in cui usa la simulazione delle onde basata sulla fisica. Il risultato viene integrato nel progetto sotto forma di tabella di ricerca e può essere modificato per effetti estetici o di gioco.

![Visualizzazione progettazione](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>Requisiti
* Unity 2018.2+ per i bake dell'acustica e Unity 5.2+ per la progettazione del suono e la distribuzione
* Editor Unity per Windows 64 bit
* Sottoscrizione di Azure Batch per i bake dell'acustica
* Il runtime di scripting di Unity deve essere impostato su ".NET 4.x equivalent"

## <a name="platform-support"></a>Supporto delle piattaforme
* Windows Desktop (x86 e AMD64)
* Windows UWP (x86, AMD64 e ARM)
* Android (x86 e ARM64)

## <a name="download"></a>Download
Se si è interessati a valutare il plug-in di acustica, registrarsi [qui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) per partecipare alla Designer Preview.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [processo di progettazione](design-process.md)
* Iniziare a [integrare l'acustica nei progetti Unity](getting-started.md)

