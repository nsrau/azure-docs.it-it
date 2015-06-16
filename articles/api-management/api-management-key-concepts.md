<properties 
	pageTitle="Concetti chiave di Gestione API" 
	description="Informazioni su API, prodotti, ruoli, gruppi e altri concetti chiave di Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

#Concetti chiave di Gestione API

Gestione API consente alle organizzazioni di pubblicare API per consentire a sviluppatori esterni, partner e interni di sfruttare tutte le potenzialità dei rispettivi dati e servizi. Le aziende di tutto il mondo avvertono l'esigenza di estendere le proprie attività come piattaforma digitale, creando nuovi canali, trovando nuovi clienti e stimolando un coinvolgimento maggiore con quelli esistenti. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione.

Per usare Gestione API, gli amministratori creano API. Ogni API è costituita da una o più operazioni e ogni API può essere aggiunta a uno o più prodotti. Per usare un'API, gli sviluppatori sottoscrivono un prodotto che contiene tale API, quindi possono chiamare l'operazione dell'API, in base ai criteri di utilizzo eventualmente applicabili.

In questo argomento viene fornita una panoramica sui concetti chiave di Gestione API.

## Contenuto dell'argomento

-   [API e operazioni][]
-   [Prodotti][]
-   [Gruppi][]
-   [Sviluppatori][]
-   [Criteri][]
-	[Portale per sviluppatori][]


## <a name="apis"> </a>API e operazioni

Le API costituiscono la base di un'istanza del servizio Gestione API. Ogni API rappresenta un set di operazioni disponibile per gli sviluppatori. Ogni API contiene un riferimento al servizio back-end che implementa l'API e le relative operazioni sono mappate alle operazioni implementate dal servizio back-end. Le operazioni in Gestione API sono altamente configurabili e offrono il controllo sul mapping degli URL, sui parametri di query e percorsi, sul contenuto della richiesta e della risposta e sulla memorizzazione nella cache della risposta delle operazioni. È anche possibile implementare i criteri relativi a restrizioni IP, quote e limitazione di frequenza al livello dell'API o della singola operazione.

Per altre informazioni, vedere [Come creare API][] e [Come aggiungere operazioni a un'API][].


## <a name="products"> </a> Prodotti

I prodotti rappresentano il modo in cui le API sono presentate agli sviluppatori. I prodotti in Gestione API contengono una o più API e sono configurati con un titolo, una descrizione e le condizioni per l'utilizzo. Quando un prodotto è pronto per essere usato dagli sviluppatori, può essere pubblicato. Dopo essere stato pubblicato, gli sviluppatori possono visualizzarlo ed effettuare la sottoscrizione. L'approvazione della sottoscrizione è configurata al livello del prodotto e può richiedere l'approvazione dell'amministratore o essere approvata automaticamente.

I gruppi permettono di gestire la visibilità dei prodotti agli sviluppatori. I prodotti garantiscono la visibilità ai gruppi e gli sviluppatori possono visualizzare ed effettuare la sottoscrizione ai prodotti visibili ai gruppi ai quali appartengono. 

Per altre informazioni, vedere [Come creare e pubblicare un prodotto][].

## <a name="groups"> </a> Gruppi

I gruppi permettono di gestire la visibilità dei prodotti agli sviluppatori. In Gestione API sono inclusi i gruppi predefiniti seguenti.

-	**Amministratori**: gli amministratori gestiscono le istanze del servizio Gestione API e creano le API, le operazioni e i prodotti usati dagli sviluppatori.
-	**Sviluppatori**: gli sviluppatori sono i clienti che compilano applicazioni usando le API. Agli sviluppatori viene concesso di accedere al [portale per sviluppatori][] e di creare applicazioni che chiamano le operazioni di un'API.
-	**Guest**: gli utenti non autenticati, ad esempio i potenziali clienti, che visitano il portale per sviluppatori di un'istanza di Gestione API rientrano in questo gruppo. È possibile concedere agli utenti guest un determinato livello di accesso di sola lettura, ad esempio la possibilità di visualizzare le API ma non di chiamarle.

Oltre ai gruppi predefiniti, gli amministratori possono creare gruppi personalizzati. I gruppi personalizzati hanno gli stessi privilegi del gruppo sviluppatori predefinito e possono essere usati per gestire più gruppi di sviluppatori. Ad esempio, è possibile creare un gruppo personalizzato per gli sviluppatori che useranno le API da un prodotto e un altro gruppo che useranno le API da un prodotto diverso.

Per altre informazioni, vedere [Come creare e usare gruppi][].

## <a name="developers"> </a> Sviluppatori

Gli sviluppatori rappresentano gli account utente nell'istanza del servizio Gestione API. Gli sviluppatori possono essere creati o invitati a partecipare dagli amministratori oppure possono iscriversi nel [portale per sviluppatori][]. Ogni sviluppatore è membro di uno o più gruppi e può effettuare la sottoscrizione ai prodotti che concedono la visibilità a tali gruppi.

Quando gli sviluppatori effettuano la sottoscrizione a un prodotto, viene concessa loro la chiave primaria e secondaria per il prodotto. Questa chiave viene usata per effettuare le chiamate alle API del prodotto.

Per altre informazioni, vedere [Come creare o invitare sviluppatori][] e [Come associare gruppi a sviluppatori][].

## <a name="policies"> </a> Criteri

I criteri sono una potente funzionalità di Gestione API che consentono all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni più comuni includono la conversione di formato da XML a JSON e la limitazione della frequenza delle chiamate per limitare la quantità di chiamate in ingresso da uno sviluppatore, ma sono disponibili numerosi altri criteri.

Per un elenco completo dei criteri di Gestione API, vedere [Riferimento ai criteri][]. Per altre informazioni sull'utilizzo e la configurazione dei criteri, vedere [Criteri in Gestione API][]. Per un'esercitazione sulla creazione di un prodotto con criteri per la limitazione della frequenza e per le quote, vedere [Come creare e configurare le impostazioni avanzate del prodotto][].


## <a name="developer-portal"> </a> Portale per sviluppatori

Il portale per sviluppatori consente agli sviluppatori di visualizzare informazioni sulle API, visualizzare e chiamare operazioni ed effettuare la sottoscrizione ai prodotti. I potenziali clienti possono visitare il portale per sviluppatori, visualizzare API e operazioni ed effettuare l'iscrizione. L'URL del portale per sviluppatori è disponibile nel dashboard del portale di Azure per l'istanza del servizio Gestione API.

È possibile personalizzare l'aspetto del portale per sviluppatori aggiungendo contenuto personalizzato, personalizzando gli stili e aggiungendo il proprio logo.

[API e operazioni]: #apis
[Prodotti]: #products
[Gruppi]: #groups
[Sviluppatori]: #developers
[Criteri]: #policies
[Portale per sviluppatori]: #developer-portal

[Come creare API]: ../api-management-howto-create-apis
[Come aggiungere operazioni a un'API]: ../api-management-howto-add-operations
[Come creare e pubblicare un prodotto]: ../api-management-howto-add-products
[Come creare e usare gruppi]: ../api-management-howto-create-groups
[Come associare gruppi a sviluppatori]: ../api-management-howto-create-groups/#associate-group-developer
[Come creare e configurare le impostazioni avanzate del prodotto]: ../api-management-howto-product-with-rules
[Come creare o invitare sviluppatori]: ../api-management-howto-create-or-invite-developers
[Riferimento ai criteri]: ../api-management-policy-reference
[Criteri in Gestione API]: ../api-management-howto-policies
[Creare un'istanza di Gestione API]: ../api-management-get-started/#create-service-instance




<!--HONumber=35.2-->

<!--HONumber=46--> 

<!--HONumber=46--> 
 