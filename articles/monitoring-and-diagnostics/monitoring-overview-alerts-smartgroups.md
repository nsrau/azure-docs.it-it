---
title: Gruppi intelligenti
description: I gruppi intelligenti sono aggregazioni di avvisi che consentono di ridurre la frequenza degli avvisi
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 57ec7c13cdf5c2e44abf23dc0452e5eab3eb77ff
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412619"
---
# <a name="smart-groups"></a>Gruppi intelligenti
Un problema comune insito nella gestione degli avvisi è estrarre le informazioni per capirne l'effettiva importanza. I gruppi intelligenti sono destinati a essere la soluzione a questo problema.  

I gruppi intelligenti vengono creati automaticamente tramite algoritmi di apprendimento automatico per combinare gli avvisi associati che rappresentano un singolo problema.  Quando viene creato un avviso, l'algoritmo lo aggiunge a un gruppo intelligente nuovo o esistente in base a informazioni come i modelli cronologici e la somiglianza delle proprietà e della struttura. Se, ad esempio, le percentuali della CPU in varie macchine virtuali in una sottoscrizione presentano simultaneamente un picco che porta a numerosi avvisi singoli e se questi avvisi si sono verificati contemporaneamente in qualsiasi momento nel passato, verranno probabilmente raggruppati in un unico gruppo intelligente, suggerendo una possibile causa radice comune. Ciò significa che, per un utente che sta effettuando la risoluzione dei problemi degli avvisi, i gruppi intelligenti non solo consentono di ridurre la frequenza degli avvisi gestendo gli avvisi correlati come una singola unità aggregata ma forniscono una guida per individuare possibili cause radice comuni per gli avvisi.

Attualmente, l'algoritmo considera solo gli avvisi dello stesso servizio di monitoraggio all'interno di una sottoscrizione. I gruppi intelligenti possono ridurre fino al 99% il rumore degli avvisi mediante questo consolidamento. È possibile visualizzare il motivo per cui gli avvisi sono stati inclusi in un gruppo nella pagina dei dettagli del gruppo intelligente.

È possibile visualizzare i dettagli dei gruppi intelligenti e impostarne lo stato in modo simile agli avvisi. Ogni avviso appartiene a un unico gruppo intelligente. 

## <a name="smart-group-state"></a>Stato del gruppo intelligente
Lo stato del gruppo intelligente è un concetto simile allo stato degli avvisi, che consente di gestire il processo di risoluzione a livello di un gruppo intelligente. Analogamente allo stato degli avvisi, quando si crea un gruppo intelligente, questo ha lo stato **Nuovo**, che può essere modificato in **Confermato** oppure **Chiuso**.

Sono supportati i seguenti stati di gruppo intelligente.

| Stato | DESCRIZIONE |
|:---|:---|
| Nuovo | Il problema è appena stato rilevato e non è ancora stato esaminato. |
| Confermato | Un amministratore ha esaminato il gruppo intelligente e ha iniziato a lavorare su di esso. |
| Chiuso | Il problema è stato risolto. Dopo che un gruppo intelligente è stato chiuso, è possibile riaprirlo modificandone lo stato. |

[Informazioni su come modificare lo stato del gruppo intelligente](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  La modifica dello stato di un gruppo intelligente non modifica lo stato degli avvisi dei membri individuali.

## <a name="smart-group-details-page"></a>Pagina dei dettagli del gruppo intelligente

La pagina dei dettagli del gruppo intelligente viene visualizzata quando si seleziona un gruppo intelligente. Fornisce dettagli relativi al gruppo intelligente, inclusi i motivi per cui il gruppo è stato creato, e consente di cambiarne lo stato.
 
![Dettagli del gruppo intelligente](media/monitoring-overview-alerts-smartgroups/smart-group-detail.png)


La pagina dei dettagli del gruppo intelligente include le sezioni seguenti.

| Sezione | DESCRIZIONE |
|:---|:---|
| Avvisi | Elenca i singoli avvisi inclusi nel gruppo intelligente. Selezionare un avviso per aprire la pagina dei dettagli corrispondente. |
| Cronologia | Elenca tutte le azioni eseguite dal gruppo intelligente e le modifiche apportate a quest'ultimo. Questa opzione è attualmente limitata alle modifiche di stato e a quelle di appartenenza degli avvisi. |

## <a name="smart-group-taxonomy"></a>Tassonomia del gruppo intelligente

Il nome di un gruppo intelligenti è il nome del suo primo avviso. Non è possibile creare o rinominare un gruppo intelligente.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire i gruppi intelligenti](https://aka.ms/managing-smart-groups)
- [Modificare lo stato di avvisi e gruppi intelligenti](https://aka.ms/managing-alert-smart-group-states)

