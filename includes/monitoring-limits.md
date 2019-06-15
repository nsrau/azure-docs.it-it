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
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66271516"
---
| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Impostazioni di scalabilità automatica |100 per area per sottoscrizione. | Uguale all'impostazione predefinita. |
| Avvisi delle metriche (versione classica) |100 regole di avviso attive per ogni sottoscrizione. | Contattare il supporto tecnico. |
| Avvisi delle metriche |1000 regole di avviso attive per ogni sottoscrizione (in cloud pubblici) e 100 regole di avviso attive per ogni sottoscrizione in Azure Cina e Azure per enti pubblici. | Contattare il supporto tecnico. |
| Avvisi dei log attività | 100 regole di avviso attive per ogni sottoscrizione. | Uguale all'impostazione predefinita. |
| Avvisi relativi ai log | 512 | Contattare il supporto tecnico. |
| Gruppi di azioni |2\.000 gruppi di azioni per ogni sottoscrizione. | Contattare il supporto tecnico. |

**Limiti specifici del gruppo di azione**

| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Push dell'app Azure | 10 azioni di app di azure per ogni gruppo di azioni. | Contattare il supporto tecnico. |
| Email | 1\.000 azioni di posta elettronica in un gruppo di azioni. Vedere anche il [informazioni sulla limitazione di frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contattare il supporto tecnico. |
| Gestione dei servizi IT | 10 azioni di gestione dei servizi IT in un gruppo di azioni. | Contattare il supporto tecnico. | 
| App per la logica | 10 azioni di app per la logica in un gruppo di azioni. | Contattare il supporto tecnico. |
| Runbook | 10 azioni di runbook in un gruppo di azioni. | Contattare il supporto tecnico. |
| sms | 10 azioni di SMS in un gruppo di azioni. Vedere anche il [informazioni sulla limitazione di frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contattare il supporto tecnico. |
| Chiamata vocale | 10 vocali azioni in un gruppo di azioni. Vedere anche il [informazioni sulla limitazione di frequenza](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Contattare il supporto tecnico. |
| webhook | 10 azioni webhook in un gruppo di azioni.  Numero massimo di chiamate di webhook è 1500 al minuto per ogni sottoscrizione. Altri limiti sono disponibili all'indirizzo [specifici per le operazioni informazioni](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Contattare il supporto tecnico. |
