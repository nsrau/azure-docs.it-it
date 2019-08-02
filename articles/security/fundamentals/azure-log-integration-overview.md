---
title: Integrare i log delle risorse di Azure con i sistemi di informazioni di sicurezza e gestione degli eventi | Microsoft Docs
description: Informazioni, funzionalità principali e funzionamento di Integrazione log di Azure.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 31d6512d7fd24a1a0abafb3b653b6d031176b274
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614290"
---
# <a name="introduction-to-azure-log-integration"></a>Introduzione a Integrazione log di Azure

>[!IMPORTANT]
> La funzionalità di integrazione dei log di Azure verrà deprecata da 06/15/2019. Il download di AzLog è stato disabilitato il 27 giugno 2018. Per materiale sussidiario su cosa fare dopo, vedere il post [Use Azure monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Usare Monitoraggio di Azure per eseguire l'integrazione con gli strumenti per le informazioni di sicurezza e gestione degli eventi) 

Lo scopo di Integrazione log di Azure è semplificare l'attività di integrazione dei log di Azure nei sistemi di informazioni di sicurezza e gestione degli eventi locali.

 Il metodo consigliato per l'integrazione dei log di Azure è l'uso dei connettori del fornitore di informazioni di sicurezza e gestione degli eventi. Monitoraggio di Azure offre la possibilità di trasmettere i log all'interno di hub eventi. I fornitori di informazioni di sicurezza e gestione degli eventi possono scrivere connettori per integrare ulteriormente i log dall'hub eventi nelle informazioni di sicurezza e gestione degli eventi.  Per una descrizione del funzionamento, seguire le istruzioni in [Monitoraggio dei flussi per gli hub eventi dati](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). L'articolo elenca anche le informazioni di sicurezza e gestione degli eventi per le quali sono già disponibili connettori di Azure diretti.  

> [!IMPORTANT]
> Se l'obiettivo principale è la raccolta di log di macchine virtuali, tenere presente che la maggior parte dei fornitori SIEM include questa opzione nella soluzione offerta. L'uso del connettore del fornitore SIEM è sempre l'opzione preferita.

La documentazione sulla funzionalità Integrazione log di Azure verrà ancora gestita fino a quando la funzionalità non verrà dichiarata deprecata.

Continuare a leggere per altre informazioni sulla funzionalità Integrazione log di Azure:

Integrazione log di Azure raccoglie gli eventi di Windows dai log del Visualizzatore eventi di Windows e dai [log attività di Azure](/azure/azure-monitor/platform/activity-logs-overview), dagli [avvisi del Centro sicurezza di Azure](/azure/security-center/security-center-intro) e dai [log di Diagnostica di Azure](/azure/azure-monitor/platform/diagnostic-logs-overview) di risorse di Azure. L'integrazione consente alla soluzione di informazioni di sicurezza e gestione degli eventi di offrire un dashboard unificato per tutti gli asset, sia locali che nel cloud. Per ricevere, aggregare, correlare e analizzare gli avvisi per gli eventi di sicurezza, è possibile usare un dashboard.

> [!NOTE]
> Integrazione log di Azure attualmente supporta solo cloud commerciali di Azure e cloud Azure per enti pubblici. Gli altri cloud non sono supportati.

![Processo di Integrazione log di Azure](media/azure-log-integration-overview/azure-log-integration.png)

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?

Azure produce registrazioni estese per ogni servizio di Azure. Esistono tre tipi di log:

* **Log di gestione/controllo**: offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview). Un log attività di Azure rappresenta un esempio di questo tipo di log.
* **Log del piano dati**: consente di visualizzare gli eventi che vengono generati quando si usa una risorsa di Azure. Un esempio di questo tipo di log sono i canali **Sistema**, **Sicurezza** e **Applicazione** del Visualizzatore eventi di Windows in una macchina virtuale Windows. Un altro esempio è la registrazione di Diagnostica di Azure, configurata tramite Monitoraggio di Azure.
* **Eventi elaborati**: offrono informazioni sugli eventi e sugli avvisi analizzati elaborate automaticamente. Un esempio di questo tipo di evento è rappresentato dagli avvisi del Centro sicurezza di Azure. Centro sicurezza di Azure elabora e analizza la sottoscrizione per fornire gli avvisi pertinenti alle condizioni di sicurezza correnti.

L'integrazione dei log di Azure supporta ArcSight, QRadar e Splunk. Verificare con il fornitore del sistema di informazioni di sicurezza e gestione degli eventi se è disponibile un connettore nativo. Non usare Integrazione log di Azure se è disponibile un connettore nativo.

Se non sono disponibili altre opzioni, è consigliabile usare Integrazione log di Azure. La tabella seguente include i suggerimenti Microsoft:

|Sistema di informazioni di sicurezza e gestione degli eventi | Cliente che usa già Integrazione log di Azure | Cliente che sta esplorando le opzioni di integrazione di informazioni di sicurezza e gestione degli eventi|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Iniziare passando al [componente aggiuntivo di Monitoraggio di Azure per Splunk](https://splunkbase.splunk.com/app/3534/). | Usare il [connettore Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Eseguire la migrazione al connettore QRadar o iniziare a usarlo. Il connettore è documentato nell'ultima sezione di [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). | Usare il connettore QRadar documentato nell'ultima sezione di [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). |
|**ArcSight** | Continuare a usare Integrazione log di Azure fino a quando non è disponibile un connettore e quindi eseguire la migrazione alla soluzione basata sul connettore.  | Prendere in considerazione l'uso dei log di monitoraggio di Azure come alternativa. Evitare di usare Integrazione log di Azure, in modo da non dover affrontare il processo di migrazione quando il connettore sarà disponibile. |

> [!NOTE]
> Integrazione log di Azure è una soluzione gratuita, ma ci sono costi di archiviazione di Azure derivanti dall'archiviazione di informazioni nei file di log.

In caso di necessità, creare una [richiesta di assistenza](/azure/azure-supportability/how-to-create-azure-support-request). Per il servizio, selezionare **Integrazione log**.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha offerto un'introduzione a Integrazione log di Azure. Per altre informazioni su Integrazione log di Azure e sui tipi di log supportati, vedere gli articoli seguenti:

* [Introduzione a Integrazione log di Azure](azure-log-integration-get-started.md). Questa esercitazione guida l'utente attraverso l'installazione di Integrazione log di Azure e descrive come integrare log da risorse di archiviazione di Diagnostica di Microsoft Azure, log attività di Azure, avvisi del Centro sicurezza di Azure e log di controllo di Azure Active Directory.
* [Domande frequenti su Integrazione log di Azure](azure-log-integration-faq.md). Queste domande frequenti riguardano Integrazione log di Azure.
* Altre informazioni su come [trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs).

