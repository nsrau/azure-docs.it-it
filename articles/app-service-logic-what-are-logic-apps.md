<properties 
	pageTitle="Cosa sono le app per la logica?" 
	description="Informazioni sulle App per la logica del servizio app" 
	authors="joshtwist" 
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
	ms.date="02/24/2015"
	ms.author="jtwist"/>

#Cosa sono le app per la logica?

Il servizio app di Azure è un'offerta di piattaforma interamente gestita come servizio (PaaS) per sviluppatori professionisti che offre un set completo di funzionalità per scenari Web, mobili e di integrazione. Le app per la logica fanno parte della suite di servizi app e consentono a qualsiasi utente o sviluppatore tecnico di automatizzare l'esecuzione dei processi aziendali tramite una finestra di progettazione visiva facile da usare.

Ancora più importante, è possibile combinare le app per la logica con le app per le API e i connettori del Marketplace per contribuire a risolvere facilmente anche i più complessi scenari di integrazione.

![Flow app designer](./media/app-service-learn-about-flows-preview/Designer.png)

##Diffusione dei sistemi SaaS, PaaS e ibridi

L'era del cloud ha prodotto un'ampia diffusione dell'uso dei sistemi SaaS e PaaS, mettendo sempre più a dura prova le capacità degli sviluppatori in ogni dove. Nel frattempo, mentre i backlog delle applicazioni dei reparti IT si allungano, queste eterogenee soluzioni distribuite presentano nuove sfide per l'integrazione.  Per di più, le aziende devono sfruttare i dati e i servizi locali nelle proprie soluzioni, e farlo in modo sicuro.

La creazione di soluzioni che comprendano tutti questi sistemi presenta sfide non irrilevanti per i team di sviluppo, che richiedono molto tempo e sono soggette a errori.

##Perché usare le app per la logica?

Le app per la logica consentono agli sviluppatori di progettare flussi di lavoro che esprimano chiaramente il loro scopo tramite un trigger e una serie di passaggi, ciascuno dei quali richiama un servizio app per app per le API e, al contempo, si occupa in modo sicuro dell'autenticazione e delle procedure consigliate, ad esempio l'esecuzione durevole.

- **Strumenti di progettazione facili da usare**: è possibile progettare le app per la logica in modalità end-to-end nel browser. Iniziare con un trigger: da una semplice pianificazione a un momento qualsiasi in cui compare un tweet relativo alla propria azienda. Quindi, gestire un numero qualsiasi di azioni usando la raccolta completa di connettori.

- **Comporre SaaS facilmente**: persino le attività di composizione che sono facili da descrivere sono invece difficili da implementare nel codice. Le app per la logica facilitano oltremodo la connessione ai diversi sistemi. Se, ad esempio, si vuole creare nel software CRM un'attività basata sulle proprie attività negli account di Facebook o Twitter oppure si vuole connettere la propria soluzione di marketing cloud al sistema di fatturazione locale, le app per la logica sono il modo più rapido e affidabile per risolvere questi problemi.

- **Iniziare rapidamente dai modelli**: per iniziare subito è stata resa disponibile una raccolta di modelli che consente di creare rapidamente alcune soluzioni comuni. Dalle soluzioni BizTalk avanzate alla semplice connettività SaaS e persino alcune soluzioni senza alcuno scopo particolare, la raccolta è il modo più veloce per comprendere il potere delle app per la logica.

- **Estensibilità integrata**: il connettore necessario non è disponibile? Le app per la logica fanno parte della suite del servizio app e sono progettate in modo da interagire con le app per le API; è possibile creare facilmente la propria app per le API da usare come connettore. Sarà possibile creare una nuova app personalizzata oppure condividerla nel Marketplace e realizzare profitti.

- **Straordinaria potenza di integrazione reale**: è possibile iniziare facilmente e svilupparsi secondo le necessità. Le app per la logica possono sfruttare facilmente la potenza di BizTalk, la soluzione di integrazione leader del settore offerta da Microsoft, che consente ai professionisti dell'integrazione di creare le soluzioni necessarie. Per altre informazioni, leggere l'articolo relativo alle [capacità di BizTalk fornite con i Servizi app][biztalk].

## Concetti delle app per la logica

Di seguito sono riportate alcune informazioni fondamentali per comprendere appieno l'esperienza delle app per la logica. 

- **Flusso di lavoro**: le app per la logica consentono di modellare graficamente i processi aziendali come una serie di passaggi, o flusso di lavoro.
- **Connettori**: le app per la logica devono accedere ai dati e ai servizi. Un connettore è un tipo speciale di app per le API, che viene creato in maniera specifica per assistere l'utente durante la connessione ai dati e la successiva elaborazione. Leggere altre informazioni sul [funzionamento dei connettori][biztalk].
- **Trigger**: alcuni connettori possono anche fungere da trigger. Un trigger avvia una nuova istanza di un flusso di lavoro basata su un evento specifico, ad esempio l'arrivo di un messaggio di posta elettronica oppure una modifica nell'account di archiviazione di Azure.
- **BizTalk**: per altri scenari di integrazione più avanzati, i Servizi app di Azure includono le capacità di BizTalk. BizTalk è la piattaforma di integrazione leader del settore di Microsoft. Le app per le API di BizTalk consentono di includere facilmente la convalida, la trasformazione, le regole e molto altro nei flussi di lavoro dell'app per la logica. Leggere altre informazioni sul [funzionamento delle app per le API di BizTalk][biztalk].

## Introduzione

Per iniziare a usare le app per la logica, seguire l'esercitazione [Creare una nuova app per la logica][create].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere l'articolo relativo al [servizio app di Azure][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md




<!--HONumber=49-->