---
title: Funzionalità supportate disponibili nel Centro sicurezza di Azure Documenti Microsoft
description: Questo documento fornisce un elenco dei servizi supportati dal Centro sicurezza di Azure.This document provides a list of services supported by Azure Security Center.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245239"
---
# <a name="feature-coverage-for-machines"></a>Copertura delle funzioni per le macchine

Le tabelle seguenti mostrano le funzionalità del Centro sicurezza di Azure disponibili per le macchine virtuali e i server.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funzionalità supportate per macchine virtuali e serverSupported features for virtual machines and servers<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Computer Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità delle macchine virtuali di Azure**|**Macchine non Azure**|**Prezzi**
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Standard|
|[Virtual Machine Behavioral Analytics (e avvisi di sicurezza)](threat-protection.md)|✔|✔|✔|Raccomandazioni (gratuito) </br></br> Avvisi di sicurezza (Standard)|
|[Avvisi di sicurezza senza file](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Avvisi di sicurezza basati sulla rete](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Accesso alle macchine virtuali Just-In-Time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Valutazione nativa della vulnerabilità](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlli adattivi delle applicazioni](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Protezione avanzata della rete adattiva](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlli adattivi della rete|✔|✔|-|Standard|
|[Report & dashboard Conformità alle normative](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Raccomandazioni e protezione dalle minacce sui contenitori IaaS ospitati da Docker|-|-|-|Standard|
|Valutazione delle patch del sistema operativo mancante|✔|✔|✔|Gratuito|
|Valutazione degli errori di configurazione della sicurezza|✔|✔|✔|Gratuito|
|[Valutazione della protezione degli endpoint](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratuito|
|Valutazione della crittografia del disco|✔|✔|-|Gratuito|
|Valutazione della vulnerabilità di terze parti|✔|-|-|Gratuito|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|Gratuito|


### <a name="linux-machines"></a>[Computer Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità delle macchine virtuali di Azure**|**Macchine non Azure**|**Prezzi**
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standard|
|[Virtual Machine Behavioral Analytics (e avvisi di sicurezza)](security-center-alerts-iaas.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Raccomandazioni (gratuito) </br></br> Avvisi di sicurezza (Standard)|
|[Avvisi di sicurezza senza file](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Avvisi di sicurezza basati sulla rete](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Accesso alle macchine virtuali Just-In-Time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Valutazione nativa della vulnerabilità](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlli adattivi delle applicazioni](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Protezione avanzata della rete adattiva](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlli adattivi della rete|✔|✔|-|Standard|
|[Report & dashboard Conformità alle normative](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Raccomandazioni e protezione dalle minacce sui contenitori IaaS ospitati da Docker|✔|✔|✔|Standard|
|Valutazione delle patch del sistema operativo mancante|✔|✔|✔|Gratuito|
|Valutazione degli errori di configurazione della sicurezza|✔|✔|✔|Gratuito|
|[Valutazione della protezione degli endpoint](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuito|
|Valutazione della crittografia del disco|✔|✔|-|Gratuito|
|Valutazione della vulnerabilità di terze parti|✔|-|-|Gratuito|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|Gratuito|

--- 


> [!TIP]
>Per sperimentare le funzionalità disponibili solo nel piano tariffario standard, gli utenti del livello gratuito possono iscriversi a una versione di valutazione di 30 giorni. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluzioni di protezione degli endpoint supportate<a name="endpoint-supported"></a>

La tabella seguente contiene una matrice che indica:

 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. Se viene individuata una soluzione di protezione degli endpoint da questo elenco, il Centro sicurezza non consiglia di installarne una.

Per informazioni su quando vengono generati suggerimenti per ognuna di queste protezioni, vedere [Valutazione e raccomandazioni](security-center-endpoint-protection.md)di Endpoint Protection .

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| No, predefinito nel sistema operativo| Sì |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Sì |
| Trend Micro – Tutte le versioni | Famiglia Windows Server  | No | Sì |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia di server Linux  | No | Sì**\*** |
| Sophos V9| Famiglia di server Linux  | No | Sì**\***  |

 **\*** Lo stato di copertura e i dati di supporto sono attualmente disponibili solo nell'area di lavoro di Log Analytics associata alle sottoscrizioni protette. Non si riflette nel portale del Centro sicurezza di Azure.It's not reflected in the Azure Security Center portal.

> [!NOTE]
> - Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell 3.0 (o versione successiva).
> - Il rilevamento della protezione Trend Micro è supportato per gli agenti Deep Security.  Gli agenti OfficeScan non sono supportati.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati e l'agente log Analytics.](security-center-enable-data-collection.md)
- Scopri come il [Centro sicurezza gestisce e salvaguarda i dati.](security-center-data-security.md)
- Informazioni su come [pianificare e comprendere le considerazioni di progettazione per l'adozione](security-center-planning-and-operations-guide.md)del Centro sicurezza di Azure.
- Esaminare le [piattaforme che supportano](security-center-os-coverage.md)il Centro sicurezza .
- Altre informazioni sulla [protezione dalle minacce per i computer Windows e Linux nel Centro sicurezza](threat-protection.md#windows-machines)di Azure.
- Domande frequenti sul Centro sicurezza di [Azure.](faq-general.md)