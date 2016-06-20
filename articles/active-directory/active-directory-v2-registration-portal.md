<properties
	pageTitle="Guida del portale di registrazione delle app | Microsoft Azure"
	description="Descrizione delle varie funzionalità disponibili nel portale di registrazione delle app di Microsoft."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="dastrock"/>

# Riferimento alla registrazione delle app
Questo documento fornisce il contesto e le descrizioni di varie funzionalità disponibili nel portale di registrazione delle app di Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com).

## Applicazioni personali
Questo elenco include tutte le applicazioni registrate per l'uso con l'endpoint Azure AD 2.0. Queste applicazioni permettono di eseguire l'accesso con account Microsoft personali e account aziendali o dell'istituto d'istruzione di Azure Active Directory. Per altre informazioni sull'endpoint Azure AD 2.0, vedere la [panoramica della versione 2.0](active-directory-appmodel-v2-overview.md). Queste applicazioni possono essere usate anche per l'integrazione con l'endpoint di autenticazione dell'account Microsoft, `https://login.live.com`.

## Applicazioni Live SDK
Questo elenco include tutte le applicazioni registrate per l'uso solo con l'account Microsoft. Non sono abilitate per l'uso con Azure Active Directory. Qui è possibile trovare tutte le applicazioni registrate in precedenza con il portale per sviluppatori di account Microsoft, all'indirizzo `https://account.live.com/developers/applications`. Tutte le funzioni eseguite in precedenza in `https://account.live.com/developers/applications` ora possono essere eseguite nel nuovo portale, `https://apps.dev.microsoft.com`. Per altre domande relative alle applicazioni dell'account Microsoft, è possibile contattare Microsoft.

## Segreti applicazione
I segreti applicazione sono credenziali che consentono all'applicazione di eseguire [l'autenticazione client](http://tools.ietf.org/html/rfc6749#section-2.3) in modo affidabile con Azure AD. In OAuth e OpenID Connect il segreto applicazione è noto come `client_secret`. Nel protocollo della versione 2.0, qualsiasi applicazione che riceve un token di sicurezza in una posizione con indirizzo Web, usando uno schema `https`, deve fare uso di un segreto applicazione per identificarsi in Azure AD al momento del riscatto del token di sicurezza. Per evitare l'archiviazione di segreti in ambienti non protetti, i client nativi che ricevono i token in un dispositivo non possono usare un segreto applicazione per eseguire l'autenticazione client.

Ogni app può contenere due segreti applicazione validi in qualsiasi momento. Mantenendo due segreti è possibile eseguire periodicamente un rollover della chiave nell'intero ambiente dell'applicazione. Dopo aver eseguito la migrazione dell'intera applicazione in un nuovo segreto, è possibile eliminare quello precedente ed effettuare il provisioning di un nuovo segreto.

A questo punto, nel portale di registrazione delle app sono consentiti solo due tipi di segreti applicazione. Scegliendo **Genera nuova password** nell'archivio dati corrispondente viene generato e archiviato un segreto condiviso, che è possibile usare nell'applicazione. Scegliendo **Genera nuova coppia di chiavi** viene creata una nuova coppia di chiavi pubblica/privata che può essere scaricata e usata per l'autenticazione client in Azure AD.

## Profilo
La sezione relativa al profilo del portale di registrazione delle app può essere usata per personalizzare la pagina di accesso dell'applicazione. Attualmente è possibile modificare il logo dell'applicazione della pagina di accesso, l'URL delle condizioni del servizio e l'informativa sulla privacy. Il logo deve essere un'immagine trasparente da 48 x 48 o 50 x 50 pixel in un file GIF, PNG o JPEG di dimensioni non superiori a 15 KB. Provare a modificare i valori e a visualizzare la pagina di accesso risultante.

## Supporto Live SDK
Quando si abilita il supporto Live SDK, per qualsiasi segreto applicazione creato viene eseguito il provisioning sia in Azure AD che negli archivi dati dell'account Microsoft. In questo modo l'applicazione potrà integrarsi direttamente con il servizio di account Microsoft (login.live.com). Se si vuole compilare un'app usando direttamente l'account Microsoft, anziché l'endpoint di Azure AD 2.0, assicurarsi che il supporto Live SDK sia abilitato.

Disabilitando il supporto Live SDK il segreto applicazione viene scritto unicamente nell'archivio dati di Azure AD. L'archivio dati di Azure AD incorpora normative di livello aziendale che permettono di soddisfare determinati standard, ad esempio la conformità FISMA. Se si abilita il supporto Live SDK, l'applicazione potrebbe non essere conforme in base ad alcuni di questi standard.

Se si intende usare solo l'endpoint di Azure AD 2.0, è possibile disabilitare il supporto Live SDK.

<!---HONumber=AcomDC_0608_2016-->