<properties
	pageTitle="Azure AD e applicazioni: guida per gli sviluppatori | Microsoft Azure"
	description="Scritto per i professionisti IT, questo articolo include linee guida per l'integrazione delle applicazioni di Azure in Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Azure AD e applicazioni: Guida per gli sviluppatori

## Panoramica

Questa guida offre una panoramica dello sviluppo di applicazioni line-of-business (LoB) per Azure Active Directory (AD) ed è stata redatta in modo specifico per gli amministratori globali di Active Directory/Office 365.

La creazione di applicazioni integrate con Azure AD offre agli utenti dell'organizzazione servizi Single Sign-On per Office 365. La disponibilità dell'applicazione in Azure AD consente di avere il controllo dei criteri di autenticazione impostati per l'applicazione. Per altre informazioni sull'accesso condizionale e su come proteggere le applicazioni con l'autenticazione a più fattori (MFA), vedere il documento seguente: [Configurare le regole di accesso](active-directory-conditional-access-azuread-connected-apps.md).

Per usare Azure Active Directory, l'applicazione deve essere registrata. La registrazione dell'applicazione consente agli sviluppatori dell'organizzazione di autenticare i membri dell'organizzazione con Azure AD e richiedere l'accesso alle loro risorse, ad esempio posta elettronica, calendario, documenti e così via.

Qualsiasi membro della directory (non guest) può registrare un'applicazione, operazione nota anche come *creazione di un oggetto applicazione*.

La registrazione di un'applicazione consente a qualsiasi utente di eseguire le operazioni seguenti:

- Ottenere un'identità per l'applicazione riconosciuta da Azure AD
- Ottenere uno o più segreti/chiavi utilizzabili dall'applicazione per l'autenticazione in AD
- Personalizzare l'applicazione con nome, logo ed altri elementi nel portale di Azure
- Sfruttare le funzionalità di autorizzazione di Azure AD per la propria applicazione
  - Controllo degli accessi in base al ruolo per l'applicazione
  - Azure Active Directory come server di autorizzazione oAuth (proteggere un'API esposta dall'applicazione)

- Dichiarare le autorizzazioni necessarie per il funzionamento previsto per l'applicazione, tra cui:
	  - Autorizzazioni per l'app (solo amministratori globali). Ad esempio:
	    - Appartenenza ai ruoli in un'altra appartenenza di ruolo o applicazione Azure AD relativa a una risorsa, un gruppo di risorse o una sottoscrizione di Azure
	  - Autorizzazioni delegate (qualsiasi utente). Ad esempio:
	    - (Azure AD) Profilo di accesso e lettura
	    - (Exchange) Lettura di posta elettronica, invio di posta elettronica
	    - (SharePoint) Lettura

> [AZURE.NOTE]Per impostazione predefinita, qualsiasi membro può registrare un'applicazione. Per informazioni su come limitare le autorizzazioni per la registrazione delle applicazioni per membri specifici, vedere il documento [Procedura per l'aggiunta delle applicazioni ad Azure AD](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Ecco cosa dovrà fare l'amministratore globale per aiutare gli sviluppatori a rendere le loro applicazioni pronte per la produzione:

- Configurare le regole di accesso (criteri di accesso/autenticazione a più fattori)
- Configurare l'app per richiedere l'assegnazione di utenti e assegnare gli utenti
- Evitare l'esperienza di autorizzazione utente predefinita

## Configurazione delle regole di accesso

Come già indicato in precedenza, fare riferimento all'articolo seguente per altre informazioni sulla configurazione delle regole di accesso per qualsiasi applicazione.

[Configurazione delle regole di accesso](active-directory-conditional-access-azuread-connected-apps.md).

## Configurare l'app per richiedere l'assegnazione di utenti e assegnare gli utenti

Per impostazione predefinita, l'assegnazione degli utenti non è obbligatoria per consentire l'accesso a un'applicazione. Tuttavia, se l'applicazione espone ruoli o si desidera che venga visualizzata nel pannello di accesso dell'utente, l'assegnazione degli utenti dovrebbe essere richiesta, così come l'assegnazione di utenti e gruppi.

[Richiedere l'assegnazione degli utenti](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Per gli abbonati ad Azure AD Premium o Enterprise Mobility Suite (EMS), è consigliabile sfruttare i gruppi. L'assegnazione di gruppi per l'applicazione consente di delegare le attività di gestione continuativa degli accessi al proprietario del gruppo. È possibile creare il gruppo oppure richiedere al responsabile dell'organizzazione di creare il gruppo con gli strumenti per la gestione dei gruppi.

[Assegnazione di utenti a un'applicazione](active-directory-applications-guiding-developers-assigning-users.md) [Assegnazione di gruppi a un'applicazione](active-directory-applications-guiding-developers-assigning-groups.md)

## Evitare la visualizzazione della richiesta di consenso dell'utente

Per impostazione predefinita, l'utente dovrà fornire il suo consenso per l'autorizzazione richiesta per eseguire l'accesso. L'esperienza di consenso, ovvero la richiesta di acconsentire alla concessione delle autorizzazioni per un'applicazione, può creare confusione tra gli utenti che non hanno familiarità con questo tipo di decisioni.

Per le applicazioni attendibili, è possibile fornire il consenso per l'applicazione per conto di tutti gli utenti dell'organizzazione.

Per altre informazioni sul consenso dell'utente e l'esperienza di consenso in Azure, vedere [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md).

##Articoli correlati

- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->