---
title: Come visualizzare le dipendenze delle app con Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Mappa è una funzionalità di Monitoraggio di Azure per le macchine virtuali che rileva automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione tra i servizi. Questo articolo contiene informazioni dettagliate su come usare la funzionalità in un'ampia gamma di scenari.
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
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 73c94b79bb315c317d39ade704e2ee70a241a348
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187746"
---
# <a name="using-azure-monitor-for-vms-preview-map-to-understand-application-components"></a>Uso della mappa di Monitoraggio di Azure per le macchine virtuali (anteprima) per comprendere i componenti delle applicazioni
I componenti di applicazione individuati in macchine virtuali Windows e Linux in esecuzione in Azure nell'ambiente dell'utente possono essere visualizzati in due modi con Monitoraggio di Azure per le macchine virtuali, ovvero direttamente da una macchina virtuale o tra gruppi di macchine virtuali in Monitoraggio di Azure. 

Questo articolo descrive la diversa esperienza tra le due prospettive e il modo in cui usare la funzionalità Mappa. Per informazioni sulla configurazione di Monitoraggio di Azure per le macchine virtuali, vedere [Enable Azure Monitor for VMs](vminsights-onboard.md) (Abilitare Monitoraggio di Azure per le macchine virtuali).

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="introduction-to-map-experience"></a>Introduzione all'esperienza Mappa
Prima di approfondire l'uso della funzionalità Mappa per una singola macchina virtuale o per un gruppo di macchine virtuali, è importante descrivere in breve la funzionalità per comprendere il modo in cui vengono presentate le informazioni e su ciò che rappresentano le visualizzazioni.  

L'esperienza è coerente sia che si selezioni la funzionalità Mappa direttamente da una macchina virtuale sia che la si selezioni in Monitoraggio di Azure.  L'unica differenza consiste nel fatto che in Monitoraggio di Azure è possibile visualizzare tutti i membri di un'applicazione multilivello o di un cluster in una mappa.

Le mappe consentono di visualizzare le dipendenze delle macchine virtuali individuando i processi in esecuzione con connessioni di rete attive tra server, latenza di connessioni in ingresso e in uscita e porte tra le architetture connesse tramite TCP in un intervallo di tempo specifico.  L'espansione di una macchina virtuale mostra i dettagli dei processi e vengono visualizzati solo i processi che comunicano con la macchina virtuale. Il numero di client front-end che si connettono alla macchina virtuale è indicato con il gruppo di client. Il numero di server back-end a cui la macchina virtuale è connessa è indicato nei gruppi di porte del server. Espandere un gruppo di porte di server per visualizzare l'elenco dettagliato dei server collegati tramite la porta.  

Quando si fa clic sulla macchina virtuale, il riquadro **Proprietà** viene espanso a destra per visualizzare le proprietà dell'elemento selezionato, ad esempio le informazioni di sistema segnalate dal sistema operativo, le proprietà della macchina virtuale di Azure e un grafico ad anello che riepiloga le connessioni individuate. 

![Proprietà di sistema del computer](./media/vminsights-maps/properties-pane-01.png)

Sul lato destro del riquadro fare clic sull'icona **Registra eventi** per trasferire lo stato attivo del riquadro in modo da visualizzare un elenco di tabelle con i dati raccolti dalla macchina virtuale, inviati a Log Analytics e disponibili per l'esecuzione di query.  Se si fa clic su uno dei tipi di record elencati, viene visualizzata la pagina **Log** che contiene i risultati per tale tipo con una query preconfigurata filtrata rispetto alla macchina virtuale specifica.  

![Elenco di ricerca log nel riquadro Proprietà](./media/vminsights-maps/properties-pane-logs-01.png)

Chiudere *Log** e tornare al riquadro **Proprietà** in cui selezionare **Avvisi** per visualizzare gli avvisi generati per la macchina virtuale in base ai criteri di integrità. Gli avvisi Azure integrati in Mappe consentono di visualizzare gli avvisi attivati per un determinato server nell'intervallo di tempo selezionato. Se sono presenti avvisi, viene visualizzata un'icona e gli avvisi vengono elencati nel riquadro specifico del computer. 

![Avvisi del computer nel riquadro Proprietà](./media/vminsights-maps/properties-pane-alerts-01.png)

Per abilitare la funzionalità Mappe per visualizzare gli avvisi relativi, creare una regola di avviso da attivare per un computer specifico. Per creare gli avvisi appropriati:
- Includere una clausola che consenta di raggruppare per computer, ad esempio **per intervallo computer 1 minuto**.
- Scegliere di impostare un avviso in base alle metriche misurate.

Per altre informazioni su Avvisi si Azure e sulla creazione di regole di avviso, vedere [Avvisi unificati in Monitoraggio di Azure](../../azure-monitor/platform/alerts-overview.md).

L'opzione **Legenda** nell'angolo superiore destro descrive i simboli e i ruoli su una mappa.  Per ingrandire la mappa per esaminarla meglio, i controlli di zoom nella parte inferiore destra della pagina impostano il livello di zoom e adattano la pagina alle dimensioni di quella corrente.  

## <a name="connection-metrics"></a>Metriche di connessione
Nel riquadro **Connessioni** vengono visualizzate le metriche di connettività standard per la connessione selezionata dalla macchina virtuale sulla porta TCP. Le metriche includono tempo di risposta, richieste al minuto, velocità effettiva del traffico e collegamenti.  

![Esempio di riquadro dei grafici di connettività di rete](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Connessioni non riuscite
Le connessioni a processi e computer non riuscite sono visualizzate nella mappa con una linea rossa tratteggiata che indica se un sistema client non è in grado di raggiungere un processo o una porta. Vengono visualizzate le connessioni non riuscite di un sistema con l'agente di dipendenza, a condizione che il sistema corrisponda a quello che esegue la connessione non riuscita. La funzionalità Mappa misura il processo osservando i socket TCP che non riescono a stabilire una connessione. Questo errore potrebbe essere causato da un firewall, da un errore di configurazione del client o server oppure da un servizio remoto non disponibile.

![Esempio di connessione non riuscita sulla mappa](./media/vminsights-maps/map-group-failed-connection-01.png)

Le informazioni sulle connessioni non riuscite facilitano la risoluzione dei problemi, la convalida della migrazione, l'analisi di sicurezza e la comprensione dell'architettura complessiva del servizio. A volte le connessioni non riuscite non offrono informazioni utili, ma spesso indicano direttamente un problema, ad esempio un ambiente di failover che diventa improvvisamente non raggiungibile oppure due livelli applicazione che non sono in grado di comunicare tra loro dopo una migrazione cloud.

### <a name="client-groups"></a>Gruppi di client
I gruppi di client sulla mappa rappresentano computer client con connessioni al computer mappato. Un singolo gruppo di client rappresenta i client per un unico processo o computer.

![Esempio di gruppi di client sulla mappa](./media/vminsights-maps/map-group-client-groups-01.png)

Per visualizzare i client monitorati e gli indirizzi IP dei sistemi in un gruppo di client, selezionare il gruppo. I contenuti del gruppo sono elencati nell'area sotto il gruppo.  

![Esempio di elenco di indirizzi IP del gruppo di client sulla mappa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se il gruppo include client monitorati e non monitorati, è possibile selezionare la sezione appropriata del grafico ad anello nel gruppo per filtrare i client.

### <a name="server-port-groups"></a>Gruppi di porte di server
I gruppi di porte di server rappresentano le porte dei server dotati di connessioni in ingresso dal computer mappato. Il gruppo contiene la porta del server con il numero di server con connessioni a tale porta. Selezionare il gruppo per visualizzare i singoli server e le connessioni elencate. 

![Esempio di gruppo di porte di server sulla mappa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se il gruppo include server monitorati e non monitorati, è possibile selezionare la sezione appropriata del grafico ad anello nel gruppo per filtrare i server.

## <a name="view-map-directly-from-a-virtual-machine"></a>Visualizzare la mappa direttamente da una macchina virtuale 

Per accedere a Monitoraggio di Azure per le macchine virtuali direttamente da una macchina virtuale, eseguire queste operazioni.

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Nell'elenco scegliere una macchina virtuale e nella sezione **Monitoraggio** scegliere **Informazioni dettagliate (anteprima)**.  
3. Selezionare la scheda **Mappa**.

La mappa consente di visualizzare le dipendenze delle macchine virtuali, ovvero i gruppi di processi in esecuzione e i processi con connessioni di rete attive in un intervallo di tempo specifico.  Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti.  Grazie al selettore **TimeRange** nell'angolo superiore a sinistra, è possibile cercare intervalli di tempo cronologici della durata massima di un'ora per visualizzare l'aspetto delle dipendenze nel passato, ad esempio durante un evento imprevisto o prima di una modifica.  

![Panoramica di mappe direttamente da macchina virtuale](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Visualizzare la mappa da Monitoraggio di Azure
In Monitoraggio di Azure la funzionalità Mappa offre una visualizzazione globale delle macchine virtuali e delle relative dipendenze.  Per accedere alla funzionalità Mappa da Monitoraggio di Azure, eseguire queste operazioni. 

1. Nel portale di Azure selezionare **Monitoraggio**. 
2. Scegliere **Macchine virtuali (anteprima)** nella sezione **Informazioni dettagliate**.
3. Selezionare la scheda **Mappa**.

![Panoramica delle mappe di più macchine virtuali in Monitoraggio di Azure](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Nel selettore **Area di lavoro** nella parte superiore della pagina, se sono disponibili più aree di lavoro di Log Analytics scegliere l'area abilitata con la soluzione e con macchine virtuali collegate. Il selettore **Gruppo** restituisce le sottoscrizioni, i gruppi di risorse, i [gruppi di computer](../../azure-monitor/platform/computer-groups.md) e i set di scalabilità delle macchine virtuali dei computer correlati all'area di lavoro selezionata. La selezione è valida solo per la funzionalità Mappa e non si applica alla funzionalità Prestazioni.

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Grazie al selettore **TimeRange**, è possibile cercare intervalli di tempo cronologici della durata massima di un'ora per visualizzare l'aspetto delle dipendenze nel passato, ad esempio durante un evento imprevisto o prima di una modifica.   

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come usare la funzionalità di integrità, vedere [View Azure VM Health](vminsights-health.md) (Visualizzare l'integrità delle macchine virtuali di Azure) o per identificare colli di bottiglia e l'uso complessivo con le prestazioni delle macchine virtuali, vedere [View Azure Monitor for VMs Performance](vminsights-performance.md) (Visualizzare Monitoraggio di Azure per le prestazioni delle macchine virtuali). 
