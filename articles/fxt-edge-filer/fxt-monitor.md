---
title: Monitorare Azure FXT Edge Filer
description: Come monitorare lo stato dell'hardware per la cache di archiviazione ibrida di Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254881"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorare lo stato hardware di Azure FXT Edge FilerMonitor Azure FXT Edge Filer hardware status

Il sistema di cache di archiviazione ibrida di Azure FXT Edge Filer dispone di più luci di stato incorporate nello chassis per consentire agli amministratori di comprendere il funzionamento dell'hardware.

## <a name="system-health-status"></a>Stato di integrità del sistema

Per monitorare le operazioni della cache a un livello superiore, utilizzare la pagina **Dashboard** del Pannello di controllo del software, come descritto nella Guida del dashboard del [Pannello di controllo](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>LED di stato hardware

Questa sezione illustra le varie luci di stato incorporate nell'hardware di Azure FXT Edge Filer.

### <a name="hard-drive-status-leds"></a>LED di stato del disco rigido

![immagine del disco rigido anteriore, orizzontale, con etichette callout 2 (angolo in alto a sinistra), 1 (angolo inferiore sinistro) e 3 (lato destro)](media/fxt-monitor/fxt-drive-callouts.png)

Ogni vettore di azionamento ha due LED di stato: un indicatore di attività (1) e un indicatore di stato (2). 

* Il LED di attività (1) si illumina quando l'unità è in uso.  
* Il LED di stato (2) indica lo stato dell'unità utilizzando i codici riportati nella tabella seguente.

| Stato dell'azionamento Stato LED              | Significato  |
|-------------------------------------|----------------------------------------------------------|
| Lampeggia in verde due volte al secondo      | Identificazione dell'unità *o* <br> Preparazione dell'unità per la rimozione  |
| Disattivato (non illuminato)                         | L'avvio del sistema non *è terminato o* <br>L'unità è pronta per essere rimossa |
| Lampeggia verde, ambra, e fuori       | Si prevede un guasto dell'unità   |
| Lampeggia ambra quattro volte al secondo | Unità non riuscita   |
| Verde fisso                         | Drive è online |

Il lato destro dell'unità (3) è etichettato con la capacità dell'unità e altre informazioni.

I numeri delle unità sono stampati nello spazio tra le unità. In Azure FXT Edge Filer, l'unità 0 è quella in alto a sinistra, mentre l'unità 1 è quella immediatamente al di sotto. La numerazione continua in questo schema. 

![Foto di un alloggiamento del disco rigido nello chassis FXT, con i numeri di unità e le etichette della capacità](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Pannello di controllo sinistro

Il pannello di controllo anteriore sinistro ha vari indicatori LED di stato (1) e un grande indicatore di stato del sistema illuminato (2). 

![pannello di stato a sinistra, con 1 indicatore di stato di etichettatura a sinistra e 2 etichettatura della luce indicatore di stato del sistema di grandi dimensioni a destra](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicatori di stato del pannello di controllo 

Gli indicatori di stato a sinistra mostrano una luce ambra solida se c'è un errore in quel sistema. Nella tabella seguente vengono descritte le possibili cause e le soluzioni per gli errori. 

Se l'errore persiste anche dopo aver provato queste soluzioni, [contattare](fxt-support-ticket.md) il supporto tecnico per assistenza. 

| Icona | Descrizione | Condizione di errore | Possibili soluzioni |
|----------------|---------------|--------------------|----------------------|
| ![icona dell'unità](media/fxt-monitor/fxt-hd-icon.jpg) | Stato dell'unità | Errore dell'unità | Controllare il registro eventi di sistema per determinare se l'unità presenta un errore, <br>Eseguire il test di diagnostica online appropriato; riavviare il sistema ed eseguire la diagnostica integrata (ePSA) o <br>Se le unità sono configurate in un array RAID, riavviare il sistema e immettere il programma di utilità di configurazione della scheda host |
|![icona temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Stato della temperatura | Errore termico - ad esempio, una ventola non è riuscita o la temperatura ambiente non è compresa nell'intervallo | Verificare le seguenti condizioni indirizzabili: <br>Una ventola di raffreddamento manca o non è riuscita <br>La copertura del sistema, la copertura dell'aria, il modulo di memoria vuoto o la staffa di riempimento posteriore viene rimossa <br>La temperatura ambiente è troppo alta <br>Il flusso d'aria esterno è ostruito |
|![icona dell'elettricità](media/fxt-monitor/fxt-electric-icon.jpg) | Stato elettrico | Errore elettrico - ad esempio, tensione fuori portata, PSU guasto o un regolatore di tensione guasto |  Controllare il registro eventi di sistema o i messaggi di sistema per il problema specifico. Se c'è un problema di PSU, controllare il LED di stato PSU e riposizionare il PSU, se necessario. | 
|![icona della memoria](media/fxt-monitor/fxt-memory-icon.jpg) | Stato della memoria | Errore di memoria | Controllare il registro eventi di sistema o i messaggi di sistema per la posizione della memoria non riuscita; reinserire il modulo di memoria. |
|![Icona PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Stato PCIe | Errore della scheda PCIe | Riavviare il sistema; aggiornare i driver della scheda PCIe; reinstallare la scheda |


### <a name="system-health-status-indicator"></a>Indicatore di stato di integrità del sistema

Il grande pulsante illuminato a destra del pannello di controllo sinistro indica lo stato generale del sistema e viene utilizzato anche come luce del localizzatore di unità in modalità ID di sistema.

Premere il pulsante stato di visualizzazione e ID del sistema per passare dalla modalità ID di sistema alla modalità di integrità del sistema e alla modalità di integrità del sistema.

|Stato integrità del sistema | Condizione |
|-------------------------------------------|-----------------------------------------------|
| Blu fisso | Funzionamento normale: il sistema è acceso, funziona normalmente e la modalità ID di sistema non è attiva. <br/>Premere il pulsante di stato e ID del sistema se si desidera passare alla modalità ID di sistema. |
| Blu lampeggiante | La modalità ID di sistema è attiva. Premere il pulsante stato di integrità del sistema e ID di sistema se si desidera passare alla modalità integrità del sistema. |
| Ambra solida | Il sistema è in modalità fail-safe. Se il problema persiste, [contattare](fxt-support-ticket.md)il Servizio Supporto Tecnico Clienti Microsoft . |
| Ambra lampeggiante | Guasto del sistema. Controllare il registro eventi di sistema per i messaggi di errore specifici. Per informazioni sui messaggi di evento e di errore generati dal firmware di sistema e dagli agenti che monitorano i componenti di sistema, vedere la pagina Ricerca codice di errore all'qrl.dell.com. |


