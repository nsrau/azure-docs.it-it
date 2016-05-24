<properties
	pageTitle="Domande frequenti su Azure Active Directory | Microsoft Azure"
	description="Domande frequenti su Azure Active Directory"
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
	ms.date="04/20/2016"
	ms.author="markusvi"/>

# Domande frequenti su Azure Active Directory

Azure Active Directory (Azure AD) è un insieme completo e basato sul cloud di funzionalità di gestione delle identità e degli accessi, che consentono di usare l'identità come servizio (IDaaS, Identity as a Service). In quanto provider di identità come servizio, Azure AD consente di determinare "*chi è autorizzato a eseguire quale operazione*" nella rete di computer. Azure AD fornisce anche l'accesso Single Sign-On (SSO) per le applicazioni. L'accesso Single Sign-On consente di migliorare in modo significativo l'esperienza di accesso degli utenti. Per informazioni dettagliate, vedere [Informazioni su Azure Active Directory](active-directory-whatis.md).



## Accesso ad Azure e Azure Active Directory

****D: Per quale motivo non si riesce a trovare Azure AD nel portale di Azure (http://portal.azure.com)?**

**R:** L'esperienza di amministrazione di Azure AD è ancora disponibile nel portale di Azure classico (`http://manage.windowsazure.com`). Il rilascio di un'anteprima pubblica della nuova esperienza di amministrazione integrata nel portale di Azure è pianificato per il mese di settembre. Per informazioni più aggiornate sui rilasci, vedere il [blog del team di Active Directory](https://blogs.technet.microsoft.com/ad/).


---

****D: Perché viene visualizzato il messaggio "Non sono state trovate sottoscrizioni" quando si prova ad accedere ad Azure AD nel portale di Azure classico (http://manage.windowsazure.com)?**

**R:** Saranno necessarie autorizzazioni per una sottoscrizione di Azure. Se si ha una sottoscrizione di Office 365 o Azure AD a pagamento, passare a [http://aka.ms/accessAAD](http://aka.ms/accessAAD) per un passaggio di attivazione da eseguire una sola volta. In caso contrario, sarà necessario attivare una [versione di valutazione di Azure](https://azure.microsoft.com/pricing/free-trial/) completa o una sottoscrizione a pagamento.

---

**D: Qual è la relazione tra Azure AD, Office 365 e Azure?**

**R:** Microsoft Azure è una piattaforma di cloud computing di livello aziendale, flessibile e aperta e un'infrastruttura per la creazione, la distribuzione e la gestione di applicazioni e servizi tramite una rete locale di data center gestiti da Microsoft. Azure offre, ad esempio, soluzioni SaaS (Software as a Service) come Office 365. Office 365 è un gruppo di sottoscrizioni di tipo software e servizi che offre software per la produttività e servizi online correlati. Azure AD si integra con Office 365 offrendo un insieme completo e basato sul cloud di funzionalità di gestione delle identità e degli accessi, che consentono di usare l'identità come servizio (IDaaS, Identity as a Service). Se si ha una sottoscrizione di Office 365, è già disponibile anche Azure AD in esecuzione di Azure.


---

**D: Quali funzionalità di Azure AD sono disponibili gratuitamente?**

**R:** Tutte le funzionalità comuni in una sottoscrizione di Azure sono disponibili gratuitamente. Per un elenco completo, vedere [Funzionalità comuni](active-directory-editions.md/#common-features).



---

D: **Come si può connettere la directory locale ad Azure AD?**

R: È possibile connettere la directory locale ad Azure AD usando **Azure AD Connect**. Per informazioni dettagliate, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).


---

**D: Come si configura l'accesso Single Sign-On tra la directory locale e le applicazioni cloud?**

**R:** È necessario solo configurare l'accesso Single Sign-On tra la directory locale e Azure AD. Se le applicazioni cloud sono gestite da Azure AD, è possibile estendere la portata dell'accesso Single Sign-On all'ambiente locale implementando la federazione con AD FS o la sincronizzazione password. Entrambe le opzioni sono incluse in **Azure AD Connect**. Per informazioni dettagliate, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
  


---

**D: Azure AD semplifica la gestione dell'infrastruttura locale?**

**R:** Sì. Azure AD Premium Edition include **Connect Health**. Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale e sui servizi di sincronizzazione. Per informazioni dettagliate, vedere [Monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](active-directory-aadconnect-health.md).



## Gestione delle password

**D: È possibile usare il writeback della password di Azure AD senza la sincronizzazione password, se ad esempio si vuole usare la reimpostazione password self-service di Azure AD con il writeback delle password senza archiviare le password sul cloud?**

**R:** Non è necessario sincronizzare le password di AD con Azure AD per consentire il writeback. In un ambiente federato, l'accesso Single Sign-On di Azure AD si basa sulla directory locale per autenticare l'utente. Questo scenario non richiede la verifica della password locale in Azure AD.

---

**D: Quanto tempo è necessario per il writeback di una password nell'istanza locale di AD?**

**R:** Il writeback delle password viene eseguito in tempo reale. Per informazioni dettagliate, vedere [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md).


---

**D: È possibile usare il writeback delle password con password gestite da un amministratore?**

**R:** Sì, se il writeback delle password è abilitato, le operazioni relative alle password eseguite da un amministratore vengono sottoposte a writeback nell'ambiente locale. Per altre risposte a domande correlate alle password, vedere [Domande frequenti sulla gestione delle password](active-directory-passwords-faq.md).



## Accesso all'applicazione


**D: Dove si può trovare un elenco di applicazioni preintegrate con Azure AD e delle rispettive funzionalità?**

**R:** Azure AD include più di 2600 applicazioni preintegrate da Microsoft, da application service provider o da partner. Per un elenco completo delle applicazioni preintegrate, vedere il [Marketplace di Active Directory](https://azure.microsoft.com/marketplace/active-directory/).


---

**D: Che cosa accade se l'applicazione cercata non è disponibile nel Marketplace di Azure AD?**

**R:** Azure AD Premium consente di aggiungere e configurare qualsiasi applicazione. In base alle funzionalità dell'applicazione e alle preferenze dell'utente, è anche possibile configurare l'accesso Single Sign-On e il provisioning automatico. Per informazioni dettagliate, vedere:

- [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](active-directory-saas-custom-apps.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md) 



---

**D: È possibile aggiungere applicazioni in esecuzione in locale?**

**R:** Il proxy di applicazione di Azure AD consente di accedere con facilità alle applicazioni Web locali in modo analogo alle app SaaS in Azure Active Directory, senza che sia necessaria una rete VPN o la modifica dell'infrastruttura di rete. Per informazioni dettagliate, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).


--- 

**D: Come si richiede l'autenticazione MFA per gli utenti che accedono a un'applicazione specifica?**

**R:** L'accesso condizionale di Azure AD consente di assegnare un criterio di accesso univoco a ogni applicazione. Nel criterio è possibile richiedere sempre l'autenticazione MFA o solo quando gli utenti non sono connessi alla rete locale. Per informazioni dettagliate, vedere [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](active-directory-conditional-access.md).

<!---HONumber=AcomDC_0518_2016-->