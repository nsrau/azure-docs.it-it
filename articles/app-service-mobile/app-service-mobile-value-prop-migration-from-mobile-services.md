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
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>In che modo può essere utile il servizio app per gli utenti di Servizi mobili?

##Panoramica
Il servizio mobile esistente è al sicuro e continuerà ad essere supportato. Vi sono tuttavia alcuni vantaggi per le app per dispositivi mobili, offerti dalla piattaforma del *servizio app di Azure*, a oggi non disponibili in Servizi mobili:

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
Nel *servizio app di Azure* il codice back-end dell'*app per dispositivi mobili* viene eseguito nello stesso contenitore dell'app Web e dell'app per le API. È pertanto possibile sfruttare tutte le funzionalità disponibili in questo contenitore, comprese alcune di quelle non attualmente presenti in Servizi mobili:

- Aggiunta di logica back-end in continua esecuzione tramite processi Web
- Garanzia che il codice back-end sia sempre in esecuzione
- Uso di CName personalizzati per fornire nomi descrittivi e stabili agli endpoint dei back-end mobili
- Scalabilità geografica dell'app con Gestione traffico
- Inserimento di tutte le librerie e i pacchetti desiderati. In .NET gli assembly distribuiti sono gli assembly che vengono usati in fase di esecuzione; non si verificheranno mai conflitti con le versioni nell'ambiente di hosting.
- (Per .NET) Uso delle funzionalità di ASP.NET, tra cui MVC


##Connettere un'*app per dispositivi mobili* all'API SaaS
Il *servizio app di Azure* semplifica la connessione dell'app per dispositivi mobili alle API SaaS, ad esempio Office 365, Dynamics, Salesforce, SAP, e altre ancora. Il *servizio app di Azure* offre un'esperienza di autenticazione pronta all'uso per conto dell'utente e consente di implementare l'autenticazione Single Sign-On in tutte le API SaaS in uso, grazie all'associazione di token per le singole API SaaS alla propria identità primaria.

##Accesso ai dati locali tramite reti virtuali
Con Servizi mobili è oggi già possibile usare connessioni ibride per accedere alle risorse locali. Vi sono tuttavia alcune situazioni in cui una soluzione VPN è preferibile. Con il *servizio app di Azure*, è possibile usare la rete virtuale di Azure per il codice back-end delle app per dispositivi mobili.

##Uso del linguaggio back-end preferito
Il *servizio app di Azure* offre un supporto più ampio e avanzato per le piattaforme di sviluppo tra cui Java, PHP e Python, oltre al supporto per .NET e Node.js già disponibile in Servizi mobili.

##Impostazione della scalabilità automatica
Con Servizi mobili, tutte le istanze del codice back-end vengono eseguite in macchine virtuali del tipo Piccola. Il *servizio app di Azure* permette di selezionare le dimensioni delle macchine virtuali da un set di opzioni molto più ampio. È anche possibile aumentarele prestazioni o il numero di istanze per gestire qualsiasi carico di lavoro in ingresso dei clienti, in base a varie metriche delle prestazioni.

##Tutto sotto controllo
Il monitoraggio e gli avvisi consentono di reagire ai problemi in tempo reale grazie alle notifiche automatiche all'utente e al suo team. L'integrazione di avanzate funzionalità analitiche e di monitoraggio fornite da New Relic e AppInsights consentono di ottenere informazioni ancora più complete sulle prestazioni dell'app per dispositivi mobili. Con il *servizio app di Azure* è ora possibile configurare avvisi a livello di codice e tramite portale in base a diverse metriche delle prestazioni.

##Asset sempre al sicuro
Backup automatico del back-end e del database. Il codice e i dati sono sempre al sicuro in situazioni di emergenza e possono essere facilmente ripristinati, consentendo la conduzione del business in tutta serenità.

##Sviluppo, test, produzione
Con il *servizio app di Azure* è ora possibile creare più ambienti privati di test e staging per le app per dispositivi mobili. Usare questi ambienti per testare le app prima della distribuzione. Passaggio alla produzione con tempo di inattività pari a zero. Le app Web sono precaricate per garantire la miglior esperienza utente possibile.

È possibile iniziare a usare il *servizio app* sperimentando i vantaggi per il servizio mobile esistente seguendo questa [esercitazione](app-service-mobile-migrating-from-mobile-services.md).
 

<!---HONumber=AcomDC_0128_2016-->