---
title: Monitorare Azure FXT Edge filtro
description: Come monitorare lo stato dell'hardware per la cache di archiviazione ibrida Azure FXT Edge filtro
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e7395c69d99884a5c662e545a69778ed195aec55
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543045"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorare lo stato dell'hardware Azure FXT Edge filtro

Il sistema di cache archiviazione ibrida Azure FXT Edge Filer ha più luci state incorporate per lo chassis per consentire agli amministratori di comprendere come l'hardware funziona.

## <a name="system-health-status"></a>Stato di integrità di sistema

Per monitorare le operazioni della cache a un livello superiore, utilizzare del Pannello di controllo software **Dashboard** pagina, come descritto nel [Guida ai Dashboard del Pannello di controllo](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>LED di stato dell'hardware

Questa sezione illustra le varie luci state incorporate in hardware Azure FXT Edge Filer.

### <a name="hard-drive-status-leds"></a>LED di stato delle unità disco rigido

![immagine di primo piano di disco rigido, orizzontale, con le etichette di callout 2 (angolo superiore sinistro), 1 (angolo inferiore sinistro) e 3 (lato destro)](media/fxt-monitor/fxt-drive-callouts.png)

Ogni supporto dell'unità ha due LED di stato: un indicatore dell'attività (1) e un indicatore di stato (2). 

* L'attività ha portato luci (1) quando l'unità è in uso.  
* Il LED di (2) indica la condizione dell'unità con i codici nella tabella seguente.

| Stato stato LED dell'unità              | Significato  |
|-------------------------------------|----------------------------------------------------------|
| Lampeggia verde due volte al secondo      | Identificazione delle unità *o* <br> Preparazione dell'unità per la rimozione  |
| Disattivare (spento)                         | Sistema non è stata completata avvio *o* <br>L'unità è pronta essere rimosso |
| Lampeggia verde, giallo e disattivato       | Errore dell'unità viene stimato   |
| LED lampeggia quattro volte al secondo | Unità non è riuscita   |
| Verdi                         | L'unità è online |

Il lato destro dell'unità (3) viene etichettato con la capacità dell'unità e altre informazioni.

Vengono stampati i numeri di unità in corrispondenza dello spazio tra le unità. In Azure FXT Edge filtro, unità 0 è la parte superiore sinistra unità e unità 1 viene immediatamente di sotto. Numerazione continua in quel modello. 

![foto di un disco rigido alloggiamento nello chassis FXT, con le etichette di capacità e i numeri di unità](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Pannello di controllo a sinistra

Il pannello di controllo anteriore a sinistra presenta vari indicatori LED di stato (1) e un indicatore di integrità di sistema evidenziato grandi dimensioni (2). 

![Pannello stato sinistro, con 1 assegnazione di etichette gli indicatori di stato sulla sinistra e 2 etichette l'indicatore di integrità di sistema di grandi dimensioni chiaro a destra](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicatori di stato Pannello di controllo 

Gli indicatori di stato a sinistra mostrano una luce ambra solid se si verifica un errore in tale sistema. Nella tabella seguente vengono descritte le possibili cause e soluzioni per gli errori. 

Se hai ancora l'errore dopo aver provato queste soluzioni, contattare il supporto tecnico per assistenza. 

| Icona | Descrizione | Condizione di errore | Possibili soluzioni |
|----------------|---------------|--------------------|----------------------|
| ![icona unità](media/fxt-monitor/fxt-hd-icon.jpg) | Stato dell'unità | Errore dell'unità | Controllare il registro eventi di sistema per determinare se l'unità dispone di un errore, o <br>Eseguire il test di diagnostica in linea appropriato. riavviare il sistema e di eseguire la diagnostica incorporata (ePSA), o <br>Se le unità sono configurate in un array RAID, riavviare il sistema e immettere il programma di utilità Configurazione di adapter host |
|![icona di temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Stato di temperatura | Errore termico - ad esempio, una ventola è guasta o temperatura ambiente non è compreso nell'intervallo | Verificare le condizioni indirizzabile seguenti: <br>Ventola di raffreddamento è manca o non è riuscita <br>Copertura del sistema, shroud air, modulo memoria vuoto o back-viene rimosso tra parentesi quadre filler <br>La temperatura è troppo elevata <br>Il flusso d'aria esterno è nascosto |
|![icona di elettricità](media/fxt-monitor/fxt-electric-icon.jpg) | Stato elettrico | Errore elettrica - ad esempio, voltaggio fuori intervallo, non è stato possibile PSU o regolatore una tensione non riuscita |  Controllare il registro eventi di sistema o i messaggi di sistema per lo specifico problema. Se si verifica un problema PSU, controllare il LED di stato PSU e ricollegare il PSU se necessario. | 
|![icona memoria](media/fxt-monitor/fxt-memory-icon.jpg) | Stato della memoria | Errore di memoria | Controllare il registro eventi di sistema o i messaggi di sistema per la posizione di memoria non riuscita. Ricollegare il modulo di memoria. |
|![Icona PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Stato PCIe | Errore di smart card PCIe | Riavviare il sistema. aggiornare i driver della scheda PCIe; Reinstalla la scheda |


### <a name="system-health-status-indicator"></a>Indicatore di stato di integrità di sistema

Il pulsante grande acceso a destra del Pannello di controllo a sinistra indica lo stato complessivo del sistema e viene usato anche come una luce localizzatore unità in modalità di ID di sistema.

Premere l'integrità del sistema e un pulsante di ID per passare dalla modalità di ID di sistema e modalità di integrità di sistema.

|Stato integrità di sistema | Condizione |
|-------------------------------------------|-----------------------------------------------|
| Blu a tinta unita | Operazione normale: il sistema è acceso, funzionamento normale, e modalità di ID di sistema non è attiva. <br/>Premere l'integrità di sistema e pulsante ID se si desidera passare alla modalità di ID di sistema. |
| Blu lampeggia | È attiva la modalità di ID di sistema. Premere l'integrità di sistema e pulsante di ID del sistema se si desidera passare alla modalità di integrità di sistema. |
| Amber a tinta unita | Il sistema è in modalità provvisoria. Se il problema persiste, contattare il supporto tecnico Microsoft Customer Service. |
| LED lampeggiante | Errore di sistema. Controllare il registro eventi di sistema per i messaggi di errore specifico. Per informazioni sui messaggi di evento e di errore generati dagli agenti che monitorano i componenti del sistema e il firmware di sistema, vedere la pagina di ricerca di codice di errore al qrl.dell.com. |


