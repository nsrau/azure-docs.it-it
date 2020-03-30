---
title: Connettere i dati di Palo Alto Networks ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati di Palo Alto Networks ad Azure Sentinel.
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
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588128"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Connettere le reti Palo Alto ad Azure SentinelConnect Palo Alto Networks to Azure Sentinel



Questo articolo illustra come connettere l'appliance Palo Alto Networks ad Azure Sentinel.This article explains how to connect your Palo Alto Networks appliance to Azure Sentinel. Il connettore dati Palo Alto Networks consente di connettere facilmente i log di Palo Alto Networks con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso delle reti Palo Alto in Azure Sentinel fornirà maggiori informazioni sull'utilizzo di Internet dell'organizzazione e ne migliorerà le funzionalità operative di sicurezza. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Registri di Forward Palo Alto Networks all'agente Syslog

Configurare le reti Palo Alto per inoltrare i messaggi Syslog in formato CEF all'area di lavoro di Azure tramite l'agente Syslog:Configure Palo Alto Networks to forward Syslog messages in CEF format to your Azure workspace via the Syslog agent:
1.  Vai a Guide di [configurazione DEL formato di evento comune (CEF)](https://docs.paloaltonetworks.com/resources/cef) e scarica il pdf per il tuo tipo di appliance. Seguire tutte le istruzioni nella guida per configurare l'appliance Palo Alto Networks per raccogliere eventi CEF. 

1.  Passare a Configurare il [monitoraggio Syslog](https://aka.ms/asi-syslog-paloalto-forwarding) e seguire i passaggi 2 e 3 per configurare l'inoltro degli eventi CEF dall'appliance Palo Alto Networks ad Azure Sentinel.

    1. Assicurarsi di impostare il **formato del server Syslog** su **BSD**.

       > [!NOTE]
       > Le operazioni di copia/incolla dal PDF potrebbero modificare il testo e inserire caratteri casuali. Per evitare questo problema, copiare il testo in un editor e rimuovere tutti i caratteri che potrebbero interrompere il formato di registro prima di incollarlo, come si può vedere in questo esempio.
 
        ![Problema di copia del testo CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi Di Alto Networks, cercare **CommonSecurityLog**.

1. Procedere al [passaggio 3: Convalidare la connettività](connect-cef-verify.md).




## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Palo Alto Networks appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


