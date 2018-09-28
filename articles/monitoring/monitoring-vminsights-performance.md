---
title: Creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali | Microsoft Docs
description: Prestazioni è una funzionalità di Monitoraggio di Azure per le macchine virtuali che rileva automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione tra i servizi. Questo articolo fornisce informazioni dettagliate su come utlizzare questa funzionalità in un'ampia gamma di scenari.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: magoedte
ms.openlocfilehash: 06073197254245727cfa41020f060d904a4e50f9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957563"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali
Monitoraggio di Azure per le macchine virtuali include un set di grafici delle prestazioni che rappresentano diversi indicatori di prestazioni chiave (KPI) per stabilire l'efficacia delle prestazioni di una macchina virtuale. I grafici mostrano l'utilizzo delle risorse in un periodo di tempo per consentire di identificare colli di bottiglia e anomalie o passare a una prospettiva che elenchi ogni macchina per visualizzare l'utilizzo delle risorse in base alla metrica selezionata. Sebbene esistano numerosi elementi da considerare riguardo alle prestazioni, Monitoraggio di Azure per le macchine virtuali è incentrato sul sistema operativo attraverso processore, memoria, schede di rete e dischi. Prestazioni si integra alla funzionalità di monitoraggio dell'integrità e consente di esporre i problemi che indicano un possibile errore dei componenti di sistema, agevolare l'ottimizzazione per aumentare l'efficienza o supportare la pianificazione della capacità.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Prospettiva di più macchine virtuali da Monitoraggio di Azure
Da Monitoraggio di Azure, la funzionalità Prestazioni offre una vista di più macchine virtuali per tutte le macchine virtuali monitorate distribuite tra più gruppi di risorse nelle sottoscrizioni o nell'ambiente in uso.  Per accedere a Monitoraggio di Azure, eseguire le operazioni seguenti. 

1. Nel portale di Azure selezionare **Monitor**. 
2. Scegliere **Macchine virtuali (anteprima)** nella sezione **Soluzioni**.
3. Selezionare la scheda **Prestazioni**.

![Visualizzazione elenco Primi N in Prestazioni di Informazioni dettagliate macchina virtuale](./media/monitoring-vminsights-performance/vminsights-performance-aggview-01.png)

Nella scheda **Primi N grafici**, se si dispone di più aree di lavoro di Log Analytics, scegliere quella integrata alla soluzione dal selettore **Area di lavoro** nella parte superiore della pagina.  Selezionare quindi dal selettore **Gruppo**, una sottoscrizione, un gruppo di risorse o una macchina specifica, in un periodo di tempo specificato.  Per impostazione predefinita, i grafici mostrano le ultime 24 ore.  Usando il selettore **Intervallo di tempo**, è possibile eseguire una query per gli intervalli di tempo cronologici di un massimo di 30 giorni per esaminare le prestazioni di un periodo precedente.   

I cinque grafici dell'utilizzo della capacità visualizzati nella pagina sono:

* % utilizzo CPU: mostra le prime cinque macchine con il valore medio più alto di utilizzo del processore 
* Memoria disponibile: mostra le prime cinque macchine con il valore medio più basso di quantità di memoria disponibile 
* % spazio su disco logico utilizzato: mostra le prime cinque macchine con il valore medio più alto di percentuale di spazio su disco in tutti i volumi del disco 
* Frequenza byte inviati: mostra le prime cinque macchine con la media più alta di byte inviati 
* Frequenza byte ricevuti: mostra le prime cinque macchine con la media più alta di byte ricevuti 

Facendo clic nell'angolo superiore destro di uno dei cinque grafici si apre la vista **Elenco primi N**.  Qui si vede l'utilizzo delle risorse per tale metrica delle prestazioni per singola macchina virtuale in una visualizzazione elenco e quale macchina ha la tendenza più alta.  

![Visualizzazione Elenco primi N per una metrica delle prestazioni selezionata](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

Quando si fa clic sulla macchina virtuale, il riquadro **Proprietà** si espande a destra per visualizzare le proprietà dell'elemento selezionato, ad esempio le informazioni di sistema segnalate dal sistema operativo, le proprietà della macchina virtuale di Azure e così via. Facendo clic su una delle opzioni nella sezione **Collegamenti rapidi** si viene direttamente reindirizzati a tale funzionalità dalla macchina virtuale selezionata.  

![Riquadro Proprietà macchina virtuale](./media/monitoring-vminsights-performance/vminsights-properties-pane-01.png)

Passare alla scheda **Grafici aggregati** per visualizzare le metriche delle prestazioni filtrate per media o percentile.  

![Visualizzazione aggregata delle prestazioni di Informazioni dettagliate macchina virtuale](./media/monitoring-vminsights-performance/vminsights-performance-aggview-02.png)

Vengono forniti i grafici di utilizzo della capacità seguenti:

* % utilizzo CPU: per impostazione predefinita mostra la media e il 95° percentile 
* Memoria disponibile: per impostazione predefinita mostra la media e il 5° e 10° percentile 
* % spazio su disco logico utilizzato: per impostazione predefinita mostra la media e il 95° percentile 
* Frequenza byte inviati: per impostazione predefinita mostra il numero medio di byte inviati 
* Frequenza byte ricevuti: per impostazione predefinita mostra il numero medio di byte ricevuti

È anche possibile modificare la granularità dei grafici entro l'intervallo di tempo selezionando **Media**, **Min**, **Max**, **50°**, **90°** e **95°** nel selettore del percentile.   

Per visualizzare l'utilizzo delle risorse per singole macchine virtuali e vedere quale macchina ha la tendenza di utilizzo più alta, selezionare la scheda **Elenco primi N**.  La pagina **Elenco primi N** mostra le prime 20 macchine ordinate a partire dalla più utilizzata per il 95° percentile per la metrica *% utilizzo CPU*.  È possibile vedere più macchine selezionando **Carica altro**; i risultati si espandono per mostrare le prime 500 macchine. 

>[!NOTE]
>L'elenco non può visualizzare più di 500 macchine alla volta.  
>

![Esempio di pagina Elenco primi N](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

Per filtrare i risultati in una macchina virtuale specifica nell'elenco, immettere il relativo nome di computer nella casella di testo **Cerca per nome**.  

Per visualizzare l'utilizzo in base a una diversa metrica delle prestazioni, dall'elenco a discesa **Metrica** selezionare **Memoria disponibile**, **% spazio su disco logico utilizzato**, **Byte di rete ricevuti/sec** oppure **Byte di rete inviati/sec** e l'elenco viene aggiornato per mostrare l'utilizzo nell'ambito di tale metrica.  

Selezionando una macchina virtuale dall'elenco si apre il riquadro **Proprietà** sul lato destro della pagina dal quale è possibile selezionare **Dettagli prestazioni**.  Si apre la pagina **Dettagli macchina virtuale** il cui ambito è la macchina virtuale in questione. L'esperienza è analoga all'accesso a Prestazioni di Informazioni dettagliate macchina virtuale direttamente dalla macchina virtuale di Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Visualizzare le prestazioni direttamente da una macchina virtuale di Azure
Per accedere direttamente da una macchina virtuale, eseguire queste operazioni.

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Dall'elenco scegliere una macchina virtuale e nella sezione **Monitoraggio** scegliere **Informazioni dettagliate (anteprima)**.  
3. Selezionare la scheda **Prestazioni**. 

Questa pagina non solo include i grafici di utilizzo delle prestazioni, ma anche una tabella che mostra capacità, utilizzo e media totale di ogni disco logico individuato per ogni misura.  

Vengono forniti i grafici di utilizzo della capacità seguenti:

* % utilizzo CPU: per impostazione predefinita mostra la media e il 95° percentile 
* Memoria disponibile: per impostazione predefinita mostra la media e il 5° e 10° percentile 
* % spazio su disco logico utilizzato: per impostazione predefinita mostra la media e il 95° percentile 
* Operazioni di I/O al secondo disco logico: per impostazione predefinita mostra la media e il 95° percentile
* MB/sec disco logico: per impostazione predefinita mostra la media e il 95° percentile
* % utilizzo massimo disco logico: per impostazione predefinita mostra la media e il 95° percentile
* Frequenza byte inviati: per impostazione predefinita mostra il numero medio di byte inviati 
* Frequenza byte ricevuti: per impostazione predefinita mostra il numero medio di byte ricevuti

![Vista di Prestazioni di Informazioni dettagliate macchina virtuale direttamente dalla macchina virtuale](./media/monitoring-vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come usare la funzionalità di integrità, vedere [Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali](monitoring-vminsights-health.md) oppure per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](monitoring-vminsights-maps.md). 