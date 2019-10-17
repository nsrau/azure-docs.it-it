---
title: Opzioni di supporto tecnico e assistenza per gli sviluppatori di app Azure AD | Microsoft Docs
description: Scoprire come ottenere assistenza e supporto per domande e problemi correlati allo sviluppo durante la creazione di applicazioni che si integrano con le identità di Microsoft (Azure Active Directory e account Microsoft)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a160f78f69ed559c576147e9758c837d5ae9b77e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429974"
---
# <a name="support-and-help-options-for-developers"></a>Opzioni di supporto tecnico e assistenza per gli sviluppatori

Se si stia iniziando l'integrazione con Azure Active Directory (Azure AD), con le identità di Microsoft o con l'API Microsoft Graph o se si sta implementando una nuova funzionalità nell'applicazione, in alcuni momenti è necessario l'aiuto della community o la comprensione approfondita delle opzioni di supporto disponibili per gli sviluppatori. Questo articolo aiuta a comprendere queste opzioni, tra cui:

> [!div class="checklist"]
> * Come eseguire una ricerca per verificare se la community non ha ancora risposto alla domanda che si vuole porre o se esiste già la documentazione della funzionalità che si sta provando a implementare
> * In alcuni casi si vogliono solo usare gli strumenti di supporto che consentono di eseguire il debug per un problema specifico
> * Se non si riesce a trovare la risposta che serve, è consigliabile porre una domanda in *Stack Overflow*
> * In caso di problemi con una delle librerie di autenticazione Microsoft, segnalare il problema in *GitHub*
> * Se, infine, è necessario contattare direttamente il personale di assistenza, è consigliabile aprire una richiesta di supporto

## <a name="search"></a>Ricerca

In caso di domande correlate allo sviluppo, le risposte potrebbero essere disponibili nella documentazione, negli [esempi in GitHub](https://github.com/azure-samples) o nelle risposte a domande in [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Ricerca per ambito

Per risultati più veloci, delimitare l'ambito della ricerca in Stack Overflow, nella documentazione e negli esempi di codice usando la query seguente nel motore di ricerca preferito:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Dove *{Your Search Terms}* corrisponde alle parole chiave di ricerca.

## <a name="use-the-development-support-tools"></a>Usare gli strumenti di supporto Microsoft per lo sviluppo

| Strumento  | Description  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Incollare un ID o un token di accesso per decodificare i nomi e i valori di un'attestazione. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Strumento che consente di effettuare richieste e visualizzare le risposte con l'API Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Pubblicare una domanda in Stack Overflow

Stack Overflow è il canale da scegliere per le domande correlate allo sviluppo. Qui, membri della community degli sviluppatori e membri dei team Microsoft sono direttamente coinvolti nell'assistenza agli utenti per la risoluzione dei problemi.

Se non si trova una risposta alla domanda tramite la ricerca, inviare una nuova domanda a Stack Overflow. Per porre domande alla community in modo che possa identificarle e rispondere più rapidamente, usare uno dei tag seguenti:

|Componente/area  | Tag |
|---------|---------|
| Libreria ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Libreria MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Middleware OWIN  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [API Microsoft Graph](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Qualsiasi altra area correlata ad argomenti relativi all'autenticazione o all'autorizzazione | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

I post di Stack Overflow seguenti contengono suggerimenti su come porre domande e su come aggiungere codice sorgente. Seguire queste linee guida per aumentare la probabilità che i membri della community valutino le domande e rispondano rapidamente:

* [Come creare una domanda utile](https://stackoverflow.com/help/how-to-ask)
* [Come creare un esempio minimo, completo e verificabile](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Segnalare un problema in GitHub

In caso di bug o di problema relativo alle librerie Microsoft, segnalare il problema nei repository di GitHub. Le librerie sono open source, quindi gli utenti possono anche inviare anche una richiesta pull.

Per un elenco di librerie e dei rispettivi repository GitHub, vedere gli argomenti seguenti:

* Librerie [ADAL](active-directory-authentication-libraries.md) e repository di GitHub
* [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL. ](https://github.com/AzureAD/microsoft-authentication-library-for-android)Librerie _c di Android e [MSAL. obj](https://github.com/AzureAD/microsoft-authentication-library-for-objc) e repository GitHub

## <a name="open-a-support-request"></a>Aprire una richiesta di supporto

Se è necessario rivolgersi a qualcuno, è consigliabile aprire una richiesta di supporto. Per i clienti Azure sono disponibili diverse opzioni di supporto. Per confrontare i piani, vedere [questa pagina](https://azure.microsoft.com/support/plans/). Il supporto per gli sviluppatori è disponibile anche per i clienti Azure. Per informazioni su come acquistare piani di supporto per sviluppatori, vedere [questa pagina](https://azure.microsoft.com/support/plans/developer/).

* Se si ha già un piano di supporto per Azure, [aprire una richiesta di supporto qui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Se l'utente non è un cliente Azure, è anche possibile aprire una richiesta di supporto con Microsoft tramite [il supporto commerciale](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Per ottenere supporto o porre domande, è anche possibile provare un [agente virtuale](https://support.microsoft.com/contactus/?ws=support).
