<properties 
	pageTitle="Panoramica della convalida XML in Enterprise Integration Pack | Servizio app di Microsoft Azure | Microsoft Azure" 
	description="Informazioni sul funzionamento della convalida in Enterprise Integration Pack e nelle app per la logica" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Enterprise Integration con convalida XML

## Panoramica
Spesso negli scenari B2B i partner di un contratto devono accertarsi che i messaggi scambiati siano validi prima di avviare l'elaborazione dei dati. In Enterprise Integration Pack, è possibile usare il connettore di convalida XML per convalidare documenti in base a uno schema predefinito.

## Come convalidare un documento con il connettore di convalida XML
1. Creare un'app per la logica e [collegarla all'account di integrazione](./app-service-logic-enterprise-integration-accounts.md "Informazioni su come collegare un account di integrazione a un'app per la logica") che contiene lo schema che verrà usato per convalidare i dati XML.
2. Aggiungere un trigger **Request - When an HTTP request is received** (Richiesta - Alla ricezione di una richiesta HTTP) all'app per la logica ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)
3. Aggiungere l'azione **XML Validation** selezionando innanzitutto **Aggiungi un'azione**
4. Immettere *xml* nella casella di ricerca per filtrare tutte le azioni su quella che si desidera usare
5. Selezionare **XML Validation** ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)
6. Selezionare la casella di testo **CONTENUTO** ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Selezionare il tag del corpo come contenuto che verrà convalidato. ![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)
8. Selezionare la casella di riepilogo **NOME SCHEMA** e scegliere lo schema che si desidera usare per convalidare il *contenuto* immesso in alto ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png)
9. Salvare il lavoro ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png)

A questo punto, la configurazione del connettore di convalida è completa. In un'applicazione reale è possibile archiviare i dati convalidati in un'applicazione LOB, ad esempio SalesForce. È possibile eseguire facilmente questa azione inviando l'output della convalida a Salesforce.

È ora possibile testare l'azione di convalida eseguendo una richiesta all'endpoint HTTP.

## Passaggi successivi

[Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")

<!---HONumber=AcomDC_0803_2016-->