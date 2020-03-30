---
title: Connettere i dati barracuda ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati Barracuda ad Azure Sentinel.Learn how to connect Barracuda data to Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588485"
---
# <a name="connect-your-barracuda-appliance"></a>Collegare l'apparecchio Barracuda 



Il connettore Barracuda Web Application Firewall (WAF) consente di connettere facilmente i log barracuda con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Ciò offre maggiori informazioni sulla rete dell'organizzazione e migliora le funzionalità di gestione della sicurezza. Azure Sentinel sfrutta l'integrazione nativa tra **Barracuda** e l'agente Log Analytics per fornire un'integrazione senza soluzione di continuità. 


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configurare e collegare Barracuda WAF
Barracuda Web Application Firewall può integrare ed esportare i log direttamente in Azure Sentinel tramite l'agente Log Analytics.
1. Passare a Flusso di [configurazione WAF Barracuda](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)e seguire le istruzioni per configurare la connessione, utilizzando i parametri seguenti:
    - **ID area di lavoro:** copia il valore dell'ID dell'area di lavoro dalla pagina del connettore Barracuda di Azure Sentinel.
    - **Chiave primaria:** copiare il valore della chiave primaria dalla pagina del connettore Barracuda di Azure Sentinel.
1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi Barracuda, cercare **CommonSecurityLog** e **barracuda_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere voluti fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Barracuda appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


