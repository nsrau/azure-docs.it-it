<properties 
	pageTitle="Cosa sono le app per le API?" 
	description="Informazioni sui motivi per i quali il servizio app di Azure è la piattaforma migliore per lo sviluppo, la pubblicazione e l'hosting di API RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tdykstra"/>

# Cosa sono le app per le API?

## Informazioni generali

Il servizio app di Azure è una piattaforma di calcolo interamente gestita per sviluppatori professionisti che offre un set completo di funzionalità per scenari Web, mobili e di integrazione. Le app per le API fanno parte della famiglia di prodotti dei servizi app e consentono a qualsiasi utente tecnico o sviluppatore di individuare, ospitare, gestire e monetizzare API e connettori SaaS su una piattaforma cloud moderna, ricca di funzionalità, scalabile e disponibile a livello globale.

## Perché usare le app per le API?

Le app per le API del servizio app di Azure semplificano lo sviluppo la pubblicazione, la gestione e la monetizzazione delle API.

- **Uso dell'API così com'è**: usare ASP.NET, Java, PHP, Node.js o Python per le API. Le API possono sfruttare le funzionalità del servizio app di Azure senza modifiche.

- **Connessione delle app alle piattaforme SaaS più diffuse**: il servizio app di Azure semplifica la connessione alle più piattaforme SaaS più diffuse, tra cui Salesforce, Office 365, Twitter, Facebook, Dropbox e molte altre ancora.

- **Semplice controllo di accesso**: le API possono essere esposte solo alle altre app all'interno del servizio app di Azure o al pubblico ed è possibile aggiungere l'autenticazione di Azure Active Directory o servizi di terze parti senza apportare modifiche al codice.

- **Condivisione delle API mediante Raccolta aziendale di API e condivisione pubblica**: è possibile condividere facilmente le API con altri team all'interno dell'organizzazione usando la propria raccolta aziendale privata di API. Le API possono essere condivise pubblicamente per l'utilizzo da parte di sviluppatori terzi.

- **Generazione automatica di SDK**: il servizio app di Azure può compilare automaticamente SDK per un'ampia gamma di linguaggi tra cui C#, Java e JavaScript, rendendo le API disponibili per più piattaforme.

- **Uso dell'IDE migliore disponibile sul mercato**: l'integrazione di [Visual
Studio](/campaigns/visual-studio-2013/) semplifica la creazione, il debug, la preparazione in pacchetti e la pubblicazione di API e consente la gestione delle app nel corso del loro intero ciclo di vita.

<!--removing per directive to cut back on size of this section
- **Nessuna operazione**: eseguire le app per le API in un ambiente a disponibilità elevata con l'applicazione di patch automatica. Le app per le API distribuite con il servizio app di Azure sono isolate e vengono ospitate in macchine virtuali dedicate alle applicazioni, garantendo prestazioni prevedibili e isolamento della sicurezza.

- **Scalabilità automatica**: il servizio app di Azure consente di aumentare o scalare orizzontalmente in modo rapido per gestire qualsiasi carico di clienti in ingresso. Selezionare manualmente il numero e le dimensioni delle macchine virtuali o configurare la
[scalabilità automatica](/documentation/videos/auto-scaling-azure-web-sites/)
per adattare i server al carico o alla pianificazione.
-->

- **Accesso ai dati in locale**: le app per le API possono usare i dati del proprio data center tramite [connessioni ibride](integration-hybrid-connection-overview.md) e [rete virtuale](web-sites-integrate-with-vnet.md).

- **Distribuzione priva di problemi**: configurare l'integrazione continua e i flussi di lavoro di distribuzione con Visual Studio Online, GitHub, TeamCity, Hudson o BitBucket, per compilare, testare e distribuire automaticamente l'app Web dopo ogni archiviazione di codice o test di integrazione eseguito correttamente.

## Concetti delle app per le API ##

- **Raccolta di app per le API**: è possibile scegliere da un elenco in continua crescita di modelli di app per le API esistenti.
- **Connettori**: un tipo di app per le API che consente di connettersi facilmente a piattaforme SaaS come Salesforce e Office 365.
- **Gateway**: un'app Web che gestisce le funzioni di Gestione API come l'autenticazione per tutte le app per le API in un gruppo di risorse. 
- **Scalabilità automatica**: le app Web possono aumentare o scalare orizzontalmente per gestire qualsiasi carico di clienti in ingresso. Selezionare manualmente il numero e le dimensioni delle macchine virtuali o configurare la scalabilità automatica per adattare i server in base al carico o alla pianificazione.
- **Integrazione continua**: configurare l'integrazione continua e i flussi di lavoro di distribuzione con VSO, GitHub, TeamCity, Hudson o BitBucket, per compilare, testare e distribuire automaticamente l'app Web dopo ogni archiviazione di codice o test di integrazione eseguito con esito positivo.

## Introduzione

Per iniziare a usare le app per le API, seguire l'esercitazione [Creare un'app per le API](app-service-dotnet-create-api-app.md).

Per altre informazioni sulla piattaforma di servizio app di Azure, vedere [Informazioni sul servizio app di Azure](app-service-value-prop-what-is.md).

<!--HONumber=49-->