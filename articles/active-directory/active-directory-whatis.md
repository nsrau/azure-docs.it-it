<properties 
                pageTitle="Informazioni su Azure Active Directory" 
                description="Usare Azure Active Directory per estendere le identità locali esistenti nel cloud o sviluppare le applicazioni integrate di Azure AD." 
                services="active-directory" 
                documentationCenter="" 
                authors="markusvi" 
                manager="swadhwa" 
                editor=""/>

<tags 
                ms.service="active-directory" 
                ms.workload="identity" 
                ms.tgt_pltfrm="na" 
                ms.devlang="na" 
                ms.topic="hero-article" 
                ms.date="07/14/2015" 
                ms.author="markusvi"/>


# Informazioni su Azure Active Directory





Azure Active Directory (Azure AD) è il servizio Microsoft di gestione di identità e directory basato sul cloud e multi-tenant.

Azure AD offre agli amministratori IT una soluzione conveniente e facile da usare per fornire a dipendenti e partner commerciali l'accesso Single Sign-On (SSO) a [migliaia di applicazioni SaaS cloud](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx), quali Office365, Salesforce.com, DropBox e Concur.

Azure AD permette agli sviluppatori di applicazioni di concentrarsi sulla creazione dell'applicazione, semplificando e velocizzando l'integrazione con una soluzione di qualità elevata per la gestione delle identità, usata da milioni di organizzazioni in tutto il mondo.

Azure AD include anche un insieme completo di funzionalità per la gestione delle identità, tra cui l'autenticazione a più fattori, la registrazione dei dispositivi, la gestione self-service delle password e dei gruppi, la gestione degli account con privilegi, il controllo degli accessi in base al ruolo, il monitoraggio dell'utilizzo dell'applicazione, il controllo avanzato e il monitoraggio e avvisi relativi alla sicurezza. Queste funzionalità possono contribuire alla protezione di applicazioni basate sul cloud, alla semplificazione dei processi IT, alla riduzione dei costi e alla soddisfazione degli obiettivi aziendali relativi alla conformità.

Con soli [quattro clic](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx), inoltre, è possibile integrare Azure AD con un'istanza esistente di Windows Server Active Directory, permettendo alle organizzazione di sfruttare gli investimenti locali esistenti a livello di gestione delle identità per gestire l'accesso alle applicazioni SaaS basate sul cloud.

Se si è clienti di Office365, Azure o Dynamics CRM Online, è possibile che si stia già usando Azure AD, senza esserne consapevoli. Ogni tenant di Office365, Azure e Dynamics CRM è in effetti già un tenant di Azure AD. Quando lo si desidera, è possibile iniziare a usare tale tenant per gestire l'accesso a migliaia di altre applicazioni cloud integrate con Azure AD.





<center>![Stack di Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png) </center>


## Affidabilità di Azure AD

La progettazione multi-tenant, geograficamente distribuita e a disponibilità elevata di Azure AD offre l'affidabilità appropriata per le esigenze aziendali più importanti. Grazie all'esecuzione in 28 data center in tutto il mondo e al failover automatizzato, Azure AD offre la certezza di un'affidabilità elevata. Anche in caso di problemi in un data center, le copie dei dati delle directory saranno sempre disponibili in almeno due data center dislocati in aree geografiche diverse, consentendo l'accesso immediato.



## Vantaggi di Azure AD

L'organizzazione può usare Azure AD per migliorare la produttività dei dipendenti, semplificare i processi IT, migliorare la sicurezza e ridurre i costi in molti modi:

-	Adozione rapida dei servizi cloud, per offrire a dipendenti e partner un'esperienza di accesso Single-Sign-On semplificata basata sulle capacità completamente automatizzate di Azure AD per la gestione dell'accesso alle app SaaS e sulle capacità dei servizi di provisioning.
-	Possibilità di offrire ai dipendenti l'accesso ad app e servizi cloud di qualità elevata e le capacità self-service ovunque si trovino e con qualsiasi dispositivo.
-	Gestione semplice e sicura dell'accesso di dipendenti e fornitori agli account aziendali per social media. 
-	Miglioramento della sicurezza delle applicazioni grazie all'autenticazione a più fattori e all'accesso condizionale di Azure AD.
-	Implementazione di una gestione coerente e self-service degli accessi alle applicazioni, in modo da permettere ai titolari di aziende di mantenere la rapidità di azione, riducendo al tempo stesso costi IT e costi generali.
-	Monitoraggio dell'utilizzo delle applicazioni e protezione dell'attività da minacce avanzate, grazie alla creazione di report e al monitoraggio della sicurezza.
-	Accesso mobile (remoto) sicuro alle applicazioni locali.






## Operazioni iniziali
-	Amministratori IT:
 - [Versione di valutazione](https://azure.microsoft.com/it-it/pricing/free-trial/): è possibile ottenere subito gratuitamente una versione di valutazione di 30 giorni e distribuire la prima soluzione cloud in meno di cinque minuti, usando il collegamento seguente.
 - Per suggerimenti relativi a come attivare ed eseguire rapidamente un tenant di Azure AD, vedere "Introduzione ad Azure AD".
-	Sviluppatori:
 - Vedere la [Guida per gli sviluppatori](https://msdn.microsoft.com/library/azure/ff800682.aspx) per Azure Active Directory <need link>.
 - [Iniziare a usare una versione di valutazione](https://azure.microsoft.com/it-it/pricing/free-trial/): è possibile ottenere subito gratuitamente una versione di valutazione di 30 giorni e iniziare a integrare la propria app con Azure AD. 



## Altre informazioni

Sono disponibili moltissime risorse utili online, che forniscono tutte le informazioni necessarie su Azure AD. Gli articoli seguenti forniscono informazioni introduttive:


- [Abilitazione della directory per la gestione ibrida con Azure AD Connect](active-directory-aadconnect.md)
- [Sicurezza aggiuntiva per un mondo sempre connesso](multi-factor-authentication.md)
- [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
- [Distribuzione di Gestione delle password e formazione degli utenti per l’uso](active-directory-passwords-best-practices.md)
- [Accesso Single-Sign-On e gestione degli accessi per migliaia di applicazioni SaaS su qualsiasi cloud](https://msdn.microsoft.com/library/azure/dn308590.aspx) 
- [Accesso remoto sicuro alle applicazioni locali](https://msdn.microsoft.com/library/azure/dn768219.aspx)
- [Gestione self-service degli accessi](https://msdn.microsoft.com/library/azure/dn641267.aspx) 
- [Individuazione di app cloud](https://msdn.microsoft.com/library/azure/mt143581.aspx)
- [Controllo degli accessi in base all'integrità del dispositivo, all'ubicazione dell'utente e all'identità](https://msdn.microsoft.com/library/azure/dn906873.aspx)
- [Usare il cloud per migliorare e monitorare i sistemi di identità locali](https://msdn.microsoft.com/library/azure/dn906722.aspx)
- [Piattaforma basata su standard avanzati per gli sviluppatori](https://msdn.microsoft.com/library/azure/ff800682.aspx)

<!---HONumber=July15_HO4-->