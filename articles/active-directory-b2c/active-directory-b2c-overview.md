---
title: Informazioni su Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come creare e gestire l'esperienza di accesso alle applicazioni con Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a7ae48d65a5cceb11339c4f304a3149741c2a7e8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172766"
---
# <a name="what-is-azure-active-directory-b2c"></a>Informazioni su Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C è un servizio di gestione delle identità che consente di personalizzare e controllare il modo in cui i clienti interagiscono con l'applicazione. Tale interazione include l'iscrizione, l'accesso e la gestione dei profili quando i clienti usano le applicazioni. È possibile scegliere applicazioni per iOS, Android e .NET, ad esempio. Azure AD B2C consente queste azioni proteggendo al tempo stesso le identità dei clienti.

Un'applicazione registrata in Azure AD B2C viene configurata per eseguire numerose attività di gestione delle identità. Di seguito sono riportati alcuni esempi:

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

Azure AD B2C supporta [OpenID Connect](https://openid.net/connect/) per tutte le esperienze dei clienti. Nell'implementazione di OpenID Connect in Azure AD B2C, l'applicazione avvia il percorso utente inviando richieste di autenticazione ad Azure AD B2C. Il risultato della richiesta è un `id_token`. Questo token di sicurezza rappresenta l'identità del cliente.

Ogni applicazione che usa Azure AD B2C deve essere registrata in un tenant di Azure AD B2C tramite il portale di Azure. Il processo di registrazione raccoglie e assegna all'applicazione alcuni valori. Tali valori includono un ID applicazione che identifica in modo univoco l'applicazione. Viene definito un URI di reindirizzamento usato per indirizzare le risposte all'applicazione.

Ogni interazione di un'applicazione segue un modello generale simile al seguente:

1. L'applicazione indirizza il cliente per l'esecuzione dei criteri.
2. Il cliente completa i criteri in base alla relativa definizione.
3. L'applicazione riceve un token di sicurezza.
4. L'applicazione usa il token di sicurezza per provare ad accedere a una risorsa protetta.
5. Il server delle risorse convalida il token di sicurezza per verificare che sia possibile concedere l'accesso.
6. L'applicazione aggiorna periodicamente il token di sicurezza.

Questi passaggi sono leggermente diversi a seconda del tipo di applicazione che si sta creando.

Azure AD B2C interagisce in sequenza con i provider di identità, i clienti, altri sistemi e la directory locale per completare un'attività di identità, ad esempio accesso di un cliente, registrazione di un nuovo cliente o reimpostazione di una password. La piattaforma sottostante che stabilisce le relazioni di trust tra più parti ed esegue questi passaggi è denominata Framework dell'esperienza di gestione delle identità. Questo framework e un criterio (detto anche percorso utente o criterio del framework di attendibilità) definiscono in modo esplicito gli attori, le azioni, i protocolli e la sequenza di passaggi da completare.

Azure AD B2C protegge le applicazioni da attacchi Denial of Service e alle password. Azure AD B2C usa tecniche di rilevamento e mitigazione, ad esempio cookie SYN e limiti di velocità e connessione, per proteggere le risorse dagli attacchi Denial of Service. Questa mitigazione è disponibile anche per gli attacchi di forza bruta e per gli attacchi alle password con dizionario.

#### <a name="user-flows"></a>Flussi degli utenti

Ogni richiesta inviata ad Azure AD B2C specifica un flusso utente, che è un criterio che controlla il comportamento dell'interazione dell'applicazione con Azure AD B2C. Nel portale di Azure AD B2C sono disponibili flussi utente predefiniti per le attività di identità più comuni, ad esempio iscrizione, accesso e modifica del profilo.  Ad esempio, i flussi utente di iscrizione consentono di controllare i comportamenti configurando le impostazioni seguenti:

- Account di social networking usati dal cliente per l'iscrizione all'applicazione
- Dati raccolti dal cliente, ad esempio il nome o il codice postale
- Autenticazione a più fattori
- Aspetto di tutte le pagine di iscrizione
- Informazioni restituite all'applicazione

#### <a name="custom-policies"></a>Criteri personalizzati 

I [criteri personalizzati](active-directory-b2c-overview-custom.md) sono file di configurazione che definiscono il comportamento del [framework dell'esperienza di gestione delle identità](trustframeworkpolicy.md) nel tenant di Azure AD B2C. I criteri personalizzati possono essere modificati per completare numerose attività. Un criterio personalizzato è costituito da uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. 

Criteri personalizzati di diversi tipi possono essere usati nel tenant di Azure AD B2C in base alle esigenze ed essere riutilizzati in più applicazioni. In questo modo è possibile definire e modificare le esperienze delle identità dei clienti con modifiche minime al codice o anche senza alcuna modifica al codice. I criteri vengono usati aggiungendo uno speciale parametro di query alle richieste di autenticazione HTTP.

I criteri personalizzati vengono usati per controllare i percorsi utente nei modi seguenti:

- Definendo l'interazione con le API per acquisire informazioni aggiuntive, verificare le attestazioni fornite dai clienti oppure attivare processi esterni.
- Modificando il comportamento in base alle attestazioni dalle API o alle attestazioni nella directory, ad esempio *migrationStatus*.
- Eseguendo qualsiasi flusso di lavoro non coperto dai criteri predefiniti, ad esempio la raccolta di informazioni aggiuntive da un cliente durante un'esperienza di accesso e l'esecuzione di un controllo delle autorizzazioni per l'accesso a una risorsa.

### <a name="identity-providers"></a>Provider di identità

Un provider di identità è un servizio che autentica le identità dei clienti e rilascia token di sicurezza. In Azure AD B2C, si configurano nel tenant diversi provider di identità, come ad esempio [account Microsoft](active-directory-b2c-setup-msa-app.md), [Facebook](active-directory-b2c-setup-fb-app.md) o [Amazon](active-directory-b2c-setup-amzn-app.md). 

Per configurare un provider di identità nel tenant di Azure AD B2C, è necessario registrare l'identificatore dell'applicazione o l'identificatore del client e la password o il segreto client dall'applicazione del provider di identità creato. Questo identificatore e la password vengono quindi usati per configurare l'applicazione.

### <a name="user-interface-experience"></a>Esperienza dell'interfaccia utente

È possibile controllare la maggior parte del contenuto HTML e CSS che viene presentato ai clienti. Con la funzionalità di personalizzazione dell'interfaccia utente della pagina, è possibile definire l'aspetto di qualsiasi criterio. Questa funzionalità di personalizzazione consente di mantenere la coerenza visiva e del marchio tra l'applicazione e Azure AD B2C.

Azure AD B2C esegue il codice nel browser del cliente e usa un approccio moderno denominato Condivisione di risorse tra le origini (CORS). In primo luogo, specificare un URL nel criterio con contenuto HTML personalizzato. Azure AD B2C unisce elementi dell'interfaccia utente con il contenuto HTML caricato dall'URL e quindi visualizza la pagina al cliente.

I parametri vengono inviati ad Azure AD B2C in una stringa di query. Passando il parametro all'endpoint HTML, il contenuto della pagina viene modificato in modo dinamico. L'immagine di sfondo della pagina di accesso o di iscrizione ad Azure AD B2C viene ad esempio modificata in base a un parametro passato dall'applicazione Web o per dispositivi mobili.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Come si può iniziare a usare Azure AD B2C?

In Azure AD B2C un tenant rappresenta l'organizzazione ed è una directory di utenti. Ogni tenant di Azure AD B2C è distinto e separato dagli altri tenant di Azure AD B2C. Un tenant contiene informazioni sui clienti che hanno effettuato l'iscrizione per l'uso dell'applicazione, ad esempio le password, i dati di profilo e le autorizzazioni.

Collegare il tenant di Azure AD B2C alla sottoscrizione di Azure per abilitare tutte le funzionalità e pagare gli addebiti per l'utilizzo. Per consentire ai clienti di accedere all'applicazione, registrarla in un tenant di Azure AD B2C.

Prima di configurare l'applicazione per l'uso di Azure AD B2C, è innanzitutto necessario creare un tenant di Azure AD B2C e registrare l'applicazione. Per registrare l'applicazione, completare i passaggi in [Esercitazione: Registrare un'applicazione per consentire l'iscrizione e l'accesso tramite Azure AD B2C](tutorial-register-applications.md).
  
Gli sviluppatori di applicazioni Web ASP.NET possono impostare l'applicazione per l'autenticazione degli account tramite la procedura descritta in [Esercitazione: Abilitare un'applicazione Web per l'autenticazione con gli account usando Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Gli sviluppatori di applicazioni desktop possono impostare l'applicazione per l'autenticazione degli account con la procedura descritta in [Esercitazione: Abilitare un'applicazione desktop per l'autenticazione con gli account tramite Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Gli sviluppatori di applicazioni a pagina singola che usano Node.js possono impostare l'applicazione per l'autenticazione degli account con la procedura descritta in [Esercitazione: Abilitare un'applicazione a pagina singola per l'autenticazione con gli account tramite Azure AD B2C](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>Passaggi successivi

Avviare la configurazione dell'applicazione per l'esperienza di iscrizione e accesso passando all'esercitazione.

> [!div class="nextstepaction"]
> [Esercitazione: Creare un tenant di Azure Active Directory B2C](tutorial-create-tenant.md)
