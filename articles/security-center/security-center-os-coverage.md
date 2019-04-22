---
title: Funzionalità e piattaforme supportate dal Centro sicurezza di Azure | Microsoft Docs
description: Questo documento offre un elenco di funzionalità e piattaforme supportate dal Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/17/2019
ms.author: monhaber
ms.openlocfilehash: b5eafd15344156965d0a191688f602ffe1b5a498
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678310"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Funzionalità e piattaforme supportate dal Centro sicurezza di Azure

Il monitoraggio dello stato della sicurezza e le raccomandazioni sono disponibili per le macchine virtuali (VM), create usando i modelli di distribuzione in versione classica e Resource Manager, e i computer.

> [!NOTE]
> È consigliabile leggere altre informazioni sui [modelli di distribuzione Azure Resource Manager e classica](../azure-classic-rm.md) per le risorse di Azure.
>
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Piattaforme che supportano l'agente di raccolta dati 

In questa sezione vengono elencate le piattaforme in cui può essere eseguito un agente del Centro sicurezza di Azure e da cui l'agente può raccogliere dati.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Piattaforme supportate per i computer e le macchine virtuali Windows
Sono supportati i sistemi operativi Windows seguenti:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> L'integrazione con Windows Defender ATP supporta solo Windows Server 2012 R2 e Windows Server 2016.
>
>

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Piattaforme supportate per i computer e le macchine virtuali Linux
Sono supportati i sistemi operativi Linux seguenti:

* Ubuntu versioni 12.04 LTS, 14.04 LTS e 16.04 LTS.
* Debian versioni 6, 7, 8 e 9.
* CentOS versioni 5, 6 e 7.
* Red Hat Enterprise Linux (RHEL) versioni 5, 6 e 7.
* SUSE Linux Enterprise Server (SLES) versioni 11 e 12.
* Oracle Linux versioni 5, 6 e 7.
* Amazon Linux da 2012.09 a 2017.
* OpenSSL 1.1.0 è supportato solo sulle piattaforme x86_64 a 64 bit.

## <a name="vms-and-cloud-services"></a>Macchine virtuali e servizi cloud
Sono supportate anche le macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Funzionalità IaaS supportate

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||
|----|----|----|----|----|
|Environment|Azure|Non Azure|Azure|Non Azure|
|Avvisi di rilevamento minacce VMBA|✔|✔|✔ (nelle versioni supportate)|✔|
|Avvisi di rilevamento delle minacce di rete|✔|X|✔|X|
|Integrazione con Windows Defender ATP|✔ (nelle versioni supportate)|✔|X|X|
|Patch mancanti|✔|✔|✔|✔|
|Configurazioni di sicurezza|✔|✔|✔|✔|
|Endpoint Protection|✔|✔|X|X|
|Accesso JIT alle VM|✔|X|✔|X|
|Controlli delle applicazioni adattivi|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|Crittografia del disco|✔|X|✔|X|
|Distribuzione di terze parti|✔|X|✔|X|
|Gruppi di sicurezza di rete|✔|X|✔|X|
|Rilevamento delle minacce senza file|✔|✔|X|X|
|Mappa di rete|✔|X|✔|X|
|Controlli adattivi della rete|✔|X|✔|X|


### <a name="supported-endpoint-protection-solutions"></a>Soluzioni di protezione endpoint supportate

La tabella seguente contiene una matrice che indica:
 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. In caso venga individuata una di queste soluzioni di protezione endpoint, il Centro sicurezza consiglierà di non installarne nessuna.

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| No, predefinito nel sistema operativo| Sì |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Sì |
| Trend Micro – Tutte le versioni | Famiglia Windows Server  | No  | Sì |
| Symantec v12.1.1100+| Famiglia Windows Server  | No  | Sì |
| McAfee v10+ | Famiglia Windows Server  | No  | Sì |
| Kaspersky| Famiglia Windows Server  | No  | No   |
| Sophos| Famiglia Windows Server  | No  | No   |

> [!NOTE]
> - Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell 3.0 (o versione successiva).
>
>

## <a name="supported-paas-features"></a>Funzionalità PaaS supportate 


|Service|Consigli|Introduzione al rilevamento delle minacce|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Account di archiviazione BLOB di Azure*|✔| ✔|
|Servizi app|✔| ✔|
|Servizi cloud|✔| X|
|Reti virtuali|✔| ND|
|Subnet|✔| ND|
|Schede di interfaccia di rete|✔| ✔|
|Gruppi di sicurezza di rete|✔| ND|
|Sottoscrizione|✔| ✔|

\* Queste funzionalità sono attualmente supportate in anteprima pubblica. 



## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).
- Leggere le informazioni su [analisi del comportamento delle macchine virtuali e analisi della memoria dump di arresto anomalo nel Centro sicurezza](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Vedere le [domande frequenti sull'uso del Centro sicurezza di Azure](security-center-faq.md).
- Vedere i [post di blog sulla sicurezza e sulla conformità di Azure](https://blogs.msdn.com/b/azuresecurity/).
