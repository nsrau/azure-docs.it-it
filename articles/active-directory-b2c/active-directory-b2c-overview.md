---
title: Informazioni su Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come creare e gestire l'esperienza di accesso delle applicazioni tramite Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/05/2018
ms.author: davidmu
ms.openlocfilehash: 4f2c47f3e2debe0dc2b919858af9c347da00be5b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="what-is-azure-active-directory-b2c"></a>Informazioni su Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C è un servizio di gestione delle identità che consente di personalizzare e controllare il modo in cui i clienti si iscrivono, accedono e gestiscono i rispettivi profili quando usano le applicazioni, incluse ad esempio le applicazioni sviluppate per iOS, Android e .NET. Azure AD B2C consente queste azioni proteggendo al tempo stesso le identità dei clienti.

È possibile configurare un'applicazione registrata con Azure AD B2C per eseguire un'ampia varietà di azioni di gestione dell'identità. Di seguito sono riportati alcuni esempi:

- Consentire a un cliente di effettuare l'iscrizione per l'uso dell'applicazione registrata
- Consentire a un cliente iscritto di accedere e iniziare a usare l'applicazione
- Consentire a un cliente iscritto di modificare il proprio profilo
- Abilitare l'autenticazione a più fattori nell'applicazione
- Consentire al cliente di iscriversi e accedere con specifici provider di identità
- Concedere l'accesso dall'applicazione alle API compilate
- Personalizzare l'aspetto dell'esperienza di iscrizione e accesso
- Gestire sessioni Single Sign-On per l'applicazione

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Aspetti da tenere in considerazione prima dell'uso di Azure AD B2C

- In che modo il cliente deve interagire con l'applicazione?
- Che tipo di esperienza dell'interfaccia utente si vuole offrire ai clienti?
- Quali provider di identità potranno scegliere i clienti nell'applicazione?
- Il processo di accesso richiede API aggiuntive per l'esecuzione?

### <a name="customer-interaction"></a>Interazione del cliente

Azure AD B2C supporta [OpenID Connect](https://openid.net/connect/) per tutte le esperienze dei clienti. Nell'implementazione di OpenID Connect in Azure AD B2C l'applicazione avvia questo percorso utente inviando richieste di autenticazione ad Azure AD B2C. Il risultato della richiesta è un `id_token`. Questo token di sicurezza rappresenta l'identità del cliente.

Ogni applicazione che usa Azure AD B2C deve essere registrata in un tenant di Azure AD B2C tramite il portale di Azure. Il processo di registrazione raccoglie e assegna all'applicazione alcuni valori. Questi valori includono un ID dell'applicazione, che identifica in modo univoco l'applicazione, e un URI di reindirizzamento, che può essere usato per reindirizzare le risposte.

Ogni interazione di un'applicazione segue un modello generale simile al seguente:

1. L'applicazione indirizza il cliente per l'esecuzione dei criteri.
2. Il cliente completa i criteri in base alla relativa definizione.
3. L'applicazione riceve un token di sicurezza.
4. L'applicazione usa il token di sicurezza per tentare di accedere a una risorsa protetta.
5. Il server delle risorse convalida il token di sicurezza per verificare che sia possibile concedere l'accesso.
6. L'applicazione aggiorna periodicamente il token di sicurezza.

Questi passaggi possono presentare lievi differenze a seconda del tipo di applicazione che si sta compilando.

Azure AD B2C interagisce in sequenza con i provider di identità, i clienti, altri sistemi e la directory locale per completare un'attività di identità, ad esempio accesso di un cliente, registrazione di un nuovo cliente o reimpostazione di una password. La piattaforma sottostante che stabilisce le relazioni di trust tra più parti ed esegue questi passaggi è denominata Framework dell'esperienza di gestione delle identità. Questo framework e un criterio (detto anche percorso utente o criterio del framework di attendibilità) definiscono in modo esplicito gli attori, le azioni, i protocolli e la sequenza di passaggi da completare.

Azure AD B2C assicura la protezione delle applicazioni da attacchi Denial of Service e alle password in diversi modi. Azure AD B2C usa tecniche di rilevamento e mitigazione, ad esempio cookie SYN e limiti di velocità e connessione, per proteggere le risorse dagli attacchi Denial of Service. Questa mitigazione è disponibile anche per gli attacchi di forza bruta e per gli attacchi alle password con dizionario.

#### <a name="built-in-policies"></a>Criteri predefiniti

Ogni richiesta inviata ad Azure AD B2C specifica un criterio. Un criterio controlla il comportamento dell'applicazione durante l'interazione con Azure AD B2C. Sono disponibili criteri predefiniti per le attività di identità più comuni, ad esempio iscrizione, accesso e modifica del profilo.  Ad esempio, i criteri di iscrizione consentono di controllare i comportamenti configurando le impostazioni seguenti:

- Account di social networking che il cliente può usare per eseguire l'iscrizione per l'applicazione
- Dati raccolti dal cliente, ad esempio il nome o il codice postale
- Autenticazione a più fattori
- Aspetto di tutte le pagine di iscrizione
- Informazioni restituite all'applicazione

#### <a name="custom-policies"></a>Criteri personalizzati 

I criteri personalizzati sono file di configurazione che definiscono il comportamento del Framework di esperienza di identità nel tenant di Azure AD B2C. I criteri personalizzati possono essere completamente modificati per completare numerose attività. Un criterio personalizzato è rappresentato come uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. 

È possibile usare più criteri personalizzati di diversi tipi nel tenant di Azure AD B2C in base alle esigenze e riutilizzarli per più applicazioni. In questo modo è possibile definire e modificare le esperienze delle identità dei clienti con modifiche minime al codice o anche senza alcuna modifica al codice. I criteri possono essere usati aggiungendo uno speciale parametro di query alle richieste di autenticazione HTTP.

I criteri personalizzati possono essere usati per controllare i percorsi utente nei modi seguenti:

- Definendo l'interazione con le API per acquisire informazioni aggiuntive, verificare le attestazioni fornite dai clienti oppure attivare processi esterni.
- Modificando il comportamento in base alle attestazioni dalle API o alle attestazioni nella directory, ad esempio *migrationStatus*.
- Eseguendo qualsiasi flusso di lavoro non coperto dai criteri predefiniti, ad esempio la raccolta di informazioni aggiuntive da un cliente durante un'esperienza di accesso e l'esecuzione di un controllo delle autorizzazioni per l'accesso a una risorsa.

### <a name="identity-providers"></a>Provider di identità

Un provider di identità è un servizio che autentica le identità dei clienti e rilascia token di sicurezza. In Azure AD B2C è possibile configurare diversi provider di identità nel tenant, ad esempio un account Microsoft, Facebook o Amazon tra gli altri. 

Per configurare un provider di identità nel tenant di Azure AD B2C, è necessario registrare l'identificatore dell'applicazione o l'identificatore del client e la password o il segreto client dall'applicazione del provider di identità creato. Questo identificatore e la password vengono quindi usati per configurare l'applicazione.

### <a name="user-interface-experience"></a>Esperienza dell'interfaccia utente

È possibile controllare la maggior parte del contenuto HTML e CSS che viene presentato ai clienti. Con la funzionalità di personalizzazione dell'interfaccia utente della pagina, è possibile definire l'aspetto di qualsiasi criterio. È anche possibile mantenere la coerenza visiva e del marchio tra l'applicazione e Azure AD B2C.

Azure AD B2C esegue il codice nel browser del cliente e usa un approccio moderno denominato Condivisione di risorse tra le origini (CORS). In primo luogo, specificare un URL nel criterio con contenuto HTML personalizzato. Azure AD B2C unisce elementi dell'interfaccia utente con il contenuto HTML caricato dall'URL e quindi visualizza la pagina al cliente.

È possibile inviare parametri ad Azure AD B2C in una stringa di query. Passando il parametro all'endpoint HTML, è possibile modificare dinamicamente il contenuto della pagina. È ad esempio possibile modificare l'immagine di sfondo della pagina di accesso o di iscrizione ad Azure AD B2C in base a un parametro passato dall'applicazione Web o per dispositivi mobili.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Come si può iniziare a usare Azure AD B2C?

In Azure AD B2C un tenant rappresenta l'organizzazione ed è una directory di utenti. Ogni tenant di Azure AD B2C è distinto e separato dagli altri tenant di Azure AD B2C. Un tenant contiene informazioni sui clienti che hanno effettuato l'iscrizione per l'uso dell'applicazione, ad esempio le password, i dati di profilo e le autorizzazioni.

È necessario collegare il tenant di Azure AD B2C alla sottoscrizione di Azure per abilitare tutte le funzionalità e pagare i costi di utilizzo. Per consentire ai clienti di Azure AD B2C di accedere all'applicazione, è necessario registrarla in un tenant di Azure AD B2C.

Prima di configurare l'applicazione per l'uso di Azure AD B2C, è innanzitutto necessario creare un tenant di Azure AD B2C e registrare l'applicazione. Per registrare l'applicazione, seguire la procedura descritta in [Tutorial: Register an application to enable sign-up and sign-in using Azure AD B2C](tutorial-register-applications.md) (Esercitazione: registrare un'applicazione per consentire l'iscrizione e l'accesso tramite Azure AD B2C).
  
Gli sviluppatori di applicazioni Web ASP.NET possono impostare l'applicazione per l'autenticazione degli account tramite la procedura descritta in [Tutorial: Enable a web application to authenticate with accounts using Azure AD B2C](active-directory-b2c-tutorials-web-app.md) (Esercitazione: abilitare un'applicazione Web per l'autenticazione con gli account tramite Azure AD B2C).

Gli sviluppatori di applicazioni desktop possono impostare l'applicazione per l'autenticazione degli account tramite la procedura descritta in [Tutorial: Enable a desktop application to authenticate with accounts using Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md) (Esercitazione: abilitare un'applicazione desktop per l'autenticazione con gli account tramite Azure AD B2C).

Gli sviluppatori di applicazioni a pagina singola che usano Node.js possono impostare l'applicazione per l'autenticazione degli account tramite la procedura descritta in [Tutorial: Enable a single-page application to authenticate with accounts using Azure AD B2C](active-directory-b2c-tutorials-spa.md) (Esercitazione: abilitare un'applicazione a pagina singola per l'autenticazione con gli account tramite Azure AD B2C).

## <a name="next-steps"></a>Passaggi successivi

Avviare la configurazione dell'applicazione per l'esperienza di iscrizione e accesso passando all'esercitazione.

> [!div class="nextstepaction"]
> [Tutorial: Register an application to enable sign-up and sign-in using Azure AD B2C](tutorial-register-applications.md) (Esercitazione: registrare un'applicazione per consentire l'iscrizione e l'accesso tramite Azure AD B2C)
