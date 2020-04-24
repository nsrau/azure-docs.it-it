---
title: Piattaforme supportate dal centro sicurezza di Azure | Microsoft Docs
description: Questo documento fornisce un elenco delle piattaforme supportate dal centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521906"
---
# <a name="supported-platforms"></a>Piattaforme supportate 

Questa pagina mostra le piattaforme e gli ambienti supportati dal centro sicurezza di Azure.

## <a name="combinations-of-environments"></a>Combinazioni di ambienti<a name="vm-server"></a>

Il Centro sicurezza di Azure supporta macchine virtuali e server su diversi tipi di ambienti ibridi:

* Solo Azure
* Azure e locale
* Azure e altri cloud
* Azure, altri cloud e in locale

Per un ambiente Azure attivato in una sottoscrizione di Azure, il Centro sicurezza di Azure rileva automaticamente le risorse IaaS distribuite nella sottoscrizione.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Il Centro sicurezza dipende dall' [agente log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Verificare che nei computer sia in esecuzione uno dei sistemi operativi supportati per questo agente, come descritto nelle pagine seguenti:

* [Agente di Log Analytics per sistemi operativi supportati da Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Agente di Log Analytics per sistemi operativi Linux supportati](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Assicurarsi inoltre che l'agente di Log Analytics sia [configurato correttamente per l'invio di dati al centro sicurezza](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Per altre informazioni sulle funzionalità specifiche del Centro sicurezza disponibili in Windows e Linux, vedere [copertura delle funzionalità per i computer](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Servizi di macchine virtuali gestiti<a name="virtual-machine"></a>

Anche le macchine virtuali vengono create in una sottoscrizione cliente come parte di alcuni servizi gestiti da Azure, ad esempio Azure Kubernetes (AKS), Azure Databricks e altro ancora. Il Centro sicurezza individua anche queste macchine virtuali e l'agente di Log Analytics può essere installato e configurato se è disponibile un sistema operativo supportato.

## <a name="cloud-services"></a>Servizi cloud<a name="cloud-services"></a>

Sono supportate anche le macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).

È supportata anche la protezione per le macchine virtuali che risiedono in Azure Stack. Per ulteriori informazioni sull'integrazione del Centro sicurezza con Azure Stack, vedere [onboarding the Azure stack Virtual Machines to Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul modo in cui [il Centro sicurezza raccoglie i dati utilizzando l'agente di log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Informazioni su come [pianificare e comprendere le considerazioni di progettazione per l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).