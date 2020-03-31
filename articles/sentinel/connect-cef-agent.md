---
title: Distribuire l'agente per connettere i dati CEF ad Azure Sentinel Preview Documenti Microsoft
description: Informazioni su come distribuire l'agente per connettere i dati CEF ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588468"
---
# <a name="step-1-deploy-the-agent"></a>Passaggio 1: Distribuire l'agenteStep 1: Deploy the agent


In questo passaggio, è necessario selezionare il computer Linux che fungerà da proxy tra Azure Sentinel e la soluzione di sicurezza. È necessario eseguire uno script sul computer proxy che:
- Installa l'agente di Log Analytics e lo configura in base alle esigenze per l'ascolto dei messaggi Syslog.
- Configura il daemon Syslog per l'ascolto dei messaggi Syslog utilizzando la porta TCP 514 e quindi inoltra solo i messaggi CEF all'agente di Log Analytics utilizzando la porta TCP 25226.
- Imposta l'agente Syslog per raccogliere i dati e inviarli in modo sicuro ad Azure Sentinel, dove viene analizzato e arricchito.
 
## <a name="deploy-the-agent"></a>Distribuire l'agenteDeploy the agent
 
1. Nel portale di Azure Sentinel fare clic su **Connettori dati** e selezionare **Formato evento comune (CEF)** e quindi **Apri pagina connettore.** 

1. In **Installare e configurare l'agente Syslog**selezionare il tipo di computer, Azure, altro cloud o locale. 
   > [!NOTE]
   > Poiché lo script nel passaggio successivo installa l'agente di Log Analytics e connette il computer all'area di lavoro di Azure Sentinel, assicurarsi che il computer non sia connesso ad altre aree di lavoro.
1. È necessario disporre di autorizzazioni elevate (sudo) nel computer. Assicurarsi di avere Python sul computer utilizzando il seguente comando:`python –version`

1. Eseguire lo script seguente nel computer proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Durante l'esecuzione dello script, verificare di non ricevere messaggi di errore o di avviso.

Continuare con [il passaggio 2: Configurare la soluzione di sicurezza per inoltrare i messaggi CEF.](connect-cef-solution-config.md)


## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect CEF appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).

