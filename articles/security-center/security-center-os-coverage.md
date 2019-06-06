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
ms.date: 6/03/2019
ms.author: monhaber
ms.openlocfilehash: c5b5b88ee1334ac6d7b39b8ad53bd020e6042454
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480531"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Funzionalità e piattaforme supportate dal Centro sicurezza di Azure

Il monitoraggio dello stato della sicurezza e le raccomandazioni sono disponibili per le macchine virtuali (VM), create usando i modelli di distribuzione in versione classica e Resource Manager, e i computer.

> [!NOTE]
> È consigliabile leggere altre informazioni sui [modelli di distribuzione Azure Resource Manager e classica](../azure-classic-rm.md) per le risorse di Azure.
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

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Piattaforme supportate per i computer e le macchine virtuali Linux

Sono supportati i sistemi operativi Linux seguenti:

> [!NOTE]
> Poiché l'elenco dei sistemi operativi Linux supportati cambiano continuamente, se si preferisce, fare clic su [qui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) per visualizzare l'elenco più aggiornato delle versioni supportate, nel caso in cui sono state apportate modifiche dall'ultima pubblicazione in questo argomento.

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
* Ubuntu Linux 14.04 LTS e 16.04 LTS

## <a name="vms-and-cloud-services"></a>Macchine virtuali e servizi cloud
Sono supportate anche le macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Funzionalità IaaS supportate

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||||Prezzi|
|----|----|----|----|----|----|----|----|
|**Environment**|**Azure**||**Non Azure**|**Azure**||**Non Azure**||
||**Macchina virtuale**|**Set di scalabilità della macchina virtuale**||**Macchina virtuale**|**Set di scalabilità della macchina virtuale**|
|Avvisi di rilevamento minacce VMBA|✔|✔|✔|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|Rilevamento delle minacce (gratuito) per le raccomandazioni (Standard)|
|Avvisi di rilevamento delle minacce di rete|✔|✔|X|✔|✔|X|Standard|
|Integrazione con Windows Defender ATP|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|X|X|X|Standard|
|Patch mancanti|✔|✔|✔|✔|✔|✔|Gratuito|
|Configurazioni di sicurezza|✔|✔|✔|✔|✔|✔|Gratuito|
|Valutazione della protezione di endpoint|✔|✔|✔|X|X|X|Gratuito|
|Accesso JIT alle VM|✔|X|X|✔|X|X|Standard|
|Controlli delle applicazioni adattivi|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Valutazione di crittografia del disco|✔|✔|X|✔|✔|X|Gratuito|
|Distribuzione di terze parti|✔|X|X|✔|X|X|Gratuito|
|Valutazione della sicurezza di rete|✔|✔|X|✔|✔|X|Gratuito|
|Rilevamento delle minacce senza file|✔|✔|✔|X|X|X|Standard|
|Mappa di rete|✔|✔|X|✔|✔|X|Standard|
|Controlli adattivi della rete|✔|✔|X|✔|✔|X|Standard|
|I report e dashboard della conformità alle normative|✔|✔|✔|✔|✔|✔|Standard|
|Le raccomandazioni e rilevamento minacce nei contenitori Docker ospitati IaaS|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Soluzioni di protezione endpoint supportate

La tabella seguente contiene una matrice che indica:
 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. In caso venga individuata una di queste soluzioni di protezione endpoint, il Centro sicurezza consiglierà di non installarne nessuna.

Per informazioni su quando vengono generate indicazioni per ognuna di queste protezioni, vedere [valutazione della protezione di Endpoint e le raccomandazioni](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| No, predefinito nel sistema operativo| Yes |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Yes |
| Trend Micro – Tutte le versioni | Famiglia Windows Server  | No | Yes |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Yes |
| McAfee v10+ | Famiglia Windows Server  | No | Yes |
| Kaspersky| Famiglia Windows Server  | No | No  |
| Sophos| Famiglia Windows Server  | No | No  |

> [!NOTE]
> - Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell 3.0 (o versione successiva).

## <a name="supported-paas-features"></a>Funzionalità PaaS supportate


|Service|Raccomandazioni (gratuite)|Rilevamento delle minacce (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Account di archiviazione Blob di Azure|✔| ✔|
|Servizi app|✔| ✔|
|Servizi cloud|✔| X|
|Reti virtuali|✔| NA|
|Subnet|✔| NA|
|Schede di interfaccia di rete|✔| NA|
|Gruppi di sicurezza di rete|✔| NA|
|Sottoscrizione|✔ **| ✔|
|Batch|✔| NA|
|Service Fabric|✔| NA|
|Account di Automazione|✔| NA|
|Bilanciamento del carico|✔| NA|
|Ricerca|✔| NA|
|Bus di servizio|✔| NA|
|Analisi dei flussi|✔| NA|
|Hub eventi|✔| NA|
|App per la logica|✔| NA|
|Account di archiviazione|✔| NA|
|Redis|✔| NA|
|Data Lake Analytics|✔| NA|
|Insieme di credenziali delle chiavi|✔| NA|




\* Queste funzionalità sono attualmente supportate in anteprima pubblica.

\*\* Consigli di AAD sono disponibili solo per le sottoscrizioni Standard



## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).
- Leggere le informazioni su [analisi del comportamento delle macchine virtuali e analisi della memoria dump di arresto anomalo nel Centro sicurezza](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Vedere le [domande frequenti sull'uso del Centro sicurezza di Azure](security-center-faq.md).
- Vedere i [post di blog sulla sicurezza e sulla conformità di Azure](https://blogs.msdn.com/b/azuresecurity/).
