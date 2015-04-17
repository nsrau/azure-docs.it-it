<properties
	pageTitle="In che modo può essere utile il servizio app per gli utenti di Servizi mobili?"
	description="Informazioni sui vantaggi che il servizio app può portare ai progetti di Servizi mobili esistenti."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>In che modo può essere utile il servizio app per gli utenti di Servizi mobili?

##Informazioni generali
Il servizio mobile esistente è al sicuro e continuerà ad essere supportato. Vi sono tuttavia alcuni vantaggi per le app mobili, offerti dalla piattaforma di *servizi app di Azure*, ad oggi non disponibili in Servizi mobili:  

- Offerte semplificate e più economiche per app che includono client sia Web che mobili.
- Nuove funzionalità host che includono processi Web, CName personalizzati e monitoraggio migliorato.
- Integrazione chiavi in mano con Office 365, Dynamics CRM, Salesforce e altre API SaaS di importanza strategica.
- Supporto per codice back-end Java e PHP, oltre a Node.js e .NET. 
- Integrazione chiavi in mano con Gestione traffico.
- Connettività con risorse locali e VPN usando reti virtuali e connessioni ibride.
- Monitoraggio e risoluzione dei problemi delle app mediante NewRelic o AppInsights e avvisi
- Spettro più ampio di risorse di calcolo sottostanti, ad esempio dimensioni delle VM. 
- Scalabilità automatica predefinita, bilanciamento del carico e monitoraggio delle prestazioni.
- Funzionalità predefinite di gestione temporanea, backup, rollback e test in ambiente di produzione. 

## Nuove funzionalità di hosting
Nei *servizi app di Azure* il codice back-end delle *app mobili* viene eseguito nello stesso contenitore usato per le app Web e per le API. È pertanto possibile sfruttare tutte le funzionalità disponibili in questo contenitore, comprese alcune di quelle non attualmente presenti in Servizi mobili: 

- Aggiunta di logica back-end in continua esecuzione tramite processi Web 
- Garanzia che il codice back-end sia sempre in esecuzione
- Uso di CName personalizzati per fornire nomi descrittivi e stabili agli endpoint dei back-end mobili
- Scalabilità geografica dell'app con Gestione traffico


##Possibilità di connettere l'*app mobile* ad API SaaS
Il *servizio app di Azure* semplifica la connessione dell'app mobile ad API SaaS come Office 365, Dynamics, Salesforce, SAP e altre ancora. Il *servizio app di Azure* offre autenticazione chiavi in mano per conto dell'utente e consente di effettuare Single Sign-On reale in tutte le API SaaS in uso, grazie all'associazione di token per API SaaS individuali alla propria identità primaria.

##Accesso ai dati locali tramite reti virtuali
Con Servizi mobili è oggi già possibile usare connessioni ibride per accedere alle risorse locali. Vi sono tuttavia alcune situazioni in cui una soluzione VPN è preferibile. Con il *servizio app di Azure*, è possibile usare la rete virtuale di Azure per il codice del back-end per app mobili.

##Uso del linguaggio di back-end preferito
Il *servizio app di Azure* offre un supporto più ampio e completo per le piattaforme degli sviluppatori tra cui Java, PHP e Python, oltre al supporto per .NET e Node.js già esistente in Servizi mobili.

##Impostazione della scalabilità automatica
In Servizi mobili, tutte le istanze del codice del back-end vengono eseguite in VM di piccole dimensioni. Il *servizio app di Azure* consente di selezionare le dimensioni delle VM da un set di opzioni molto più ampio. È anche possibile aumentare o eseguire la scalabilità orizzontale per gestire qualsiasi carico di lavoro dei clienti in ingresso, sulla base di varie metriche delle prestazioni. 

##Sempre in controllo
Il monitoraggio e gli avvisi consentono di reagire ai problemi in tempo reale grazie alle notifiche automatiche all'utente e al suo team. L'integrazione di avanzate funzionalità analitiche e di monitoraggio fornite da New Relic e AppInsights consentono di ottenere informazioni ancora più complete sulle prestazioni dell'app mobile. Con il *servizio app di Azure* è ora possibile configurare avvisi a livello di codice e tramite portale sulla base di una varietà di metriche delle prestazioni.

##Asset sempre al sicuro
Backup automatico del back-end e del database. Il codice e i dati sono sempre al sicuro in situazioni di emergenza e possono essere facilmente ripristinati, consentendo la conduzione del business in tutta serenità.

##Sviluppo, test, produzione
Con il *servizio app di Azure* è ora possibile creare più ambienti privati di test e gestione temporanea per le app mobili. Questi possono essere usati per testare le app prima della distribuzione. Passaggio alla produzione con tempo di inattività pari a zero. Le app Web sono precaricate per garantire la miglior esperienza utente possibile.



Quando il *servizio app di Azure* verrà reso generalmente disponibile, per chi sceglie di eseguire il passaggio, verrà fornita una soluzione per migrare in modo trasparente i servizi mobili esistenti al servizio app. Nel frattempo, è possibile iniziare a esplorare i *servizi app di Azure* e sfruttarne i vantaggi per il proprio servizio mobile esistente seguendo la relativa esercitazione. [tutorial](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md).



<!--HONumber=49-->