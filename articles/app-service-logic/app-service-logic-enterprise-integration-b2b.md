<properties 
	pageTitle="Creazione di soluzioni B2B con Enterprise Integration Pack | Servizio app di Microsoft Azure | Microsoft Azure" 
	description="Informazioni sulla ricezione di dati mediante le funzionalità B2B di Enterprise Integration Pack" 
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

# Informazioni sulla ricezione di dati mediante le funzionalità B2B di Enterprise Integration Pack#

## Overview ##

Questo documento fa parte di Enterprise Integration Pack per le App per la logica. Per altre informazioni sulle [capacità di Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md), vedere la panoramica.

## Prerequisiti ##

Per usare le azioni AS2 e X12 è necessario un account Enterprise Integration

[Come creare un account Enterprise Integration](./app-service-logic-enterprise-integration-accounts.md)

## Come usare i connettori delle app per la logica B2B ##

Dopo aver creato un account di integrazione e aver aggiunto i partner e i contratti, è possibile creare un'app per la logica che implementa un flusso di lavoro Business to Business (B2B).

In questa procedura dettagliata verrà illustrato come usare le azioni AS2 e X12 per creare un'app per la logica Business-to-Business che riceve dati da un partner commerciale.

1. Creare una nuova app per la logica e [collegarla all'account di integrazione](./app-service-logic-enterprise-integration-accounts.md).
2. Aggiungere un trigger **Request - When an HTTP request is received** (Richiesta - Alla ricezione di una richiesta HTTP) all'app per la logica ![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)
3. Aggiungere l'azione **Decode AS2** (Decodifica AS2) selezionando innanzitutto **Aggiungi un'azione** ![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)
4. Immettere la parola **as2** nella casella di ricerca per filtrare tutte le azioni su quella che si desidera usare ![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)
6. Selezionare l'azione **AS2 - Decode AS2 message** (AS2 - Decodifica messaggio AS2) ![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)
7. Come illustrato, aggiungere il corpo (**Body**) che sarà accettato come input. In questo esempio selezionare il corpo della richiesta HTTP che ha attivato l'app per la logica. In alternativa è possibile immettere un'espressione per inserire le intestazioni nel campo **HEADERS** (Intestazioni):

    @triggerOutputs()['headers']

8. Aggiungere le intestazioni (**Headers**) richieste per AS2. Queste si troveranno nelle intestazioni della richiesta HTTP. In questo esempio selezionare le intestazioni della richiesta HTTP che ha attivato l'app per la logica.
9. Ora aggiungere l'azione Decode X12 message (Decodifica messaggio X12) selezionando di nuovo **Aggiungi un'azione** ![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)
10. Immettere la parola **x12** nella casella di ricerca per filtrare tutte le azioni su quella che si desidera usare ![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)
11. Selezionare l'azione **X12 - Decode X12 message** (X12 - Decodifica messaggio X12) per aggiungerla all'app per la logica ![](./media/app-service-logic-enterprise-integration-b2b/b2b-as2message.png)
12. Ora è necessario specificare l'input per questa azione che sarà l'output dell'azione AS2 sopra. Il contenuto effettivo del messaggio si trova in un oggetto JSON e presenta una codifica base64. È pertanto necessario specificare un'espressione come input, perciò inserire la seguente espressione nel campo di input **X12 FLAT FILE MESSAGE TO DECODE** (Messaggio file flat X12 da decodificare)

    @base64ToString(body('Decode\_AS2\_message')?['AS2Message']?['Content'])

13. Questo passaggio decodificherà i dati X12 ricevuti dal partner commerciale e restituirà determinati elementi in un oggetto JSON. Per informare il partner della ricezione dei dati, è possibile inviare una risposta contenente la notifica di disposizione del messaggio (Message Disposition Notification o MDN) AS2 in un'azione di risposta HTTP
14. Aggiungere l'azione **Response** (Risposta) selezionando **Aggiungi un'azione** ![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)
15. Immettere la parola **response** nella casella di ricerca per filtrare tutte le azioni su quella che si desidera usare ![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)
16. Selezionare l'azione **Response** (Risposta) per aggiungerla ![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)
17. Impostare il campo **BODY** (Corpo) della risposta usando l'espressione seguente per accedere all'MDN dall'output dell'azione **Decode X12 message** (Decodifica messaggio X12)

    @base64ToString(body('Decode\_AS2\_message')?['OutgoingMdn']?['Content'])

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)
18. Salvare il lavoro ![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)

A questo punto la configurazione dell'app per la logica B2B è completa. In un'applicazione reale è possibile archiviare i dati X12 decodificati in un'applicazione LOB o in un archivio dati. Per farlo, è possibile aggiungere facilmente altre azioni o scrivere API personalizzate per connettersi alle proprie applicazioni LOB e usare queste API nelle proprie app per la logica.

## Funzionalità e casi d'uso ##

- Le azioni AS2 e X12 di decodifica e codifica consentono di ricevere e inviare dati ai partner commerciali mediante protocolli standard di settore usando le app per la logica
- È possibile usare AS2 e X12 insieme o meno per scambiare dati con i partner commerciali in base alle esigenze
- Le azioni B2B semplificano la creazione di partner e contratti nell'account di integrazione e il loro uso in un'app per la logica
- Mediante l'estensione dell'app per la logica con altre azioni è possibile inviare e ricevere dati da e verso altre applicazioni e servizi, ad esempio SalesForce

## Altre informazioni ##

[Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0914_2016-->