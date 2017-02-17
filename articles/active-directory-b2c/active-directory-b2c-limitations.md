---
title: 'Azure Active Directory B2C: limitazioni e restrizioni |Documentazione Microsoft'
description: Elenco di limitazioni e restrizioni relative ad Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 04ec3310-98bb-4bb1-856d-ddc66913b390
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: bfffb074a905184269992a19993aabc22bb1256f
ms.openlocfilehash: b65c54819374e90a8318a3f3eecce5b71b01b17f


---
# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active Directory B2C: limitazioni e restrizioni
Diverse funzionalità e caratteristiche di Azure Active Directory (Azure AD) B2C non sono ancora supportate. Molti di questi problemi e limiti noti verranno risolti in futuro, tuttavia è necessario tenerli in considerazione se si creano applicazioni basate su Azure AD B2C destinate agli utenti finali.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problemi durante la creazione di tenant di Azure AD B2C
Se si verificano problemi durante la [creazione di un tenant di Azure AD B2C](active-directory-b2c-get-started.md), per istruzioni vedere [Creazione di un tenant di Azure Active Directory o di un tenant di Azure AD B2C: problemi e soluzioni](active-directory-b2c-support-create-directory.md).

Si noti che si verificano problemi noti quando si elimina un tenant B2C esistente e lo si crea nuovamente con lo stesso nome di dominio. È necessario creare un tenant B2C con un nome di dominio diverso.

## <a name="branding-issues-on-verification-email"></a>Problemi relativi alla personalizzazione nei messaggi di posta elettronica di verifica
Il messaggio di posta elettronica di verifica predefinito include la personalizzazione "Microsoft", che verrà rimossa in futuro. Per il momento è possibile rimuoverla tramite la [funzionalità di personalizzazione della società](../active-directory/active-directory-add-company-branding.md).

## <a name="branding-issues-on-local-account-sign-in-page-in-a-sign-in-policy"></a>Problemi relativi alla personalizzazione nella pagina di accesso all'account locale in un criterio di accesso
Per personalizzare la pagina di accesso all'account locale in un criterio di accesso è possibile usare unicamente la [funzionalità di personalizzazione della società](../active-directory/active-directory-add-company-branding.md) e non la funzionalità di personalizzazione dell'interfaccia utente della pagina descritta [qui](active-directory-b2c-reference-ui-customization.md). Inoltre, non sono disponibili etichette o segnaposto nei campi nome utente e password. Per risolvere questo problema, è consigliabile usare il "criterio di iscrizione o di accesso", completamente personalizzabile. Se si vuole avere la possibilità di personalizzare interamente la pagina di accesso all'account locale in un criterio di accesso, votare per la funzionalità in [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13062033-b2c-fully-customizable-sign-in-page).

## <a name="restrictions-on-applications"></a>Restrizioni relative alle applicazioni
I tipi di applicazioni seguenti non sono attualmente supportati in Azure AD B2C. Per una descrizione dei tipi di applicazioni supportati, vedere [Azure Active Directory B2C: tipi di applicazioni](active-directory-b2c-apps.md).

### <a name="daemons--server-side-applications"></a>Applicazioni daemon e sul lato server
Anche le applicazioni che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali applicazioni possono autenticarsi e ottenere i token usando la propria identità, invece di un'identità delegata dell'utente, mediante il [flusso di credenziali client OAuth 2.0](active-directory-b2c-reference-protocols.md). Questo flusso non è ancora disponibile in Azure AD B2C, quindi attualmente le applicazioni possono ottenere i token solo dopo l'esecuzione di un flusso di accesso utente interattivo.

### <a name="standalone-web-apis"></a>API Web autonome
In Azure AD B2C è possibile [creare un'API Web protetta mediante token OAuth 2.0](active-directory-b2c-apps.md#web-apis). Tuttavia, tale API Web sarà in grado di ricevere token solo da un client che condivide lo stesso ID applicazione. La creazione di un'API Web accessibile da diversi client non è supportata.

### <a name="web-api-chains-on-behalf-of"></a>Catene di API Web (On-Behalf-Of)
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client nativi che dispongono di un back-end dell'API Web, che a sua volta chiama un servizio online Microsoft come l'API Graph di Azure AD.

Questo scenario dell'API Web concatenata può essere supportato tramite la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Il flusso On-Behalf-Of, tuttavia, non è attualmente implementato in Azure AD B2C.

## <a name="restrictions-on-reply-urls"></a>Restrizioni per gli URL di risposta
Attualmente, le app registrate in Azure AD B2C sono limitate a un set limitato di valori di URL di risposta. Gli URL di risposta per le app e i servizi Web devono iniziare con lo schema `https` e tutti i valori degli URL di risposta devono condividere un singolo dominio DNS. Non è possibile ad esempio registrare un'app Web con uno degli URL diretti seguenti:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Il sistema di registrazione confronta l'intero nome DNS dell'URL di risposta esistente con il nome DNS dell'URL di risposta che si sta aggiungendo. La richiesta di aggiungere il nome DNS non viene soddisfatta se si verificano le condizioni seguenti:

* Il nome DNS intero del nuovo URL di risposta non corrisponde al nome DNS dell'URL di risposta esistente.
* Il nome DNS intero del nuovo URL di risposta non è un sottodominio secondario dell'URL di risposta esistente.

Se ad esempio l'applicazione ha questo URL di risposta:

`https://login.contoso.com`

è possibile eseguire un'aggiunta analoga alla seguente:

`https://login.contoso.com/new`

In questo caso, il nome DNS corrisponde esattamente. In alternativa, è possibile eseguire un'aggiunta analoga alla seguente:

`https://new.login.contoso.com`

In questo caso, si fa riferimento a un sottodominio DNS di login.contoso.com. Se si desidera un'app con URL di risposta login-east.contoso.com e login-west.contoso.com, è necessario aggiungere tali URL nell'ordine seguente:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Gli ultimi due URL possono essere aggiunti perché si tratta di sottodomini del primo URL di risposta, ovvero contoso.com. Questa limitazione verrà rimossa in una versione futura.

Per informazioni su come registrare un'app in Azure AD B2C, vedere [Come registrare l'applicazione con Azure Active Directory B2C](active-directory-b2c-app-registration.md).

## <a name="restriction-on-libraries-and-sdks"></a>Restrizioni relative alle librerie e agli SDK
Il set di librerie supportate da Microsoft che funzionano con Azure AD B2C è molto limitato al momento. È disponibile il supporto per i servizi e le app Web basati su .NET, nonché per i servizi e le app Web NodeJS.  Esiste anche una libreria client .NET di anteprima, nota come MSAL, che può essere usata con Azure AD B2C nelle app di Windows e in altre app .NET.

Al momento non è disponibile il supporto di librerie per altri linguaggi o piattaforme, tra cui iOS e Android.  Se per la compilazione si vuole usare una piattaforma diversa da quelle indicate sopra, è consigliabile usare un SDK open source. Se necessario, vedere l'articolo di [riferimento ai protocolli OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md).  Azure AD B2C implementa OAuth e OpenID Connect, che rende possibile l'uso di una libreria OAuth oppure OpenID Connect generica per l'integrazione.

Le esercitazioni di avvio rapido per iOS e Android usano librerie open source testate per la compatibilità con Azure AD B2C.  Tutte le esercitazioni introduttive sono disponibili nella sezione [Introduzione](active-directory-b2c-overview.md#get-started) .

## <a name="restriction-on-protocols"></a>Restrizioni relative ai protocolli
Azure AD B2C supporta OpenID Connect e OAuth 2.0. Tuttavia, non tutte le funzionalità e le caratteristiche di ciascun protocollo sono state implementate. Per comprendere meglio l'ambito della funzionalità del protocollo supportata in Azure AD B2C, vedere l'articolo di [riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-b2c-reference-protocols.md). Il supporto dei protocolli SAML e WS-Fed non è disponibile.

## <a name="restriction-on-tokens"></a>Restrizioni relative ai token
Molti dei token generati da Azure AD B2C vengono implementati come token Web JSON o JWT. Tuttavia, non tutte le informazioni contenute in JWT (note come "attestazioni") sono come dovrebbero essere o sono presenti, Un esempio è l'attestazione "preferred_username".  Mentre i valori, il formato o il significato delle attestazioni cambiano nel tempo, i token per i criteri esistenti rimarranno invariati, quindi è possibile fare affidamento sui relativi valori nelle app di produzione.  Via via che i valori cambiano, verrà offerta la possibilità di configurare tali modifiche per ognuno dei criteri creati.  Per comprendere meglio i token attualmente generati dal servizio Azure AD B2C, leggere il [riferimento ai token](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Restrizioni relative ai gruppi annidati
L'appartenenza a gruppi annidati non è supportata nei tenant di Azure AD B2C. Non è prevista l'aggiunta di questa funzionalità.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Restrizione per la funzionalità di query differenziale nell'API Graph di Azure AD
La [funzionalità di query differenziale nell'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) non è supportata nei tenant di Azure AD B2C. È tuttavia prevista nella roadmap a lungo termine.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problemi relativi alla gestione degli utenti nel portale di Azure classico
Le funzionalità B2C sono accessibili nel portale di Azure, ma è possibile usare il portale di Azure classico per accedere ad altre funzionalità del tenant, compresa la gestione degli utenti. Esistono attualmente alcuni problemi noti relativi alla gestione degli utenti nella scheda **Utenti** del portale di Azure classico:

* Per un utente con un account locale, ad esempio un utente che esegue l'iscrizione con indirizzo di posta elettronica e password o con nome utente e password, il campo **Nome utente** non corrisponde all'identificatore di accesso (indirizzo di posta elettronica o nome utente) usato durante l'iscrizione. Questo avviene perché il campo visualizzato nel portale di Azure classico è il nome dell'entità utente, che non viene usato in scenari B2C. Per visualizzare l'identificatore di accesso dell'account locale, individuare l'oggetto utente in [Esplora Graph](https://graphexplorer.cloudapp.net/). Si noterà lo stesso problema con un account utente di social networking (ad esempio, un utente iscritto con Facebook, Google+ e così via), ma in tal caso non esiste un effettivo identificatore di accesso.
  
    ![Account locale - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)
* Per un account utente locale, non è possibile modificare i campi e salvare le modifiche nella scheda **Profilo** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problemi relativi alla reimpostazione della password avviata dall'amministratore nel portale di Azure classico
Se si reimposta la password per un utente con account locale nel portale di Azure classico, usando il comando **Reimposta password** nella scheda **Utenti**, l'utente non riuscirà a modificare la password all'accesso successivo, se si usano criteri di iscrizione o di accesso, e non potrà accedere alle applicazioni. Come soluzione alternativa, usare l' [API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) per reimpostare la password dell'utente, senza scadenza della password, o usare criteri di accesso invece di criteri di iscrizione o accesso.

## <a name="issues-with-creating-a-custom-attribute"></a>Problemi nella creazione di un attributo personalizzato
Un [attributo personalizzato aggiunto nel portale di Azure](active-directory-b2c-reference-custom-attr.md) non viene creato immediatamente nel tenant B2C. Sarà necessario usare l'attributo personalizzato in almeno uno dei criteri, perché venga creato nel tenant B2C e diventi disponibile tramite l'API Graph.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problemi relativi alla verifica di un dominio nel portale di Azure classico
Non è attualmente possibile verificare correttamente un dominio nel [portale di Azure classico](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problemi di accesso con i criteri MFA nei browser Safari
Le richieste ai criteri di accesso, con autenticazione MFA attivata, hanno esito negativo a intermittenza nei browser Safari con errori HTTP 400 (richiesta non valida). Il problema è dovuto al fatto che in Safari i limiti per le dimensioni dei cookie sono bassi e può essere risolto in due modi:

* Usare un criterio di "iscrizione o accesso" anziché un criterio di "accesso".
* Ridurre il numero di **attestazioni dell'applicazione** richieste nei criteri.

## <a name="issues-with-windows-desktop-wpf-apps-using-azure-ad-b2c"></a>Problemi con applicazioni di Windows Desktop WPF che utilizzano Azure AD B2C
Talvolta le richieste di Azure AD B2C da un'app WPF Desktop di Windows non riescono con il seguente messaggio di errore: "Finestra di dialogo di autenticazione basata su browser non completata. Motivo: Il protocollo non è noto e non sono stati inseriti protocolli di collegamento che corrispondano.".

Ciò è dovuto alle dimensioni dei codici di autorizzazione forniti da Azure AD B2C; le dimensioni sono correlate al numero di attestazioni richieste in un token. Una soluzione per questo problema consiste nel ridurre il numero di attestazioni richieste nel token e di eseguire una query API Graph separatamente per le altre attestazioni.



<!--HONumber=Feb17_HO2-->


