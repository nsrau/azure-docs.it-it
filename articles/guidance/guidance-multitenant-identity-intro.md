<properties
   pageTitle="Gestione delle identità per le applicazioni multi-tenant | Microsoft Azure"
   description="Introduzione alla gestione delle identità nelle applicazioni multi-tenant"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Gestione delle identità per le applicazioni multi-tenant - Introduzione

Questo articolo fa [parte di una serie]. Per questa serie è anche disponibile un'[applicazione di esempio] completa.

Si supponga di scrivere un'applicazione SaaS aziendale che deve essere ospitata nel cloud. L'applicazione avrà alcuni utenti:

![Utenti](media/guidance-multitenant-identity/users.png)

Ma tali utenti appartengono a organizzazioni:

![Utenti dell'organizzazione](media/guidance-multitenant-identity/org-users.png)

Esempio: Tailspin vende le sottoscrizioni per la propria applicazione SaaS. Contoso e Fabrikam si iscrivono all'app. Quando Alice (`alice@contoso`) accede, l'applicazione deve sapere che questo utente fa parte di Contoso.

- Alice _deve_ avere accesso ai dati di Contoso.
- Alice _non deve_ avere accesso ai dati di Fabrikam.

Queste informazioni generali illustrano come gestire le identità utente in un'applicazione multi-tenant, usando [Azure Active Directory][AzureAD] (Azure AD) per gestire l'accesso e l'autenticazione.

## Che cosa si intende per multi-tenancy?

Un _tenant_ è un gruppo di utenti. In un'applicazione SaaS, il tenant è un sottoscrittore o un cliente dell'applicazione. Per _multi-tenancy_ si intende un'architettura in cui più tenant condividono la stessa istanza fisica dell'app. Anche se i tenant condividono le risorse fisiche, ad esempio VM o risorse di archiviazione, ogni tenant ottiene la propria istanza logica dell'app.

In genere, i dati dell'applicazione vengono condivisi tra gli utenti all'interno di un tenant, ma non con altri tenant.

![Multi-tenant](media/guidance-multitenant-identity/multitenant.png)

Si confronti questa architettura con un'architettura a tenant singolo, in cui ogni tenant ha un'istanza fisica dedicata. In un'architettura a tenant singolo, si aggiungono tenant aumentando le istanze dell'app.

![Tenant singolo](media/guidance-multitenant-identity/single-tenant.png)

### Multi-tenancy e scalabilità orizzontale

Per ottenere la scalabilità nel cloud, in genere si aggiungono istanze fisiche. Questa procedura è nota come _scalabilità orizzontale_. Si consideri un'app Web. Per gestire più traffico, è possibile aggiungere altre VM del server e inserirle in un servizio di bilanciamento del carico. Ogni VM esegue un'istanza fisica separata dell'app Web.

![Bilanciamento del carico di un sito Web](media/guidance-multitenant-identity/load-balancing.png)

Le richieste possono essere indirizzate a qualsiasi istanza. Nell'insieme, il sistema funziona come una singola istanza logica. È possibile interrompere una VM o aggiungerne una nuova, senza che ciò abbia alcun impatto sugli utenti. In questa architettura, ogni istanza fisica è di tipo multi-tenant ed è possibile eseguire la scalabilità mediante l'aggiunta di più istanze. Se un'istanza viene disattivata, non ha alcun impatto sui tenant.

## Identità in un'app multi-tenant

In un'app multi-tenant è necessario considerare gli utenti nel contesto dei tenant.

**Autenticazione**

- Gli utenti accedono all'app con le credenziali dell'organizzazione. Non devono creare nuovi profili utente per l'app.
- Gli utenti all'interno della stessa organizzazione fanno parte dello stesso tenant.
- Quando un utente esegue l'accesso, l'applicazione conosce a quale tenant appartiene l'utente.

**Autorizzazione**

- Quando si autorizzano azioni dell'utente, ad esempio la visualizzazione di una risorsa, l'app deve tenere in considerazione il tenant dell'utente.
- Agli utenti è possibile assegnare ruoli all'interno dell'applicazione, ad esempio "Amministratore" o "Utente Standard". Le assegnazioni di ruolo devono essere gestite dal cliente, non dal provider SaaS.

**Esempio.** Alice, un dipendente di Contoso, passa all'applicazione nel proprio browser e fa clic sul pulsante di accesso. Viene reindirizzata a una schermata di accesso in cui immette le proprie credenziali aziendali (nome utente e password). A questo punto, viene registrata nell'app come `alice@contoso.com`. L'applicazione individua in Alice un utente amministratore per questa applicazione. Grazie a tale ruolo, Alice può visualizzare un elenco di tutte le risorse appartenenti a Contoso. Tuttavia, non può visualizzare le risorse di Fabrikam, poiché ha il ruolo di amministratore solo nel proprio tenant.

In queste informazioni aggiuntive si esamina l'uso di Azure AD per la gestione delle identità.

- Si presuppone che il cliente archivia i profili utente in Azure AD (tra cui i tenant di Office 365 e Dynamics CRM).
- I clienti con account Active Directory (AD) locale possono usare [Azure AD Connect][ADConnect] per sincronizzare il proprio account AD locale con Azure AD.

Se un cliente con account AD locale non può usare Azure AD Connect (a causa di criteri IT aziendali o per altri motivi), il provider SaaS può attuare la federazione con il cliente di AD mediante Active Directory Federation Services (AD FS). Questa opzione è descritta nell'argomento relativo alla [federazione con un'istanza AD FS del cliente].

Queste informazioni aggiuntive non prendono in considerazione altri aspetti della multi-tenancy, ad esempio il partizionamento dei dati, la configurazione per tenant e così via.

<!-- Links -->
[ADConnect]: ../active-directory/active-directory-aadconnect.md
[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/
[parte di una serie]: guidance-multitenant-identity.md
[federazione con un'istanza AD FS del cliente]: guidance-multitenant-identity-adfs.md
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->