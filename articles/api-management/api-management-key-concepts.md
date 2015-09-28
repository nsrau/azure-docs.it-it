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
	ms.date="09/15/2015" 
	ms.author="sdanie"/>

#Informazioni su Gestione API

Gestione API consente alle organizzazioni di pubblicare API per consentire a sviluppatori esterni, partner e interni di sfruttare tutte le potenzialità dei rispettivi dati e servizi. Le aziende di tutto il mondo avvertono l'esigenza di estendere le proprie attività come piattaforma digitale, creando nuovi canali, trovando nuovi clienti e stimolando un coinvolgimento maggiore con quelli esistenti. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

Per usare Gestione API, gli amministratori creano API. Ogni API è costituita da una o più operazioni e ogni API può essere aggiunta a uno o più prodotti. Per usare un'API, gli sviluppatori sottoscrivono un prodotto che contiene tale API, quindi possono chiamare l'operazione dell'API, in base ai criteri di utilizzo eventualmente applicabili.

In questo argomento viene fornita una panoramica sui concetti chiave di Gestione API.

>[AZURE.NOTE]Per altre informazioni, vedere il white paper in formato PDF [Cloud-based API Management: Harnessing the Power of APIs](http://j.mp/ms-apim-whitepaper) sulla gestione delle API basate sul cloud. Questo white paper introduttivo sulla gestione delle API redatto da CITO Research tratta gli argomenti seguenti:
>
> - Problematiche e requisiti comuni delle API
>     - Disaccoppiamento delle API e presentazione delle facciate
>     - Rendere operativi gli sviluppatori rapidamente
>     - Protezione dell'accesso
>     - Analisi e metrica
> - Ottenere il controllo e la conoscenza con una piattaforma di gestione API
> - Uso di soluzioni cloud e locali a confronto
> - Gestione API di Azure

## <a name="apis"> </a>API e operazioni

Le API costituiscono la base di un'istanza del servizio Gestione API. Ogni API rappresenta un set di operazioni disponibile per gli sviluppatori. Ogni API contiene un riferimento al servizio back-end che implementa l'API e le relative operazioni sono mappate alle operazioni implementate dal servizio back-end. Le operazioni in Gestione API sono altamente configurabili e offrono il controllo sul mapping degli URL, sui parametri di query e percorsi, sul contenuto della richiesta e della risposta e sulla memorizzazione nella cache della risposta delle operazioni. È anche possibile implementare i criteri relativi a restrizioni IP, quote e limitazione di frequenza al livello dell'API o della singola operazione.

Per altre informazioni, vedere [Come creare le API][] e [Come aggiungere operazioni a un'API][].


## <a name="products"> </a> Prodotti

I prodotti rappresentano il modo in cui le API sono presentate agli sviluppatori. I prodotti in Gestione API contengono una o più API e sono configurati con un titolo, una descrizione e le condizioni per l'utilizzo. I prodotti possono essere **Aperto** o **Protetto**. Per usare i prodotti protetti, è prima di tutto necessaria una sottoscrizione, mentre i prodotti aperti possono essere usati senza sottoscrizione. Quando un prodotto è pronto per essere usato dagli sviluppatori, può essere pubblicato. Dopo la pubblicazione, gli sviluppatori possono visualizzare il prodotto e, nel caso dei prodotti protetti, possono sottoscriverlo. L'approvazione della sottoscrizione è configurata al livello del prodotto e può richiedere l'approvazione dell'amministratore o essere approvata automaticamente.

I gruppi permettono di gestire la visibilità dei prodotti agli sviluppatori. I prodotti garantiscono la visibilità ai gruppi e gli sviluppatori possono visualizzare ed effettuare la sottoscrizione ai prodotti visibili ai gruppi ai quali appartengono.

Per altre informazioni, vedere [Come creare e pubblicare un prodotto][] e il video seguente.

> [AZURE.VIDEO using-products]

## <a name="groups"> </a> Gruppi

I gruppi permettono di gestire la visibilità dei prodotti agli sviluppatori. In Gestione API sono inclusi i gruppi di sistema non modificabili seguenti.

-	**Amministratori**: gli amministratori delle sottoscrizioni di Azure sono membri di questo gruppo. Gli amministratori gestiscono le istanze del servizio Gestione API e creano le API, le operazioni e i prodotti usati dagli sviluppatori.
-	**Sviluppatori**: gli utenti autenticati del portale per sviluppatori rientrano in questo gruppo. Gli sviluppatori sono i clienti che compilano applicazioni usando le API. Agli sviluppatori viene concesso di accedere al portale per sviluppatori e di creare applicazioni che chiamano le operazioni di un'API.
-	**Guest**: gli utenti non autenticati del portale per sviluppatori, ad esempio i potenziali clienti, che visitano il portale per sviluppatori di un'istanza di Gestione API rientrano in questo gruppo. È possibile concedere agli utenti guest un determinato livello di accesso di sola lettura, ad esempio la possibilità di visualizzare le API ma non di chiamarle.

Oltre a questi gruppi di sistema, gli amministratori possono creare gruppi personalizzati oppure [sfruttare i gruppi esterni nei tenant di Azure Active Directory associati](api-management-howto-aad.md/#how-to-add-an-external-azure-active-directory-group). È possibile usare gruppi personalizzati ed esterni assieme ai gruppi di sistema per offrire agli sviluppatori visibilità e accesso ai prodotti API. Ad esempio, si può creare un gruppo personalizzato per gli sviluppatori affiliati a un'organizzazione partner specifica e consentire loro di accedere alle API da un prodotto che contiene solo le API pertinenti. Un utente può essere un membro di più gruppi.

Per altre informazioni, vedere [Come creare e usare i gruppi][].

## <a name="developers"> </a> Sviluppatori

Gli sviluppatori rappresentano gli account utente nell'istanza del servizio Gestione API. Gli sviluppatori possono essere creati o invitati a partecipare dagli amministratori oppure possono iscriversi nel [Portale per sviluppatori][]. Ogni sviluppatore è membro di uno o più gruppi e può effettuare la sottoscrizione ai prodotti che concedono la visibilità a tali gruppi.

Quando gli sviluppatori effettuano la sottoscrizione a un prodotto, viene concessa loro la chiave primaria e secondaria per il prodotto. Questa chiave viene usata per effettuare le chiamate alle API del prodotto.

Per altre informazioni, vedere [Come creare o invitare gli sviluppatori][] e [Come associare i gruppi agli sviluppatori][].

## <a name="policies"> </a> Criteri

I criteri sono una potente funzionalità di Gestione API che consentono all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni più comuni includono la conversione di formato da XML a JSON e la limitazione della frequenza delle chiamate per limitare la quantità di chiamate in ingresso da uno sviluppatore, ma sono disponibili numerosi altri criteri.

Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, a meno che i criteri non specifichino diversamente. Alcuni criteri, come [Control flow][] e [Set variable][], sono basati su espressioni di criteri. Per altre informazioni, vedere [Advanced policies][] ed [Espressioni di criteri][] e guardare il video seguente.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Per un elenco completo dei criteri di Gestione API, vedere [Riferimenti per i criteri][]. Per altre informazioni sull'uso e la configurazione dei criteri, vedere [Criteri di Gestione API][]. Per un'esercitazione sulla creazione di un prodotto con criteri per la limitazione della frequenza e per le quote, vedere [Come creare e configurare impostazioni di prodotto avanzate][]. Per una dimostrazione, vedere il video seguente.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"> </a> Portale per sviluppatori

Il portale per sviluppatori consente agli sviluppatori di visualizzare informazioni sulle API, visualizzare e chiamare operazioni ed effettuare la sottoscrizione ai prodotti. I potenziali clienti possono visitare il portale per sviluppatori, visualizzare API e operazioni ed effettuare l'iscrizione. L'URL del portale per sviluppatori è disponibile nel dashboard del portale di Azure per l'istanza del servizio Gestione API.

È possibile personalizzare l'aspetto del portale per sviluppatori aggiungendo contenuto personalizzato, personalizzando gli stili e aggiungendo il proprio logo.

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Portale per sviluppatori]: #developer-portal

[Come creare le API]: api-management-howto-create-apis.md
[Come aggiungere operazioni a un'API]: api-management-howto-add-operations.md
[Come creare e pubblicare un prodotto]: api-management-howto-add-products.md
[Come creare e usare i gruppi]: api-management-howto-create-groups.md
[Come associare i gruppi agli sviluppatori]: api-management-howto-create-groups.md#associate-group-developer
[Come creare e configurare impostazioni di prodotto avanzate]: api-management-howto-product-with-rules.md
[Come creare o invitare gli sviluppatori]: api-management-howto-create-or-invite-developers.md
[Riferimenti per i criteri]: api-management-policy-reference.md
[Criteri di Gestione API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 

<!---HONumber=Sept15_HO3-->