---
title: Funzionalità supportate disponibili nel centro sicurezza di Azure | Microsoft Docs
description: Questo documento fornisce un elenco dei servizi supportati dal centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: c4f3ccce03f91b0567980d55b59bfb15d6985bb7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299746"
---
# <a name="feature-coverage-for-machines"></a>Copertura delle funzionalità per i computer

Le due schede seguenti illustrano le funzionalità del Centro sicurezza di Azure disponibili per le macchine virtuali e i server Windows e Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funzionalità supportate per le macchine virtuali e i server <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Computer Windows**](#tab/features-windows)

|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità di macchine virtuali di Azure**|**Computer non Azure**|**Prezzi**
|----|:----:|:----:|:----:|:----:|
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Standard|
|[Analisi del comportamento delle macchine virtuali (e avvisi di sicurezza)](threat-protection.md)|✔|✔|✔|Standard|
|[Avvisi di sicurezza non file](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Avvisi di sicurezza basati sulla rete](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Accesso JIT alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Standard|
|[Valutazione della vulnerabilità nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Standard|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlli delle applicazioni adattivi](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Protezione adattiva della rete](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[Dashboard di conformità normativa & report](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Raccomandazioni e protezione dalle minacce nei contenitori IaaS ospitati da Docker|-|-|-|Standard|
|Valutazione patch del sistema operativo mancanti|✔|✔|✔|Azure: gratuito<br><br>Non Azure: standard|
|Valutazione delle configurazioni errate della sicurezza|✔|✔|✔|Azure: gratuito<br><br>Non Azure: standard|
|[Valutazione di Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: gratuito<br><br>Non Azure: standard|
|Valutazione della crittografia del disco|✔|✔|-|Livello gratuito|
|Valutazione della vulnerabilità di terze parti|✔|-|-|Livello gratuito|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|Livello gratuito|


### <a name="linux-machines"></a>[**Computer Linux**](#tab/features-linux)

|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità di macchine virtuali di Azure**|**Computer non Azure**|**Prezzi**
|----|:----:|:----:|:----:|:----:|
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standard|
|[Analisi del comportamento delle macchine virtuali (e avvisi di sicurezza)](security-center-alerts-iaas.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Standard|
|[Avvisi di sicurezza non file](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Avvisi di sicurezza basati sulla rete](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Accesso JIT alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Standard|
|[Valutazione della vulnerabilità nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Standard|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlli delle applicazioni adattivi](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Protezione adattiva della rete](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[Dashboard di conformità normativa & report](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Raccomandazioni e protezione dalle minacce nei contenitori IaaS ospitati da Docker|✔|✔|✔|Standard|
|Valutazione patch del sistema operativo mancanti|✔|✔|✔|Azure: gratuito<br><br>Non Azure: standard|
|Valutazione delle configurazioni errate della sicurezza|✔|✔|✔|Azure: gratuito<br><br>Non Azure: standard|
|[Valutazione di Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Livello gratuito|
|Valutazione della crittografia del disco|✔|✔|-|Livello gratuito|
|Valutazione della vulnerabilità di terze parti|✔|-|-|Livello gratuito|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|Livello gratuito|

--- 


> [!TIP]
>Per sperimentare le funzionalità disponibili solo nel piano tariffario standard, gli utenti del livello gratuito possono iscriversi a una versione di valutazione di 30 giorni. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluzioni di Endpoint Protection supportate <a name="endpoint-supported"></a>

La tabella seguente contiene una matrice che indica:

 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. Se viene individuata una soluzione di Endpoint Protection da questo elenco, il Centro sicurezza non consiglia di installarne uno.

Per informazioni sul momento in cui vengono generate le indicazioni per ognuna di queste protezioni, vedere [Endpoint Protection Assessment and raccomandazioni](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 o versione successiva| No, predefinito nel sistema operativo| Sì |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Sì |
| Trend Micro-Deep Security | Famiglia Windows Server  | No | Sì |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia di server Linux  | No | Sì **\*** |
| Sophos V9 +| Famiglia di server Linux  | No | Sì  **\***  |

 **\*** Lo stato di code coverage e i dati di supporto sono attualmente disponibili solo nell'area di lavoro Log Analytics associata alle sottoscrizioni protette. Non viene riflessa nel portale del Centro sicurezza di Azure.

> [!NOTE]
> Il rilevamento di System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2 richiede l'installazione di SCEP dopo PowerShell (v 3.0 o versione successiva).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati e l'agente di log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Esaminare le [piattaforme che supportano il Centro sicurezza](security-center-os-coverage.md).