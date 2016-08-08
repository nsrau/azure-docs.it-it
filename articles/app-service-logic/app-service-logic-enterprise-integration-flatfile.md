<properties 
	pageTitle="Informazioni su come codificare o decodificare i file flat usando Enterprise Integration Pack e le app per la logica | Servizio app di Microsoft Azure | Microsoft Azure" 
	description="Usare le funzionalità di Enterprise Integration Pack e delle app per la logica per codificare o decodificare i file flat" 
	services="app-service\logic" 
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

# Enterprise Integration con file flat

## Panoramica

Si userà il connettore di codifica file flat dall'interno di un'app per la logica che codifica contenuto XML. In uno scenario B2B potrebbe essere necessario codificare i contenuti XML prima di inviarli a un partner commerciale. L'app per la logica che viene creata può ottenere il proprio contenuto XML da varie origini fra cui un trigger di richiesta HTTP o un'altra applicazione o anche da uno dei numerosi [connettori](../connectors/apis-list.md). Per altre informazioni sulle capacità delle app per la logica vedere la [documentazione di tali app](./app-service-logic-what-are-logic-apps.md "Altre informazioni sulle app per la logica").

## Come creare il connettore di codifica file flat

Per creare un'app per la logica e aggiungere a essa un connettore di codifica file flat seguire questi passaggi

1. Creare un'app per la logica e [collegarla all'account di integrazione](./app-service-logic-enterprise-integration-accounts.md "Informazioni su come collegare un account di integrazione a un'app per la logica") che contiene lo schema che verrà usato per codificare i dati XML.
2. Aggiungere un trigger **Request - When an HTTP request is received** (Richiesta - Alla ricezione di una richiesta HTTP) all'app per la logica ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
3. Aggiungere l'azione di codifica file flat:
-  Selezionare il segno **più**
-  Selezionare il collegamento **Aggiungi un'azione** che viene visualizzato dopo aver selezionato il segno più
-  Immettere *Flat* nella casella di ricerca per filtrare tutte le azioni su quella che si desidera usare
-  Selezionare l'azione **Flat File Encoding** (Codifica file flat) nell'elenco ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
6. Selezionare la casella di testo **CONTENT** (Contenuto) sul controllo di codifica file flat che appare ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)
7. Selezionare il tag del corpo come contenuto che verrà codificato. Il tag body popolerà il campo del contenuto. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)
8. Selezionare la casella di riepilogo **NOME SCHEMA** e scegliere lo schema che si desidera usare per codificare il *contenuto* immesso sopra ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)
9. Salvare il lavoro ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

A questo punto la configurazione del connettore di codifica del file flat è completa. In un'applicazione reale è possibile archiviare i dati codificati in un'applicazione LOB, ad esempio SalesForce, o inviare i dati codificati a un partner commerciale. È possibile aggiungere facilmente un'azione per inviare l'output dell'azione di codifica a Salesforce o a partner commerciali mediante uno qualsiasi degli altri connettori forniti.

È ora possibile testare il connettore effettuando una richiesta all'endpoint HTTP e includendo il contenuto XML nel corpo della richiesta.

## Come creare il connettore di decodifica file flat

### Prerequisito
Per poter completare questi passaggi è necessario disporre di un file di schema già caricato nell'account di integrazione.

1. Aggiungere un trigger **Request - When an HTTP request is received** (Richiesta - Alla ricezione di una richiesta HTTP) all'app per la logica ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
2. Aggiungere l'azione di codifica file flat:
-  Selezionare il segno **più**
-  Selezionare il collegamento **Aggiungi un'azione** che viene visualizzato dopo aver selezionato il segno più
-  Immettere *Flat* nella casella di ricerca per filtrare tutte le azioni su quella che si desidera usare
-  Selezionare l'azione **Flat File Decoding** (Decodifica file flat) nell'elenco ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
- Selezionare il controllo **CONTENT** (Contenuto). Si otterrà un elenco del contenuto dai passaggi precedenti che è possibile usare come contenuto da decodificare. Si noterà che il corpo (*Body*) della richiesta HTTP in ingresso è disponibile per essere usato come contenuto da decodificare. Si noti che è possibile immettere anche il contenuto da decodificare direttamente nel controllo **CONTENT** (Contenuto). In questo esempio è stato selezionato *Body* (Corpo) per usare il corpo della richiesta HTTP in ingresso dal passaggio 1 della procedura di decodifica.
- Selezionare il tag *Body*. Si noti che il tag Body è ora nel controllo CONTENT (Contenuto).
- Selezionare il nome dello schema da usare per decodificare il contenuto. In questo esempio si seleziona *OrderFile*. Si noti che OrderFile è il nome si uno schema caricato nell'account di integrazione in precedenza. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)
- Selezionare il collegamento **Salva** dal menu per salvare il lavoro ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

A questo punto la configurazione del connettore di decodifica file flat è completa. In un'applicazione reale è possibile archiviare i dati decodificati in un'applicazione LOB, ad esempio SalesForce. È possibile eseguire facilmente questa azione inviando l'output della decodifica a Salesforce.

È ora possibile testare il connettore effettuando una richiesta all'endpoint HTTP e includendo il contenuto XML da decodificare nel corpo della richiesta.

## Altre informazioni
- [Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")

<!---HONumber=AcomDC_0727_2016-->