---
title: Funzionalità supportate disponibili nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento contiene un elenco di servizi supportati dal Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 59a87e7c91266dd1ec2d7263df7149d07f6e7ce8
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342298"
---
# <a name="feature-coverage-for-machines"></a>Copertura delle funzionalità per le macchine virtuali

Le due schede seguenti illustrano le funzionalità del Centro sicurezza di Azure disponibili per server e macchine virtuali Windows e Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funzionalità supportate per server e macchine virtuali <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Computer Windows**](#tab/features-windows)

|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità di macchine virtuali di Azure**|**Computer con abilitazione di Azure Arc**|**Azure Defender necessario**
|----|:----:|:----:|:----:|:----:|
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Sì|
|[Analisi comportamentale delle macchine virtuali (e avvisi di sicurezza)](alerts-reference.md)|✔|✔|✔|Sì|
|[Avvisi di sicurezza senza file](alerts-reference.md#alerts-windows)|✔|✔|✔|Sì|
|[Avvisi di sicurezza basati sulla rete](other-threat-protections.md#network-layer)|✔|✔|-|Sì|
|[Accesso JIT alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Sì|
|[Valutazione della vulnerabilità nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Sì|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sì|
|[Controlli delle applicazioni adattivi](security-center-adaptive-application.md)|✔|-|✔|Sì|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Sì|
|[Protezione adattiva della rete](security-center-adaptive-network-hardening.md)|✔|-|-|Sì|
|[Dashboard e report di conformità alle normative](security-center-compliance-dashboard.md)|✔|✔|✔|Sì|
|Raccomandazioni e protezione dalle minacce nei contenitori IaaS ospitati da Docker|-|-|-|Sì|
|Valutazione delle patch del sistema operativo mancanti|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|Valutazione degli errori di configurazione della sicurezza|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|[Valutazione della protezione endpoint](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|Valutazione della crittografia dischi|✔</br>(per gli [scenari supportati](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|No|
|Valutazione della vulnerabilità di terze parti|✔|-|-|No|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|No|


### <a name="linux-machines"></a>[**Computer Linux**](#tab/features-linux)

|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità di macchine virtuali di Azure**|**Computer con abilitazione di Azure Arc**|**Azure Defender necessario**
|----|:----:|:----:|:----:|:----:|
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Sì|
|[Analisi comportamentale delle macchine virtuali (e avvisi di sicurezza)](./azure-defender.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Sì|
|[Avvisi di sicurezza senza file](alerts-reference.md#alerts-windows)|-|-|-|Sì|
|[Avvisi di sicurezza basati sulla rete](other-threat-protections.md#network-layer)|✔|✔|-|Sì|
|[Accesso JIT alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Sì|
|[Valutazione della vulnerabilità nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Sì|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sì|
|[Controlli delle applicazioni adattivi](security-center-adaptive-application.md)|✔|-|✔|Sì|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Sì|
|[Protezione adattiva della rete](security-center-adaptive-network-hardening.md)|✔|-|-|Sì|
|[Dashboard e report di conformità alle normative](security-center-compliance-dashboard.md)|✔|✔|✔|Sì|
|Raccomandazioni e protezione dalle minacce nei contenitori IaaS ospitati da Docker|✔|✔|✔|Sì|
|Valutazione delle patch del sistema operativo mancanti|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|Valutazione degli errori di configurazione della sicurezza|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|[Valutazione della protezione endpoint](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|No|
|Valutazione della crittografia dischi|✔</br>(per gli [scenari supportati](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|No|
|Valutazione della vulnerabilità di terze parti|✔|-|-|No|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|No|

--- 


> [!TIP]
>Per sperimentare le funzionalità disponibili solo con Azure Defender, è possibile registrarsi per ottenere una versione di valutazione valida 30 giorni. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluzioni di protezione endpoint supportate <a name="endpoint-supported"></a>

La tabella seguente contiene una matrice che indica:

 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. Se viene individuata una delle soluzioni di protezione endpoint di questo elenco, il Centro sicurezza non consiglierà di installarne una.

Per informazioni sui casi in cui vengono generate raccomandazioni per ognuna di queste protezioni, vedere [Valutazione e raccomandazioni per la protezione endpoint](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 o versione successiva| No, predefinito nel sistema operativo| Sì |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Sì |
| Trend Micro Deep Security | Famiglia Windows Server  | No | Sì |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia di server Linux  | No | Sì * *\** _ |
| Sophos V9+| Famiglia di server Linux  | No | Sì _*\**_  |

 _ *\** * I dati sullo stato di copertura e di supporto sono attualmente disponibili solo nell'area di lavoro Log Analytics associata alle sottoscrizioni protette. Non sono riportati nel portale del Centro sicurezza di Azure.

> [!NOTE]
> Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell (versione 3.0 o successiva).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul modo in cui il [Centro sicurezza raccoglie i dati tramite l'agente di Log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Informazioni sulle [piattaforme che supportano il Centro sicurezza](security-center-os-coverage.md).