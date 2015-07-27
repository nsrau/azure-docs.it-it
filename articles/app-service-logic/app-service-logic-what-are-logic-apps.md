<properties 
	pageTitle="Cosa sono le app per la logica?" 
	description="Informazioni sulle App per la logica del servizio app" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/08/2015"
	ms.author="klam"/>

#Cosa sono le app per la logica?

Azure App Service è una piattaforma distribuita come servizio (PaaS) completamente gestita per gli sviluppatori e facilita la creazione di app Web, mobili e di integrazione. Le app per la logica rappresentano una parte di questa suite e consentono a qualsiasi utente o sviluppatore tecnico di automatizzare l'esecuzione e il flusso di lavoro dei processi aziendali tramite una finestra di progettazione visiva facile da usare.

Ancora più importante, è possibile combinare le app per la logica con le app per le API e i [connettori][connectors] del Marketplace per contribuire a risolvere facilmente anche i più complessi scenari di integrazione.

![Finestra di progettazione del flusso di app](./media/app-service-logic-what-are-logic-apps/Designer.png)

Se si vogliono replicare automaticamente nuovi record nel database SQL e inviarli alla reception o automaticamente trovare tweet negativi e inviarli a un canale slack

##Perché usare le app per la logica?

Le app per la logica consentono agli sviluppatori di progettare flussi di lavoro che vengono avviati da un trigger e quindi eseguono una serie di passaggi, ognuno dei quali richiama un'app per le API del servizio app e, al contempo, si occupa in modo sicuro dell'autenticazione e delle procedure consigliate, ad esempio il checkpoint e l'esecuzione durevole.

Se si vuole automatizzare qualsiasi processo aziendale (ad esempio, individuare tweet negativi e pubblicarli nel canale slack interno o replicare nuovi record del cliente da SQL, appena arrivano, nel sistema CRM), le app logiche facilitano l'integrazione di diverse origini dati, dal cloud a un'infrastruttura locale. Controllare i [connettori][connectors] per altre informazioni e [iniziare][create] ora per scoprire le attività che è possibile eseguire.

Con le [app per le API BizTalk][biztalk] è anche possibile passare a scenari di integrazione avanzati con la potenza di un [motore regole][rules], della [gestione dei partner trader][tpm] e altro ancora.

- **Strumenti di progettazione facili da usare**: è possibile progettare le app per la logica in modalità end-to-end nel browser. Iniziare con un trigger: da una semplice pianificazione a un momento qualsiasi in cui compare un tweet relativo alla propria azienda. Quindi, gestire un numero qualsiasi di azioni usando la raccolta completa di connettori.

- **Comporre SaaS facilmente**: persino le attività di composizione che sono facili da descrivere sono invece difficili da implementare nel codice. Le app per la logica facilitano oltremodo la connessione ai diversi sistemi. Se, ad esempio, si vuole creare nel software CRM un'attività basata sulle proprie attività negli account di Facebook o Twitter oppure si vuole connettere la propria soluzione di marketing cloud al sistema di fatturazione locale, le app per la logica sono il modo più rapido e affidabile per risolvere questi problemi.

- **Iniziare rapidamente con i modelli**: per iniziare subito è disponibile una [raccolta di modelli][templates] che consente di creare rapidamente alcune soluzioni comuni. Dalle soluzioni BizTalk avanzate alla semplice connettività SaaS e persino alcune soluzioni senza alcuno scopo particolare, la raccolta è il modo più veloce per comprendere il potere delle app per la logica.

- **Estensibilità integrata**: il connettore necessario non è disponibile? Le app per la logica fanno parte della suite del servizio app e sono progettate in modo da interagire con le app per le API; è possibile creare facilmente la propria app per le API da usare come connettore. Sarà possibile creare una nuova app personalizzata oppure condividerla nel Marketplace e realizzare profitti.

- **Straordinaria potenza di integrazione reale**: è possibile iniziare facilmente e svilupparsi secondo le necessità. Le app per la logica possono sfruttare facilmente la potenza di BizTalk, la soluzione di integrazione leader del settore offerta da Microsoft, che consente ai professionisti dell'integrazione di creare le soluzioni necessarie. Per altre informazioni, leggere l'articolo relativo alle [capacità di BizTalk fornite con i Servizi app][biztalk].

## Concetti delle app per la logica

Di seguito sono riportate alcune informazioni fondamentali per comprendere appieno l'esperienza delle app per la logica.

- **Flusso di lavoro**: le app per la logica consentono di modellare graficamente i processi aziendali come una serie di passaggi, o flusso di lavoro.
- **Connettori**: le app per la logica devono avere accesso ai dati e ai servizi. Un connettore è un tipo speciale di app per le API, che viene creato in maniera specifica per assistere l'utente durante la connessione ai dati e la successiva elaborazione. Visualizzare l'elenco dei connettori ora disponibile in [uso dei connettori][connectors].
- **Trigger**: alcuni connettori possono anche fungere da trigger. Un trigger avvia una nuova istanza di un flusso di lavoro basata su un evento specifico, ad esempio l'arrivo di un messaggio di posta elettronica oppure una modifica nell'account di archiviazione di Azure.
-  **Azioni**: ogni passaggio dopo il trigger in un flusso di lavoro viene chiamato azione. Ogni azione viene in genere mappata a un'operazione sul connettore o app per le API personalizzate.
- **BizTalk**: per altri scenari di integrazione più avanzati, Azure App Service include le capacità di BizTalk. BizTalk è la piattaforma di integrazione leader del settore di Microsoft. Le app per le API di BizTalk consentono di includere facilmente la convalida, la trasformazione, le regole e molto altro nei flussi di lavoro dell'app per la logica. Leggere altre informazioni sul [funzionamento delle app per le API di BizTalk][biztalk].

## Introduzione

Per iniziare a usare le app per la logica, seguire l'esercitazione [Creare una nuova app per la logica][create].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[connectors]: app-service-logic-connectors-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md
 

<!---HONumber=July15_HO3-->