---
title: Monitorare i messaggi nelle transazioni B2B - App per la logica di Azure | Documentazione Microsoft
description: Monitorare e tenere traccia dei messaggi durante la comunicazione B2B tra i processi e le app usando App per la logica nell&quot;account di integrazione.
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: dc760b4c08d0e1afff3bc1276f6ed2367d67629e
ms.lasthandoff: 03/01/2017


---
# <a name="start-or-enable-logging-of-as2-x12-and-edifact-messages-to-monitor-success-errors-and-message-properties"></a>Avviare o attivare la registrazione dei messaggi AS2, X12 ed EDIFACT per monitorare il completamento dell'operazione, gli errori e le proprietà dei messaggi

La comunicazione B2B comporta lo scambio di messaggi tra due processi o applicazioni aziendali in esecuzione. La relazione definisce un contratto tra processi aziendali. Dopo che è stata stabilita la comunicazione, è possibile impostare il monitoraggio dei messaggi per controllare il corretto funzionamento della comunicazione. È possibile configurare l'account di integrazione in modo che usi la diagnostica per ottenere dettagli e debug più avanzati.

Il rilevamento dei messaggi è disponibile per i protocolli B2B: AS2, X12 ed EDIFACT. 

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free).
* Un account di integrazione. È possibile creare un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md).
* Un'app per la logica. È possibile creare un'[app per la logica](logic-apps-create-a-logic-app.md) e [abilitare la registrazione](logic-apps-monitor-your-logic-apps.md).

## <a name="enable-logging-for-an-integration-account"></a>Abilitare la registrazione per un account di integrazione

È possibile abilitare la registrazione per un account di integrazione mediante il **portale di Azure** o il **Monitoraggio**.

### <a name="enable-logging-with-azure-portal"></a>Abilitare la registrazione con il portale di Azure

1. Selezionare l'account di integrazione, quindi selezionare **Log di diagnostica**.

    ![Selezionare l'account di integrazione](media/logic-apps-monitor-b2b-message/pic5.png)

2. Selezionare la **Sottoscrizione** e il **Gruppo di risorse**. In **Tipo di risorsa** selezionare **Account di integrazione**. In **Risorsa** selezionare il proprio account di integrazione. Fare clic su **Attiva diagnostica** per abilitare la diagnostica per l'account di integrazione selezionato.

    ![Configurare la diagnostica per l'account di integrazione](media/logic-apps-monitor-b2b-message/pic2.png)

3. Selezionare lo stato **ON** (Attivato).

    ![Attivare lo stato di diagnostica](media/logic-apps-monitor-b2b-message/pic3.png)

4. Selezionare **Send to Log Analytics** (Invia a Log Analytics) e configurare Log Analytics per l'emissione dei dati.

    ![Inviare i dati di diagnostica a un log](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>Abilitare la registrazione con Monitoraggio

0. Selezionare **Monitoraggio** e fare clic su **Log di diagnostica**.

    ![Selezionare Monitoraggio, Log di diagnostica](media/logic-apps-monitor-b2b-message/pic1.png)

0. Selezionare la **Sottoscrizione** e il **Gruppo di risorse**. In **Tipo di risorsa** selezionare **Account di integrazione**. In **Risorsa** selezionare il proprio account di integrazione. Fare clic su **Attiva diagnostica** per abilitare la diagnostica per l'account di integrazione selezionato.

    ![Configurare la diagnostica per l'account di integrazione](media/logic-apps-monitor-b2b-message/pic2.png)

0. Selezionare lo stato **ON** (Attivato).

    ![Attivare lo stato di diagnostica](media/logic-apps-monitor-b2b-message/pic3.png) 

0. Selezionare **Send to Log Analytics** (Invia a Log Analytics) e configurare **Log Analytics** per l'emissione dei dati.

    ![Inviare i dati di diagnostica a un log](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>Estendere la soluzione

Oltre a **Log Analytics**, è possibile configurare l'account di integrazione e le [App per la logica](./logic-apps-monitor-your-logic-apps.md) in un hub eventi o un account di archiviazione.

![Impostazioni di diagnostica di Azure](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

I dati di telemetria di Hub eventi o Archiviazione possono essere usati in altri servizi come [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) e [Power BI](https://powerbi.com) per ottenere il monitoraggio in tempo reale dei flussi di lavoro di integrazione.

## <a name="supported-tracking-schema"></a>Schema di rilevamento supportato

Microsoft supporta questi tipi di schemi di rilevamento. Tutti includono schemi corretti ad eccezione del tipo personalizzato.

* [Schema di rilevamento personalizzato](logic-apps-track-integration-account-custom-tracking-schema.md)
* [Schema di rilevamento AS2](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schema di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

[Rilevamento dei messaggi B2B nel portale di OMS](logic-apps-track-b2b-messages-omsportal.md "Rilevamento dei messaggi B2B")

[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")


