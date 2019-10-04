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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: 1d13db922ae84e4032304a8865ba6fcdafa65748
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201627"
---
# <a name="supported-platforms"></a>Piattaforme supportate 

## Macchine virtuali/server<a name="vm-server"></a>

Il Centro sicurezza supporta macchine virtuali/server su diversi tipi di ambienti ibridi:

* Solo Azure
* Azure e locale
* Azure e altri cloud
* Azure, altri cloud e in locale

Per un ambiente Azure attivato in una sottoscrizione di Azure, il Centro sicurezza di Azure rileva automaticamente le risorse IaaS distribuite nella sottoscrizione.

> [!NOTE]
> Per ricevere il set completo di funzionalità di sicurezza, è necessario che l' [agente di log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), usato dal centro sicurezza di Azure, sia installato e [configurato correttamente per l'invio di dati al centro sicurezza di Azure](security-center-enable-data-collection.md#manual-agent).


Le sezioni seguenti elencano i sistemi operativi server supportati in cui è possibile eseguire l' [agente di log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), usato dal centro sicurezza di Azure.

### Sistemi operativi Windows Server<a name="os-windows"></a>

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> L'integrazione con Microsoft Defender ATP supporta solo Windows Server 2012 R2 e Windows Server 2016.

Per ulteriori informazioni sulle funzionalità supportate per i sistemi operativi Windows, elencate in precedenza, vedere [funzionalità supportate di macchine virtuali/server](security-center-services.md##vm-server-features).

### Sistemi operativi Linux<a name="os-linux"></a>

64 bit

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bit
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14,04 LTS e 16,04 LTS

> [!NOTE]
> Poiché l'elenco dei sistemi operativi Linux supportati è in continua evoluzione, se lo si preferisce, fare clic [qui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) per visualizzare l'elenco più aggiornato delle versioni supportate, nel caso in cui siano state apportate modifiche dall'ultima pubblicazione di questo argomento.

Per ulteriori informazioni sulle funzionalità supportate per i sistemi operativi Linux, elencate in precedenza, vedere la pagina relativa alle [funzionalità supportate di macchine virtuali e server](security-center-services.md##vm-server-features).

### Servizi di macchine virtuali gestiti<a name="virtual-machine"></a>

Anche le macchine virtuali vengono create in una sottoscrizione cliente come parte di alcuni servizi gestiti di Azure, ad esempio Azure Kubernetes (AKS), Azure Databricks e altro ancora. Queste macchine virtuali vengono anche individuate dal centro sicurezza di Azure e l'agente di log Analytics può essere installato e configurato in base ai [sistemi operativi Windows/Linux](#os-windows)supportati, elencati in precedenza.

### Servizi cloud<a name="cloud-services"></a>

Sono supportate anche le macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).

## Servizi PaaS<a name="paas-services"></a>

Le risorse PaaS di Azure seguenti sono supportate dal centro sicurezza di Azure:

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* Account di archiviazione
* Servizio app
* Funzione
* Servizio cloud
* Rete virtuale
* Subnet
* Scheda di interfaccia di rete
* Gruppo di sicurezza di rete
* Account Batch
* Account di Service Fabric
* Account di Automazione
* Servizio di bilanciamento del carico
* Cerca
* Spazio dei nomi del bus di servizio
* Analisi di flusso
* Spazio dei nomi dell'hub eventi
* App per la logica
* Redis
* Data Lake Analytics
* Data Lake Store
* Insieme di credenziali delle chiavi

Per altre informazioni sulle funzionalità supportate per l'elenco precedente di risorse PaaS, vedere [funzionalità supportate di PaaS Services](security-center-services.md#paas-services).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati e l'agente di log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).
- Informazioni sulle [funzionalità disponibili per i diversi ambienti cloud](security-center-services.md).
- Altre informazioni sul [rilevamento delle minacce per le macchine virtuali & Server nel centro sicurezza di Azure](security-center-alerts-iaas.md).
- Vedere le [domande frequenti sull'uso del Centro sicurezza di Azure](security-center-faq.md).
- Vedere i [post di blog sulla sicurezza e sulla conformità di Azure](https://blogs.msdn.com/b/azuresecurity/).
