---
title: 'Azure Active Directory B2C: limitazioni e restrizioni | Microsoft Docs'
description: Elenco di limitazioni e restrizioni relative ad Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2016
ms.author: swkrish

---
# Azure Active Directory B2C: limitazioni e restrizioni
Diverse funzionalità e caratteristiche di Azure Active Directory (Azure AD) B2C non sono ancora supportate. Molti di questi problemi e limiti noti verranno risolti in futuro, tuttavia è necessario tenerli in considerazione se si creano applicazioni basate su Azure AD B2C destinate agli utenti finali.

## Problemi durante la creazione di tenant di Azure AD B2C
Se si verificano problemi durante la [creazione di un tenant di Azure AD B2C](active-directory-b2c-get-started.md), per istruzioni vedere [Creazione di un tenant di Azure Active Directory (Azure AD) o di un tenant di Azure AD B2C: problemi e soluzioni](active-directory-b2c-support-create-directory.md).

Si noti che si verificano problemi noti quando si elimina un tenant B2C esistente e lo si crea nuovamente con lo stesso nome di dominio. È necessario creare un tenant B2C con un nome di dominio diverso.

## Nota sulle quote dei tenant B2C
Per impostazione predefinita, in un tenant B2C il numero di utenti è limitato a 50.000. Se occorre aumentare la quota del tenant B2C, è necessario contattare il supporto tecnico.

## Problemi relativi alla personalizzazione nei messaggi di posta elettronica di verifica
Il messaggio di posta elettronica di verifica predefinito include la personalizzazione "Microsoft", che verrà rimossa in futuro. Per il momento è possibile rimuoverla tramite la [funzionalità di personalizzazione della società](../active-directory/active-directory-add-company-branding.md).

## Restrizioni relative alle applicazioni
I tipi di applicazioni seguenti non sono attualmente supportati in Azure AD B2C. Per una descrizione dei tipi di applicazioni supportati, vedere [Azure Active Directory B2C: tipi di applicazioni](active-directory-b2c-apps.md).

### Applicazioni a pagina singola (JavaScript)
Molte applicazioni moderne dispongono di un front-end per applicazioni a pagina singola scritto principalmente in JavaScript e spesso tramite appositi framework, ad esempio AngularJS, Ember.js, Durandal e così via. Questo flusso non è ancora disponibile in Azure AD B2C.

### Applicazioni daemon e sul lato server
Anche le applicazioni che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali applicazioni possono autenticarsi e ottenere i token usando la propria identità, invece di un'identità delegata dell'utente, mediante il [flusso di credenziali client OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Questo flusso non è ancora disponibile in Azure AD B2C, quindi attualmente le applicazioni possono ottenere i token solo dopo l'esecuzione di un flusso di accesso utente interattivo.

### API Web autonome
In Azure AD B2C è possibile [creare un'API Web protetta mediante token OAuth 2.0](active-directory-b2c-apps.md#web-apis). Tuttavia, tale API Web sarà in grado di ricevere token solo da un client che condivide lo stesso ID applicazione. La creazione di un'API Web accessibile da diversi client non è supportata.

### Catene di API Web (On-Behalf-Of)
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client nativi che dispongono di un back-end dell'API Web, che a sua volta chiama un servizio online Microsoft come l'API Graph di Azure AD.

Questo scenario dell'API Web concatenata può essere supportato tramite la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Il flusso On-Behalf-Of, tuttavia, non è attualmente implementato in Azure AD B2C.

## Restrizioni relative alle librerie e agli SDK
Il set di librerie supportate da Microsoft che funzionano con Azure AD B2C è molto limitato al momento. È disponibile il supporto per i servizi e le app Web basati su .NET, nonché per i servizi e le app Web NodeJS. Esiste anche una libreria client .NET di anteprima, nota come MSAL, che può essere usata con Azure AD B2C nelle app di Windows e in altre app .NET.

Al momento non è disponibile il supporto di librerie per altri linguaggi o piattaforme, tra cui iOS e Android. Se per la compilazione si vuole usare una piattaforma diversa da quelle indicate sopra, è consigliabile usare un SDK open source. Se necessario, vedere l'articolo di [riferimento ai protocolli OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md). Azure AD B2C implementa OAuth e OpenID Connect, che rende possibile l'uso di una libreria OAuth oppure OpenID Connect generica per l'integrazione.

Le esercitazioni di avvio rapido per iOS e Android usano librerie open source testate per la compatibilità con Azure AD B2C. Tutte le esercitazioni introduttive sono disponibili nella sezione [Introduzione](active-directory-b2c-overview.md#getting-started).

## Restrizioni relative ai protocolli
Azure AD B2C supporta OpenID Connect e OAuth 2.0. Tuttavia, non tutte le funzionalità e le caratteristiche di ciascun protocollo sono state implementate. Per comprendere meglio l'ambito della funzionalità del protocollo supportata in Azure AD B2C, vedere l'articolo di [riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-b2c-reference-protocols.md). Il supporto dei protocolli SAML e WS-Fed non è disponibile.

## Restrizioni relative ai token
Molti dei token generati da Azure AD B2C vengono implementati come token Web JSON o JWT. Tuttavia, non tutte le informazioni contenute in JWT (note come "attestazioni") sono come dovrebbero essere o sono presenti, ad esempio le attestazioni "sub" e "preferred\_username". Mentre i valori, il formato o il significato delle attestazioni cambiano nel tempo, i token per i criteri esistenti rimarranno invariati, quindi è possibile fare affidamento sui relativi valori nelle app di produzione. Via via che i valori cambiano, verrà offerta la possibilità di configurare tali modifiche per ognuno dei criteri creati. Per comprendere meglio i token attualmente generati dal servizio Azure AD B2C, leggere il [riferimento ai token](active-directory-b2c-reference-tokens.md).

## Restrizioni relative ai gruppi annidati
L'appartenenza a gruppi annidati non è supportata nei tenant di Azure AD B2C. Non è prevista l'aggiunta di questa funzionalità.

## Restrizione per la funzionalità di query differenziale nell'API Graph di Azure AD
La [funzionalità di query differenziale nell'API Graph di Azure AD ](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) non è supportata nei tenant di Azure AD B2C. È tuttavia prevista nella roadmap a lungo termine.

## Problemi relativi alla gestione degli utenti nel portale di Azure classico
Le funzionalità B2C sono accessibili nel portale di Azure, ma è possibile usare il portale di Azure classico per accedere ad altre funzionalità del tenant, compresa la gestione degli utenti. Esistono attualmente alcuni problemi noti relativi alla gestione degli utenti nella scheda **Utenti** del portale di Azure classico:

* Per un utente con un account locale, ad esempio un utente che esegue l'iscrizione con indirizzo di posta elettronica e password o con nome utente e password, il campo **Nome utente** non corrisponde all'identificatore di accesso (indirizzo di posta elettronica o nome utente) usato durante l'iscrizione. Questo avviene perché il campo visualizzato nel portale di Azure classico è il nome dell'entità utente, che non viene usato in scenari B2C. Per visualizzare l'identificatore di accesso dell'account locale, individuare l'oggetto utente in [Esplora Graph](https://graphexplorer.cloudapp.net/). Si noterà lo stesso problema con un account utente di social networking (ad esempio, un utente iscritto con Facebook, Google+ e così via), ma in tal caso non esiste un effettivo identificatore di accesso.
  
    ![Account locale - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)
* Per un account utente locale, non è possibile modificare i campi e salvare le modifiche nella scheda **Profilo**.

## Problemi relativi alla reimpostazione della password avviata dall'amministratore nel portale di Azure classico
Se si reimposta la password per un utente con account locale nel portale di Azure classico, usando il comando **Reimposta password** nella scheda **Utenti**, l'utente non riuscirà a modificare la password all'accesso successivo, se si usano criteri di iscrizione o di accesso, e non potrà accedere alle applicazioni. Come soluzione alternativa, usare l'[API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) per reimpostare la password dell'utente, senza scadenza della password, o usare criteri di accesso invece di criteri di iscrizione o accesso.

## Problemi nella creazione di un attributo personalizzato
Un [attributo personalizzato aggiunto nel portale di Azure](active-directory-b2c-reference-custom-attr.md) non viene creato immediatamente nel tenant B2C. Sarà necessario usare l'attributo personalizzato in almeno uno dei criteri, perché venga creato nel tenant B2C e diventi disponibile tramite l'API Graph.

## Problemi relativi alla verifica di un dominio nel portale di Azure classico
Non è attualmente possibile verificare correttamente un dominio nel [portale di Azure classico](https://manage.windowsazure.com/).

## Problemi di accesso con i criteri MFA nei browser Safari
Le richieste ai criteri di accesso, con autenticazione MFA attivata, hanno esito negativo a intermittenza nei browser Safari con errori HTTP 400 (richiesta non valida). Il problema è dovuto al fatto che in Safari i limiti per le dimensioni dei cookie sono bassi e può essere risolto in due modi:

* Usare un criterio di "iscrizione o accesso" anziché un criterio di "accesso".
* Ridurre il numero di **attestazioni dell'applicazione** richieste nei criteri.

<!---HONumber=AcomDC_0831_2016-->