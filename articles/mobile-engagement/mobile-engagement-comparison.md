---
title: Confronto tra Azure Mobile Engagement e altri servizi Azure simili
description: 'Confronto tra Azure Mobile Engagement e altri servizi Azure simili: HockeyApp, AppInsights e Notification Hubs'
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo

---
# Confronto tra Azure Mobile Engagement e altri servizi Azure simili
L'elenco dei servizi offerti da Microsoft Azure è in continua espansione; a volte ci si potrebbe chiedere quale sia la differenza tra Azure Mobile Engagement e gli altri servizi di cui è sentito parlare o di cui si ha letto qualcosa. Questo articolo mira a fare chiarezza e a guidare alla scelta di Azure Mobile Engagement quando è più adatto l'uso previsto.

Azure Mobile Engagement è un servizio dedicato appositamente **agli addetti al marketing digitale e ai direttori marketing**, ma può essere utilizzato da qualsiasi **proprietario o autore di app** che intenda aumentare l'utilizzo, la conservazione e la monetizzazione delle proprie app.

*Si tratta di una piattaforma SaaS (software-as-a-service) di engagement degli utenti che offre informazioni basate sui dati relative all'utilizzo dell'app e alla segmentazione degli utenti in tempo reale, nonché notifiche push intelligenti e contestualizzate e messaggistica in-app.*

Da questa definizione emergono le seguenti caratteristiche principali che evidenziano la straordinaria proposta di valore:

1. **Notifiche push contestualizzate e messaggistica in-app**
   
   Questo è l'obiettivo principale del prodotto: notifiche push personalizzate e mirate. A tale scopo, vengono raccolti dati avanzati di analisi comportamentale.
2. **Analisi dell'uso delle app in base ai dati**
   
   Sono disponibili SDK per tutte le piattaforme per raccogliere le analisi comportamentali relative agli utenti delle app. Si parla di analisi comportamentali e non di analisi delle prestazioni perché l'obiettivo principale è conoscere la modalità con cui gli utenti usano l'app. Vengono raccolti i dati di base sulle analisi delle prestazioni relativi ad errori e arresti anomali del sistema, ma non è questa la principale funzionalità del prodotto.
3. **Segmentazione degli utenti in tempo reale**
   
   Dopo aver raccolto i dati di analisi comportamentale degli utenti dell'app, è possibile segmentare i destinatari in base a diversi parametri e ai dati raccolti per eseguire campagne push mirate.
4. **SaaS (Software-as-a-service):**
   
   Microsoft offre un portale separato dal portale di gestione di Azure ottimizzato per interagire e visualizzare le analisi comportamentali avanzate relative agli utenti di app ed eseguire campagne push di marketing. Il prodotto è stato studiare per essere subito pronto all'uso.

In base a questo set di differenziazione, ecco come si relaziona il prodotto con altre offerte Azure apparentemente simili, tenendo presente che quest'articolo non propone un confronto tra le funzionalità ma si concentra maggiormente sullo scenario per definire il prodotto più adatto:

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) è la soluzione DevOps mobile di Microsoft. Permette di distribuire le compilazioni ai beta tester, raccogliere dati sugli arresti anomali del sistema e ottenere commenti e suggerimenti degli utenti. Si integra con Visual Studio Team Services, semplificando la distribuzione delle compilazioni e l'integrazione degli elementi di lavoro.
   
   È incentrato su DevOps e sulla raccolta dei dati sulle analisi delle prestazioni relativi alle app per dispositivi mobili. Alla fine si potrebbe decidere di integrare nell'app sia HockeyApps che Mobile Engagement e non sarebbe una scelta insolita, sebbene i dati raccolti possano a volte sovrappporsi, l'obiettivo primario dei prodotti sia differente e gli obiettivi perseguiti siano diversi.
2. [Application Insights](../application-insights/app-insights-overview.md) Se l'app per dispositivi mobili ha un lato server, si utilizza Application Insights per monitorare il lato server web dell'app, mentre per le app per dispositivi mobili sul lato client è necessario utilizzare HockeyApp.
3. Gli [hub di notifica](https://azure.microsoft.com/services/notification-hubs/) offrono un servizio efficace perché non richiedono un SDK integrato nell'app, offrono il pieno controllo sull'app e permettono di inviare notifiche push con funzionalità essenziali di aggiunta di tag. Queste caratteristiche sono perfette per qualsiasi proprietario di app che non sia molto interessato alla distribuzione mirata e preferisca l'invio/la comunicazione immediata delle informazioni agli utenti dell'app (trasmissione a molti utenti).
   
   In questo caso, l'obiettivo principale è l'invio di notifiche in tempi strettissimi con funzionalità di segmentazione di base.

Seguono alcuni scenari:

1. Giulio fa parte del team di marketing digitale di Adventure Works, che pubblica app per dispositivi mobili per gli utenti. L'obiettivo di Giulio è fare in modo che gli utenti che scaricano le app per dispositivi mobili continuino a utilizzarle in modo frequente. In questo modo non aumenta soltanto la fedeltà al marchio tra gli utenti dell'app, ma anche la monetizzazione quando gli utenti dell'app fanno acquisti dall'app. Per questo motivo, Giulio dovrà inviare agli utenti dell'app notifiche mirate che siano utili e inducano ad aprire l'app e a consultarla spesso. In questo contesto, a Giulio servirà Mobile Engagement.
2. Giovanna fa parte del team di sviluppo delle app per dispositivi mobili di Adventure Works e ha bisogno di un prodotto che le permetta di registrare i dettagli degli arresti anomali e delle eccezioni e di ottenere la telemetria delle prestazioni da un'app. In questo contesto, a Giovanna farà comodo HockeyApp. Giovanna potrebbe integrare nella stessa app sia HockeyApp per gli scenari destinati agli sviluppatori che Azure Mobile Engagement per Giulio, per sfruttare i vantaggi di entrambe le soluzioni.
3. Roberta fa parte del team di sviluppo delle app per dispositivi mobili del network Contoso News e desidera inviare avvisi sulle notizie di rilievo a tutti gli utenti senza un'eccessiva segmentazione non appena è disponibile l'avviso sulle notizie. In questo caso, a Roberta serviranno gli hub di notifica. In questo scenario è possibile tuttavia che, quando l'app viene perfezionata, sia necessario raffinare la segmentazione e ottenere dettagli sul comportamento degli utenti dell'app. A quel punto Roberta dovrà valutare Azure Mobile Engagement.

Ricapitolando, Mobile Engagement non serve soltanto alla raccolta di analisi, non è semplicemente un altro prodotto di analisi di Microsoft. Permette di inviare notifiche push mirate e a tal fine vengono raccolti dati di analisi comportamentali, ma l'obiettivo principale rimane l'invio di notifiche push utili agli utenti dell'app che non vengano percepite come posta indesiderata.

Per ulteriori dettagli, vedere questo [video rapido](mobile-engagement-overview.md) che sintetizza Mobile Engagement.

<!---HONumber=AcomDC_0824_2016-->