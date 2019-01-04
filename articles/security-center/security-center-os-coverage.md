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
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 4108355415d1230f98db36a4f83497de2fa848f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185580"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Funzionalità e piattaforme supportate dal Centro sicurezza di Azure

Il monitoraggio dello stato della sicurezza e le raccomandazioni sono disponibili per le macchine virtuali (VM), create usando i modelli di distribuzione in versione classica e Resource Manager, e i computer.

> [!NOTE]
> È consigliabile leggere altre informazioni sui [modelli di distribuzione Azure Resource Manager e classica](../azure-classic-rm.md) per le risorse di Azure.
>
>

## <a name="supported-platforms"></a>Piattaforme supportate 

In questa sezione vengono elencate le piattaforme in cui può essere eseguito un agente del Centro sicurezza di Azure e da cui l'agente può raccogliere dati.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Piattaforme supportate per i computer e le macchine virtuali Windows
Sono supportati i sistemi operativi Windows seguenti:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


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

> [!NOTE]
> L'analisi del comportamento delle macchine virtuali non è ancora disponibile per i sistemi operativi Linux.
>
>

## <a name="vms-and-cloud-services"></a>Macchine virtuali e servizi cloud
Sono supportate anche le macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Funzionalità IaaS supportate

> [!div class="mx-tableFixed"]
> 

|Server| Windows||Linux||
|----|----|----|----|----|
|Environment|Azure|Non Azure|Azure|Non Azure|
|Avvisi di rilevamento minacce VMBA|✔|✔|✔ (nelle versioni supportate)|✔|
|Avvisi di rilevamento delle minacce di rete|✔|X|✔|X|
|Integrazione con Windows Defender ATP*|✔ (nelle versioni supportate)|✔|X|X|
|Patch mancanti|✔|✔|✔|✔|
|Configurazioni di sicurezza|✔|✔|✔|✔|
|Programmi antimalware|✔|✔|X|X|
|Accesso JIT alle VM|✔|X|✔|X|
|Controlli delle applicazioni adattivi|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|Crittografia del disco|✔|X|✔|X|
|Distribuzione di terze parti|✔|X|✔|X|
|Gruppi di sicurezza di rete|✔|X|✔|X|
|Rilevamento delle minacce senza file|✔|✔|X|X|
|Mappa di rete|✔|X|✔|X|
|Controlli adattivi della rete|✔|X|✔|X|

\* Queste funzionalità sono attualmente supportate in anteprima pubblica.


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
