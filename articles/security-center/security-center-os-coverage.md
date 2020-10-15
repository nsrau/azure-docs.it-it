---
title: Piattaforme supportate dal Centro sicurezza di Azure | Microsoft Docs
description: Questo documento include un elenco di piattaforme supportate dal Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 68cb738ae6e4689a0356ea56c1de2d383ea83ad6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449933"
---
# <a name="supported-platforms"></a>Piattaforme supportate 

Questa pagina illustra le piattaforme e gli ambienti supportati dal Centro sicurezza di Azure.

## <a name="combinations-of-environments"></a>Combinazioni di ambienti <a name="vm-server"></a>

Il Centro sicurezza di Azure supporta macchine virtuali e server in diversi tipi di ambienti ibridi:

* Solo Azure
* Azure e in locale
* Azure e altri cloud
* Azure, altri cloud e in locale

Per un ambiente di Azure attivato in una sottoscrizione di Azure, il Centro sicurezza di Azure rileva automaticamente le risorse IaaS distribuite nella sottoscrizione.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Il Centro sicurezza dipende dall'[agente di Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Verificare che nei computer sia in esecuzione uno dei sistemi operativi supportati per questo agente, come descritto nelle pagine seguenti:

* [Agente di Log Analytics per i sistemi operativi Windows supportati](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [Agente di Log Analytics per i sistemi operativi Linux supportati](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Assicurarsi inoltre che l'agente di Log Analytics sia [correttamente configurato per l'invio di dati al Centro sicurezza](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Per altre informazioni sulle specifiche funzionalità del Centro sicurezza disponibili in Windows e in Linux, vedere [Copertura delle funzionalità per i computer](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Servizi gestiti delle macchine virtuali <a name="virtual-machine"></a>

Nelle sottoscrizioni dei clienti vengono create anche macchine virtuali come parte di alcuni servizi gestiti di Azure, come Azure Kubernetes, Azure Databricks e altri ancora. Il Centro sicurezza individua anche queste macchine virtuali e, se è disponibile un sistema operativo supportato, è possibile installare e configurare l'agente di Log Analytics.

## <a name="cloud-services"></a>Servizi cloud <a name="cloud-services"></a>

Sono supportate anche le macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).

È supportata anche la protezione per le VM residenti in Azure Stack. Per altre informazioni sull'integrazione del Centro sicurezza con Azure Stack, vedere [Onboarding delle macchine virtuali di Azure Stack nel Centro sicurezza](quickstart-onboard-machines.md). 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul modo in cui il [Centro sicurezza raccoglie i dati tramite l'agente di Log Analytics](security-center-enable-data-collection.md).
- Informazioni sul modo in cui il [Centro sicurezza gestisce e salvaguarda i dati](security-center-data-security.md).
- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).