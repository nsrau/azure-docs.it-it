---
title: Distribuire l'agente per connettere i dati CEF ad Anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come distribuire l'agente per connettere i dati CEF ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 5451382763195172b48e93ef2ea859552b05d154
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615372"
---
# <a name="step-1-deploy-the-agent"></a>Passaggio 1: distribuire l'agente


In questo passaggio è necessario selezionare il computer Linux che fungerà da proxy tra Azure Sentinel e la soluzione di sicurezza. Sarà necessario eseguire uno script nel computer proxy che:
- Consente di installare l'agente di Log Analytics e di configurarlo in base alle esigenze per l'ascolto dei messaggi syslog.
- Configura il daemon syslog in modo che ascolti i messaggi syslog usando la porta TCP 514 e quindi inoltri solo i messaggi CEF all'agente Log Analytics usando la porta TCP 25226.
- Imposta l'agente syslog per raccogliere i dati e inviarli in modo sicuro ad Azure Sentinel, in cui vengono analizzati e arricchiti.
 
## <a name="deploy-the-agent"></a>Distribuire l'agente
 
1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **formato evento comune (CEF)** , quindi **aprire la pagina del connettore**. 

1. In **Install and configure the syslog Agent**selezionare il tipo di computer, Azure, other cloud o in locale. 
   > [!NOTE]
   > Poiché lo script nel passaggio successivo installa l'agente di Log Analytics e connette il computer all'area di lavoro di Azure Sentinel, verificare che il computer non sia connesso ad altre aree di lavoro.
1. È necessario disporre di autorizzazioni elevate (sudo) nel computer. Assicurarsi di avere Python nel computer usando il comando seguente: `python –version`

1. Eseguire lo script seguente nel computer proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Mentre lo script è in esecuzione, assicurarsi che non vengano visualizzati messaggi di errore o di avviso.

Continuare con [il passaggio 2: configurare la soluzione di sicurezza per l'invio dei messaggi CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance CEF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).

