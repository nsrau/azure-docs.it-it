---
title: Monitorare il filer di Azure FXT Edge
description: Come monitorare lo stato dell'hardware per la cache di archiviazione ibrida di Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254881"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorare lo stato hardware del filer di Azure FXT Edge

Il sistema di cache di archiviazione ibrida di Azure FXT Edge presenta più spie di stato integrate nello chassis per aiutare gli amministratori a comprendere il funzionamento dell'hardware.

## <a name="system-health-status"></a>Stato di integrità del sistema

Per monitorare le operazioni della cache a un livello superiore, utilizzare la pagina **Dashboard** del pannello di controllo software, come descritto nella [Guida del dashboard del pannello di controllo](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>LED di stato hardware

Questa sezione illustra i vari indicatori luminosi di stato incorporati nell'hardware del filer di Azure FXT Edge.

### <a name="hard-drive-status-leds"></a>LED di stato del disco rigido

![immagine del disco rigido anteriore, orizzontale, con etichette di callout 2 (angolo superiore sinistro), 1 (angolo inferiore sinistro) e 3 (lato destro)](media/fxt-monitor/fxt-drive-callouts.png)

Ogni vettore di unità dispone di due LED di stato: un indicatore di attività (1) e un indicatore di stato (2). 

* Il LED attività (1) si accende quando l'unità è in uso.  
* Il LED di stato (2) indica la condizione dell'unità usando i codici nella tabella seguente.

| Stato del LED di stato unità              | Significato  |
|-------------------------------------|----------------------------------------------------------|
| Lampeggia due volte al secondo      | Identificazione dell'unità *o* <br> Preparazione dell'unità per la rimozione  |
| Disattivato (spento)                         | Il sistema non ha completato l'avvio *o* <br>L'unità è pronta per essere rimossa |
| Lampeggia verde, ambra e disattivato       | Errore dell'unità stimato   |
| Flash ambra quattro volte al secondo | Unità non riuscita   |
| Verde continuo                         | Unità online |

Il lato destro dell'unità (3) è etichettato con la capacità dell'unità e altre informazioni.

I numeri delle unità sono stampati nello spazio tra le unità. In Azure FXT Edge Filer, l'unità 0 è quella in alto a sinistra, mentre l'unità 1 è quella immediatamente al di sotto. La numerazione continua con questo modello. 

![Foto di un alloggiamento del disco rigido nello chassis FXT, con i numeri di unità e le etichette della capacità](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Pannello di controllo a sinistra

Il pannello di controllo anteriore sinistro presenta diversi indicatori LED di stato (1) e un indicatore di integrità del sistema chiaro (2). 

![Pannello di stato sinistro, con 1 indicatori di stato con etichetta a sinistra e 2 con l'etichetta della luce dell'indicatore di integrità del sistema grande a destra](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicatori di stato del pannello di controllo 

Gli indicatori di stato a sinistra mostrano una luce ambra chiara se si verifica un errore nel sistema. Nella tabella seguente vengono descritte le possibili cause e soluzioni per gli errori. 

Se è ancora presente l'errore dopo aver provato queste soluzioni, [contattare il supporto tecnico](fxt-support-ticket.md) per assistenza. 

| Icona | Descrizione | Condizione di errore | Possibili soluzioni |
|----------------|---------------|--------------------|----------------------|
| ![icona dell'unità](media/fxt-monitor/fxt-hd-icon.jpg) | Stato unità | Errore unità | Controllare il registro eventi di sistema per determinare se si è verificato un errore nell'unità oppure <br>Eseguire il test di diagnostica online appropriato. riavviare il sistema ed eseguire la diagnostica incorporata (ePSA) o <br>Se le unità sono configurate in un array RAID, riavviare il sistema e immettere il programma dell'utilità di configurazione dell'adapter host |
|![Icona temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Stato temperatura | Errore termico. ad esempio, una ventola ha avuto esito negativo o la temperatura ambiente non è compresa nell'intervallo | Verificare le seguenti condizioni indirizzabili: <br>Ventola di raffreddamento mancante o non riuscita <br>La copertura del sistema, il telo aereo, il modulo di memoria vuoto o la parentesi graffa di riempimento indietro è stata rimossa <br>Temperatura ambiente troppo elevata <br>Il flusso d'aria esterno è ostruito |
|![icona di elettricità](media/fxt-monitor/fxt-electric-icon.jpg) | Stato elettrico | Errore elettrico, ad esempio una tensione fuori intervallo, un alimentatore non riuscito o un regolatore di tensione non riuscito |  Controllare il registro eventi di sistema o i messaggi di sistema per il problema specifico. Se si verifica un problema di PSU, controllare il LED di stato PSU e riposizionare il PSU, se necessario. | 
|![icona memoria](media/fxt-monitor/fxt-memory-icon.jpg) | Stato memoria | Errore di memoria | Controllare il registro eventi di sistema o i messaggi di sistema per il percorso della memoria non riuscita. Riposizionare il modulo memoria. |
|![Icona PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Stato PCIe | Errore della scheda PCIe | Riavviare il sistema; aggiornare i driver delle schede PCIe; reinstallare la scheda |


### <a name="system-health-status-indicator"></a>Indicatore dello stato di integrità del sistema

Il pulsante illuminato grande a destra del pannello di controllo a sinistra indica lo stato complessivo del sistema e viene usato anche come indicatore di posizione dell'unità in modalità ID sistema.

Premere il pulsante integrità sistema e ID per passare dalla modalità ID sistema alla modalità integrità sistema e viceversa.

|Stato di integrità del sistema | Condizione |
|-------------------------------------------|-----------------------------------------------|
| Blu continuo | Normale operazione: il sistema è acceso, funziona normalmente e la modalità ID sistema non è attiva. <br/>Se si desidera passare alla modalità ID sistema, premere il pulsante integrità sistema e ID. |
| Blu lampeggiante | La modalità ID sistema è attiva. Premere il pulsante integrità sistema e ID sistema se si desidera passare alla modalità integrità sistema. |
| Ambra tinta unita | Il sistema è in modalità fail-safe. Se il problema persiste, [contattare il servizio supporto tecnico clienti Microsoft](fxt-support-ticket.md). |
| Ambra lampeggiante | Errore di sistema. Controllare se nel registro eventi di sistema sono presenti messaggi di errore specifici. Per informazioni sui messaggi di evento e di errore generati dal firmware di sistema e dagli agenti che controllano i componenti di sistema, vedere la pagina relativa alla ricerca del codice di errore in qrl.dell.com. |


