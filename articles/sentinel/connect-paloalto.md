---
title: Connettere i dati di Palo Alto Networks ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore di dati Palo Alto Networks per connettere facilmente i log di Palo Alto Networks a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564568"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Connettere Palo Alto Networks ad Azure Sentinel



Questo articolo illustra come connettere il dispositivo Palo Alto Networks a Sentinel di Azure. Palo Alto Networks Data Connector consente di connettere facilmente i log di Palo Alto Networks a Azure Sentinel per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di Palo Alto Networks in Azure Sentinel ti offre un maggior numero di informazioni sull'uso di Internet dell'organizzazione e ne migliora le funzionalità di sicurezza. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Inviare i log di Palo Alto Networks all'agente syslog

Configurare Palo Alto Networks per l'invio dei messaggi syslog in formato CEF nell'area di lavoro di Azure tramite l'agente syslog:
1.  Passare a [guide di configurazione CEF (Common Event Format)](https://docs.paloaltonetworks.com/resources/cef) e scaricare il file PDF per il tipo di dispositivo. Seguire tutte le istruzioni riportate nella Guida per configurare l'appliance Palo Alto Networks per raccogliere gli eventi CEF. 

1.  Passare a [Configure syslog Monitoring](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring) e seguire i passaggi 2 e 3 per configurare l'invio di eventi CEF dall'appliance Palo Alto Networks ad Azure Sentinel.

    1. Assicurarsi di impostare il **formato del server syslog** su **BSD**.

       > [!NOTE]
       > Le operazioni di copia/incolla dal PDF potrebbero modificare il testo e inserire i caratteri casuali. Per evitare questo problema, copiare il testo in un editor e rimuovere i caratteri che potrebbero interrompere il formato di log prima di incollarli, come si può notare in questo esempio.
 
        ![Problema di copia del testo CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Per usare lo schema pertinente in Log Analytics per gli eventi Palo Alto Networks, cercare **CommonSecurityLog**.

1. Continuare con il [PASSAGGIO 3: Convalidare la connettività](connect-cef-verify.md).




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance Palo Alto Networks ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


