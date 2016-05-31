<properties
	pageTitle="Domande frequenti su Azure Active Directory | Microsoft Azure"
	description="Domande frequenti su Azure Active Directory, che forniscono risposte a domande relative all'accesso di Azure e Azure Active Directory, alla gestione delle password e all'accesso alle applicazioni."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/20/2016"
	ms.author="markusvi"/>

# Domande frequenti su Azure Active Directory

Azure Active Directory è una soluzione IDaaS (Identity as a Service) completa che si estende a tutti gli aspetti relativi a identità, gestione dell'accesso e sicurezza.


Per altri dettagli, vedere [Informazioni su Azure Active Directory](active-directory-whatis.md).



## Accesso ad Azure e Azure Active Directory


****D: Perché viene visualizzato il messaggio "Non sono state trovate sottoscrizioni" quando si prova ad accedere ad Azure AD nel portale di Azure classico (https://manage.windowsazure.com)?**

**A:** Per accedere al portale di Azure classico è necessario che ogni utente abbia le autorizzazioni per una sottoscrizione di Azure. Se si ha una sottoscrizione di Office 365 o Azure AD a pagamento, passare a [http://aka.ms/accessAAD](http://aka.ms/accessAAD) per un passaggio di attivazione da eseguire una sola volta. In caso contrario, sarà necessario attivare una [versione di valutazione di Azure](https://azure.microsoft.com/pricing/free-trial/) completa o una sottoscrizione a pagamento.

Per informazioni dettagliate, vedere:

- [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Gestire la directory per la sottoscrizione di Office 365 in Azure](active-directory-manage-o365-subscription.md)

---

**D: Qual è la relazione tra Azure AD, Office 365 e Azure?**

**D:** Azure Active Directory fornisce funzionalità comuni per la gestione di identità e accesso a tutti i Microsoft Online Services. Se si usa Office 365, Microsoft Azure, Intune o altri servizi, si sta già usando un'istanza di Azure AD per abilitare l'accesso e la gestione dell'accesso per tutti questi servizi.

Tutti gli utenti abilitati per Microsoft Online Services sono in effetti definiti come account utente in una o più istanze di Azure AD. È possibile abilitare questi account per funzionalità di Azure AD gratuite come l'accesso alle applicazioni cloud.
 
I servizi a pagamento di Azure AD, ad esempio Azure AD Basic, Premium, EMS e così via, sono inoltre complementari ad altri servizi online, come Office 365 e Microsoft Azure, con soluzioni di gestione e di sicurezza complete di scala aziendale.


---



## Introduzione alla distribuzione ibrida di Azure AD


**D: Come si può connettere la directory locale ad Azure AD?**

**R:** È possibile connettere la directory locale ad Azure AD usando **Azure AD Connect**.

Per informazioni dettagliate, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).


---

**D: Come si configura l'accesso Single Sign-On tra la directory locale e le applicazioni cloud?**

**R:** È necessario solo configurare l'accesso Single Sign-On tra la directory locale e Azure AD. Se si accede alle applicazioni cloud tramite Azure AD, il servizio richiederà automaticamente agli utenti di eseguire l'autenticazione corretta con le credenziali locali.

L'implementazione dell'accesso Single Sign-On dall'ambiente locale può essere ottenuta facilmente mediante soluzioni di federazione come AD FS o mediante la configurazione della sincronizzazione degli hash delle password. È possibile distribuire con facilità entrambe le opzioni mediante la configurazione guidata di Azure AD Connect.
  

Per altre informazioni, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
  

---

**D: Azure Active Directory offre un portale self-service per gli utenti dell'organizzazione?**

**D:** Sì, Azure Active Directory offre il [riquadro di accesso di Azure AD](http://myapps.microsoft.com) per l'accesso self-service di utenti e applicazioni. I clienti di Office 365 possono trovare molte funzionalità analoghe nel portale di Office 365.

Per altre informazioni, vedere [Introduzione al riquadro di accesso](active-directory-saas-access-panel-introduction.md).



---

**D: Azure AD semplifica la gestione dell'infrastruttura locale?**

**R:** Sì. Azure AD Premium Edition include **Connect Health**. Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale e sui servizi di sincronizzazione.

Per informazioni dettagliate, vedere [Monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](active-directory-aadconnect-health.md).

---

## Gestione delle password

**D: È possibile usare il writeback delle password di Azure AD senza la sincronizzazione password, se ad esempio si vuole usare la reimpostazione password self-service di Azure AD con il writeback delle password senza archiviare le password sul cloud?**

**R:** Non è necessario sincronizzare le password di AD con Azure AD per consentire il writeback. In un ambiente federato, l'accesso Single Sign-On di Azure AD si basa sulla directory locale per autenticare l'utente. Questo scenario non richiede la verifica della password locale in Azure AD.

---

**D: Quanto tempo è necessario per il writeback di una password nell'istanza locale di AD?**

**R:** Il writeback delle password viene eseguito in tempo reale.

Per informazioni dettagliate, vedere [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md).


---

**D: È possibile usare il writeback delle password con password gestite da un amministratore?**

**R:** Sì, se il writeback delle password è abilitato, le operazioni relative alle password eseguite da un amministratore vengono sottoposte a writeback nell'ambiente locale.

Per altre risposte a domande correlate alle password, vedere [Domande frequenti sulla gestione delle password](active-directory-passwords-faq.md).

---

## Accesso all'applicazione


**D: Dove si può trovare un elenco di applicazioni preintegrate con Azure AD e delle rispettive funzionalità?**

**R:** Azure AD include più di 2600 applicazioni preintegrate da Microsoft, da application service provider o da partner. Tutte le applicazioni preintegrate supportano l'accesso Single Sign-On, che consente di usare le credenziali aziendali per accedere alle app. Alcune applicazioni supportano anche il provisioning e il deprovisioning automatici.

Per un elenco completo delle applicazioni preintegrate, vedere il [Marketplace di Active Directory](https://azure.microsoft.com/marketplace/active-directory/).


---

**D: Che cosa accade se l'applicazione cercata non è disponibile nel Marketplace di Azure AD?**

**R:** Azure AD Premium consente di aggiungere e configurare qualsiasi applicazione. In base alle funzionalità dell'applicazione e alle preferenze dell'utente, è possibile configurare l'accesso Single Sign-On e il provisioning automatico.

Per informazioni dettagliate, vedere:

- [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](active-directory-saas-custom-apps.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md) 


---

**D: In che modo gli utenti possono accedere alle applicazioni mediante Azure Active Directory?**
 
**R:** Azure Active Directory consente agli utenti di visualizzare e accedere alle applicazioni in molti modi, ad esempio:

- Riquadro di accesso di Azure AD

- Applicazione di avvio di Office 365

- Accesso diretto alle applicazioni federate

- Collegamenti diretti per applicazioni federate, basate su password o esistenti

Per altre informazioni, vedere [Distribuzione di applicazioni integrate di Azure AD agli utenti](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**D: Quali sono le diverse modalità usate da Azure Active Directory per abilitare l'autenticazione e l'accesso Single Sign-On alle applicazioni?**
 
**R:** Azure Active Directory supporta molti protocolli standardizzati per l'autenticazione e l'autorizzazione, ad esempio SAML 2.0, OpenID Connect, OAuth 2.0 e WS-Federation. Azure AD supporta anche funzionalità relative all'insieme di credenziali delle password e all'accesso automatico per le app che supportano solo l'autenticazione basata su form.

Per altre informazioni, vedere:

- [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md)

- [Protocolli di autenticazione di Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Come funziona Single Sign-On con Azure Active Directory (PHP)?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**D: È possibile aggiungere applicazioni in esecuzione in locale?**

**R:** Il proxy dell'applicazione Azure AD offre un accesso semplice e sicuro alle applicazioni Web locali scelte. È possibile accedere a queste applicazioni in modo analogo all'accesso alle app SaaS in Azure Active Directory. Non è necessario avere una VPN o modificare l'infrastruttura di rete.

Per informazioni dettagliate, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).


--- 

**D: Come si richiede l'autenticazione MFA per gli utenti che accedono a un'applicazione specifica?**

**R:** L'accesso condizionale di Azure AD consente di assegnare un criterio di accesso univoco per ogni applicazione. Nel criterio è possibile richiedere sempre l'autenticazione MFA o solo quando gli utenti non sono connessi alla rete locale.

Per informazioni dettagliate, vedere [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](active-directory-conditional-access.md).


---

**D: Che cos'è il provisioning utenti automatizzato per le app SaaS?**

**R:** Azure Active Directory consente di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in molte applicazioni cloud popolari (SaaS).

Per altre informazioni, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

---

<!---HONumber=AcomDC_0525_2016-->