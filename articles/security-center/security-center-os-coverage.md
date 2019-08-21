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
ms.author: v-mohabe
ms.openlocfilehash: 1f5a144728614ee13bf4f08bb594f0546d446880
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640792"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Funzionalità e piattaforme supportate dal Centro sicurezza di Azure

Il monitoraggio dello stato della sicurezza e le raccomandazioni sono disponibili per le macchine virtuali (VM), create usando i modelli di distribuzione in versione classica e Resource Manager, e i computer.

> [!NOTE]
> È consigliabile leggere altre informazioni sui [modelli di distribuzione Azure Resource Manager e classica](../azure-classic-rm.md) per le risorse di Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Piattaforme che supportano l'agente di raccolta dati 
Questa sezione elenca le piattaforme in cui è possibile eseguire l'agente di Log Analytics, usato dal centro sicurezza di Azure.

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
> Poiché l'elenco dei sistemi operativi Linux supportati è in continua evoluzione, se lo si preferisce, fare clic [qui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) per visualizzare l'elenco più aggiornato delle versioni supportate, nel caso in cui siano state apportate modifiche dall'ultima pubblicazione di questo argomento.

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

## <a name="vms-and-cloud-services"></a>Macchine virtuali e servizi cloud
Sono supportate anche le macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Funzionalità IaaS supportate

> [!div class="mx-tableFixed"]
> 

|Server|Windows|||Linux|||Prezzi|
|----|----|----|----|----|----|----|----|
|**Environment**|**Azure**||**Non Azure**|**Azure**||**Non Azure**||
||**Macchina virtuale**|**Set di scalabilità della macchina virtuale**||**Macchina virtuale**|**Set di scalabilità della macchina virtuale**|
|Avvisi di rilevamento minacce VMBA|✔|✔|✔|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|Indicazioni (gratuite) rilevamento minacce (standard)|
|Avvisi di rilevamento delle minacce di rete|✔|✔|x|✔|✔|x|Standard|
|Integrazione con Windows Defender ATP|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|X|X|X|Standard|
|Patch mancanti|✔|✔|✔|✔|✔|✔|Gratuito|
|Configurazioni della sicurezza|✔|✔|✔|✔|✔|✔|Gratuito|
|Valutazione di Endpoint Protection|✔|✔|✔|X|X|x|Gratuito|
|Accesso JIT alle VM|✔|X|X|✔|x|X|Standard|
|Controlli applicazione adattivi|✔|x|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Valutazione della crittografia del disco|✔|✔|X|✔|✔|X|Gratuito|
|Distribuzione di terze parti|✔|X|X|✔|x|x|Gratuito|
|Valutazione NSG|✔|✔|x|✔|✔|X|Gratuito|
|Rilevamento delle minacce senza file|✔|✔|✔|X|X|x|Standard|
|Mappa di rete|✔|✔|X|✔|✔|X|Standard|
|Controlli adattivi della rete|✔|✔|X|✔|✔|X|Standard|
|Dashboard di conformità normativa & report|✔|✔|✔|✔|✔|✔|Standard|
|Raccomandazioni e rilevamento delle minacce nei contenitori IaaS ospitati da Docker|x|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Soluzioni di protezione endpoint supportate

La tabella seguente contiene una matrice che indica:
 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. In caso venga individuata una di queste soluzioni di protezione endpoint, il Centro sicurezza consiglierà di non installarne nessuna.

Per informazioni sul momento in cui vengono generate le indicazioni per ognuna di queste protezioni, vedere [Endpoint Protection Assessment and raccomandazioni](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| No, predefinito nel sistema operativo| Sì |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Sì |
| Tendenza Micro-tutte le versioni * | Famiglia Windows Server  | No | Sì |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia di server Linux  | No | Sì **\*** |
| Sophos V9 +| Famiglia di server Linux  | No | Sì **\***  |

 **\*** Lo stato di code coverage e i dati di supporto sono attualmente disponibili solo nell'area di lavoro Log Analytics associata alle sottoscrizioni protette e non vengono riflesse nel portale del Centro sicurezza di Azure.

> [!NOTE]
> - Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell 3.0 (o versione successiva).
> - Il rilevamento di tendenza Micro Protection è supportato per gli agenti di sicurezza approfonditi.  Gli agenti OfficeScan non sono supportati.

## <a name="supported-paas-features"></a>Funzionalità PaaS supportate


|Service|Raccomandazioni (gratuito)|Rilevamento delle minacce (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Account di archiviazione BLOB di Azure|✔| ✔|
|Servizi app|✔| ✔|
|Servizi cloud|✔| x|
|Reti virtuali|✔| ND|
|Subnet|✔| ND|
|Schede di interfaccia di rete|✔| ND|
|Gruppi di sicurezza di rete|✔| ND|
|Sottoscrizione|✔ **| ✔|
|Batch|✔| ND|
|Service Fabric|✔| ND|
|Account di Automazione|✔| ND|
|Servizio di bilanciamento del carico|✔| ND|
|Cerca|✔| ND|
|Bus di servizio|✔| ND|
|Analisi di flusso|✔| ND|
|Hub eventi|✔| ND|
|App per la logica|✔| ND|
|Account di archiviazione|✔| ND|
|Redis|✔| ND|
|Data Lake Analytics|✔| ND|
|Insieme di credenziali delle chiavi|✔| ND|




\* Queste funzionalità sono attualmente supportate in anteprima pubblica.

\*\*Le raccomandazioni di AAD sono disponibili solo per le sottoscrizioni standard



## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).
- Altre informazioni sul [rilevamento delle minacce per le macchine virtuali & Server nel centro sicurezza di Azure](security-center-alerts-iaas.md).
- Vedere le [domande frequenti sull'uso del Centro sicurezza di Azure](security-center-faq.md).
- Vedere i [post di blog sulla sicurezza e sulla conformità di Azure](https://blogs.msdn.com/b/azuresecurity/).
