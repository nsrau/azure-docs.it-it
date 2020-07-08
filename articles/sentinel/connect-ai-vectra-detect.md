---
title: Connetti le informazioni di i/o al rilevamento dei dati in Sentinel di Azure | Microsoft Docs
description: Informazioni su come connettere l'intelligenza artificiale per rilevare i dati in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 964bab2b81f6ce69e77fd69cab55f6d1894e4edb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368403"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Connetti i rilevamenti di AI Vectra ad Azure Sentinel

Questo articolo illustra come connettere l'appliance di rilevamento delle informazioni per la [rilevanza di ai Vectra](https://www.vectra.ai/product/cognito-detect) ad Azure Sentinel. Il connettore di rilevamento dei dati di i/o rilevati da AI Vectra consente di riportare facilmente i dati in Azure Sentinel, in modo che sia possibile visualizzarli nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>Configurare l'appliance per la rilevanza di AI Vectra per l'invio di messaggi CEF  

Configurare la rilevanza di i/o al rilevamento per l'avanzamento dei messaggi syslog con formato CEF nell'area di lavoro di Log Analytics tramite il server d'accesso del registro di sistema configurato nel [passaggio 1: distribuire il server d'istruzione](connect-cef-agent.md).

1. Dall'interfaccia Vectra passare a impostazioni > notifiche e scegliere Modifica configurazione syslog. Per configurare la connessione, seguire le istruzioni seguenti:

    - Aggiungere una nuova destinazione (il nome host del [server d'avanzamento log](connect-cef-agent.md))
    - Impostare la porta come **514**
    - Impostare il protocollo come **UDP**
    - Impostare il formato su **CEF**
    - Imposta tipi di log (Seleziona tutti i tipi di log disponibili)
    - Fare clic su **Salva**

2. È possibile fare clic sul pulsante **test** per forzare l'invio di alcuni eventi di test al server d'inoltro di log.

3. Per usare lo schema pertinente in Log Analytics per gli eventi di rilevamento di i/o, cercare **CommonSecurityLog**.

4. Continuare con il [PASSAGGIO 3: Convalidare la connettività](connect-cef-verify.md).

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere i dispositivi di rilevamento della rilevanza di intelligenza artificiale ad Azure Sentinel. Per sfruttare appieno le funzionalità integrate in questo connettore di dati, fare clic sulla scheda **passaggi successivi** della pagina connettore dati. In questa pagina sono disponibili alcune query di esempio pronte per iniziare a trovare informazioni utili.

Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
