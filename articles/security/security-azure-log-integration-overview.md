---
title: Introduzione all&quot;integrazione dei log di Microsoft Azure | Documentazione Microsoft
description: "Informazioni sull&quot;integrazione dei log di Azure, le funzionalità principali e il funzionamento."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 68c0e3c73b3192aedc18ec3588f1201b3aa36f35


---
# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Introduzione all'integrazione dei log di Microsoft Azure (Anteprima)
Informazioni sull'integrazione dei log di Azure, le funzionalità principali e il funzionamento.

## <a name="overview"></a>Overview
I servizi PaaS (Platform as a Service, piattaforma distribuita come servizio) e IaaS (Infrastructure as a Service, infrastruttura distribuita come servizio) ospitati in Azure generano una grande quantità di dati nei log di sicurezza. Questi log contengono dati importanti che possono fornire informazioni utili e approfondite sulle violazioni dei criteri, le minacce interne ed esterne, i problemi di conformità alle normative e le anomalie della rete, dell'host e dell'attività dell'utente.

L'integrazione dei log di Azure consente di integrare log non elaborati delle risorse di Azure nei sistemi di gestione di informazioni ed eventi di sicurezza locali. L'integrazione dei log di Azure raccoglie i dati di Diagnostica di Azure dalle macchine virtuali Windows *(WAD)*, i log attività di Azure, gli avvisi del Centro sicurezza di Azure e i log dei provider di risorse di Azure. Questa integrazione fornisce un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza.

![Integrazione dei log di Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?
Azure produce registrazioni complete per ogni servizio di Azure. I log sono classificati in due tipi principali:

* **Log di gestione/controllo**, che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager. I log di controllo di Azure sono un esempio di questo tipo di log.
* **Log del piano dati**, che offrono visibilità sugli eventi generati come parte dell'uso di una risorsa di Azure. Esempi di questi tipi di log sono rappresentati dal registro di sistema di Windows, dai log di sicurezza e delle applicazioni di una macchina virtuale.

L'integrazione dei log di Azure supporta attualmente l'integrazione dei log di controllo di Azure, dei log delle macchine virtuali e degli avvisi del Centro sicurezza di Azure.

In caso di domande sull'integrazione dei log di Azure, inviare un messaggio di posta elettronica all'indirizzo [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stata presentata l'integrazione dei log di Azure. Per altre informazioni sull'integrazione dei log di Azure e sui tipi di log supportati, vedere gli argomenti seguenti:

* [Integrazione dei log di Microsoft Azure per i log di Azure (Anteprima)](https://www.microsoft.com/download/details.aspx?id=53324) – Area download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione sull'integrazione dei log di Azure.
* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-get-started.md) - Questa esercitazione illustra come installare l'integrazione dei log di Azure e integrare i log dall'archiviazione di Azure, i log di controllo di Azure e gli avvisi del Centro sicurezza.
* [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
* [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) - Questo documento mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.
* [Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) - Questo post di blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png



<!--HONumber=Dec16_HO2-->


