---
title: "Opzioni di supporto tecnico e assistenza per gli sviluppatori di identità di Azure | Microsoft Docs"
description: "Scoprire come ottenere assistenza e supporto per domande e problemi correlati allo sviluppo durante la creazione di applicazioni che si integrano con le identità di Microsoft Azure (Azure Active Directory e account del servizio gestito)"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 06da07aa699d19602449dc365abb971867214a31
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="support-and-help-options-for-developers"></a>Opzioni di supporto tecnico e assistenza per gli sviluppatori 

Indipendentemente dal fatto che si stia iniziando l'integrazione con Azure Active Directory, con le identità di Microsoft o con l'API Microsoft Graph, o che si stia implementando una nuova funzionalità nell'applicazione, in alcuni momenti è necessario l'aiuto della community o la comprensione approfondita delle opzioni di supporto disponibili per gli sviluppatori. Questo articolo aiuta a comprendere queste opzioni. Ecco un riepilogo:

> [!div class="checklist"]
> * Eseguire una ricerca per verificare se la community non ha ancora risposto alla domanda che si vuole porre per un problema o se esiste già la documentazione della funzionalità che si sta tentando di implementare
> * In alcuni casi si vogliono solo usare gli strumenti di supporto che consentano di eseguire il debug per un problema specifico
> * Se non si riesce a trovare la risposta che serve, è consigliabile porre una domanda in *Stack Overflow*
> * In caso di problemi con una delle librerie di autenticazione Microsoft, segnalare il problema in *GitHub*
> * Se, infine, è necessario contattare direttamente il personale di assistenza, è consigliabile aprire una richiesta di supporto


## <a name="search"></a>Ricerca

In caso di domande correlate allo sviluppo, le risposte necessarie potrebbero essere disponibili nella documentazione, negli [esempi in GitHub](https://github.com/azure-samples) o nelle risposte a domande in [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Ricerca per ambito
Per risultati più veloci, delimitare l'ambito della ricerca in Stack Overflow, nella documentazione e negli esempi di codice usando quanto segue nel [motore di ricerca preferito](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Dove *{Your Search Terms}* corrisponde alle parole chiave di ricerca.
<br/>

## <a name="use-our-development-support-tools"></a>Usare gli strumenti di supporto Microsoft per lo sviluppo

|Strumento  |DESCRIZIONE  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| Incollare un ID o un token di accesso per decodificare i nomi e i valori di un'attestazione |
|[Analizzatore dei codici di errore](https://apps.dev.microsoft.com/portal/tools/errors)| Incollare un codice di errore ricevuto durante l'accesso o dalle pagine di consenso per visualizzare le possibili cause e le soluzioni corrispondenti |
|[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Strumento che consente di effettuare richieste e visualizzare le risposte con l'API Microsoft Graph|

<br/>

[![Stack Overflow](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Pubblicare una domanda in Stack Overflow

Stack Overflow è il canale preferito per le domande correlate allo sviluppo. In questo canale, membri della community e membri dei team Microsoft sono direttamente coinvolti nell'assistenza agli utenti per la risoluzione dei problemi di questi ultimi.

Se non si riesce a trovare la soluzione a un problema tramite la ricerca, è possibile inviare una nuova domanda a Stack Overflow. Al momento di creare una domanda, usare uno dei tag seguenti per facilitare l'identificazione del problema e consentire quindi una risposta tempestiva alla domanda stessa:

|Componente/Area  |Tag  |
|---------|---------|
|Libreria ADAL |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|Libreria MSAL     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|Middleware OWIN  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[API Microsoft Graph](https://developer.microsoft.com/graph/) |[[microsoft-graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Qualsiasi altra area correlata ad argomenti relativi all'autenticazione o all'autorizzazione |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Il post di Stack Overflow seguente contiene suggerimenti su come porre domande e su come aggiungere codice sorgente. Se ci si attiene a queste linee guida, aumenta la probabilità che i membri della community valutino le domande e rispondano rapidamente:  
> - [Come creare una domanda utile](https://stackoverflow.com/help/how-to-ask)
> - [Come creare un esempio minimo, completo e verificabile](https://stackoverflow.com/help/mcve)

<br/>


[![Stack Overflow](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Segnalare un problema in GitHub

 In caso di bug o di un problema relativo alle librerie Microsoft, segnalare il problema nei repository di GitHub. Le librerie sono open source e gli utenti sono quindi liberi di inviare anche una richiesta pull. L'articolo seguente contiene un elenco di librerie e i repository corrispondenti in GitHub:

- Librerie [ADAL, MSAL e middleware Owin](active-directory-authentication-libraries.md)e repository GitHub

<br/>

## <a name="open-a-support-request"></a>Aprire una richiesta di supporto

Se è necessario rivolgersi a qualcuno, è consigliabile aprire una richiesta di supporto. Per i clienti Azure sono disponibili diverse opzioni di supporto. Per confrontare i piani, vedere [questa pagina](https://azure.microsoft.com/support/plans/). Il supporto per gli sviluppatori è disponibile anche per i clienti Azure. Per informazioni su come acquistare piani di supporto per sviluppatori, vedere [questa pagina](https://azure.microsoft.com/support/plans/developer/).

- Se si ha già un piano di supporto per Azure, [aprire una richiesta di supporto qui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Se l'utente non è un cliente Azure, è anche possibile aprire una richiesta di supporto con Microsoft tramite [il supporto commerciale](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Per ottenere supporto o porre domande, è anche possibile provare [l'agente virtuale](https://support.microsoft.com/contactus/?ws=support).

### <a name="free-chat-support-for-a-limited-time"></a>Supporto tecnico via chat disponibile gratuitamente per un periodo di tempo limitato

È anche possibile usare il supporto via chat, gratuito per i partner Microsoft per un periodo di tempo limitato. Se la società non è partner Microsoft, è possibile registrarla gratuitamente e ottenere altri vantaggi visitando [questa pagina](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Dopo la registrazione della società, è possibile avviare la richiesta di chat [qui](https://aka.ms/devchat).