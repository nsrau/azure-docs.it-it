---
title: Pianificazioni della manutenzione di Azure (anteprima) | Microsoft Docs
description: La pianificazione della manutenzione consente ai clienti di programmare tutti gli eventi di manutenzione pianificata necessari usati da Azure SQL Data Warehouse per distribuire nuovi aggiornamenti, patch e funzionalità.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 03/13/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b97e27b86ecad1f7f87a6de4d43b09d69c167c6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075329"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Usare le pianificazioni della manutenzione per gestire gli aggiornamenti e la manutenzione dei servizi

Le pianificazioni di manutenzione sono ora disponibili in tutte le aree di Azure SQL Data Warehouse. Questa funzionalità si integra con le notifiche di manutenzione pianificata dell'integrità dei servizi, il monitoraggio per il controllo dell'integrità risorse e il servizio di pianificazione della manutenzione di Azure SQL Data Warehouse.

La pianificazione della manutenzione consente di scegliere una finestra temporale per ricevere al momento opportuno le nuove funzionalità, gli aggiornamenti e le patch. Si sceglie una finestra di manutenzione primaria e secondaria entro un periodo di sette giorni. Un esempio è una finestra primaria dalle 22.00 di sabato alle 01.00 di domenica 01:00 e una finestra secondaria dalle 19.00 alle 22:00 di mercoledì. Se SQL Data Warehouse non riesce a eseguire operazioni di manutenzione durante la finestra di manutenzione primaria, cercherà di effettuare tale operazione nell'ambito della finestra di manutenzione secondaria. Manutenzione dei servizi può verificarsi durante il database primario e le finestre secondarie. Per garantire il rapido completamento di tutte le operazioni di manutenzione, DW400(c) più bassi livelli e dati warehouse è stato possibile completare la manutenzione di fuori di una finestra di manutenzione prestabilito.

Tutte le istanze di Azure SQL Data Warehouse appena create avranno una pianificazione di manutenzione definita dal sistema applicata durante la distribuzione. È possibile modificare la pianificazione non appena la distribuzione viene completata.

Ogni finestra di manutenzione può essere di tre-otto ore. La manutenzione può avvenire in qualsiasi momento nell'ambito della finestra ed è possibile che si verifichi una breve perdita di connettività perché il servizio distribuisce nuovo codice nel data warehouse.

Per usare questa funzione è necessario identificare una finestra primaria e secondaria all'interno di intervalli giornalieri separati. Tutte le operazioni di manutenzione devono terminare entro le finestre di manutenzione pianificate. Nessuna manutenzione verrà eseguita al di fuori delle finestre di manutenzione specificate senza preavviso. Se il data warehouse è sospeso durante una manutenzione pianificata, verrà aggiornato durante l'operazione di ripresa.  

## <a name="alerts-and-monitoring"></a>Avvisi e monitoraggio

L'integrazione con le notifiche sull'integrità dei servizi e il monitoraggio per il controllo dell'integrità delle risorse consente ai clienti di essere aggiornati sulle attività di manutenzione imminenti. La nuova automazione si avvale di Monitoraggio di Azure. È possibile decidere come si vuole ricevere una notifica degli eventi di manutenzione imminenti. Si può anche decidere quali flussi automatici consentono di gestire i tempi di inattività e ridurre al minimo l'impatto sulle operazioni.

Un preavviso di 24 ore prima tutti gli eventi di manutenzione, con l'eccezione corrente di DW400c e i livelli inferiori. Per ridurre al minimo i tempi di inattività delle istanze, assicurarsi che non siano presenti transazioni con esecuzione prolungata nel data warehouse prima del periodo di manutenzione scelto. Quando inizia la manutenzione verranno annullate tutte le sessioni attive. Le transazioni di commit non verranno annullate e il data warehouse si verifica una breve perdita di connettività. L'utente riceverà una notifica immediatamente al termine della manutenzione del data warehouse.

Se si riceve un preavviso prima dell'esecuzione della manutenzione, ma SQL Data Warehouse non riesce a eseguire la manutenzione nell'orario prestabilito, si riceverà una notifica di annullamento. La manutenzione verrà quindi ripresa durante il successivo periodo di manutenzione pianificato.

Tutti gli eventi di manutenzione attivi verranno visualizzati nella sezione **Integrità dei servizi - Manutenzione pianificata**. La cronologia dell'integrità dei servizi include un record completo degli eventi precedenti. La manutenzione può essere monitorata tramite il dashboard del portale di controllo dell'integrità dei servizi di Azure durante un evento attivo.

### <a name="maintenance-schedule-availability"></a>Disponibilità della pianificazione della manutenzione

Anche se la pianificazione della manutenzione non è disponibile nell'area selezionata, è possibile visualizzare e modificare la pianificazione di manutenzione in qualsiasi momento. Quando la pianificazione della manutenzione sarà disponibile nella propria area, la pianificazione identificata diventerà immediatamente attiva nel data warehouse.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](viewing-maintenance-schedule.md) sulla visualizzazione di una pianificazione di manutenzione.
- [Altre informazioni](changing-maintenance-schedule.md) sulla modifica di una pianificazione di manutenzione.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sulla creazione, visualizzazione e gestione degli avvisi con Monitoraggio di Azure.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sulle azioni webhook per le regole di avviso relative ai log.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Creazione e gestione di gruppi di azioni.
- [Altre informazioni](https://docs.microsoft.com/azure/service-health/service-health-overview) sull'integrità dei servizi di Azure.
