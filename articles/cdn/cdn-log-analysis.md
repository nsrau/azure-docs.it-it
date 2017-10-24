---
title: Analizzare i modelli di utilizzo della rete CDN di Azure | Microsoft Docs
description: "Il cliente può abilitare l'analisi dei log per la rete CDN di Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: af396e9f8847421d529c32956216cfc47294edb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizzare i modelli di utilizzo della rete CDN di Azure

Dopo aver abilitato la rete CDN per l'applicazione, è possibile monitorare l'uso della rete CDN, controllare l'integrità della distribuzione e risolvere i potenziali problemi. La rete CDN di Azure fornisce queste funzionalità in due modi, come indicato di seguito: 

## <a name="verizon-core-reports"></a>Report principali da Verizon

Come un utente di rete CDN di Azure con un profilo Verizon standard o premium, è possibile visualizzare i report principali da Verizon nel portale supplementare di Verizon. Il portale supplementare Verizon offre un'ampia gamma di grafici e visualizzazioni ed è accessibile tramite l'opzione **Gestisci** dal portale di Azure. Per altre informazioni, vedere [Report principali da Verizon](cdn-analyze-usage-patterns.md).

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analisi principale tramite i log di diagnostica di Azure

L'analisi principale è disponibile per tutti gli endpoint della rete CDN che appartengono a profili CDN Verizon (Standard e Premium) e Akamai (Standard). I log di diagnostica di Azure consentono di esportare l'analisi principale in archiviazione di Azure, negli hub eventi o in Operations Management Suite (OMS) Log Analytics. OMS Log Analytics offre una soluzione con grafici che sono configurabili e personalizzabili dall'utente. Per altre informazioni, vedere i [log di diagnostica Azure](cdn-azure-diagnostic-logs.md).

