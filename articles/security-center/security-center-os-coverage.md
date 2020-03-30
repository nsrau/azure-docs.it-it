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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: e13149ba802f0f8b9a565e0aabd86ae05167f18b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208819"
---
# <a name="supported-platforms"></a>Piattaforme supportate 

## <a name="virtual-machines--servers"></a>Macchine virtuali/server<a name="vm-server"></a>

Il Centro sicurezza supporta macchine virtuali/server in diversi tipi di ambienti ibridi:

* Solo Azure
* Azure e locale
* Azure e altri cloudAzure and other clouds
* Azure, altri cloud e localeAzure, other clouds, and on-premises

Per un ambiente Azure attivato in una sottoscrizione di Azure, Il Centro sicurezza di Azure rileverà automaticamente le risorse IaaS distribuite all'interno della sottoscrizione.

> [!NOTE]
> Per ricevere il set completo di funzionalità di sicurezza, è necessario che [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), utilizzato dal Centro sicurezza di Azure, sia installato e configurato [correttamente per l'invio](security-center-enable-data-collection.md#manual-agent)di dati al Centro sicurezza di Azure.

Nelle sezioni seguenti sono elencati i sistemi operativi server supportati in cui è possibile eseguire [log analytics Agent,](../azure-monitor/platform/agents-overview.md#log-analytics-agent)usato dal Centro sicurezza di Azure.The following sections list the supported server operating systems on which the Log Analytics Agent , which is used by Azure Security Center, can run.

### <a name="windows-server-operating-systems"></a>Sistemi operativi server Windows<a name="os-windows"></a>

|OS|Supportato dal Centro sicurezza di AzureSupported by Azure Security Center|Supporto per l'integrazione con Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Per ulteriori informazioni sulle funzionalità supportate per i sistemi operativi Windows, elencate in precedenza, vedere [Funzionalità supportate da macchina virtuale/server](security-center-services.md#vm-server-features).

### <a name="windows-operating-systems"></a>Sistemi operativi Windows<a name="os-windows (non-server)"></a>

Il Centro sicurezza di Azure si integra con i servizi di Azure per monitorare e proteggere le macchine virtuali basate su Windows.Azure Security Center integrates with Azure services to monitor and protect your Windows-based virtual machines.

### <a name="linux-operating-systems"></a>Sistemi operativi Linux<a name="os-linux"></a>

64 bit

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e Oracle Linux 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bit
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS

> [!NOTE]
> Poiché l'elenco dei sistemi operativi Linux supportati cambia continuamente, se preferisci, fai clic [qui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) per visualizzare l'elenco più aggiornato delle versioni supportate, nel caso in cui ci siano state modifiche dall'ultima pubblicazione di questo argomento.

Per ulteriori informazioni sulle funzionalità supportate per i sistemi operativi Linux, elencate in precedenza, vedere [Funzionalità supportate da macchine virtuali/server](security-center-services.md#vm-server-features).

### <a name="managed-virtual-machine-services"></a>Servizi di macchine virtuali gestite<a name="virtual-machine"></a>

Le macchine virtuali vengono inoltre create in una sottoscrizione cliente come parte di alcuni servizi gestiti di Azure, ad esempio Azure Kubernetes (AKS), Azure Databricks e altro ancora. Queste macchine virtuali vengono individuate anche dal Centro sicurezza di Azure e l'agente di analisi dei log può essere installato e configurato in base ai [sistemi operativi Windows/Linux](#os-windows)supportati, elencati in precedenza.

### <a name="cloud-services"></a>Servizi cloud<a name="cloud-services"></a>

Sono supportate anche le macchine virtuali eseguite in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).

È supportata anche la protezione per le macchine virtuali che risiedano in Azure Stack.Protection for Virtual Machines residing in Azure Stack is also supported. Per altre informazioni sull'integrazione del Centro sicurezza con Azure Stack, vedere [Eseguire l'onboarding](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)delle macchine virtuali di Azure Stack nel Centro sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati e l'agente log Analytics.](security-center-enable-data-collection.md)
- Scopri come il [Centro sicurezza gestisce e salvaguarda i dati.](security-center-data-security.md)
- Informazioni su come [pianificare e comprendere le considerazioni di progettazione per l'adozione](security-center-planning-and-operations-guide.md)del Centro sicurezza di Azure.
- Informazioni sulle [funzionalità disponibili per i diversi ambienti cloud.](security-center-services.md)
- Altre informazioni sulla [protezione dalle minacce per i computer Windows e Linux nel Centro sicurezza](threat-protection.md#windows-machines)di Azure.
