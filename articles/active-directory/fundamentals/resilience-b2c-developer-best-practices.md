---
title: Resilienza tramite le procedure consigliate per gli sviluppatori che usano Azure AD B2C | Microsoft Docs
description: Resilienza tramite le procedure consigliate per gli sviluppatori nella gestione delle identità e degli accessi dei clienti usando Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecde474abf3c814b7c3afa4ae18d044868785cf5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919669"
---
# <a name="resilience-through-developer-best-practices"></a>Resilienza tramite le procedure consigliate per gli sviluppatori

In questo articolo vengono illustrate alcune informazioni basate sulla nostra esperienza nell'utilizzo di clienti di grandi dimensioni. È possibile considerare questi consigli nella progettazione e nell'implementazione dei servizi.

![Immagine che mostra i componenti dell'esperienza per sviluppatori](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Utilizzare Microsoft Authentication Library (MSAL)

[Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) e [Microsoft Identity Web authentication Library per ASP.NET](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries) semplificano l'acquisizione, la gestione, la memorizzazione nella cache e l'aggiornamento dei token richiesti da un'applicazione. Queste librerie sono ottimizzate in modo specifico per supportare l'identità Microsoft, incluse le funzionalità che migliorano la resilienza dell'applicazione.

Gli sviluppatori devono adottare le versioni più recenti di MSAL e rimanere sempre aggiornati. Scopri [come aumentare la resilienza di autenticazione e autorizzazione](resilience-app-development-overview.md) nelle tue applicazioni. Laddove possibile, evitare di implementare lo stack di autenticazione e utilizzare librerie ben stabilite.

## <a name="optimize-directory-reads-and-writes"></a>Ottimizza letture e scritture directory

Il servizio directory Microsoft Azure AD B2C supporta miliardi di autenticazioni al giorno. È progettato per un numero elevato di letture al secondo. Ottimizza le Scritture per ridurre al minimo le dipendenze e aumentare la resilienza.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Come ottimizzare le operazioni di lettura e scrittura nella directory

- **Evitare di scrivere funzioni nella directory durante l'accesso**: non eseguire mai una scrittura sull'accesso senza una precondizione (clausola IF) nei criteri personalizzati. Un caso d'uso che richiede una scrittura in un accesso è la [migrazione JIT delle password utente](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Evitare scenari che richiedono una scrittura a ogni accesso.

  - [Le precondizioni](https://docs.microsoft.com/azure/active-directory-b2c/userjourneys) in un percorso utente hanno un aspetto simile al seguente:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - È possibile creare una resistenza alle iscrizioni basate su bot [grazie all'integrazione con un sistema captcha](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - Usare un [esempio di test di carico](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing) per simulare l'iscrizione e l'accesso. 

- **Informazioni sulla limitazione delle richieste**: la directory implementa le regole di limitazione delle richieste a livello di applicazione e tenant. Sono previsti ulteriori limiti di velocità per le operazioni di lettura/lettura, scrittura/POST, aggiornamento/inserimento ed eliminazione/eliminazione e ogni operazione presenta limiti diversi.

  - Una scrittura al momento dell'accesso rientrerà in un POST per i nuovi utenti o per gli utenti esistenti.

  - Un criterio personalizzato che crea o aggiorna un utente a ogni accesso, può potenzialmente raggiungere un limite di frequenza PUT o POST a livello di applicazione. Gli stessi limiti si applicano quando si aggiornano gli oggetti directory tramite Azure AD o Microsoft Graph. Analogamente, esaminare le letture per limitare il numero di letture a ogni accesso.

  - Stimare il picco di carico per stimare la frequenza delle scritture delle directory ed evitare la limitazione. Le stime di picco del traffico dovrebbero includere stime per azioni quali l'iscrizione, l'accesso e l'autenticazione a più fattori (multi-factor authentication). Assicurarsi di testare il sistema Azure AD B2C e l'applicazione per il picco del traffico. È possibile che Azure AD B2C possa gestire il carico senza limitazione, quando le applicazioni o i servizi downstream non lo sono.

  - Comprendere e pianificare la sequenza temporale di migrazione. Quando si pianifica la migrazione degli utenti a Azure AD B2C usando Microsoft Graph, considerare i limiti dell'applicazione e del tenant per calcolare il tempo necessario per completare la migrazione degli utenti. Se si suddivide il processo di creazione dell'utente o lo script utilizzando due applicazioni, è possibile utilizzare il limite per applicazione. Deve comunque rimanere sotto la soglia per ogni tenant.

  - Comprendere gli effetti del processo di migrazione in altre applicazioni. Si consideri il traffico in tempo reale gestito da altre applicazioni per assicurarsi che non si verifichino limitazioni a livello di tenant e di risorse per l'applicazione Live. Per ulteriori informazioni, vedere la [Guida alla limitazione delle richieste Microsoft Graph](https://docs.microsoft.com/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Estendi durata token

In un evento improbabile, quando il servizio di autenticazione Azure AD B2C non è in grado di completare nuove iscrizioni e accessi, è comunque possibile garantire la mitigazione per gli utenti che hanno eseguito l'accesso. Con la [configurazione](https://docs.microsoft.com/azure/active-directory-b2c/configure-tokens)è possibile consentire agli utenti che hanno già eseguito l'accesso di continuare a usare l'applicazione senza alcuna distorsione percepita fino a quando l'utente non si disconnette dall'applicazione o il timeout della [sessione](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior) a causa di inattività.

I requisiti aziendali e l'esperienza dell'utente finale desiderata determineranno la frequenza di aggiornamento dei token per le applicazioni Web e a pagina singola (Spa).

### <a name="how-to-extend-token-lifetimes"></a>Come estendere la durata del token

- **Applicazioni Web**: per le applicazioni Web in cui il token di autenticazione viene convalidato all'inizio dell'accesso, l'applicazione dipende dal cookie di sessione per continuare a estendere la validità della sessione.

  - Consentire agli utenti di rimanere connessi implementando tempi di sessione in sequenza che continueranno a rinnovare le sessioni in base all'attività dell'utente. Se si verifica un'interruzione dell'emissione di token a lungo termine, è possibile che questi tempi di sessione aumentino ulteriormente come una configurazione unica nell'applicazione. Mantieni la durata massima consentita per la sessione.

- **Spa: un'** applicazione a singola pagina può dipendere dai token di accesso per effettuare chiamate alle API. Una SPA usa tradizionalmente il flusso implicito che non genera un token di aggiornamento. La SPA può usare un iframe nascosto per eseguire nuove richieste di token sull'endpoint di autorizzazione se il browser ha ancora una sessione attiva con la Azure AD B2C. Per le Spa sono disponibili alcune opzioni che consentono all'utente di continuare a usare l'applicazione.

  - Estendere la durata di validità del token di accesso per soddisfare i requisiti aziendali.

  - Compilare l'applicazione per usare un gateway API come proxy di autenticazione. In questa configurazione, la SPA viene caricata senza alcuna autenticazione e le chiamate API vengono effettuate al gateway API. Il gateway API invia l'utente tramite un processo di accesso usando una [concessione del codice di autorizzazione](https://oauth.net/2/grant-types/authorization-code/) basata su un criterio e autentica l'utente. Successivamente, la sessione di autenticazione tra il gateway API e il client viene mantenuta usando un cookie di autenticazione. Le API vengono gestite dal gateway API usando il token ottenuto dal gateway API o un altro metodo di autenticazione diretta, ad esempio certificati, credenziali client o chiavi API.

  - [Eseguire la migrazione della Spa dalla concessione implicita](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) al [flusso di concessione del codice di autorizzazione](https://docs.microsoft.com/azure/active-directory-b2c/implicit-flow-single-page-application) con chiave di prova per lo scambio di codice (PKCE) e il supporto per la condivisione di risorse tra le origini (CORS). Eseguire la migrazione dell'applicazione da MSAL.js 1. x a MSAL.js 2. x per realizzare la resilienza delle applicazioni Web.

  - Per le applicazioni per dispositivi mobili, è consigliabile estendere la durata dei token di aggiornamento e di accesso.

- **Applicazioni back-end o microservizi**: poiché le applicazioni back-end (daemon) non sono interattive e non si trovano in un contesto utente, la prospettiva del furto di token è notevolmente diminuita. Si consiglia di raggiungere un equilibrio tra sicurezza e durata e impostare una durata del token lungo.

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-on

Con [Single Sign-on (SSO)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on), gli utenti accedono una volta con un singolo account e ottengono l'accesso a più applicazioni. L'applicazione può essere un'applicazione Web, per dispositivi mobili o a pagina singola (SPA), indipendentemente dalla piattaforma o dal nome di dominio. Quando l'utente accede inizialmente a un'applicazione, Azure AD B2C rende permanente una [sessione basata su cookie](https://docs.microsoft.com/azure/active-directory-b2c/session-overview).

Dopo le successive richieste di autenticazione, Azure AD B2C legge e convalida la sessione basata su cookie e rilascia un token di accesso senza richiedere all'utente di eseguire di nuovo l'accesso. Se SSO è configurato con un ambito limitato a un criterio o a un'applicazione, l'accesso successivo ad altri criteri e applicazioni richiederà una nuova autenticazione.

### <a name="how-to-configure-sso"></a>Come configurare SSO

[Configurare SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start) in modo che sia a livello di tenant (impostazione predefinita) per consentire a più applicazioni e flussi utente nel tenant di condividere la stessa sessione utente. La configurazione a livello di tenant offre la massima resilienza a una nuova autenticazione.  

## <a name="safe-deployment-practices"></a>Procedure di distribuzione sicure

I più comuni distruttori del servizio sono il codice e le modifiche alla configurazione. L'adozione di processi e strumenti di integrazione continua e recapito continuo (CICD) consente una distribuzione rapida su larga scala e riduce gli errori umani durante i test e la distribuzione nell'ambiente di produzione. Adottare CICD per la riduzione, l'efficienza e la coerenza degli errori. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) è un esempio di CICD.

## <a name="web-application-firewall"></a>Web application firewall

Proteggi le tue applicazioni da vulnerabilità note, ad esempio attacchi Distributed Denial of Service (DDoS), SQL injection, script tra siti, esecuzione di codice in modalità remota e molti altri, come documentato in [OWASP Top 10](https://owasp.org/www-project-top-ten/). La distribuzione di un Web Application Firewall (WAF) può difendersi da exploit e vulnerabilità comuni.

- Usare Azure [WAF](https://docs.microsoft.com/azure/web-application-firewall/overview), che offre una protezione centralizzata dagli attacchi.

- Usare WAF con Azure AD [Identity Protection e l'accesso condizionale per fornire la protezione a più livelli quando si](https://docs.microsoft.com/azure/active-directory-b2c/conditional-access-identity-protection-overview) USA Azure ad B2C.  

## <a name="secrets-rotation"></a>Rotazione dei segreti

Azure AD B2C usa i segreti per le applicazioni, le API, i criteri e la crittografia. I segreti proteggono l'autenticazione, le interazioni esterne e l'archiviazione. Il National Institute of Standards and Technology (NIST) chiama l'intervallo di tempo durante il quale una chiave specifica è autorizzata per l'uso da parte di entità legittime a cryptoperiod. Scegliere la lunghezza corretta di [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) per soddisfare le esigenze aziendali. Gli sviluppatori devono impostare manualmente la scadenza e ruotare i segreti in anticipo della scadenza.

### <a name="how-to-implement-secret-rotation"></a>Come implementare la rotazione del segreto

- Usare le [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per le risorse supportate per l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure ad. Quando si usano le identità gestite, è possibile gestire automaticamente le risorse, inclusa la rotazione delle credenziali.

- Eseguire un inventario di tutte le [chiavi e i certificati configurati](https://docs.microsoft.com/azure/active-directory-b2c/policy-keys-overview) in Azure ad B2C. È probabile che questo elenco includa chiavi utilizzate in criteri personalizzati, [API](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api), token ID firma e certificati per SAML.

- Utilizzando CICD, ruotare i segreti che stanno per scadere entro due mesi dalla stagione di picco prevista. Il cryptoperiod massimo consigliato di chiavi private associate a un certificato è di un anno.

- Monitorare e ruotare in modo proattivo le credenziali di accesso all'API, ad esempio le password e i certificati.

## <a name="test-rest-apis"></a>API REST di test

Nel contesto della resilienza, il test delle API REST deve includere la verifica dei codici HTTP, il payload della risposta, le intestazioni e le prestazioni. I test non devono includere solo test di percorso soddisfatti, ma anche controllare se l'API gestisce correttamente gli scenari di problema.

### <a name="how-to-test-apis"></a>Come testare le API

Il piano di test è consigliato per includere [test API completi](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing). Se si sta pianificando un aumento imminente a causa del traffico di promozione o festività, è necessario rivedere il test di carico con le nuove stime. Eseguire test di carico delle API e della rete per la distribuzione di contenuti (CDN) in un ambiente di sviluppo e non in produzione.

## <a name="next-steps"></a>Passaggi successivi

- [Risorse di resilienza per sviluppatori Azure AD B2C](resilience-b2c.md)
  - [Esperienza utente finale resiliente](resilient-end-user-experience.md)
  - [Interfacce resilienti con processi esterni](resilient-external-processes.md)
  - [Resilienza tramite monitoraggio e analisi](resilience-with-monitoring-alerting.md)
- [Resilienza di compilazione nell'infrastruttura di autenticazione](resilience-in-infrastructure.md)
- [Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni](resilience-app-development-overview.md)
