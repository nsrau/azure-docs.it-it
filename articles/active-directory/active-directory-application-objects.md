<properties
   pageTitle="Oggetti applicazione e oggetti entità servizio"
   description="Una descrizione della relazione tra gli oggetti applicazione ed entità servizio in Azure Active Directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   services="active-directory"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/08/2015"
   ms.author="mbaldwin"/>


# Oggetti applicazione e oggetti entità servizio

In questo diagramma viene illustrata la relazione tra un oggetto applicazione e un oggetto entità di servizio nel contesto di un’applicazione di esempio denominata **app HR**. Sono disponibili tre tenant: **Adatum**, il tenant che sviluppa l'app, **Contoso** e **Fabrikam**, i tenant che utilizzano l’**app HR**.

![Relazione tra un oggetto applicazione e un oggetto entità servizio](./media/active-directory-application-objects/application-objects-relationship.png)


Quando si registra un'app nel portale di gestione di Azure, vengono creati due oggetti nel tenant della directory:

- **Oggetto applicazione**: questo oggetto rappresenta una definizione per l'app. È possibile trovare una descrizione dettagliata delle relative proprietà nella sezione **Oggetto applicazione** di seguito.

- **Oggetto entità servizio**: questo oggetto rappresenta un’istanza dell’app nel tenant di directory. È possibile applicare criteri agli oggetti entità di servizio, inclusa l'assegnazione di autorizzazioni agli oggetti entità servizio che consentono all'app di leggere i dati di directory del tenant. Ogni volta che si modifica l'oggetto applicazione, le modifiche vengono applicate anche all'oggetto entità servizio associato nel tenant.


> [AZURE.NOTE]Se l'applicazione è configurata per l'accesso esterno, le modifiche apportate all'oggetto applicazione non vengono riflesse nell’oggetto entità servizio del tenant fino a quando il consumer tenant rimuove l'accesso e lo concede nuovamente.



Nel diagramma precedente, il passaggio "1" è il processo di creazione degli oggetti applicazione ed entità servizio.

Nel passaggio 2, quando l’amministratore di una società concede l'accesso, viene creato un oggetto entità servizio nel tenant Azure AD della società e viene assegnato il livello di accesso alla directory concesso dall'amministratore della società.

Nel passaggio 3, i tenant consumer di un'app (ad esempio Contoso e Fabrikam) dispongono ciascuno di un proprio oggetto entità servizio che rappresenta l'istanza dell'app. In questo esempio, ciascuno di essi dispone di un oggetto entità servizio che rappresenta l'app HR.





## Proprietà dell'oggetto applicazione

Nelle tabelle seguenti sono elencate tutte le proprietà di un oggetto applicazione e sono inclusi dettagli importanti per gli sviluppatori. Queste proprietà si applicano ad applicazioni Web, API Web e applicazioni client native che vengono registrate con Azure AD.


### Generale

Proprietà | Descrizione
| ------------- | -----------
| Nome | Nome visualizzato dell’applicazione. Proprietà obbligatoria nella procedura guidata **Aggiungi applicazione**.
| Logo | Logo di app che rappresenta l'applicazione o la società. Questo logo consente agli utenti esterni di associare più facilmente la richiesta di concessione dell’accesso all'app. Quando si carica un logo, rispettare le specifiche nella procedura guidata **Carica logo**. Se non si fornisce un logo, viene visualizzato un logo predefinito.
| External access | Determina se gli utenti di organizzazioni esterne possono concedere all’app l'accesso Single Sign-On ai dati nella directory dell’organizzazione. Questo controllo interessa solo la capacità di concedere l'accesso. Non modifica l'accesso che è già stato concesso. Solo gli amministratori della società possono concedere l'accesso.


### Single Sign-On

Proprietà | Descrizione
| ------------- | -----------
| App ID URI | Identificatore logico univoco per l'applicazione. Proprietà obbligatoria nella procedura guidata **Aggiungi applicazione**. <br><br>Poiché App ID URI è un identificatore logico, non deve risolvere un indirizzo Internet. Viene presentato dall'app quando si invia una richiesta di Single Sign-On ad Azure AD. Azure AD identifica l'app e invia la risposta Single Sign-On (un token SAML) all'URL di risposta che è stato fornito durante la registrazione dell’app. Usare il valore App ID URI per impostare la proprietà wtrealm (per WS-Federation) o la proprietà Issuer (per SAML-P) quando si effettua una richiesta di accesso. Il valore di **App ID URI** deve essere un univoco in Azure AD dell'organizzazione<br><br>* * Nota * *: quando si abilita un'app per gli utenti esterni, il valore di App ID URI dell'app deve essere un indirizzo in uno dei domini verificati della directory. Di conseguenza, non può essere un URN. In tal modo viene impedito alle altre organizzazioni di specificare (e utilizzare) una proprietà univoca appartenente all'organizzazione. Durante lo sviluppo, è possibile modificare App ID URI in un percorso nel dominio iniziale dell'organizzazione (se non è stato verificato un dominio personalizzato/personale) e aggiornare l'app in modo che utilizzi questo nuovo valore. Il dominio iniziale è il dominio di livello 3 si crea durante l'iscrizione, ad esempio contoso.onmicrosoft.com.
| App URL | L'indirizzo di una pagina web a cui gli utenti possono accedere e utilizzare l'app. Proprietà obbligatoria nella procedura guidata **Aggiungi applicazione**.<br><BR>**Nota**: il valore impostato per questa proprietà nella procedura guidata Aggiungi applicazione è impostato anche come valore di Reply URL.
| URL di risposta | L'indirizzo fisico dell'app. Azure AD invia un token con la risposta Single Sign-On a questo indirizzo. Durante la prima registrazione nella procedura guidata **Aggiungi applicazione**, il valore impostato per App URL viene impostato anche per Reply URL. Quando si esegue una richiesta di accesso, utilizzare il valore Reply URL per impostare la proprietà wreply (per WS-Federation) oppure la proprietà **AssertionConsumerServiceURL** (per SAML-P).<br><BR>**Nota**: quando si abilita un’applicazione per gli utenti esterni, il valore di Reply URL deve essere un indirizzo **https://**. | Federation Metadata URL | (Facoltativa). Rappresenta l'URL fisico del documento di metadati di federazione per l'app. È necessario per supportare la disconnessione SAML-P. Azure AD scarica il documento di metadati che è ospitato nell’endpoint e lo utilizza per individuare la parte pubblica del certificato usato per verificare la firma sulle richieste di disconnessione e l'URL di disconnessione dell'app. Quando si aggiunge l'app per la prima volta, non è possibile configurare questa proprietà. Può essere configurata solo in un secondo momento.<br><BR>**Nota**: se è necessario supportare la disconnessione SAML-P, ma non si dispone di un endpoint di metadati federati per l’app, contattare l’Assistenza clienti per altre opzioni.


### Chiamata dell'API Graph o delle API Web

Proprietà | Descrizione
| ------------- | -----------
| ID client | Identificatore logico univoco per l'app. È necessario utilizzare questo identificatore nelle chiamate all'API Graph oppure ad altre API Web registrate con Azure AD. Azure AD genera automaticamente questo valore durante la registrazione dell'app e non può essere modificato.<BR><BR>Per consentire all'app di accedere alla directory (in lettura o in scrittura) tramite l'API Graph, sono necessari un ID client e una chiave (nota in OAuth 2.0 come segreto client). L'app utilizza l'ID client e la chiave per richiedere un token di accesso dall’endpoint del token Azure AD OAuth 2.0. Per visualizzare tutti gli endpoint di Azure AD, nella barra dei comandi, fare clic su **Visualizza endpoint**. Quando si utilizza l'API Graph per ottenere o impostare (modificare) i dati della directory, l'app utilizza questo token di accesso nell'intestazione Authorize della richiesta all'API Graph.
| Chiavi | Se l'app legge o scrive i dati in Azure AD, ad esempio i dati resi disponibili tramite l'API Graph, necessita di una chiave. Quando si richiede un token di accesso per chiamare l'API Graph, l'app fornisce **Client ID** e **chiave**. L’endpoint del token utilizza l'ID e la chiave per autenticare l'app prima di rilasciare il token di accesso. È possibile creare più chiavi per risolvere gli scenari di rollover della chiave. Inoltre, è possibile eliminare le chiavi che sono scadute, compromesse o non più in uso.
| Manage Access | Scegliere uno dei tre diversi livelli di accesso: Single Sign-On (SSO), SSO e lettura dati directory, oppure SSO e lettura/scrittura dati di directory. È inoltre possibile rimuovere l'accesso. Per ulteriori informazioni sull'accesso alla directory, vedere [Livelli di accesso applicazione](https://msdn.microsoft.com/library/azure/b08d91fa-6a64-4deb-92f4-f5857add9ed8#BKMK_AccessLevels).<br><BR>* * Nota * *: le modifiche a livello di accesso directory dell'app vengono applicate solo alla directory. Le modifiche non si applicano ai clienti che hanno concesso l'accesso all'app.


### Client nativi

Proprietà | Descrizione
| ------------- | -----------
| Redirect URI | L’URI a cui Azure AD reindirizzerà l'agente utente in risposta a una richiesta di autorizzazione OAuth 2.0. Il valore non deve necessariamente essere un endpoint fisico, ma un URI valido.

##

<!---HONumber=Sept15_HO2-->