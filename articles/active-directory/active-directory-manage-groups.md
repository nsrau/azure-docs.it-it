<properties
	pageTitle="Gestione dell'accesso alle risorse con Azure Active Directory groups| Microsoft Azure"
	description="Come utilizzare i gruppi in Azure Active Directory per la gestione dell’accesso ad applicazioni cloud e locali e risorse."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory

Azure Active Directory (Azure AD) è una soluzione completa di gestione dell’identità e dell’accesso che fornisce una gamma affidabile di funzionalità per gestire l'accesso alle applicazioni cloud e locali e delle risorse tra cui servizi online di Microsoft quali Office 365 e un mondo di applicazioni non Microsoft SaaS.


> [AZURE.NOTE] Per usare Azure Active Directory, è necessario un account Azure. Se non si dispone di un account, è possibile [iscriversi per un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).


All'interno di Azure Active Directory, una delle caratteristiche principali è la possibilità di gestire l'accesso alle risorse. Queste risorse possono far parte della directory, come nel caso delle autorizzazioni per gestire oggetti tramite i ruoli nella directory, o risorse che sono esterne alla directory, come ad esempio le applicazioni SaaS, i servizi di Azure e i siti di SharePoint o in risorse locali. Esistono 4 modalità con le quali possono assegnare a un utente i diritti di accesso a una risorsa:


1\. Assegnazione diretta

Agli utenti può essere assegnata direttamente una risorsa dal proprietario della risorsa.

2\. Appartenenza al gruppo

Un gruppo può essere assegnato a una risorsa dal proprietario della risorsa, così facendo, si garantisce ai membri di quel gruppo l'accesso alla risorsa. L’appartenenza al gruppo può quindi essere gestita dal proprietario del gruppo. Il proprietario della risorsa delega in modo efficace l'autorizzazione ad assegnare agli utenti la loro risorsa al proprietario del gruppo.

3\. Basato sulla regola

Il proprietario della risorsa può utilizzare una regola per esprimere a quali utenti deve essere assegnato l'accesso a una risorsa. Il risultato della regola dipende dagli attributi utilizzati nella regola e, dai relativi valori per utenti specifici, così facendo, il proprietario della risorsa delega in modo efficiente il diritto di gestire l'accesso alle sue risorse alla fonte autorevole per le attribuzioni utilizzate nella regola. Si noti che il proprietario della risorsa gestisce ancora la regola stessa e determina quali attributi e valori forniscono l'accesso alle risorse.

4\. Autorità esterna

L'accesso a una risorsa è derivato da un'origine esterna; ad esempio, un gruppo che è sincronizzato da un'origine attendibile come ad esempio una directory locale o da un'applicazione SaaS, ad esempio WorkDay. Il proprietario della risorsa assegna al gruppo il compito di fornire l'accesso alla risorsa, e la fonte esterna gestisce i membri del gruppo.

  ![Panoramica del diagramma di gestione dell’accesso](./media/active-directory-access-management-groups/access-management-overview.png)


## Guarda un video che illustra la gestione dell’accesso.

È possibile guardare un breve video che fornisce ulteriori spiegazioni su questo:

[Azure AD: introduzione alle appartenenze dinamiche per i gruppi](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups)

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## Come funziona la gestione dell'accesso in Azure Active Directory?
Al centro della soluzione per la gestione dell’accesso alla Azure Active Directory c’è il gruppo di sicurezza. L’utilizzo di un gruppo di sicurezza per gestire l'accesso alle risorse è un paradigma noto, che consente un modo flessibile e facilmente comprensibile di fornire l'accesso a una risorsa per il gruppo di utenti previsto. Il proprietario della risorsa (o l'amministratore della directory) può assegnare ad un gruppo il compito di fornire un determinato diritto di accesso per le risorse che possiede. Ai membri del gruppo verrà fornito l'accesso, e il proprietario della risorsa può delegare il diritto di gestire l'elenco di membri di un gruppo a un utente, ad esempio un responsabile del reparto o un amministratore dell'helpdesk.

![Diagramma di gestione dell’accesso di Azure Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png) Il proprietario di un gruppo può inoltre rendere tale gruppo disponibile per le richieste self-service. In questo modo, un utente finale può cercare e trovare il gruppo e chiedere di partecipare, richiedendo in modo efficace l'autorizzazione ad accedere alle risorse gestite tramite il gruppo. Il proprietario del gruppo può impostare il gruppo in modo che le richieste di adesione vengano approvate automaticamente oppure richiedano l'approvazione da parte del proprietario del gruppo. Quando un utente effettua una richiesta di adesione a un gruppo, la richiesta di adesione viene inoltrata ai proprietari del gruppo. Se uno dei proprietari approva la richiesta, l'utente richiedente viene informato e viene poi aggiunto al gruppo. Se uno dei proprietari rifiuta la richiesta, l'utente richiedente riceve una notifica, ma non viene aggiunto al gruppo.


## Introduzione alla gestione dell’accesso
Informazioni introduttive È consigliabile provare alcune delle attività di base che è possibile eseguire con i gruppi di Microsoft Azure AD. Utilizzare queste funzionalità per fornire accesso specializzato a diversi gruppi di utenti per diverse risorse all'interno dell'organizzazione. Un elenco di passaggi di base sono elencati di seguito.


* [Creazione di una regola semplice per configurare l’appartenenza dinamica per un gruppo](active-directory-accessmanagement-simplerulegroup.md)

* [Utilizzare un gruppo per gestire l'accesso alle applicazioni SaaS](active-directory-accessmanagement-group-saasapps.md)

* [Rendere la modalità self-service disponibile a un gruppo di utenti](active-directory-accessmanagement-self-service-group-management.md)

* [Sincronizzare un gruppo locale in Azure utilizzando AD Azure Connect](active-directory-aadconnect.md)

* [Gestione dei proprietari di un gruppo](active-directory-accessmanagement-managing-group-owners.md)


## Passaggi successivi per la gestione degli accessi
Ora che si sono comprese le nozioni di base di gestione degli accessi, ecco alcune funzionalità avanzate aggiuntive disponibili in Azure Active Directory per gestire l'accesso alle applicazioni e alle risorse.

* [Utilizzo di una regola semplice per creare un gruppo](active-directory-accessmanagement-simplerulegroup.md)

* [Utilizzo di attributi per la creazione di regole avanzate](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Gestione dei gruppi di sicurezza in Azure Active Directory](active-directory-accessmanagement-manage-groups.md)

* [Impostazione dei gruppi dedicati in Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md)


## Altre informazioni
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

* [Informazioni di riferimento all'API Graph per gruppi](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

<!---HONumber=AcomDC_0211_2016-->