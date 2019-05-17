---
title: File di inclusione
description: File di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 21f1fca1e8c5cf2b060cee41d1e474996f306f8e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787662"
---
| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Impostazioni di scalabilità automatica |100 per area per sottoscrizione. | Uguale all'impostazione predefinita. |
| Avvisi delle metriche (versione classica) |100 regole di avviso attive per ogni sottoscrizione. | Contattare il supporto tecnico. |
| Avvisi delle metriche |100 regole di avviso attive per ogni sottoscrizione. | Contattare il supporto tecnico. |
| Avvisi del log attività | 100 regole di avviso attive per ogni sottoscrizione. | Uguale all'impostazione predefinita. |
| Avvisi relativi ai log | 512 | Contattare il supporto tecnico. |
| Gruppi di azioni |2.000 gruppi di azioni per ogni sottoscrizione. | Contattare il supporto tecnico. |

**Limiti specifici del gruppo di azione**

| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Push dell'app Azure | 10 azioni di app di azure per ogni gruppo di azioni. | Contattare il supporto tecnico. |
| Posta | 1.000 azioni di posta elettronica in un gruppo di azioni. Vedere anche il [informazioni sulla limitazione di frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contattare il supporto tecnico. |
| ITSM | 10 azioni di gestione dei servizi IT in un gruppo di azioni. | Contattare il supporto tecnico. | 
| App per la logica | 10 azioni di app per la logica in un gruppo di azioni. | Contattare il supporto tecnico. |
| Runbook | 10 azioni di runbook in un gruppo di azioni. | Contattare il supporto tecnico. |
| SMS | 10 azioni di SMS in un gruppo di azioni. Vedere anche il [informazioni sulla limitazione di frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contattare il supporto tecnico. |
| Voce | 10 vocali azioni in un gruppo di azioni. Vedere anche il [informazioni sulla limitazione di frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contattare il supporto tecnico. |
| Webhook | 10 azioni webhook in un gruppo di azioni.  Numero massimo di chiamate di webhook è 1500 al minuto per ogni sottoscrizione. Altri limiti sono disponibili all'indirizzo [specifici per le operazioni informazioni](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Contattare il supporto tecnico. |
