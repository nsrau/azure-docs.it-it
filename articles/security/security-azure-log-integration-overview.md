---
title: Integrare i log delle risorse di Azure nei sistemi SIEM | Microsoft Docs
description: "Informazioni, funzionalità principali e funzionamento di Integrazione log di Azure."
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
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introduzione a Integrazione log di Microsoft Azure

Integrazione log di Azure consente di integrare i log non elaborati delle risorse di Azure nei sistemi SIEM (informazioni di sicurezza e gestione degli eventi) nel caso in cui un connettore a [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-get-started.md) non sia ancora disponibile per il fornitore SIEM.

Il metodo preferito per l'integrazione dei log di Azure consiste nell'usare un connettore per Monitoraggio di Azure del fornitore SIEM e seguire queste [istruzioni](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Tuttavia, se il fornitore SIEM non offre un connettore per Monitoraggio di Azure, è possibile usare Integrazione log di Azure come soluzione temporanea (se SIEM è supportato da Integrazione log di Azure) fino a quando non sia disponibile un connettore di questo tipo.

>[!IMPORTANT]
>Se l'obiettivo principale è la raccolta di log di macchine virtuali, tenere presente che la maggior parte dei fornitori SIEM include questa opzione nella soluzione offerta. L'uso del connettore del fornitore SIEM deve costituire sempre l'opzione preferita.

L'integrazione dei log di Azure raccoglie gli eventi di Windows da log del Visualizzatore eventi di Windows, [log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [avvisi del Centro sicurezza di Azure](../security-center/security-center-intro.md) e [log di Diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) dalle risorse di Azure. Questa integrazione aiuta il sistema SIEM a fornire un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza.

>[!NOTE]
Al momento i soli cloud supportati sono quello commerciale e quello di Azure per enti pubblici. Gli altri cloud non sono supportati.

![Integrazione log di Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?

Azure produce registrazioni complete per ogni servizio di Azure. Questi log rappresentano tre tipi di log:

* I **log di gestione/controllo** che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). I log attività di Azure sono un esempio di questo tipo di log.
* I **log del piano dati** che offrono visibilità sugli eventi generati come parte dell'uso di una risorsa di Azure. Un esempio di questo tipo di log sono i canali **Sistema**, **Sicurezza** e **Applicazione** del Visualizzatore eventi di Windows in una macchina virtuale Windows. Un altro esempio è la registrazione di diagnostica configurata tramite Monitor di Azure
* Gli **eventi elaborati** forniscono eventi analizzati e informazioni sugli avvisi elaborate per conto del cliente. Un esempio di questo tipo di evento è costituito dagli avvisi del Centro sicurezza di Azure, in cui il Centro sicurezza di Azure ha elaborato e analizzato la sottoscrizione per fornire avvisi pertinenti alle condizioni di sicurezza attuali.

Integrazione log di Azure supporta ArcSight, QRadar e Splunk. Verificare sempre con il fornitore SIEM se è disponibile un connettore nativo. Non è opportuno usare Integrazione log di Azure quando sono disponibili i connettori nativi.

Se non sono presenti altre opzioni, considerare l'uso di Integrazione log di Azure. La tabella seguente include i suggerimenti Microsoft.

|**SIEM** | **Cliente che ha già adottato Integrazione log** | **Cliente che sta esplorando le opzioni dell'integrazione SIEM**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | Avviare la migrazione al [componente aggiuntivo di Monitoraggio di Azure per Splunk](https://splunkbase.splunk.com/app/3534/) | Usare il [connettore SPLUNK](https://splunkbase.splunk.com/app/3534/) |
|**IBM QRADAR** | Eseguire la migrazione o avviarla tramite il connettore QRadar descritto alla fine dell'articolo disponibile all'indirizzo http://aka.ms/azmoneventhub | Usare il connettore QRadar descritto alla fine dell'articolo disponibile all'indirizzo http://aka.ms/azmoneventhub  |
|**ARCSIGHT** | Continuare a usare Integrazione log fino a quando non è disponibile un connettore, quindi eseguire la migrazione alla soluzione basata sul connettore.  | In alternativa, usare Azure Log Analytics. Evitare di usare Integrazione log di Azure, in modo da non dover affrontare il processo di migrazione quando il connettore risulta disponibile. |

>[!NOTE]
>L'integrazione dei log di Azure è una soluzione gratuita, ma ci saranno costi di archiviazione di Azure derivanti dall'archiviazione di informazioni nel file di log.

Per ricevere assistenza, aprire [una richiesta di supporto](../azure-supportability/how-to-create-azure-support-request.md). A tale scopo selezionare **Integrazione log** come servizio per cui richiedere supporto.

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stata presentata l'integrazione dei log di Azure. Per altre informazioni sull'integrazione dei log di Azure e sui tipi di log supportati, vedere gli argomenti seguenti:

* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-get-started.md) - Questa esercitazione illustra come installare l'integrazione dei log di Azure e integrare i log dall'archiviazione di Azure WAD, i log attività di Azure, gli avvisi del Centro sicurezza di Azure e i log di controllo di Azure Active Directory.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
* [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
