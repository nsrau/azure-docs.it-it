---
title: Piattaforme supportate dal Centro sicurezza di Azure Documenti Microsoft
description: Questo documento fornisce un elenco delle piattaforme supportate dal Centro sicurezza di Azure.This document provides a list of platforms supported by Azure Security Center.
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

Questa pagina mostra le piattaforme e gli ambienti supportati dal Centro sicurezza di Azure.This page shows the platforms and environments supported by Azure Security Center.

## <a name="combinations-of-environments"></a>Combinazioni di ambienti<a name="vm-server"></a>

Il Centro sicurezza di Azure supporta macchine virtuali e server in diversi tipi di ambienti ibridi:Azure Security Center supports virtual machines and servers on different types of hybrid environments:

* Solo Azure
* Azure e locale
* Azure e altri cloudAzure and other clouds
* Azure, altri cloud e localeAzure, other clouds, and on-premises

Per un ambiente Azure attivato in una sottoscrizione di Azure, Il Centro sicurezza di Azure rileverà automaticamente le risorse IaaS distribuite all'interno della sottoscrizione.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Il Centro sicurezza dipende [dall'agente di Log Analytics.](../azure-monitor/platform/agents-overview.md#log-analytics-agent) Verificare che nei computer sia in esecuzione uno dei sistemi operativi supportati per questo agente, come descritto nelle pagine seguenti:

* [Agente di Log Analytics per i sistemi operativi supportati da WindowsLog Analytics agent for Windows supported operating systems](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Agente log Analytics per sistemi operativi supportati da LinuxLog Analytics agent for Linux supported operating systems](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Assicurarsi inoltre che l'agente di Log Analytics sia [configurato correttamente per l'invio di dati al Centro sicurezza](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Per ulteriori informazioni sulle funzionalità specifiche del Centro sicurezza disponibile in Windows e Linux, vedere [Copertura delle funzionalità per i computer](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Servizi di macchine virtuali gestite<a name="virtual-machine"></a>

Le macchine virtuali vengono inoltre create in una sottoscrizione cliente come parte di alcuni servizi gestiti da Azure, ad esempio Azure Kubernetes (AKS), Azure Databricks e altro ancora. Anche il Centro sicurezza individua queste macchine virtuali e l'agente log Analytics può essere installato e configurato se è disponibile un sistema operativo supportato.

## <a name="cloud-services"></a>Servizi cloud<a name="cloud-services"></a>

Sono supportate anche le macchine virtuali eseguite in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).

È supportata anche la protezione per le macchine virtuali che risiedano in Azure Stack.Protection for VMs residing in Azure Stack is also supported. Per altre informazioni sull'integrazione del Centro sicurezza con Azure Stack, vedere [Eseguire l'onboarding](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)delle macchine virtuali di Azure Stack nel Centro sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati utilizzando Log Analytics Agent](security-center-enable-data-collection.md).
- Scopri come il [Centro sicurezza gestisce e salvaguarda i dati.](security-center-data-security.md)
- Informazioni su come [pianificare e comprendere le considerazioni di progettazione per l'adozione](security-center-planning-and-operations-guide.md)del Centro sicurezza di Azure.