<properties
   pageTitle="API Graph di Azure Active Directory"
   description="Panoramica e guida introduttiva per l'API Graph che consente l'accesso a livello di codice ad Azure AD tramite endpoint dell'API REST."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/08/2015"
   ms.author="mbaldwin" />

# API Graph di Azure Active Directory

> *Passare direttamente a [Guida rapida di API Graph](active-directory-graph-api-quickstart.md) e [la documentazione di riferimento API Graph interattiva](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).*

L'API Graph di Azure Active Directory consente l'accesso a livello di codice ad Azure AD tramite endpoint dell'API REST. Le applicazioni possono usare l'API Graph per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory. Ad esempio, l'API Graph supporta le operazioni comuni seguenti per un oggetto utente:

- Creare un nuovo utente nella directory

- Recuperare le proprietà dettagliate dell'utente, ad esempio i gruppi a cui appartiene

- Aggiornare le proprietà dell'utente, ad esempio indirizzo e numero di telefono, oppure modifica della password

- Verificare l'appartenenza a gruppi di un utente per l'accesso in base al ruolo

- Disabilitare o eliminare completamente l'account di un utente

È possibile eseguire operazioni simili anche su altri oggetti, ad esempio gruppi e applicazioni. Per chiamare l'API Graph su una directory, è necessario che l'applicazione sia registrata in Azure AD e che sia configurata per consentire l'accesso alla directory. A tale scopo, viene in genere eseguito un flusso di consenso utente o amministratore.

Per iniziare a utilizzare l'API Azure Active Directory Graph, vedere la [Guida rapida di Graph API](active-directory-graph-api-quickstart.md), o visualizzare la [documentazione di riferimento interattiva per l’API Graph ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## Funzionalità

L'API Graph offre le funzionalità seguenti:

- **Endpoint API REST**: l'API Graph è un servizio RESTful che include endpoint a cui viene eseguito l'accesso tramite richieste HTTP standard. L'API Graph supporta i tipi di contenuto XML o JSON (JavaScript Object Notation) per le richieste e le risposte. Per altre informazioni, vedere [Riferimento all'API REST di Azure AD Graph](https://msdn.microsoft.com/library/azure/hh974478.aspx).

- **Autenticazione con Azure AD**: ogni richiesta all'API Graph deve essere autenticata mediante l'aggiunta di un token Web JSON (JWT) nell'intestazione dell'autorizzazione della richiesta. Per acquisire il token è necessario inviare una richiesta all'endpoint token di Azure AD e specificare credenziali valide. Per acquisire un token per chiamare l'API Graph, è possibile usare il flusso delle credenziali client OAuth 2.0 o il flusso di concessione del codice di autorizzazione. Per altre informazioni, vedere [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Autorizzazione in base al ruolo (RBAC)**: vengono usati gruppi di sicurezza per il controllo degli accessi in base al ruolo nell'API Graph. Se, ad esempio, si vuole stabilire se un utente può accedere a una risorsa specifica, l'applicazione può chiamare l'operazione di [controllo dell'appartenenza a gruppi (transitiva)](https://msdn.microsoft.com/library/azure/dn151601.aspx), che restituirà true o false.

- **Query differenziale**: per verificare se sono state apportate modifiche a una directory tra due intervalli di tempo senza dover interrogare di frequente l'API Graph, è possibile inviare una richiesta di query differenziale. Questo tipo di richiesta restituisce solo le modifiche apportate nel periodo trascorso tra la richiesta di query differenziale precedente e la richiesta corrente. Per altre informazioni, vedere [Query differenziale dell'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/jj836245.aspx).

- **Estensioni della directory**: se si sviluppa un'applicazione che deve eseguire la lettura o la scrittura di proprietà univoche per gli oggetti directory, è possibile eseguire la registrazione e usare i valori dell'estensione mediante l'API Graph. Ad esempio, se l'applicazione richiede una proprietà ID Skype per ogni utente, è possibile registrare la nuova proprietà nella directory e renderla disponibile per ogni oggetto utente. Per altre informazioni, vedere [Estensioni dello schema di directory dell'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## Scenari

Gli scenari di applicazione dell'API Graph sono numerosi. Ecco i più comuni:

- **Applicazione line-of-business (single-tenant)**: in questo scenario, uno sviluppatore aziendale lavora per un'organizzazione con un abbonamento a Office 365. Lo sviluppatore sta creando un'applicazione Web che interagisce con Azure AD per eseguire diverse attività, ad esempio l'assegnazione di una licenza a un utente. Questa attività richiede l'accesso all'API Graph, in modo che lo sviluppatore possa registrare l'applicazione single-tenant in Azure AD e configurare le autorizzazioni di lettura e scrittura per l'API Graph. L'applicazione viene quindi configurata in modo da usare le proprie credenziali o quelle dell'utente che ha eseguito l'accesso per acquisire un token per chiamare l'API Graph.

- **Applicazione SaaS (multi-tenant)**: in questo scenario, un fornitore di software indipendente (ISV) sviluppa un'applicazione Web multi-tenant ospitata che fornisce funzionalità per la gestione degli utenti per altre organizzazioni che usano Azure AD. Queste funzionalità richiedono l'accesso agli oggetti directory, quindi l'applicazione deve chiamare l'API Graph. Lo sviluppatore registra l'applicazione in Azure AD, la configura per richiedere le autorizzazioni di lettura e scrittura per l'API Graph, quindi abilita l'accesso esterno in modo che altre organizzazioni possano fornire il consenso per l'uso dell'applicazione nella propria directory. Quando un utente in un'altra organizzazione esegue l'autenticazione nell'applicazione per la prima volta, viene visualizzata una finestra di dialogo di consenso con le autorizzazioni richieste dall'applicazione. Se si concede il consenso, l'applicazione riceverà le autorizzazioni richieste per l'API Graph nella directory dell'utente. Per altre informazioni sul framework di consenso, vedere [Panoramica del framework di consenso](https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Consent).

## Vedere anche

[Guida introduttiva per l'API Graph di Azure AD](active-directory-graph-api-quickstart.md)

[Documentazione di riferimento all'API Graph di Azure AD ](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

<!---HONumber=Sept15_HO3-->