<properties
   pageTitle="Guida introduttiva all'integrazione di Azure Active Directory con le applicazioni | Microsoft Azure"
   description="Questo articolo è una guida introduttiva per l'integrazione di Azure Active Directory (AD) con applicazioni locali e applicazioni cloud."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/16/2015"
      ms.author="inhenk"/>

# Guida introduttiva all'integrazione di Azure Active Directory con le applicazioni
## Panoramica
Questo argomento intende fornire una guida di orientamento per l'integrazione di applicazioni con Azure Active Directory (AD). Ogni sezione seguente contiene un breve riepilogo di un argomento più dettagliato, per consentire di identificare le parti di questa guida introduttiva rilevanti per la situazione specifica. Per approfondimenti su ogni argomento, selezionare i collegamenti.

## Considerazioni preliminari
Prima di iniziare a integrare applicazioni con Azure AD, è importante avere un quadro preciso della situazione attuale e degli obiettivi desiderati. Le domande seguenti sono utili per valutare i diversi aspetti del progetto di integrazione delle applicazioni con Azure AD.

### Inventario delle applicazioni
- Dove si trovano tutte le applicazioni? Quali sono i proprietari?
- Che tipo di autenticazione richiedono le applicazioni?
- Chi deve accedere a quali applicazioni?
- Si vuole distribuire una nuova applicazione?
  - L'applicazione verrà compilata internamente e distribuita in un'istanza di calcolo di Azure?
  - Si userà un'applicazione disponibile nella raccolta di applicazioni di Azure?

### Inventario di utenti e gruppi
- Dove si trovano gli account utente?
 - Active Directory locale
 - Azure AD
 - In un database dell'applicazione separato di proprietà dell'utente
 - In applicazioni non approvate
 - Tutte le precedenti
- Quali autorizzazioni e assegnazioni di ruoli hanno attualmente i singoli utenti? È necessario verificarne l'accesso o si è sicuri che gli accessi utente e le assegnazioni dei ruoli siano appropriati per la situazione attuale?
- Sono già stati definiti gruppi in Active Directory locale?
 - In che modo sono organizzati i gruppi?
 - Chi sono i membri dei gruppi?
 - Quali autorizzazioni/assegnazioni di ruolo hanno attualmente i gruppi?
- Sarà necessario pulire i database di utenti/gruppi prima dell'integrazione? Questa è una domanda molto importante. La qualità del risultato dipende dalla qualità dei dati di partenza.

### Inventario della gestione degli accessi
- In che modo si gestisce attualmente l'accesso utente alle applicazioni? È necessario modificarlo? Sono stati valutati altri modi per gestire l'accesso, ad esempio [RBAC](role-based-access-control-configure.md)?
- Chi deve accedere a cosa?

È possibile che non siano immediatamente disponibili le risposte a tutte queste domande. Questa guida è utile per rispondere ad alcune domande e prendere decisioni informate.

## Prerequisiti
- Una sottoscrizione di Azure e una directory di Azure Active Directory. Se non si ha ancora una sottoscrizione di Azure, è possibile ottenere una versione di prova gratuita di Azure per 30 giorni. [provarlo,](https://azure.microsoft.com/trial/get-started-active-directory/)

## Integrazione delle applicazioni con Azure AD
### Ricerca di applicazioni cloud non autorizzate con Cloud App Discovery
Come indicato in precedenza, è possibile che alcune applicazioni non siano state gestite dall'organizzazione fino a oggi. Come parte del processo di inventario, è possibile individuare applicazioni cloud non approvate. Vedere [Ricerca di applicazioni cloud non autorizzate con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

### Tipi di autenticazione
È possibile che ogni applicazione abbia requisiti di autenticazione diversi. Con Azure AD la firma dei certificati può essere usata con applicazioni che usano i protocolli di connessione SAML 2.0, WS-Federation oppure OpenID, oltre a Password Single Sign-On. Per altre informazioni sui tipi di autenticazione per le applicazioni che possono essere usati con Azure AD, vedere [Gestione di certificati per Single Sign-On federato in Azure Active Directory](active-directory-sso-certs.md) e [Accesso Single Sign-On basato su password](active-directory-appssoaccess-whatis.md).

### Abilitazione di SSO con il proxy dell'app di Azure AD
Con il proxy di applicazione di Microsoft Azure AD è possibile garantire l'accesso ad applicazioni che si trovano all'interno della rete privata in modo sicuro da qualsiasi posizione e su qualsiasi dispositivo. Dopo aver installato un connettore del proxy di applicazione all'interno dell'ambiente, è possibile configurarlo facilmente con Azure AD. Vedere [Abilitazione dell'accesso ibrido con il proxy dell'app di Azure AD](active-directory-appssoaccess-enable-hybrid-access.md) e [Pubblicazione di nuove applicazioni con il proxy dell'app di Azure AD](active-directory-application-proxy-configure.md).

### Integrazione di applicazioni con Azure AD
Gli articoli seguenti illustrano i diversi modi in cui le applicazioni si integrano con Azure AD e forniscono alcune indicazioni.

- [Scelta del tipo di Active Directory da usare](active-directory-administer.md)
- [Integrazione con le applicazioni esistenti](active-directory-sso-integrate-existing-apps.md)
- [Pubblicazione di nuove applicazioni con il proxy dell'app di Azure AD](active-directory-application-proxy-configure.md)
- [Uso delle applicazioni della raccolta di applicazioni di Azure](active-directory-appssoaccess-whatis.md/#get-started-with-the-azure-ad-application-gallery.md)
- [Elenco delle esercitazioni sull'integrazione di applicazioni SaaS](active-directory-saas-tutorial-list.md)

## Gestione dell'accesso alle applicazioni
Gli articoli seguenti illustrano i modi in cui è possibile gestire l'accesso alle applicazioni dopo l'integrazione con Azure AD mediante i connettori di Azure AD e Azure AD nel portale di Azure.

- [Gestione dell'accesso alle app tramite Azure AD](active-directory-managing-access-to-apps.md)
- [Automazione con i connettori di Azure AD](active-directory-saas-app-provisioning.md)
- [Assegnazione di utenti a un'applicazione](active-directory-applications-guiding-developers-assigning-users.md) con il portale di Azure.
- [Assegnazione di gruppi a un'applicazione](active-directory-applications-guiding-developers-assigning-groups.md) con il portale di Azure.
- [Condivisione di account](active-directory-sharing-accounts.md)

## Integrazione di applicazioni personalizzate
Se si sviluppa una nuova applicazione e si vuole aiutare gli sviluppatori a sfruttare i vantaggi di Azure AD, vedere [Guida per gli sviluppatori](active-directory-applications-guiding-developers-for-lob-applications.md).

Per aggiungere un'applicazione personalizzata alla raccolta di applicazioni di Azure, vedere il post di blog su ["Bring your own app" con la configurazione SAML Self-Service di Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

<!---HONumber=Oct15_HO4-->