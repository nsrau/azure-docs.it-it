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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79245239"
---
# <a name="feature-coverage-for-machines"></a>Copertura delle funzionalità per i computer

Le tabelle seguenti illustrano le funzionalità del Centro sicurezza di Azure disponibili per le macchine virtuali e i server.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funzionalità supportate per le macchine virtuali e i server<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Computer Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità delle macchine virtuali di Azure**|**Computer non Azure**|**Prezzi**
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Standard|
|[Analisi del comportamento delle macchine virtuali (e avvisi di sicurezza)](threat-protection.md)|✔|✔|✔|Raccomandazioni (gratuito) </br></br> Avvisi di sicurezza (standard)|
|[Avvisi di sicurezza non file](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Avvisi di sicurezza basati sulla rete](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Accesso just-in-time alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Standard|
|[Valutazione della vulnerabilità nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlli delle applicazioni adattivi](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Protezione adattiva della rete](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlli adattivi della rete|✔|✔|-|Standard|
|[Dashboard di conformità normativa & report](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Raccomandazioni e protezione dalle minacce nei contenitori IaaS ospitati da Docker|-|-|-|Standard|
|Valutazione patch del sistema operativo mancanti|✔|✔|✔|Gratuito|
|Valutazione delle configurazioni errate della sicurezza|✔|✔|✔|Gratuito|
|[Valutazione di Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratuito|
|Valutazione della crittografia del disco|✔|✔|-|Gratuito|
|Valutazione della vulnerabilità di terze parti|✔|-|-|Gratuito|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|Gratuito|


### <a name="linux-machines"></a>[Computer Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità delle macchine virtuali di Azure**|**Computer non Azure**|**Prezzi**
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standard|
|[Analisi del comportamento delle macchine virtuali (e avvisi di sicurezza)](security-center-alerts-iaas.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Raccomandazioni (gratuito) </br></br> Avvisi di sicurezza (standard)|
|[Avvisi di sicurezza non file](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Avvisi di sicurezza basati sulla rete](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Accesso just-in-time alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Standard|
|[Valutazione della vulnerabilità nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlli delle applicazioni adattivi](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Protezione adattiva della rete](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlli adattivi della rete|✔|✔|-|Standard|
|[Dashboard di conformità normativa & report](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Raccomandazioni e protezione dalle minacce nei contenitori IaaS ospitati da Docker|✔|✔|✔|Standard|
|Valutazione patch del sistema operativo mancanti|✔|✔|✔|Gratuito|
|Valutazione delle configurazioni errate della sicurezza|✔|✔|✔|Gratuito|
|[Valutazione di Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuito|
|Valutazione della crittografia del disco|✔|✔|-|Gratuito|
|Valutazione della vulnerabilità di terze parti|✔|-|-|Gratuito|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|Gratuito|

--- 


> [!TIP]
>Per sperimentare le funzionalità disponibili solo nel piano tariffario standard, gli utenti del livello gratuito possono iscriversi a una versione di valutazione di 30 giorni. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluzioni di Endpoint Protection supportate<a name="endpoint-supported"></a>

La tabella seguente contiene una matrice che indica:

 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. Se viene individuata una soluzione di Endpoint Protection da questo elenco, il Centro sicurezza non consiglia di installarne uno.

Per informazioni sul momento in cui vengono generate le indicazioni per ognuna di queste protezioni, vedere [Endpoint Protection Assessment and raccomandazioni](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| No, predefinito nel sistema operativo| Sì |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Sì |
| Tendenza Micro-tutte le versioni * | Famiglia Windows Server  | No | Sì |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia di server Linux  | No | Sì**\*** |
| Sophos V9 +| Famiglia di server Linux  | No | Sì**\***  |

 **\*** Lo stato di code coverage e i dati di supporto sono attualmente disponibili solo nell'area di lavoro Log Analytics associata alle sottoscrizioni protette. Non viene riflessa nel portale del Centro sicurezza di Azure.

> [!NOTE]
> - Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell 3.0 (o versione successiva).
> - Il rilevamento di tendenza Micro Protection è supportato per gli agenti di sicurezza approfonditi.  Gli agenti OfficeScan non sono supportati.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati e l'agente di log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Informazioni su come [pianificare e comprendere le considerazioni di progettazione per l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).
- Esaminare le [piattaforme che supportano il Centro sicurezza](security-center-os-coverage.md).
- Scopri di più sulla [protezione dalle minacce per i computer Windows e Linux nel centro sicurezza di Azure](threat-protection.md#windows-machines).
- Trova le [domande frequenti sul centro sicurezza di Azure](faq-general.md).