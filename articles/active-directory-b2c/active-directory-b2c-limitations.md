<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Limitazioni e restrizioni | Microsoft Azure"
	description="Elenco di limitazioni e restrizioni relative ad Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: Limitazioni e restrizioni

Nel periodo di anteprima di Azure Active Directory (AD) B2C molte funzionalità e caratteristiche non sono ancora supportate. Molte di queste limitazioni verranno rimosse prima che Azure AD B2C sia disponibile a livello generale. È tuttavia necessario tenerle presenti se si creano applicazioni per utenti basate su Azure AD B2C durante la fase di anteprima.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Problemi durante la creazione di directory di Azure AD B2C

Sono stati rilevati alcuni problemi che possono verificarsi durante la [creazione di un tenant di Azure AD B2C](active-directory-b2c-get-started). Per istruzioni, consultare questo [articolo](active-directory-b2c-support-create-directory.md).

## Problemi di personalizzazione nei messaggi di posta elettronica e nelle pagine di reimpostazione della password self-service

I messaggi di posta elettronica di verifica e le pagine di reimpostazione della password self-service predefiniti contengono elementi di personalizzazione "Microsoft" e "Azure". Questi in futuro verranno rimossi. È possibile modificare la personalizzazione in queste pagine utilizzando la [funzionalità di personalizzazione della società](./active-directory/active-directory-add-company-branding.md), su cui tali elementi di personalizzazione non verranno visualizzati.

## Supporto per le applicazioni di produzione

Le applicazioni che si integrano con Azure AD B2C non devono essere rilasciate al pubblico come applicazioni di produzione. Azure AD B2C è attualmente in fase di anteprima. Eventuali modifiche possono essere introdotte in qualsiasi momento e non esiste alcun contratto di servizio garantito. Non verrà fornito alcun supporto per tutti gli eventi imprevisti che si possono verificare. Se si è disposti ad accettare il rischio di usare una dipendenza su un servizio che è ancora in fase di sviluppo, è necessario contattare Microsoft tramite tweet, usando l'hashtag @AzureAD, per discutere l'ambito dell'applicazione o del servizio.

## Restrizioni relative alle applicazioni

I tipi di applicazioni seguenti non sono attualmente supportati nell'anteprima di Azure AD B2C. Per una descrizione dei tipi di applicazioni supportati, fare riferimento a [questo articolo](active-directory-b2c-apps).

### Applicazioni a pagina singola (JavaScript)

Molte applicazioni moderne dispongono di un front-end per applicazioni a pagina singola scritto principalmente in JavaScript e spesso tramite appositi framework, ad esempio AngularJS, Ember.js, Durandal e così via. Questo flusso non è ancora disponibile nell'anteprima di Azure AD B2C.

### Applicazioni daemon e sul lato server

Anche le applicazioni che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali applicazioni possono autenticarsi e ottenere i token usando la propria identità, anziché un'identità delegata dell'utente, mediante il [flusso delle credenziali client di OAuth 2.0](active-directory-b2c-protocols.md#oauth2-client-credentials-grant-flow). Questo flusso non è ancora disponibile nell'anteprima di Azure AD B2C. In altre parole, le applicazioni possono ottenere i token solo dopo l'esecuzione di un flusso di accesso utente interattivo.

### API Web autonome

Nell'anteprima di Azure AD B2C è possibile [creare un'API Web protetta mediante token OAuth 2.0](active-directory-b2c-apps.md#web-apis). Tuttavia, tale API Web sarà in grado di ricevere token solo da un client che condivide lo stesso ID applicazione. La creazione di un'API Web accessibile da diversi client non è supportata.

### Catene di API Web (On-Behalf-Of)

Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client nativi che dispongono di un back-end dell'API Web, che a sua volta chiama un servizio Microsoft Online come l'API Graph di Azure AD.

Questo scenario dell'API Web concatenata può essere supportato tramite la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Tuttavia, il flusso On-Behalf-Of non è attualmente implementato nell'anteprima di Azure AD B2C.

## Restrizioni relative alle librerie e agli SDK

Non tutti i linguaggi e le piattaforme dispongono di librerie che supportano l'anteprima di Azure AD B2C. Il set di librerie di autenticazione è attualmente limitato a .NET, iOS, Android e NodeJS. Esercitazioni rapide per ogni libreria sono disponibili nella sezione [Introduzione](active-directory-b2c-overview.md#getting-started).

Se si desidera integrare un'applicazione con l'anteprima di Azure AD B2C usando un linguaggio o una piattaforma differente, vedere il [riferimento ai protocolli OAuth 2.0 e OpenID Connect](active-directory-b2c-protocols.md) che contiene informazioni sulla creazione dei messaggi HTTP necessari per comunicare con il servizio Azure AD B2C.

## Restrizioni relative ai protocolli

L'anteprima di Azure AD B2C supporta OpenID Connect e OAuth 2.0. Tuttavia, non tutte le funzionalità e le caratteristiche di ciascun protocollo sono state implementate. Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'anteprima di Azure AD B2C, leggere il [riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-b2c-protocols.md).

## Restrizioni relative ai token

Molti dei token generati dall'anteprima di Azure AD B2C vengono implementati come token JWT (token Web JSON). Tuttavia, non tutte le informazioni contenute in JWT (note come "attestazioni") sono come dovrebbero essere o sono presenti, ad esempio le attestazioni "sub" e "preferred\_username". In questo ambito si potranno riscontrare diversi cambiamenti durante la fase di anteprima. Per comprendere meglio i token attualmente generati dal servizio Azure AD B2C, leggere il [riferimento ai token](active-directory-b2c-tokens.md).

## Problemi relativi alla gestione degli utenti nel portale di Azure

Le funzionalità B2C sono accessibili nel portale di anteprima di Azure, ma è possibile usare il portale di Azure per accedere ad altre funzionalità di tenant, compresa la gestione degli utenti. Attualmente sono stati rilevati due problemi relativi alla gestione degli utenti (scheda **Utenti**) nel portale di Azure.

- Per un utente di account locale (ad esempio, un utente iscritto con indirizzo di posta elettronica e password o con nome utente e password), il campo **Nome utente** non corrisponde all'identificatore di accesso (indirizzo di posta elettronica o nome utente) usato durante l'iscrizione. Questo avviene perché il campo visualizzato nel portale di Azure è il nome dell'entità utente (UPN), che non viene usato in scenari B2C. Per visualizzare l'identificatore di accesso dell'account locale, individuare l'oggetto utente in [Esplora Graph](https://graphexplorer.cloudapp.net/). Si noterà lo stesso problema con un account utente di social networking (ad esempio, un utente iscritto con Facebook, Google+ e così via), ma in tal caso non esiste un effettivo identificatore di accesso.

    ![Account locale - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Per un account utente locale, non è possibile modificare i campi e salvare le modifiche nella scheda **Profilo**. Questo problema verrà corretto presto.

## Problemi con la reimpostazione della Password avviata dall'amministratore nel portale di Azure

Se si reimposta la password per un consumer basato sull'account locale nel portale di Azure (il comando **Reimposta Password** nella scheda **Utenti**), il consumer non sarà in grado di modificare la propria password nell’accesso successivo e sarà bloccato nelle applicazioni. Stiamo lavorando su correzione del problema. In alternativa, utilizzare l’[API Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) per reimpostare la password del consumer.

## Restrizioni relative all'eliminazione di directory di Azure AD B2C

Non è possibile eliminare un tenant di Azure AD B2C nel portale di Azure.

<!---HONumber=Oct15_HO3-->