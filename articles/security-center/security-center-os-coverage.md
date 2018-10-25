---
title: Funzionalità e piattaforme supportate dal Centro sicurezza di Azure | Microsoft Docs
description: Questo documento offre un elenco di funzionalità e piattaforme supportate dal Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 279818e6b43e53206deb9e33591f75ef381a8962
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319983"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Funzionalità e piattaforme supportate dal Centro sicurezza di Azure

Il monitoraggio dello stato della sicurezza e le indicazioni in merito sono disponibili per le macchine virtuali (VM), create usando i modelli di distribuzione classica e Resource Manager, e i computer.

> [!NOTE]
> È consigliabile leggere altre informazioni sui [modelli di distribuzione Azure Resource Manager e classica](../azure-classic-rm.md) per le risorse di Azure.
>
>

## <a name="supported-platforms"></a>Piattaforme supportate 

In questa sezione vengono elencate le piattaforme in cui può essere eseguito un agente del Centro sicurezza di Azure e da cui l'agente può raccogliere dati.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Piattaforme supportate per i computer e le macchine virtuali Windows
Sistemi operativi Windows supportati:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Piattaforme supportate per i computer e le macchine virtuali Linux
Sistemi operativi Linux supportati:

* Versioni di Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS
* Versioni di Debian 6, 7, 8, 9
* Versioni di CentOS 5, 6, 7
* Versioni di Red Hat Enterprise Linux (RHEL) 5, 6, 7
* Versioni di SUSE Linux Enterprise Server (SLES) 11, 12
* Versioni di Oracle Linux 5, 6, 7
* Amazon Linux dalla 2012.09 alla 2017
* OpenSSL 1.1.0 è supportato solo su piattaforme x86_64 (64 bit)

> [!NOTE]
> L'analisi comportamentali della macchina virtuale non è ancora disponibile per i sistemi operativi Linux.
>
>

## <a name="vms-and-cloud-services"></a>Macchine virtuali e servizi cloud
Sono supportate anche macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli Web e di lavoro dei servizi cloud in esecuzione negli slot di produzione. Per altre informazioni sul servizio cloud, vedere [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Funzionalità IaaS supportate

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||
|----|----|----|----|----|
|Environment|Azure|Non Azure|Azure|Non Azure|
|Avvisi di rilevamento minacce VMBA|✔|✔|✔ (nelle versioni supportate)|✔|
|Avvisi di rilevamento minacce in base alla rete|✔|X|✔|X|
|Integrazione con Windows Defender ATP*|✔ (nelle versioni supportate)|✔|X|X|
|Patch mancanti|✔|✔|✔|✔|
|Configurazioni di sicurezza|✔|✔|✔|✔|
|Antimalware|✔|✔|X|X|
|Accesso JIT alle VM|✔|X|✔|X|
|Controlli delle applicazioni adattivi|✔ (solo Azure)|X|X|X|
|FIM|✔|✔|✔|✔|
|Crittografia del disco|✔|X|✔|X|
|Distribuzione di terze parti|✔|X|✔|X|
|Gruppi di sicurezza di rete|✔|X|✔|X|
|Filess V1|✔|✔|X|X|
|Mappa di rete|✔|X|✔|X|
|Protezione adattiva della rete|✔|X|✔|X|

* Queste funzionalità sono attualmente supportate in anteprima pubblica.


## <a name="supported-paas-features"></a>Funzionalità PaaS supportate


|Service|Consigli|Introduzione al rilevamento delle minacce|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Account di archiviazione BLOB*|✔| ✔|
|Servizi app|✔| ✔|
|Servizi cloud|✔| X|
|Cache Redis|✔| X|
|Service Fabric|✔| X|
|Automazione di Azure|✔| X|
|Data lake |✔| X|
|Insieme di credenziali delle chiavi|✔| X|
|Bus di servizio|✔| X|
|Analisi dei flussi|✔| X|
|Batch|✔| X|
|App per la logica|✔| X|
|Reti virtuali|✔| ND|
|Subnet|✔| ND|
|Schede di interfaccia di rete|✔| ✔|
|Gruppi di sicurezza di rete|✔| ND|
|Sottoscrizione|✔| ✔|

* Queste funzionalità sono attualmente supportate in anteprima pubblica.

## <a name="next-steps"></a>Passaggi successivi

- [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure
- [Avvisi di sicurezza per tipo nel Centro sicurezza di Azure](security-center-alerts-type.md#virtual-machine-behavioral-analysis): altre informazioni sull'analisi comportamentale della macchina virtuale e sull'analisi della memoria dump di arresto anomalo del sistema nel Centro sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure
