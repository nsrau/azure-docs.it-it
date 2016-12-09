---
title: Monitorare i messaggi B2B | Documentazione Microsoft
description: Come monitorare l&quot;account di integrazione
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>Monitorare i messaggi B2B
La comunicazione B2B comporta lo scambio di messaggi tra due processi o applicazioni aziendali in esecuzione. La relazione definisce un contratto tra processi aziendali. Dopo aver stabilito la comunicazione, sarà necessario ricorrere a un metodo per monitorarne il corretto funzionamento.  Il rilevamento dei messaggi è stato implementato per i protocolli B2B: AS2, X12 ed EDIFACT.  È possibile configurare l'account di integrazione in modo che usi la diagnostica per ottenere dettagli e debug più avanzati.

## <a name="prerequisites"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un account di integrazione. È possibile creare un [account di integrazione](app-service-logic-enterprise-integration-create-integration-account.md)
* Un'app per la logica. È possibile creare un'[app per la logica](app-service-logic-create-a-logic-app.md) e abilitare la registrazione. La procedura è disponibile [qui](app-service-logic-monitor-your-logic-apps.md)

## <a name="enable-logging-for-an-integration-account"></a>Abilitare la registrazione per un account di integrazione
È possibile abilitare la registrazione per un account di integrazione mediante il **portale di Azure** o il **Monitoraggio**.

### <a name="enable-logging-with-azure-portal"></a>Abilitare la registrazione con il portale di Azure

1. Selezionare **Account di integrazione** e quindi **Log di diagnostica** 
![Selezione dell'account di integrazione](./media/app-service-logic-monitor-integration-account/Pic5.png)  

2. Selezionare la **Sottoscrizione**, il **Gruppo di risorse** e l'**Account di integrazione** da Tipo di risorsa e quindi selezionare l'**Account di integrazione** dall'elenco a discesa Risorsa per abilitare la diagnostica.  Fare clic su **Attiva diagnostica** per abilitare la diagnostica per l'account di integrazione selezionato.               
![Selezione dell'account di integrazione](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. Selezionare lo stato **SÌ**       
![Attivare la diagnostica](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Selezionare **Send to Log Analytics** (Invia a Log Analytics) e configurare Log Analytics per l'emissione dei dati.               
![Attivare la diagnostica](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>Abilitare la registrazione con Monitoraggio

1. Selezionare **Monitoraggio** e fare clic su **Log di diagnostica**   
![Selezione di Monitoraggio](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. Selezionare la **Sottoscrizione**, il **Gruppo di risorse** e l'**Account di integrazione** da Tipo di risorsa e quindi selezionare l'**Account di integrazione** dall'elenco a discesa Risorsa per abilitare la diagnostica.  Fare clic su **Attiva diagnostica** per abilitare la diagnostica per l'account di integrazione selezionato.               
![Selezione dell'account di integrazione](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. Selezionare lo stato **SÌ**       
![Attivare la diagnostica](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Selezionare **Send to Log Analytics** (Invia a Log Analytics) e configurare Log Analytics per l'emissione dei dati ![Attivare la diagnostica](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>Estensione della soluzione
Oltre a **Log Analytics**, è possibile configurare l'account di integrazione e le [App per la logica](./app-service-logic-monitor-your-logic-apps.md) in un hub eventi o un account di archiviazione.         
![Impostazioni di Diagnostica di Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

I dati di telemetria di Hub eventi o Archiviazione possono essere usati in altri servizi come [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) e [Power BI](https://powerbi.com) per ottenere il monitoraggio in tempo reale dei flussi di lavoro di integrazione.

## <a name="supported-tracking-schema"></a>Schema di rilevamento supportato
Sono supportati i tipi di schema di rilevamento seguenti.  Tutti i tipi di schema sono fissi, ad eccezione del tipo personalizzato.

* [Schema di rilevamento personalizzato](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [Schema di rilevamento AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Schema di rilevamento X12](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>Passaggi successivi
[Rilevamento dei messaggi B2B nel portale di OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


