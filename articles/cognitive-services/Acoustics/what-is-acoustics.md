---
title: Panoramica di Progetto Acustica
titlesuffix: Azure Cognitive Services
description: Progetto Acustica è un motore di acustica per esperienze 3D interattive, che integra la simulazione della fisica delle onde con bake e controlli di progettazione interattivi.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: a654dd966c40b5b079f92b910ade52f4eda10344
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704745"
---
# <a name="what-is-project-acoustics"></a>Informazioni su Project Acoustics
Progetto Acustica è un motore di acustica delle onde per esperienze interattive 3D. Modella effetti d'onda come diffrazione, portaling e riverbero in scene complesse senza markup manuale delle zone. Include anche l'integrazione di motori di gioco e middleware audio. La filosofia di Progetto Acustica è simile all'illuminazione statica: si effettua il bake della fisica dettagliata offline per fornire una baseline fisica e si usa il runtime leggero con controlli di progettazione espressivi per soddisfare gli obiettivi artistici.

![Screenshot di Gears of War 4 che mostra i voxel di Progetto Acustica](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Uso della fisica delle onde per l'acustica interattiva
I metodi di acustica basati su raggi possono verificare l'occlusione usando un singolo cast del raggio da sorgente ad ascoltatore oppure pilotare il riverbero stimando il volume della scena locale con pochi raggi. Ma queste tecniche possono essere inaffidabili perché un sassolino occlude tanto quanto un masso. I raggi non tengono conto del modo in cui il suono devia intorno agli oggetti, un fenomeno noto come diffrazione. La simulazione di Progetto Acustica acquisisce questi effetti con una simulazione basata sulle onde. Il risultato è più prevedibile e affidabile.

L'innovazione chiave di Progetto Acustica consiste nell'abbinare la simulazione acustica ai concetti tradizionali di progettazione del suono. Converte i risultati della simulazione in parametri DSP audio tradizionali per occlusione, portaling e riverbero. Sono disponibili controlli da usare per questo processo di conversione. Per altri dettagli sulle tecnologie di base di Progetto Acustica, vedere la [pagina del progetto di ricerca](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animazione che mostra una sezione 2D orizzontale della propagazione delle onde in una scena](media/wave-simulation.gif)

## <a name="setup"></a>Configurazione
[L'integrazione di Progetto Acustica con Unity](unity-integration.md) avviene mediante trascinamento della selezione e include un plug-in per motore audio Unity. È possibile potenziare i controlli della sorgente audio Unity collegando un componente di controlli C# di Progetto Acustica a ogni oggetto audio.

L'[integrazione di Progetto Acustica con Unreal](unreal-integration.md) include plug-in di editor e gioco per Unreal e un plug-in per mixer Wwise. Un componente audio personalizzato estende le familiari funzionalità di Wwise all'interno di Unreal con i controlli di progettazione dell'acustica dal vivo. I controlli di progettazione sono esposti in Wwise anche nel plug-in del mixer.

## <a name="workflow"></a>Flusso di lavoro
* **Bake preliminare:** iniziare la configurazione del bake selezionando la geometria rispondente all'acustica, ad esempio ignorando i fasci di luce. Modificare quindi le assegnazioni automatiche dei materiali e selezionare le aree di navigazione per pilotare il campionamento dell'ascoltatore. Non si eseguono markup manuali per riverbero/portale/zone dell'ambiente.
* **Bake:** un passaggio dell'analisi viene eseguito localmente, con la voxelizzazione e altre analisi geometriche della scena in base alle selezioni precedenti. I risultati vengono visualizzati nell'editor per verificare la configurazione della scena. Al momento dell'invio del bake, i dati voxel vengono inviati ad Azure e si ottiene una risorsa di gioco acustico.
* **Runtime:** è sufficiente caricare la risorsa nel proprio livello per ascoltare l'acustica nel livello stesso. Progettare l'acustica dal vivo nell'editor usando controlli granulari per ogni sorgente. I controlli possono anche essere gestiti da script di livello.

## <a name="platforms"></a>Piattaforme
I plug-in di runtime di Progetto Acustica possono essere attualmente distribuiti nelle piattaforme seguenti:
* Windows
* Android
* Xbox One

## <a name="download"></a>Download
* [Plug-in ed esempi di Progetto Acustica in Unity](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Plug-in ed esempi di Progetto Acustica in Unreal e Wwise](https://www.microsoft.com/download/details.aspx?id=58090)
  * Per i file binari e il supporto di Xbox, contattare Microsoft tramite l'apposito modulo di iscrizione in basso

## <a name="contact-us"></a>Contatti
* [Report dei problemi e discussioni di Progetto Acustica](https://github.com/microsoft/ProjectAcoustics/issues)
* [Iscriversi per ricevere aggiornamenti su Progetto Acustica](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Passaggi successivi
* Provare un [avvio rapido di Progetto Acustica per Unity](unity-quickstart.md) o [Unreal](unreal-quickstart.md)
* Esaminare la [filosofia di progettazione del suono di Progetto Acustica](design-process.md)

