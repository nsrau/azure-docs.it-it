---
title: Connettere i dati Barracuda ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Barracuda ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: b3ca93d9e70456d25d5f78b2ca1fde8e4ea24f8d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240209"
---
# <a name="connect-your-barracuda-appliance"></a>Connettere l'appliance Barracuda 



Il connettore Barracuda Web Application Firewall (WAF) consente di connettere facilmente i log di Barracuda con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. In questo modo è possibile ottenere informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza. Azure Sentinel sfrutta l'integrazione nativa tra **Barracuda** e log Analytics Agent per offrire un'integrazione perfetta. 


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configura e connetti Barracuda WAF
Barracuda Web Application Firewall può integrare ed esportare i log direttamente in Sentinel di Azure tramite Log Analytics Agent.
1. Passare a [Barracuda WAF Configuration Flow](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)e seguire le istruzioni per configurare la connessione, usando i parametri seguenti:
    - **ID area di lavoro**: copiare il valore dell'ID dell'area di lavoro dalla pagina del connettore Azure Sentinel Barracuda.
    - **Chiave primaria**: copiare il valore della chiave primaria dalla pagina del connettore Azure Sentinel Barracuda.
2. Nel portale di Sentinel di Azure passare all'area di lavoro in cui è stato distribuito Azure Sentinel e selezionare i puntini di sospensione (...) alla fine della riga e selezionare **Impostazioni avanzate**. 
1. Selezionare **dati** e quindi **syslog**.
1. Verificare che la funzionalità impostata in Barracuda esista e impostare la gravità e fare clic su **Save (Salva**).
6. Per usare lo schema pertinente in Log Analytics per gli eventi Barracuda, cercare **CommonSecurityLog** e **barracuda_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance Barracuda ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

