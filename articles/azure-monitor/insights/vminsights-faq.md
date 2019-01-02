---
title: Domande frequenti su Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Monitoraggio di Azure per le macchine virtuali (anteprima) è una soluzione in Azure che combina il macchina virtuale di Azure. Individua automaticamente i componenti dell'applicazione e le dipendenze con altre risorse ed eseguire il mapping della comunicazione tra di essi. Questo articolo fornisce le risposte alle domande frequenti.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184392"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>Domande frequenti su Monitoraggio di Azure per le macchine virtuali (anteprima)
Questo articolo risponde alle domande frequenti su Monitoraggio di Azure per le macchine virtuali. Per altre domande sulla soluzione, visitare il [forum di discussione di Azure](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>È possibile distribuire macchine virtuali in un'area di lavoro esistente?
Se le macchine virtuali sono già connesse a un'area di lavoro di Log Analytics, è possibile continuare a usare l'area di lavoro quando si distribuiscono le VM in Monitoraggio di Azure per le macchine virtuali. L'area di lavoro deve trovarsi in una delle aree supportate elencate nella sezione "Prerequisiti" di [Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima)](vminsights-onboard.md#prerequisites).

Durante la distribuzione, si configurano i contatori delle prestazioni per l'area di lavoro. In questo modo, le macchine virtuali che inviano dati all'area di lavoro iniziano a raccogliere le informazioni per la visualizzazione e l'analisi in Monitoraggio di Azure per le macchine virtuali. Di conseguenza, verranno visualizzati i dati sulle prestazioni di tutte le macchine virtuali connesse all'area di lavoro selezionata. Le funzionalità di integrità e mappa sono abilitate solo per le macchine virtuali specificate per la distribuzione.

Per altre informazioni sui contatori delle prestazioni abilitati, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima)](vminsights-onboard.md).

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>È possibile distribuire le macchine virtuali in una nuova area di lavoro? 
Se le macchine virtuali non sono attualmente connesse a un'area di lavoro di Log Analytics esistente, è necessario creare una nuova area di lavoro per archiviare i dati. È possibile creare una automaticamente configurando una singola macchina virtuale per Monitoraggio di Azure per le macchine virtuali nel portale di Azure.

Se si sceglie di usare il metodo basato su script, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima)](vminsights-onboard.md). 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Cosa occorre fare se la macchina virtuale invia già informazioni a un'area di lavoro esistente?
Se già si raccolgono dati dalle macchine virtuali, è possibile che sia già stato configurato l'invio di dati a un'area di lavoro di Log Analytics esistente. Se quest'area di lavoro è in una delle aree supportate, è possibile abilitare Monitoraggio di Azure per le macchine virtuali per tale area di lavoro preesistente. Microsoft sta lavorando attivamente per supportare altre aree.

>[!NOTE]
>Microsoft configura i contatori delle prestazioni per l'area di lavoro, con effetto su tutte le macchine virtuali che inviano informazioni all'area di lavoro, anche se non si è scelto di distribuirle in Monitoraggio di Azure per le macchine virtuali. Per altre informazioni sulla configurazione dei contatori delle prestazioni per l'area di lavoro, vedere, vedere la sezione "Configurazione dei contatori delle prestazioni" in [Origini dati per le prestazioni di Windows e Linux in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md). Per informazioni sui contatori configurati per Monitoraggio di Azure per le macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima)](vminsights-onboard.md). 

## <a name="why-did-my-vm-deployment-fail"></a>Perché la distribuzione della macchina virtuale non è riuscita?
Quando si esegue la distribuzione di una macchina virtuale di Azure dal portale di Azure, si verificano gli eventi seguenti:

* Viene creata un'area di lavoro di Log Analytics predefinita, se tale opzione è stata selezionata.
* I contatori delle prestazioni vengono configurati per l'area di lavoro selezionata. Se questo passaggio non riesce, alcuni grafici delle prestazioni e alcune tabelle non mostrano i dati relativi alla macchina virtuale distribuita. Per risolvere il problema, eseguire lo script di PowerShell documentato nella sezione "Eseguire l'abilitazione con PowerShell" di [Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima)](vminsights-onboard.md#enable-with-powershell).
* L'agente di Log Analytics viene installato nelle macchine virtuali di Azure mediante un'estensione VM, se necessario. 
* Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali viene installato nelle macchine virtuali di Azure con un'estensione, se necessario. 
* I componenti di Monitoraggio di Azure che supportano la funzionalità Integrità vengono configurati, se necessario, e la macchina virtuale viene configurata per l'invio dei dati sull'integrità.

Durante la distribuzione viene controllato lo stato di ciascun passaggio, visualizzando una notifica nel portale. La configurazione dell'area di lavoro e l'installazione dell'agente richiedono in genere 5-10 minuti. La visualizzazione dei dati di monitoraggio e sull'integrità nel portale di Azure richiede altri 5-10 minuti. 

Se la distribuzione è stata avviata e vengono visualizzati dei messaggi che indicano che occorre distribuire la macchina virtuale, attendere 30 minuti affinché la macchina virtuale completi il processo. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>I grafici delle prestazioni della macchina virtuale non contengono alcuni dati o non ne contengono affatto
Se nella tabella del disco o nei grafici delle prestazioni non compaiono i dati sulle prestazioni, è possibile che i contatori delle prestazioni non siano stati configurati nell'area di lavoro. Per risolvere il problema, eseguire lo script di PowerShell documentato nella sezione "Eseguire l'abilitazione con PowerShell" di [Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima)](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>In cosa differisce la funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali da Mapping dei servizi?
La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali si basa su Mapping dei servizi, ma presenta le differenze seguenti:

* È possibile accedere alla visualizzazione della mappa dal pannello della macchina virtuale e da Monitoraggio di Azure per le macchine virtuali in Monitoraggio di Azure.
* Le connessioni nella mappa ora sono selezionabili e mostrano i dati delle metriche di connessione nel pannello laterale.
* È disponibile una nuova API di creazione mappe, che supporta meglio le mappe più complesse.
* Le macchine virtuali monitorate sono ora incluse nel nodo del gruppo client e il grafico ad anello mostra la proporzione tra le macchine virtuali monitorate e non monitorate. È anche possibile filtrare l'elenco delle macchine quando il gruppo viene espanso.
* Le macchine virtuali monitorate sono ora incluse nei nodi del gruppo di porte server e il grafico ad anello mostra la proporzione tra le macchine virtuali monitorate e non monitorate. È anche possibile filtrare l'elenco delle macchine quando il gruppo viene espanso.
* Lo stile della mappa è stato aggiornato per maggiore coerenza con la mappa delle app di Azure Application Insights.
* I pannelli laterali sono stati aggiornati, ma non dispongono ancora del set completo di integrazioni supportate in Mapping dei servizi: Gestione aggiornamenti, Rilevamento modifiche, Sicurezza e Service Desk. 
* L'opzione per la scelta dei gruppi e delle macchine di cui eseguire il mapping è stata aggiornata. Ora supporta sottoscrizioni, gruppi di risorse, set di scalabilità di macchine virtuali di Azure e servizi cloud.
* Non è possibile creare nuovi gruppi di computer di Mapping dei servizi nella funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali. 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Perché nei grafici delle prestazioni appaiono delle linee tratteggiate?

I grafici delle prestazioni possono visualizzare linee tratteggiate anziché che linee continue per due motivi:
* Potrebbe esserci un vuoto nella raccolta di dati. 

* L'impostazione predefinita per il campionamento dei dati è ogni 60 secondi. È possibile che nel grafico appaiano delle linee tratteggiate se si sceglie un intervallo di tempo più breve per il grafico e la frequenza di campionamento è inferiore alle dimensioni del bucket usate nel grafico. Si supponga di aver scelto una frequenza di campionamento di 10 minuti e che ogni bucket del grafico sia di 5 minuti. In questo caso, se si sceglie di visualizzare un intervallo di tempo più ampio, le linee del grafico appariranno continue e non tratteggiate.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>I gruppi sono supportati con Monitoraggio di Azure per le macchine virtuali?
Sì, dopo aver installato Dependency Agent, vengono raccolte informazioni dalle macchine virtuali per visualizzare i gruppi in base alla sottoscrizione, al gruppo di risorse, ai set di scalabilità di macchine virtuali e ai servizi cloud. Se si usa Mapping dei servizi e sono stati creati gruppi di computer, vengono visualizzati anch'essi. Se per l'area di lavoro che si sta esaminando sono stati creati gruppi di computer, verranno visualizzati nel filtro dei gruppi. 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Come visualizzare i dettagli alla base della linea del 95° percentile nei grafici delle prestazioni in forma aggregata?
Per impostazione predefinita, l'elenco è ordinato in modo da mostrare le macchine virtuali con il valore più elevato per il 95° percentile in relazione alla metrica selezionata. Un'eccezione è rappresentata dal grafico **Memoria disponibile**, che mostra i computer con il valore più basso per il 5° percentile. Selezionare il per aprire la visualizzazione **elenco Primi N** con la metrica appropriata selezionata.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>In che modo la funzionalità di mappa gestisce gli indirizzi IP duplicati su reti virtuali e subnet diverse?
Se si duplicano gli intervalli IP usando macchine virtuali o set di scalabilità di macchine virtuali di Azure su subnet e reti virtuali, la funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali potrebbe mostrare informazioni non corrette. Si tratta di un problema noto ed è in corso l'analisi delle opzioni per migliorare questa esperienza.

## <a name="does-the-map-feature-support-ipv6"></a>La funzionalità di mappa supporta il protocollo IPv6?
La funzionalità di mappa supporta attualmente solo il protocollo IPv4 ed è in corso l'analisi del supporto per IPv6. È supportato anche il protocollo IPv4 con a tunneling all'interno di IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>Perché, quando si carica una mappa per un gruppo di risorse o un altro gruppo di grandi dimensioni, la mappa è di difficile visualizzazione?
Anche se sono stati apportati miglioramenti alla funzionalità di mappa per gestire le configurazioni complesse e di grandi dimensioni, una mappa può contenere molti nodi, connessioni e nodi che funzionano come cluster. Microsoft si impegna a migliorare costantemente il supporto per aumentare la scalabilità.  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>Perché il grafico di rete nella scheda Prestazioni ha un aspetto diverso rispetto al grafico di rete nella pagina di panoramica della macchina virtuale di Azure?

La pagina di panoramica di una macchina virtuale di Azure mostra i grafici basati sulla misurazione dell'attività dell'host nella macchina virtuale guest. Il grafico di rete nella panoramica della macchina virtuale di Azure mostra solo il traffico che verrà fatturato. Questa visualizzazione non include il traffico tra reti virtuali. I dati e i grafici visualizzati per Monitoraggio di Azure per le macchine virtuali si basano sui dati ottenuti dalla macchina virtuale guest e il grafico di rete mostra tutto il traffico TCP/IP in ingresso e in uscita verso tale macchina virtuale, incluso quello tra reti virtuali.

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Quali sono le limitazioni del piano tariffario Gratuito di Log Analytics?
Se Monitoraggio di Azure è stato configurato con un'area di lavoro di Log Analytics che usa il piano tariffario *Gratuito*, la funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali supporta solo cinque computer connessi all'area di lavoro. 

Si supponga ad esempio di avere cinque macchine virtuali connesse a un'area di lavoro gratuita. Se si disconnette una delle macchine virtuali e in un secondo momento si connette una nuova macchina virtuale, la nuova macchina virtuale non viene monitorata e riflessa nella pagina della mappa. In questo scenario, quando si apre la nuova macchina virtuale viene chiesto di usare l'opzione **Prova adesso** e selezionare **Informazioni dettagliate (anteprima)** nel riquadro a sinistra, anche dopo che è stato installato nella macchina virtuale. Non vengono tuttavia mostrate opzioni, come avverrebbe normalmente se la macchina virtuale non fosse stata distribuita in Monitoraggio di Azure per le macchine virtuali. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sui requisiti e i metodi per abilitare il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima)](vminsights-onboard.md).
