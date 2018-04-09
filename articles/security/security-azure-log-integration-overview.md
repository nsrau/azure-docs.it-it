---
title: Integrare i log delle risorse di Azure con i sistemi di informazioni di sicurezza e gestione degli eventi | Microsoft Docs
description: Informazioni, funzionalità principali e funzionamento di Integrazione log di Azure.
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
ms.openlocfilehash: 6d91692a64a4d3def80990a439fe0a0898bf2f09
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-log-integration"></a>Introduzione a Integrazione log di Azure

È possibile usare Integrazione log di Azure per integrare log non elaborati delle risorse di Azure nei sistemi di informazioni di sicurezza e gestione degli eventi locali. Usare Integrazione log di Azure solo se presso il fornitore del sistema di informazioni di sicurezza e gestione degli eventi non è disponibile un connettore a [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-get-started.md).

Il metodo preferito per l'integrazione dei log di Azure consiste nell'usare il connettore di Monitoraggio di Azure del fornitore del sistema di informazioni di sicurezza e gestione degli eventi. Per usare il connettore, seguire le istruzioni in [Monitoraggio dei flussi per gli hub eventi dati](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). 

Se tuttavia il fornitore del sistema di informazioni di sicurezza e gestione degli eventi non offre un connettore per Monitoraggio di Azure, è possibile usare Integrazione log di Azure come soluzione temporanea finché il connettore non è disponibile. È possibile usare Integrazione log di Azure solo se Integrazione log di Azure supporta il sistema di informazioni di sicurezza e gestione degli eventi in uso.

> [!IMPORTANT]
> Se l'obiettivo principale è la raccolta di log di macchine virtuali, tenere presente che la maggior parte dei fornitori SIEM include questa opzione nella soluzione offerta. L'uso del connettore del fornitore SIEM è sempre l'opzione preferita.

Integrazione log di Azure raccoglie gli eventi di Windows dai log del Visualizzatore eventi di Windows e dai [log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), dagli [avvisi del Centro sicurezza di Azure](../security-center/security-center-intro.md) e dai [log di Diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) di risorse di Azure. L'integrazione consente alla soluzione di informazioni di sicurezza e gestione degli eventi di offrire un dashboard unificato per tutti gli asset, sia locali che nel cloud. Per ricevere, aggregare, correlare e analizzare gli avvisi per gli eventi di sicurezza, è possibile usare un dashboard.

> [!NOTE]
> Integrazione log di Azure attualmente supporta solo cloud commerciali di Azure e cloud Azure per enti pubblici. Gli altri cloud non sono supportati.

![Diagramma del processo di Integrazione log di Azure][1]

## <a name="what-logs-can-i-integrate"></a>Log che è possibile integrare

Azure produce registrazioni estese per ogni servizio di Azure. Esistono tre tipi di log:

* **Log di gestione/controllo**: offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Un log attività di Azure rappresenta un esempio di questo tipo di log.
* **Log di piano dati**: consente di visualizzare gli eventi che vengono generati quando si usa una risorsa di Azure. Un esempio di questo tipo di log sono i canali **Sistema**, **Sicurezza** e **Applicazione** del Visualizzatore eventi di Windows in una macchina virtuale Windows. Un altro esempio è la registrazione di Diagnostica di Azure configurata tramite Monitoraggio di Azure.
* **Eventi elaborati**: offrono informazioni sugli eventi e sugli avvisi analizzati elaborate automaticamente. Un esempio di questo tipo di evento è rappresentato dagli avvisi del Centro sicurezza di Azure. Centro sicurezza di Azure elabora e analizza la sottoscrizione per fornire gli avvisi pertinenti alle condizioni di sicurezza correnti.

L'integrazione dei log di Azure supporta ArcSight, QRadar e Splunk. Verificare con il fornitore del sistema di informazioni di sicurezza e gestione degli eventi se è disponibile un connettore nativo. Non usare Integrazione log di Azure se è disponibile un connettore nativo.

Se non sono disponibili altre opzioni, è consigliabile usare Integrazione log di Azure. La tabella seguente include i suggerimenti Microsoft:

|Sistema di informazioni di sicurezza e gestione degli eventi | Cliente che usa già Integrazione log di Azure | Cliente che sta esplorando le opzioni di integrazione di informazioni di sicurezza e gestione degli eventi|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Iniziare passando al [componente aggiuntivo di Monitoraggio di Azure per Splunk](https://splunkbase.splunk.com/app/3534/). | Usare il [connettore Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Eseguire la migrazione al connettore QRadar o iniziare a usarlo. Il connettore è documentato nell'ultima sezione di [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). | Usare il connettore QRadar documentato nell'ultima sezione di [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Continuare a usare Integrazione log di Azure finché non è disponibile un connettore e quindi eseguire la migrazione alla soluzione basata sul connettore.  | In alternativa, si considera di usare Azure Log Analytics. Evitare di usare Integrazione log di Azure, in modo da non dover affrontare il processo di migrazione quando il connettore sarà disponibile. |

> [!NOTE]
> Integrazione log di Azure è una soluzione gratuita, ma ci sono costi di archiviazione di Azure derivanti dall'archiviazione di informazioni nei file di log.

In caso di necessità, creare una [richiesta di assistenza](../azure-supportability/how-to-create-azure-support-request.md). Per il servizio, selezionare **Integrazione log**.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo rappresenta l'introduzione a Integrazione log di Azure. Per altre informazioni su Integrazione log di Azure e sui tipi di log supportati, vedere gli articoli seguenti:

* [Introduzione a Integrazione log di Azure](security-azure-log-integration-get-started.md). Questa esercitazione guida l'utente attraverso l'installazione di Integrazione log di Azure e descrive come integrare log da risorse di archiviazione di Diagnostica di Microsoft Azure, log attività di Azure, avvisi del Centro sicurezza di Azure e log di controllo di Azure Active Directory.
* [Domande frequenti su Integrazione log di Azure](security-azure-log-integration-faq.md). Queste domande frequenti riguardano Integrazione log di Azure.
* Altre informazioni su come [trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
