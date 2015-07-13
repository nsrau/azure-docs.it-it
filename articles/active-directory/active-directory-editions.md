<properties
	pageTitle="Edizioni di Azure Active Directory"
	description="Argomento che illustra la possibilità di scegliere tra edizioni gratuite e a pagamento di Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="TerryLan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/01/2015"
	ms.author="curtand"/>

# Edizioni di Azure Active Directory

Azure Active Directory è un servizio che offre funzionalità complete di gestione delle identità e degli accessi nel cloud. Combina servizi directory, governance avanzata delle identità, gestione degli accessi alle applicazioni e una piattaforma avanzata basata su standard per gli sviluppatori. Per altre informazioni, [vedere questo video](http://azure.microsoft.com/documentation/videos/teched-europe-2014-cloud-identity-microsoft-azure-active-directory-explained/).

Le edizioni Premium e Basic di Azure Active Directory, realizzate a partire da un'ampia gamma di funzionalità gratuite di Microsoft Azure Active Directory, forniscono un set di funzionalità più avanzate alle aziende con esigenze particolarmente complesse di gestione delle identità e degli accessi. Per le opzioni relative ai prezzi per queste edizioni, vedere [Prezzi di Azure Active Directory](http://azure.microsoft.com/pricing/details/active-directory/). Quando si effettua la sottoscrizione ad Azure, è possibile scegliere tra le seguenti edizioni gratuite e a pagamento di Azure Active Directory:

- **Gratuito**: l'edizione gratuita di Azure Active Directory fa parte di ogni sottoscrizione Azure. Non è necessaria una licenza, né componenti da installare. Con questa edizione è possibile gestire gli account utente, sincronizzare le directory locali, ottenere l'accesso Single Sign-On per Azure, Office 365 e migliaia di popolari applicazioni SaaS, ad esempio Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e altro ancora.
- **Basic**: l'edizione Basic di Azure Active Directory fornisce l'accesso alle applicazioni e i requisiti di gestione delle identità self-service per chi si occupa delle attività e ha esigenze basate prima di tutto sul cloud. L'edizione Basic di Azure Active Directory offre tutte le funzionalità di Azure Active Directory Free, oltre a gestione degli accessi basata sui gruppi, reimpostazione delle password self-service per le applicazioni cloud, proxy di applicazione di Azure Active Directory (per pubblicare applicazioni Web locali usando Azure Active Directory), ambiente personalizzabile per il lancio di applicazioni cloud aziendali e consumer e un contratto di servizio a livello aziendale che garantisce un tempo di attività del 99,9%. Un amministratore con una licenza per l'edizione Basic di Azure Active Directory può anche attivare una versione di prova di Azure Active Directory Premium.
- **Premium**: l'edizione Premium di Azure Active Directory offre tutte le funzionalità disponibili nelle edizioni Gratuito e Basic di Azure Active Directory, oltre ad altre funzionalità avanzate di gestione delle identità a livello aziendale, illustrate più avanti.

Per iscriversi e iniziare a usare subito Active Directory Premium, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]Azure Active Directory Premium e Azure Active Directory Basic non sono attualmente supportate in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).

## Funzionalità di Azure Active Directory Basic

L'edizione Basic di Active Directory è un'offerta a pagamento di Azure Active Directory e include le funzionalità seguenti:

- **Informazioni personalizzate distintive dell'azienda**: per migliorare ancora di più l'esperienza utente, è possibile aggiungere il logo e le combinazioni colori aziendali alle pagine Accedi e Pannello di accesso dell'organizzazione. Dopo avere aggiunto il logo, è possibile aggiungere anche versioni localizzate del logo per lingue e impostazioni locali diverse. Per altre informazioni, vedere [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine Accedi e Pannello di accesso](active-directory-add-company-branding.md).
- **Accesso alle applicazioni basato sui gruppi**: usare i gruppi per il provisioning degli utenti e l'assegnazione in blocco dell'accesso utente a migliaia di applicazioni SaaS. Questi gruppi possono essere creati esclusivamente nel cloud oppure è possibile sfruttare i gruppi esistenti sincronizzati da Active Directory locale. Per altre informazioni, vedere [Assegnare a un gruppo l'accesso a un'applicazione SaaS in Azure AD](https://msdn.microsoft.com/library/azure/dn621141.aspx).
- **Reimpostazione password self-service**: Azure ha sempre consentito agli amministratori delle directory reimpostare le password. Con Azure Active Directory Basic, ora è possibile ridurre il numero di chiamate al supporto tecnico se gli utenti dimenticano una password, dando a tutti gli utenti presenti nella directory la possibilità di reimpostare la password, con la stessa esperienza di accesso di Office 365. Per altre informazioni, vedere [Gestione delle password in Azure AD](https://msdn.microsoft.com/library/azure/dn510386.aspx).
- **Contratto di servizio aziendale del 99,9%**: è garantita una disponibilità minima del 99,9% del servizio Azure Active Directory Basic.
- [**Proxy di applicazione di Azure Active Directory**](https://msdn.microsoft.com/library/azure/dn768214.aspx): offre ai dipendenti l'accesso sicuro alle applicazioni locali, ad esempio SharePoint ed Exchange/OWA, dal cloud usando Azure Active Directory.

## Funzionalità di Azure Active Directory Premium

L'edizione Premium di Active Directory è un'offerta a pagamento di Azure Active Directory e include tutte le funzionalità delle edizioni Gratuito e Basic, oltre alle funzionalità seguenti:

- **Gestione self-service dei gruppi**: Azure Active Directory Premium semplifica l'amministrazione quotidiana dei gruppi consentendo agli utenti di creare i gruppi, richiedere l'accesso ad altri gruppi, delegare la proprietà dei gruppi, in modo che altri possano approvare le richieste, e gestire le appartenenze del gruppo.

    Per altre informazioni, vedere [Gestione dei gruppi in modalità self-service per gli utenti in Azure AD](https://msdn.microsoft.com/library/azure/dn641267.aspx).

- **Report e avvisi di sicurezza avanzata**: è possibile monitorare e proteggere l'accesso alle applicazioni cloud visualizzando log dettagliati che mostrano report più avanzati relativi ad anomalie e modelli di accesso non coerenti. I report avanzati sono basati su Machine Learning e consentono di ottenere nuove informazioni dettagliate per migliorare la sicurezza dall'accesso e rispondere a potenziali minacce.

    Per altre informazioni, vedere [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md).

- **Multi-Factor Authentication**: Multi-Factor Authentication ora è incluso nell'edizione Premium e consente di proteggere l'accesso alle applicazioni locali (VPN, RADIUS, ecc.), ad Azure, a Microsoft Online Services, ad esempio Office 365 e Dynamics CRM Online, e a migliaia di servizi cloud non MS pre-integrati in Azure Active Directory. È sufficiente abilitare Multi-Factor Authentication per le identità di Azure Active Directory e agli utenti verrà chiesto di configurare un'ulteriore verifica al successivo accesso.

    Per altre informazioni, vedere [Aggiunta di Multi-Factor Authentication ad Azure Active Directory](https://msdn.microsoft.com/library/azure/dn249466.aspx).

- **Microsoft Identity Manager (MIM)**: l'edizione Premium consente di concedere i diritti per usare un server MIM (e le licenze CAL) nella rete locale per supportare qualsiasi combinazione di soluzioni di identità ibride. Si tratta di un'ottima opzione se è presente una variazione nelle directory e nei database locali che si vuole sincronizzare direttamente ad Azure Active Directory. Non sono previsti limiti per il numero di server FIM che è possibile usare, ma le licenze CAL MIM vengono concesse in base all'allocazione di una licenza utente di Azure Active Directory Premium.

    Per altre informazioni, vedere [Distribuire MIM 2010 R2](https://www.microsoft.com/server-cloud/products/forefront-identity-manager/features.aspx).

- **Contratto di servizio aziendale del 99,9%**: è garantita una disponibilità minima del 99,9% del servizio Azure Active Directory Premium.

    Per altre informazioni, vedere [Contratto di servizio di Azure Active Directory Premium](http://azure.microsoft.com/support/legal/sla/).

- **Reimpostazione delle password con writeback**: è possibile eseguire il writeback della reimpostazione delle password self-service nelle directory locali.




## Confronto tra le edizioni: funzionalità comuni a tutte le edizioni

- Directory come servizio. Per l'edizione Free, esiste un limite di 500.000 oggetti. Il limite di 500.000 oggetti non si applica però a Office 365, Microsoft Intune o qualsiasi altro servizio online Microsoft basato su Azure Active Directory per i servizi directory. Per le edizioni Basic e Premium non esiste alcun limite per gli oggetti.
- Gestione di utenti e gruppi con l'interfaccia utente o i cmdlet di Windows PowerShell
- Registrazione del dispositivo
- Portale del Pannello di accesso per l'accesso utente basato su SSO (Single-Sign-On) ad applicazioni SaaS e personalizzate. Con Azure Active Directory Free e Azure Active Directory Basic, gli utenti finali, a cui è stato assegnato l'accesso a ogni app SaaS, possono visualizzare fino a 10 app nel Pannello di accesso e ottenere l'accesso Single-Sign-On a queste app (presumendo che prima siano state configurate con SSO dall'amministratore). Con l'edizione Gratuito gli amministratori possono configurare SSO e assegnare l'accesso utente a tutte le app SaaS desiderate, ma gli utenti finali vedranno solo 10 app alla volta nel Pannello di accesso. Per Azure Active Directory Premium non è previsto alcun limite per le applicazioni.
- Gestione e provisioning degli accessi alle applicazioni basati sugli utenti
- Modifica delle password self-service per gli utenti cloud
- Strumento di sincronizzazione directory: per la sincronizzazione tra Active Directory locale e Azure Active Directory
- Report sulla sicurezza standard

## Confronto tra le edizioni: funzionalità Premium e Basic

- Tempo di attività previsto dal contratto di servizio con disponibilità elevata (99,9%)
- Gestione e provisioning degli accessi alle applicazioni basati sui gruppi
- Personalizzazione del logo e dei colori aziendali per le pagine Accedi e Pannello di accesso
- Reimpostazione delle password self-service per gli utenti cloud
- Proxy dell'applicazione: accesso remoto sicuro e Single-Sign-On ad applicazioni Web locali

## Confronto tra le edizioni: funzionalità solo Premium

- Gestione di gruppi self-service per gli utenti cloud
- Reimpostazione delle password self-service con writeback locale
- Licenze server Microsoft Identity Manager (MIM): per la sincronizzazione tra database e/o directory locali e Azure Active Directory
- Report avanzati sulla sicurezza dalle anomalie (basati su Machine Learning)
- [Cloud app discovery](http://channel9.msdn.com/Series/EMS/Azure-Cloud-App-Discovery)
- Creazione di report avanzati sull'utilizzo di applicazioni
- Servizio Multi-Factor Authentication per gli utenti cloud
- Server Multi-Factor Authentication per gli utenti locali

## Funzionalità attualmente disponibili in anteprima pubblica

Le funzionalità seguenti sono attualmente disponibili in anteprima pubblica e verranno aggiunte presto:

- [Unità amministrative](https://msdn.microsoft.com/library/azure/dn832057.aspx): nuovo contenitore di risorse di Azure Active Directory che può essere usato per la delega di autorizzazioni amministrative su subset di utenti e per l'applicazione di criteri a un subset di utenti.
- [Aggiungere applicazioni SaaS](https://msdn.microsoft.com/library/azure/dn893637.aspx) ad Azure Active Directory.
- [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx): è possibile monitorare l'integrità dell'infrastruttura Active Directory locale e ottenere analisi di utilizzo.
- Rollover della password per Facebook, Twitter e LinkedIn. Per altre informazioni, leggere [questo articolo](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx).
- Appartenenza a gruppi dinamici. Per altre informazioni, vedere [questo articolo](https://msdn.microsoft.com/library/azure/dn913807.aspx).
- [Accesso condizionale](https://msdn.microsoft.com/library/azure/dn906877.aspx): Multi-Factor Authentication per ogni applicazione.
- Integrazione applicazioni HR: giornata lavorativa
- Gestione identità con privilegi: offre una migliore supervisione per soddisfare i requisiti di conformità alle normative e dei contratti di servizio.
- Richieste di applicazioni self-service: gli amministratori possono fornire agli utenti un elenco di app SaaS, dal quale è possibile scegliere le app da usare, che saranno disponibili immediatamente o dopo l'approvazione.
- API di creazione report di Azure: i dati per ogni report di sicurezza di Azure Active Directory saranno disponibili per altri strumenti di monitoraggio o SIEM.


## Passaggi successivi

- [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine Accedi e Pannello di accesso](active-directory-add-company-branding.md)
- [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)
 

<!---HONumber=62-->