<properties 
	pageTitle="Cosa sono i connettori e le app per le API di BizTalk" 
	description="Informazioni sulle app per le API, sui connettori e sulle app per le API BizTalk" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="joshtwist" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="jtwist"/>

# Cosa sono i connettori e le app per le API di BizTalk

I Servizi app di Azure sono creati in base a un principio di estendibilità e di connettività comune tramite le app per le API. Un *Connector* è un tipo di app per le API incentrato sulla connettività. I connettori, come qualsiasi altra app per le API, vengono usati dalle app Web, dalle app mobili e dalle app per la logica. I connettori facilitano la connessione ai servizi esistenti e aiutano a gestire l'autenticazione, a fornire il monitoraggio, l'analisi e molto altro.

Qualsiasi sviluppatore può creare le proprie app per le API e distribuirle privatamente e in futuro sarà possibile condividerle e renderle redditizie tramite il Marketplace. 

![API Apps Marketplace](./media/app-service-learn-about-flows-preview/Marketplace.png)

Per accelerare lo sviluppo di soluzioni con i Servizi app di Azure, il team di Microsoft Azure ha aggiunto una serie di connettori al Marketplace per soddisfare molti scenari comuni. Inoltre, per estendere la portata dei Servizi app agli scenari di integrazione complessi e avanzati, è disponibile anche una serie di capacità Premium e BizTalk.

[Elenco di connettori e app per le API nel servizio app di Microsoft Azure](app-service-logic-connectors-list.md)


## Connettori per protocolli
I Servizi app sono nati dal Web e favoriscono la comunicazione tramite HTTP e la condivisione dei metadati tramite formati aperti come Swagger. Naturalmente, le aziende hanno bisogno di comunicare attraverso una varietà di protocolli. I connettori per protocolli possono colmare il divario e rendere la comunicazione con i servizi che usano FTP, SFTP, POP3/IMAP, SMTP e SOAP facile come effettuare una chiamata HTTP.  

[Connettori per protocolli nel servizio app di Microsoft Azure](app-service-logic-protocol-connectors.md)


## Connettori SaaS
I connettori SaaS dei Servizi app forniscono una soluzione chiavi in mano per connettere le app Web, mobili e per la logica ad alcune delle più importanti realtà SaaS odierne, tra cui Office 365, SalesForce, SugarCRM, OneDrive, Dropbox, Marketo, persino Facebook e molte altre.

[Connettori per social network nel servizio app di Microsoft Azure](app-service-logic-social-connectors.md)

[Connettori per i servizi app e dati nel servizio app di Microsoft Azure](app-service-logic-data-connectors.md)


## Connettori Premium 
I connettori Premium estendono ulteriormente la copertura dei Servizi app nell'azienda con la connettività per SAP, Siebel, Oracle, DB2 e molto altro.

[Connettori aziendali nel servizio app di Microsoft Azure](app-service-logic-enterprise-connectors.md)


## App per le API di BizTalk
La creazione di app aziendali critiche richiede molto più della semplice connettività. Basandosi sulla creazione della piattaforma di integrazione leader del settore di Microsoft, BizTalk Server, le app per le API BizTalk offrono funzioni di integrazione avanzate che è possibile includere nelle app Web, mobili e per la logica senza alcuna difficoltà. Include l'invio e la suddivisione in batch, VETR (Validate, Extract, Transform e Route) e il supporto dei formati EDI come X12, EDIFACT e AS2.

[Connettori Business to Business e app per le API nel servizio app di Microsoft Azure](app-service-logic-b2b-connectors.md)<br/>

[App per le API BizTalk Integration nel servizio app di Microsoft Azure](app-service-logic-integration-connectors.md)

<!--HONumber=49-->