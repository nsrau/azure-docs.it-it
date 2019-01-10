---
title: Panoramica del rendering - Azure Batch
description: Introduzione all'uso di Azure per il rendering e panoramica delle funzionalità di rendering di Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: bc8c96345aeb1886696326edd230666ac8b6c41d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542211"
---
# <a name="rendering-using-azure"></a>Rendering tramite Azure

Il rendering è il processo di conversione di modelli 3D in immagini 2D. I file delle scene 3D vengono creati in applicazioni come Autodesk 3ds Max, Autodesk Maya e Blender.  Le applicazioni di rendering come Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray e Blender Cycles producono immagini 2D.  A volte dai file delle scene vengono create singole immagini. Tuttavia, è prassi comune eseguire la modellazione e il rendering di più immagini e quindi combinarle in un'animazione.

Il carico di lavoro di rendering viene usato in modo intensivo per gli effetti speciali (VFX) nel settore dei media e dell'intrattenimento. Il rendering viene usato anche in molti altri settori, tra cui quelli pubblicitario, manifatturiero, della vendita al dettaglio e di gas e petrolio.

Il processo di rendering usa risorse di calcolo in modo intensivo, in quanto possono essere presenti molti fotogrammi/immagini da produrre e il rendering di ogni immagine può richiedere molte ore.  Di conseguenza, il rendering è un carico di lavoro di elaborazione batch perfetto, che può sfruttare Azure e Azure Batch per eseguire molte attività di rendering in parallelo.

## <a name="why-use-azure-for-rendering"></a>Perché usare Azure per il rendering?

Per molti motivi, il rendering è un carico di lavoro assolutamente ideale per Azure e Azure Batch:

* I processi di rendering possono essere suddivisi in molte parti, che a loro volta possono essere eseguite in parallelo usando diverse macchine virtuali:
  * Le animazioni sono costituite da molti fotogrammi, di ciascuno dei quali è possibile eseguire il rendering in parallelo.  Più sono le macchine virtuali disponibili per elaborare ogni fotogramma, più velocemente possono essere prodotti tutti i fotogrammi e l'animazione.
  * Alcuni software di rendering permettono la suddivisione di singoli fotogrammi in più parti, ad esempio in riquadri o sezioni.  Di ogni parte è possibile eseguire il rendering separatamente, per poi ricombinarle nell'immagine finale al termine di tutte le operazioni.  Più sono le macchine virtuali disponibili, più rapidamente avverrà il rendering di un fotogramma.
* I progetti di rendering possono richiedere una scalabilità elevatissima:
  * Il rendering di singoli fotogrammi, che possono essere anche molto complessi, può richiedere molte ore, anche su hardware di fascia alta. Le animazioni possono essere costituite da centinaia di migliaia di fotogrammi.  Per il rendering di animazioni di qualità elevata, è necessaria una notevole capacità di calcolo in un periodo di tempo ragionevole.  In alcuni casi, sono stati usati oltre 100.000 core per il rendering di migliaia di fotogrammi in parallelo.
* Le attività di rendering sono basate sul progetto e richiedono capacità di calcolo variabili:
  * Allocare la capacità di calcolo e archiviazione nel modo necessario, aumentarla o ridurla in base al carico durante un progetto e rimuoverla al termine del progetto.
  * Pagare per la capacità solo dopo che è stata allocata, evitando di pagare in assenza di carico, ad esempio tra progetti.
  * Predisporre l'ambiente per i picchi dovuti a modifiche impreviste: aumentare la capacità in caso di modifiche impreviste nelle fasi successive di un progetto se queste modifiche devono essere elaborate in tempi ridotti.
* Scegliere tra un'ampia gamma di opzioni hardware in base ad applicazione, carico di lavoro e intervallo di tempo:
  * In Azure è disponibile una vasta gamma di soluzioni hardware che possono essere allocate e gestite con Batch.
  * A seconda del progetto, il requisito più importante può essere il miglior rapporto tra prezzo e prestazioni o migliori prestazioni nel complesso.  Scene e/o applicazioni di rendering diverse avranno requisiti di memoria diversi.  Alcune applicazioni di rendering possono sfruttare le GPU per ottenere prestazioni ottimali o determinate funzionalità. 
* Le macchine virtuali con priorità bassa riducono i costi:
  * Le macchine virtuali con priorità bassa sono ora disponibili a un prezzo notevolmente scontato rispetto alle normali macchine virtuali on demand e sono adatte per alcuni tipi di processo.
  * Le macchine virtuali con priorità bassa possono essere allocate da Azure Batch, che offre flessibilità di utilizzo per soddisfare un ampio insieme di requisiti.  I pool di Batch possono essere costituiti da macchine virtuali sia dedicate sia con priorità bassa, con la possibilità di modificare la combinazione dei tipi di macchina virtuale in qualsiasi momento.

## <a name="options-for-rendering-on-azure"></a>Opzioni per il rendering in Azure

Azure offre un'ampia gamma di funzionalità che possono essere usate per carichi di lavoro di rendering.  La scelta delle funzionalità da usare dipende dall'ambiente esistente e dai requisiti.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Ambiente di rendering locale esistente con un'applicazione di gestione del rendering

Il caso più comune prevede la presenza di una farm di rendering locale esistente gestita da un'applicazione di gestione del rendering come PipelineFX Qube, Royal Render o Thinkbox Deadline.  Il requisito consiste nell'estendere la capacità della farm di rendering locale tramite macchine virtuali di Azure.

Il software di gestione del rendering include il supporto di Azure integrato oppure Microsoft rende disponibili alcuni plug-in che aggiungono tale supporto di Azure. Per altre informazioni sugli strumenti di gestione del rendering supportati e sulle funzionalità abilitate, vedere l'articolo sull'[uso di applicazioni di gestione del rendering](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Flusso di lavoro di rendering personalizzato

Il requisito è che le macchine virtuali estendano una farm di rendering esistente.  I pool di Azure Batch possono allocare numeri elevati di macchine virtuali, permettere l'uso e la scalabilità automatica dinamica di macchine virtuali con priorità bassa con macchine virtuali a prezzo pieno e fornire licenze con pagamento in base al consumo per le applicazioni di rendering più diffuse.

### <a name="no-existing-render-farm"></a>Nessuna farm di rendering esistente

Le workstation client possono eseguire il rendering, ma il carico di lavoro di rendering aumenta e impiega troppo tempo per poter usare unicamente la capacità delle workstation.  È possibile usare Azure Batch per allocare alla farm di rendering capacità di calcolo on demand e pianificare i processi di rendering nella farm di rendering di Azure.

## <a name="azure-batch-rendering-capabilities"></a>Funzionalità di rendering di Azure Batch

Azure Batch permette l'esecuzione di carichi di lavoro paralleli in Azure.  Permette inoltre la creazione e la gestione di un numero elevato di macchine virtuali sulle quali vengono installate ed eseguite le applicazioni.  Offre anche funzionalità di pianificazione dei processi complete per l'esecuzione di istanze nelle applicazioni, fornendo l'assegnazione di attività alle macchine virtuali, l'accodamento, il monitoraggio delle applicazioni e così via.

Azure Batch viene usato per molti carichi di lavoro, ma sono disponibili le funzionalità seguenti per semplificare e accelerare in particolare l'esecuzione dei carichi di lavoro di rendering.

* Immagini di macchina virtuale con applicazioni di rendering e grafica preinstallate:
  * Sono disponibili immagini di macchina virtuale di Azure Marketplace, che contengono applicazioni di grafica e rendering tra le più diffuse, per evitare la necessità di installare personalmente le applicazioni o di creare immagini personalizzate con le applicazioni installate. 
* Licenze con pagamento in base al consumo per applicazioni di rendering:
  * È possibile scegliere di pagare per le applicazioni in tempo reale, oltre al pagamento delle macchine virtuali di calcolo, evitando di dover acquistare licenze ed eventualmente configurare un server licenze per le applicazioni.  Il pagamento in base al consumo significa che è possibile gestire carichi variabili e imprevisti, in quanto il numero di licenze non è fisso.
  * È anche possibile usare le applicazioni preinstallate con licenze proprie, evitando di usare le licenze con pagamento in base al consumo. A tale scopo si installa in genere un server licenze locale o basato su Azure e si usa una rete virtuale di Azure per connettere il pool per il rendering per il server licenze.
* Plug-in per applicazioni di progettazione e modellazione client:
  * I plug-in permettono agli utenti finali di utilizzare Azure Batch direttamente dall'applicazione client, ad esempio Autodesk Maya, per poter creare pool, inviare processi e fare uso di una maggiore capacità di calcolo per eseguire rendering più rapidi.
* Integrazione di applicazioni di gestione del rendering:
  * Azure Batch è integrato in applicazioni di gestione del rendering oppure sono disponibili plug-in per fornire questa integrazione.

È possibile usare Azure Batch in diversi modi, tutti validi anche per il rendering di Azure Batch.

* API:
  * Scrivere codice tramite API [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch) o altre API supportate.  Gli sviluppatori possono integrare funzionalità di Azure Batch nelle applicazioni o nel flusso di lavoro esistenti, nel cloud o in locale.  Ad esempio, il [plug-in Autodesk Maya](https://github.com/Azure/azure-batch-maya) utilizza l'API Phyton per Batch per richiamare Batch, creare e gestire pool, inviare processi e attività e monitorare lo stato.
* Strumenti da riga di comando:
  * Per scrivere script per l'uso di Batch, è possibile usare la [riga di comando di Azure](https://docs.microsoft.com/cli/azure/) o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
  * In particolare, il supporto dei modelli dell'interfaccia della riga di comando di Batch semplifica notevolmente la creazione di pool e l'invio di processi.
* Interfacce utente:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) è uno strumento client multipiattaforma che permette anche la gestione e il monitoraggio degli account di Batch, ma offre alcune funzionalità avanzate rispetto all'interfaccia utente del portale di Azure.  Viene fornito un set di modelli di pool e di processo ottimizzati per ogni applicazione supportata, che possono essere usati per creare pool e inviare processi in tutta semplicità.
  * Il portale di Azure può essere usato per gestire e monitorare Azure Batch.
* Plug-in per le applicazioni client:
  * Sono disponibili plug-in che permettono l'uso del rendering di Batch direttamente dalle applicazioni di progettazione e modellazione client. I plug-in principalmente richiamano l'applicazione Batch Explorer con informazioni contestuali sul modello 3D corrente.
  * Sono disponibili i plug-in seguenti:
    * [Azure Batch per Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Introduzione al rendering di Azure Batch

Per provare il rendering di Azure Batch, vedere le esercitazioni introduttive seguenti:

* [Eseguire il rendering di una scena di Blender con Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Usare l'interfaccia della riga di comando di Batch per eseguire il rendering di una scena di Autodesk 3ds Max](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Passaggi successivi

Determinare l'elenco di applicazioni di rendering e le versioni incluse nelle immagini di macchina virtuale di Azure Marketplace in [questo articolo](https://docs.microsoft.com/azure/batch/batch-rendering-applications).