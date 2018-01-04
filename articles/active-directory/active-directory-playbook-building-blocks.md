---
title: Blocchi predefiniti del playbook dei modelli di verifica di Azure Active Directory | Microsoft Docs
description: "Esplorare e implementare rapidamente gli scenari di Gestione delle identità e degli accessi"
services: active-directory
keywords: azure active directory, studio, modello di verifica, PoC
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: bc0bc80b45e97efc048d9a9c26b8dd5d5f39ce8d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Playbook dei modelli di verifica di Azure Active Directory: blocchi predefiniti

## <a name="catalog-of-roles"></a>Catalogo dei ruoli

| Ruolo | DESCRIZIONE | Responsabilità del modello di verifica |
| --- | --- | --- |
| **Team di sviluppo e architettura identità** | Questo è in genere quello che progetta la soluzione, implementa i prototipi, indirizza le approvazioni e gestisce le consegne al team operativo | Disponibilità degli ambienti e valutazione dei diversi scenari dal punto di vista della gestibilità |
| **Team di gestione dell'identità locale** | Gestisce le diverse origini di identità locali: foreste di Active Directory, directory LDAP, sistemi HR e provider di identità federativa. | Disponibilità dell'accesso alle risorse locali necessarie per gli scenari PoC.<br/>Deve essere coinvolto nella misura minore possibile|
| **Proprietari tecnici delle applicazioni** | Proprietari tecnici dei diversi servizi e applicazioni cloud che verranno integrati con Azure AD | Disponibilità di informazioni dettagliate sulle applicazioni SaaS (potenzialmente istanze per il test) |
| **Amministrazione globale di Azure AD** | Gestisce la configurazione di Azure AD | Disponibilità delle credenziali per la configurazione del servizio di sincronizzazione. Di solito è lo stesso team che si occupa dell'architettura identità durante la verifica ma è un team separato durante la fase operativa|
| **Team responsabile del database** | Proprietario dell'infrastruttura di database | Disponibilità dell'accesso all'ambiente SQL (AD FS o Azure AD Connect) per operazioni di preparazione specifiche dello scenario.<br/>Deve essere coinvolto nella misura minore possibile |
| **Team responsabile della rete** | Proprietario dell'infrastruttura di rete | Disponibilità dell'accesso richiesto a livello di rete per i server di sincronizzazione per accedere correttamente alle origini dati e ai servizi cloud (regole del firewall, porte aperte, regole IPSec e così via). |
| **Team responsabile della sicurezza** | Definisce la strategia di sicurezza, analizza i report di sicurezza provenienti da diverse origini e adotta misure in base ai risultati. | Disponibilità di scenari di valutazione della sicurezza della destinazione |

## <a name="common-prerequisites-for-all-building-blocks"></a>Prerequisiti comuni per tutti i blocchi predefiniti

Di seguito sono riportati alcuni prerequisiti necessari per qualsiasi modello di verifica usato con Azure AD Premium.

| Prerequisito. | Risorse |
| --- | --- |
| Tenant di Azure AD definito con una sottoscrizione di Azure valida | [Come ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md)<br/>**Nota:** se si ha già un ambiente con licenze di Azure AD Premium, accedere a https://aka.ms/accessaad per ottenere una sottoscrizione gratuita <br/>Per altre informazioni, vedere: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ e https://technet.microsoft.com/library/dn832618.aspx |
| Domini definiti e verificati | [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Nota:** alcuni carichi di lavoro, ad esempio Power BI, possono avere eseguito il provisioning di un tenant di azure AD dietro le quinte. Per verificare se un determinato dominio è associato a un tenant, passare a https://login.microsoftonline.com/{dominio}/v2.0/.well-known/openid-configuration. Se si ottiene una risposta positiva, il dominio è già assegnato a un tenant e potrebbe essere necessario acquisire la proprietà. In questo caso, contattare Microsoft per le istruzioni. Altre informazioni sulle opzioni di acquisizione della proprietà sono disponibili in: [Informazioni sull'iscrizione self-service per Azure](active-directory-self-service-signup.md) |
| Versione di valutazione di Azure AD Premium o EMS abilitata | [Azure Active Directory Premium gratis per un mese](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Licenze di Azure AD Premium o EMS assegnate agli utenti dei modelli di verifica | [Concessione di licenze a se stessi e agli utenti in Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Credenziali di amministratore globale di Azure AD | [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Facoltativo ma vivamente consigliato: ambiente di laboratorio parallelo come fallback | [Prerequisiti di Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Sincronizzazione directory: sincronizzazione dell'hash delle password (PHS), nuova installazione

Tempo di completamento approssimativo: un'ora per meno di 1.000 utenti dei modelli di verifica

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Server ad Azure AD Connect | [Prerequisiti di Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |
| Definire gli utenti dei modelli di verifica, nello stesso dominio e parte di un gruppo di sicurezza, e l'unità organizzativa | [Installazione personalizzata di Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Identificate le funzionalità di Azure AD Connect necessarie per il modello di verifica | [Connettere Active Directory ad Azure Active Directory - Configurare le funzionalità di sincronizzazione](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Disponibilità delle credenziali necessarie per gli ambienti locale e cloud  | [Azure AD Connect: Account e autorizzazioni](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Scaricare la versione più recente di Azure AD Connect | [Scaricare Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Installare Azure AD Connect con il percorso più semplice: Express <br/>1. Filtrare in base all'unità organizzativa di destinazione per ridurre al minimo la durata del ciclo di sincronizzazione<br/>2. Scegliere il set di utenti di destinazione nel gruppo locale.<br/>3. Distribuire le funzionalità necessarie per gli altri temi del modello di verifica | [Azure AD Connect: Installazione personalizzata: Filtro unità organizzativa e dominio](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Installazione personalizzata: Filtro di sincronizzazione basato sui gruppi](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrazione delle identità locali con Azure Active Directory: Configurare le funzionalità di sincronizzazione](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Aprire l'interfaccia utente di AD Azure Connect e visualizzare i profili in esecuzione completati (importazione, sincronizzazione ed esportazione) | [Servizio di sincronizzazione Azure AD Connect: Utilità di pianificazione](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Aprire il [portale di gestione di Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), passare al pannello "Tutti gli utenti", aggiungere la colonna "Origine dell'autorità" e verificare che gli utenti vengano visualizzati correttamente contrassegnati come provenienti da "Windows Server AD" | [Portale di gestione di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Considerazioni

1. Esaminare le considerazioni sulla sicurezza della sincronizzazione degli hash delle password [qui](./connect/active-directory-aadconnectsync-implement-password-synchronization.md).  Se la sincronizzazione degli hash delle password per gli utenti di produzione pilota non può essere assolutamente usata, considerare le seguenti alternative:
   * Creare utenti test nel dominio di produzione. Assicurarsi di non sincronizzare altri account
   * Passare a un ambiente UAT
2.  Se si vuole procedere con la federazione, è importante capire quali sono i costi associati a una soluzione federativa con un provider di identità locale oltre il modello di verifica e valutarli in relazione ai vantaggi che si sta cercando:
    * È nel percorso critico quindi è necessaria la progettazione per un'elevata disponibilità
    * È un servizio locale per cui è necessaria la pianificazione della capacità
    * È un servizio locale che è necessario monitorare, gestire, aggiornare con patch

Altre informazioni: [Informazione sulla gestione delle identità di Office 365 e Azure Active Directory - Identità federative](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Personalizzazione

Tempo previsto per il completamento: 15 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Risorse (immagini, logo e così via). Per una migliore visualizzazione verificare che le dimensioni delle risorse siano quelle consigliate. | [Aggiungere informazioni personalizzate distintive dell'azienda nella pagina di accesso in anteprima di Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Facoltativo: se l'ambiente ha un server AD FS, accedere al server per personalizzare il tema Web | [Personalizzazione dell'accesso per utenti AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Computer client per eseguire l'esperienza di accesso dell'utente finale |  |
| Facoltativo: dispositivi mobili per convalidare l'esperienza |  |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Accedere al portale di gestione di Azure AD | [Portale di gestione di Azure AD - Informazioni personalizzate distintive dell'azienda](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Caricare le risorse per la pagina di accesso (logo alto, logo piccolo, etichette e così via). Facoltativamente, se si usa AD FS, allineare le stesse risorse con le pagine di accesso di AD FS | [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso: elementi personalizzabili](customize-branding.md) |
| Attendere un paio di minuti che la modifica diventi effettiva |  |
| Accedere con le credenziali utente del modello di verifica a https://myapps.microsoft.com |  |
| Verificare l'aspetto nel browser | [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](customize-branding.md) |
| Se necessario, verificare l'aspetto in altri dispositivi |  |

### <a name="considerations"></a>Considerazioni

Se l'aspetto precedente rimane dopo aver eseguito la personalizzazione, svuotare la cache del client del browser e ripetere l'operazione.

## <a name="group-based-licensing"></a>Licenze basate sui gruppi

Tempo previsto per il completamento: 10 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Tutti gli utenti dei moduli di verifica fanno parte di un gruppo di sicurezza (cloud o locale) | [Creare un gruppo e aggiungere membri in Azure Active Directory](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Passare al pannello delle licenze nel portale di gestione di Azure AD | [Portale di gestione di Azure AD: Licenze](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Assegnare le licenze al gruppo di sicurezza con gli utenti dei moduli di verifica. | [Assegnare licenze a un gruppo di utenti in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Considerazioni

In caso di problemi, vedere [Scenari, limitazioni e problemi noti relativi all'uso dei gruppi per gestire le licenze in Azure Active Directory](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>SaaS: configurazione SSO federato

Tempo previsto per il completamento: 60 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Ambiente di test dell'applicazione SaaS disponibile. In questa guida viene usato ServiceNow come esempio.<br/>Si consiglia di usare un'istanza di test per ridurre al minimo i possibili problemi quando si esamina la qualità e i mapping dei dati esistenti. | Accedere a https://developer.servicenow.com/app.do#!/home per avviare il processo di richiesta di un'istanza di test |
| Accesso amministrativo alla console di gestione ServiceNow | [Esercitazione: Integrazione di Azure Active Directory con ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Definire un set di utenti a cui assegnare l'applicazione. È consigliabile un gruppo di sicurezza contenente gli utenti dei moduli di verifica. <br/>Se la creazione del gruppo non è fattibile, assegnare gli utenti direttamente all'applicazione per il modello di verifica | [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Condividere l'esercitazione con tutti gli attori dalla documentazione Microsoft  | [Esercitazione: Integrazione di Azure Active Directory con ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Impostare una riunione di lavoro e seguire i passaggi dell'esercitazione con ogni attore. | [Esercitazione: Integrazione di Azure Active Directory con ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Assegnare l'app al gruppo identificato nei prerequisiti. Se il modello di verifica usa l'accesso condizionale nell'ambito, è possibile rivederlo in un secondo momento e aggiungere l'autenticazione a più fattori e altro ancora. <br/>Tenere presente che in questo modo si avvia il processo di provisioning (se configurato) |  [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Creare un gruppo e aggiungere membri in Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Usare il portale di gestione di Azure AD per aggiungere l'applicazione ServiceNow dalla raccolta| [Portale di gestione di Azure AD: applicazioni aziendali](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Novità della gestione delle applicazioni aziendali in Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| Nel pannello "Single sign-on" dell'app ServiceNow abilitare "SAML-based Sign-on" (Accesso basato su SAML) |  |
| Compilare i campi "URL di accesso" e "Identificatore" con l'URL di ServiceNow<br/>Selezionare l'opzione che consente di rendere attivo il nuovo certificato<br/>e salvare le impostazioni |  |
| Aprire il pannello "Configura ServiceNow" nella parte inferiore del riquadro per visualizzare le istruzioni per la configurazione di ServiceNow |  |
| Seguire le istruzioni per configurare ServiceNow |  |
| Nel pannello "Provisioning" dell'applicazione ServiceNow abilitare il provisioning automatico | [Gestione del provisioning degli account utente per app aziendali nel nuovo portale di Azure](active-directory-enterprise-apps-manage-provisioning.md) |
| Attendere alcuni minuti il completamento del provisioning.  Nel frattempo, è possibile verificare i report di provisioning |  |
| Accedere a https://myapps.microsoft.com/ come utente test con accesso | [Che cos'è il pannello di accesso?](active-directory-saas-access-panel-introduction.md) |
| Fare clic sul riquadro per l'applicazione appena creata. Confermare l'accesso |  |
| Facoltativamente, è possibile controllare i report sull'utilizzo dell'applicazione. Si noti che esiste una certa latenza, quindi è necessario attendere un certo tempo per vedere il traffico nei report. | [Report delle attività di accesso nel portale di Azure Active Directory: Utilizzo di applicazioni gestite](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Criteri di conservazione dei report di Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Considerazioni

1. L'[esercitazione](active-directory-saas-servicenow-tutorial.md) di cui sopra fa riferimento all'esperienza di gestione precedente di Azure AD. Ma il modulo di verifica si basa sull'esperienza di [Avvio rapido](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away).
2. Se l'applicazione di destinazione non è presente nella raccolta, è possibile usare "Bring your own app". Altre informazioni: [Novità della gestione delle applicazioni aziendali in Azure Active Directory: Aggiungere applicazioni personalizzate da un'unica posizione](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>SaaS: configurazione SSO con password

Tempo previsto per il completamento: 15 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Ambiente di test per le applicazioni SaaS. Esempi di SSO con password sono HipChat e Twitter. Per qualsiasi altra applicazione, è necessario l'URL esatto della pagina con modulo di accesso html. | [Twitter in Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat in Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testare gli account per le applicazioni. | [Iscrizione a Twitter](https://twitter.com/signup?lang=en)<br/>[Iscrizione gratuita: HipChat](https://www.hipchat.com/sign_up) |
| Definire un set di utenti a cui assegnare l'applicazione. È consigliabile un gruppo di sicurezza che contiene gli utenti. | [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Accesso come amministratore locale a un computer per distribuire l'estensione Pannello di accesso per Internet Explorer, Firefox o Chrome | [Estensione Pannello di accesso per IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Estensione Pannello di accesso per Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Estensione Pannello di accesso per Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Installare l'estensione browser | [Estensione Pannello di accesso per IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Estensione Pannello di accesso per Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Estensione Pannello di accesso per Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configurare l'applicazione dalla raccolta | [Novità della gestione delle applicazioni aziendali in Azure Active Directory: Raccolta di applicazioni con novità e miglioramenti](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configurare l'accesso SSO con password | [Gestione dell'accesso Single Sign-On per app aziendali nel nuovo portale di Azure: accesso basato su password](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Assegnare l'app al gruppo identificato nei prerequisiti | [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Accedere a https://myapps.microsoft.com/ come utente test con accesso |  |
| Fare clic sul riquadro per l'applicazione appena creata. | [Che cos'è il pannello di accesso?: Single Sign-On basato su password senza provisioning delle identità](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Indicare le credenziali dell'applicazione | [Che cos'è il pannello di accesso?: Single Sign-On basato su password senza provisioning delle identità](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Chiudere il browser e ripetere l'accesso. Questa volta l'utente ottiene l'accesso all'applicazione in modo trasparente. |  |
| Facoltativamente, è possibile controllare i report sull'utilizzo dell'applicazione. Si noti che esiste una certa latenza, quindi è necessario attendere un certo tempo per vedere il traffico nei report. | [Report delle attività di accesso nel portale di Azure Active Directory: Utilizzo di applicazioni gestite](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Criteri di conservazione dei report di Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Considerazioni

Se l'applicazione di destinazione non è presente nella raccolta, è possibile usare "Bring your own app". Altre informazioni: [Novità della gestione delle applicazioni aziendali in Azure Active Directory: Aggiungere applicazioni personalizzate da un'unica posizione](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tenere presente quanto segue:
   * L'applicazione deve avere un URL di accesso noto
   * La pagina di accesso deve contenere un modulo HTML con uno o più campi di testo che le estensioni del browser siano in grado di popolare automaticamente. Deve contenere come minimo il nome utente e la password.

## <a name="saas-shared-accounts-configuration"></a>SaaS: configurazione di account condivisi

Tempo previsto per il completamento: 30 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| L'elenco di applicazioni di destinazione e gli URL di accesso esatti in anticipo. Ad esempio, è possibile usare Twitter. | [Twitter in Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Iscrizione a Twitter](https://twitter.com/signup?lang=en) |
| Credenziali condivise per questa applicazione SaaS. | [Condivisione di account con Azure AD](active-directory-sharing-accounts.md)<br/>[Post sul rollover automatizzato delle password in Azure AD per Facebook, Twitter e LinkedIn ora in anteprima nel blog su sicurezza e mobilità aziendale] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Credenziali per almeno due membri del team che accedono allo stesso account. Devono fare parte di un gruppo di sicurezza. | [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Accesso come amministratore locale a un computer per distribuire l'estensione Pannello di accesso per Internet Explorer, Firefox o Chrome | [Estensione Pannello di accesso per IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Estensione Pannello di accesso per Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Estensione Pannello di accesso per Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Installare l'estensione browser | [Estensione Pannello di accesso per IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Estensione Pannello di accesso per Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Estensione Pannello di accesso per Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configurare l'applicazione dalla raccolta | [Novità della gestione delle applicazioni aziendali in Azure Active Directory: Raccolta di applicazioni con novità e miglioramenti](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configurare l'accesso SSO con password | [Gestione dell'accesso Single Sign-On per app aziendali nel nuovo portale di Azure: Accesso basato su password](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Assegnare l'app al gruppo identificato nei prerequisiti quando si assegnano le credenziali | [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Accedere come utenti diversi che accedono all'app come **stesso account condiviso.**  |  |
| Facoltativamente, è possibile controllare i report sull'utilizzo dell'applicazione. Si noti che esiste una certa latenza, quindi è necessario attendere un certo tempo per vedere il traffico nei report. | [Report delle attività di accesso nel portale di Azure Active Directory: Utilizzo di applicazioni gestite](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Criteri di conservazione dei report di Azure Active Directory](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Considerazioni

Se l'applicazione di destinazione non è presente nella raccolta, è possibile usare "Bring your own app". Altre informazioni: [Novità della gestione delle applicazioni aziendali in Azure Active Directory: Aggiungere applicazioni personalizzate da un'unica posizione](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tenere presente quanto segue:
   * L'applicazione deve avere un URL di accesso noto
   * La pagina di accesso deve contenere un modulo HTML con uno o più campi di testo che le estensioni del browser siano in grado di popolare automaticamente. Deve contenere come minimo il nome utente e la password.

## <a name="app-proxy-configuration"></a>Configurazione del proxy delle app

Tempo previsto per il completamento: 20 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Una sottoscrizione di Microsoft Azure AD Basic o Premium e una directory di Azure AD di cui si è un amministratore globale | [Edizioni di Azure Active Directory](active-directory-editions.md) |
| Un'applicazione Web ospitata in locale che si vuole configurare per l'accesso remoto |  |
| Un server che esegue Windows Server 2012 R2 oppure Windows 8.1 o versioni successive in cui poter installare il connettore del proxy dell'applicazione | [Comprendere i connettori del proxy applicazione Azure AD](application-proxy-understand-connectors.md) |
| Se nel percorso è presente un firewall, verificare che sia aperto in modo che il connettore possa inviare richieste HTTPS (TCP) al proxy dell'applicazione | [Attività iniziali del proxy di applicazione e installazione del connettore: Prerequisiti del proxy dell'applicazione](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| Se l'organizzazione usa server proxy per la connessione a Internet, vedere il post del blog relativo all'uso di server proxy locali esistenti per informazioni dettagliate sulla configurazione | [Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Installare un connettore sul server | [Attività iniziali del proxy di applicazione e installazione del connettore: Installare e registrare un connettore](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| Pubblicare l'applicazione locale in Azure AD come applicazione proxy dell'applicazione | [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-publish-azure-portal.md) |
| Assegnare gli utenti test | [Pubblicare applicazioni mediante il proxy dell'applicazione AD Azure: Aggiungere un utente di test](application-proxy-publish-azure-portal.md#add-a-test-user) |
| Facoltativamente, configurare un'esperienza Single Sign-On per gli utenti | [Fornire accesso Single Sign-On mediante il proxy dell'applicazione Azure AD](application-proxy-sso-azure-portal.md) |
| Testare l'app effettuando l'accesso al portale di MyApps come utente assegnato | https://myapps.microsoft.com |

### <a name="considerations"></a>Considerazioni

1. Benché sia consigliabile inserire il connettore nella rete aziendale, esistono casi in cui le prestazioni risultano migliori se viene posizionato nel cloud. Altre informazioni: [Considerazioni relative alla topologia di rete quando si usa il proxy di applicazione di Azure Active Directory](application-proxy-network-topology-considerations.md)
2. Per informazioni dettagliate sulla sicurezza e su come garantire una soluzione di accesso remoto particolarmente protetta gestendo solo le connessioni in uscita, vedere: [Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD](application-proxy-security-considerations.md)

## <a name="generic-ldap-connector-configuration"></a>Configurazione del connettore LDAP generico

Tempo previsto per il completamento: 60 minuti

> [!IMPORTANT]
> Si tratta di una configurazione avanzata che richiede una certa conoscenza di FIM o MIM. Se usata in produzione, è consigliabile leggere le domande su questa configurazione in [supporto tecnico Premier](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Azure AD Connect installato e configurato | Blocco predefinito: [Sincronizzazione directory: Sincronizzazione dell'hash delle password](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Istanza ADLDS conforme ai requisiti | [Documentazione tecnica sul connettore Generic LDAP: Panoramica del connettore Generic LDAP](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| Elenco dei carichi di lavoro usati dagli utenti e attributi associati a questi carichi di lavoro | [Servizio di sincronizzazione Azure AD Connect: Attributi sincronizzati con Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Aggiungere un connettore Generic LDAP | [Documentazione tecnica sul connettore Generic LDAP: Creare un nuovo connettore](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| Creare profili di esecuzione per il connettore creato (importazione completa, importazione delta, sincronizzazione completa, sincronizzazione delta, esportazione) | [Create a Management Agent Run Profile](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx) (Creare un profilo di esecuzione dell'agente di gestione)<br/> [Uso dei connettori con Sync Service Manager di Azure AD Connect](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Eseguire il profilo di importazione completa e verificare se sono presenti oggetti nello spazio connettore | [Search for a Connector Space Object](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx) (Ricerca di un oggetto nello spazio connettore)<br/>[Sezione sulla ricerca nello spazio connettore in Uso dei connettori con Sync Service Manager di Azure AD Connect](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Creare regole di sincronizzazione in modo che gli oggetti nel metaverse abbiano gli attributi necessari per i carichi di lavoro | [Servizio di sincronizzazione Azure AD Connect: Procedure consigliate per modificare la configurazione predefinita: Modifiche apportate alle regole di sincronizzazione](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Avviare un ciclo di sincronizzazione completa | [Servizio di sincronizzazione Azure AD Connect: Utilità di pianificazione: Avviare l'utilità di pianificazione](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| In caso di problemi, eseguire le procedure per la risoluzione | [Risoluzione dei problemi relativi a un oggetto che non esegue la sincronizzazione in Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Verificare che utente LDAP possa eseguire l'accesso e accedere all'applicazione | https://myapps.microsoft.com |

### <a name="considerations"></a>Considerazioni

> [!IMPORTANT]
> Si tratta di una configurazione avanzata che richiede una certa conoscenza di FIM o MIM. Se usata in produzione, è consigliabile leggere le domande su questa configurazione in [supporto tecnico Premier](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Gruppi: proprietà delegata

Tempo previsto per il completamento: 10 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Applicazione SaaS (SSO federato o con password) già configurata | Blocco predefinito: [SaaS: configurazione SSO federato](#saas-federated-sso-configuration) |
| Il gruppo cloud a cui è assegnato l'accesso all'applicazione in 1 è identificato | Blocco predefinito: [SaaS: configurazione SSO federato](#saas-federated-sso-configuration) <br/>[Creare un gruppo e aggiungere membri in Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Sono disponibili le credenziali per il proprietario del gruppo | [Gestire l'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md) |
| Identificate le credenziali per gli Information Worker che accedono all'app | [Che cos'è il pannello di accesso?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Identificare il gruppo a cui è stato concesso l'accesso all'applicazione e configurare il proprietario di un determinato gruppo| [Gestire le impostazioni per un gruppo in Azure Active Directory](active-directory-groups-settings-azure-portal.md) |
| Accedere come proprietario del gruppo, visualizzare l'appartenenza al gruppo nella scheda dei gruppi del Pannello di accesso | [Pagina di gestione dei gruppi di Azure Active Directory](https://account.activedirectory.windowsazure.com/r/#/groups) |
| Aggiungere l'Information Worker da testare |  |
| Accedere come l'Information Worker, verificare che il riquadro è disponibile | [Che cos'è il pannello di accesso?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Considerazioni

Se per l'applicazione è abilitato il provisioning, può essere necessario attendere alcuni minuti il completamento del provisioning per poter accedere all'applicazione come l'Information Worker.

## <a name="saas-and-identity-lifecycle"></a>SaaS e ciclo di vita delle identità

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Applicazione SaaS (SSO federato o con password) già configurata | Blocco predefinito: [SaaS: configurazione SSO federato](#saas-federated-sso-configuration) |
| Il gruppo cloud a cui è assegnato l'accesso all'applicazione in 1 è identificato | Blocco predefinito: [SaaS: configurazione SSO federato](#saas-federated-sso-configuration) <br/>[Creare un gruppo e aggiungere membri in Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Identificate le credenziali per gli Information Worker che accedono all'app | [Che cos'è il pannello di accesso?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Rimuovere l'utente dal gruppo di cui è assegnata l'applicazione | [Gestire l'appartenenza al gruppo per gli utenti nel tenant di Azure Active Directory](active-directory-groups-members-azure-portal.md) |
| Attendere alcuni minuti il completamento del deprovisioning | [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory: Come funziona il provisioning automatizzato?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| In una sessione separata del browser accedere come l'Information Worker al portale delle app personali e verificare se il riquadro è mancante | http://myapps.microsoft.com |


### <a name="considerations"></a>Considerazioni

Estrapolare lo scenario del modello di verifica per scenari con utenti assenti temporaneamente o definitivamente. Se l'utente viene disabilitato in Active Directory locale o viene rimosso, non è più possibile accedere all'applicazione SaaS.

## <a name="self-service-access-to-application-management"></a>Accesso self-service alla gestione delle applicazioni

Tempo previsto per il completamento: 10 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Identificare gli utenti del modello di verifica che richiederanno l'accesso alle applicazioni, come parte del gruppo di sicurezza | Blocco predefinito: [SaaS: configurazione SSO federato](#saas-federated-sso-configuration) |
| Applicazione di destinazione distribuita | Blocco predefinito: [SaaS: configurazione SSO federato](#saas-federated-sso-configuration) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Accedere al pannello delle applicazioni aziendali nel portale di gestione di Azure AD | [Portale di gestione di Azure AD: Applicazioni aziendali](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Configurare l'applicazione dai prerequisiti con self-service | [Novità della gestione delle applicazioni aziendali in Azure Active Directory: Configurare l'accesso alle applicazioni self-service](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Accedere come l'Information Worker al portale delle app personali | http://myapps.microsoft.com |
| Si noti il pulsante di aggiunta app nella parte superiore della pagina. Usarlo per accedere all'app |  |

### <a name="considerations"></a>Considerazioni

Le applicazioni scelte potrebbero avere requisiti di provisioning, quindi accedere immediatamente all'app può causare errori. Se l'applicazione selezionata supporta il provisioning con Azure AD e viene configurata, è possibile sfruttare questa opportunità per visualizzare l'intero flusso da un'estremità all'altra. Per altre indicazioni, vedere il blocco predefinito [SaaS: configurazione SSO federato](#saas-federated-sso-configuration)

## <a name="self-service-password-reset"></a>Reimpostazione della password self-service

Tempo previsto per il completamento: 15 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Abilitare la gestione delle password self-service nel tenant. | [Reimpostazione delle password in Azure Active Directory per gli amministratori IT](active-directory-passwords-update-your-own-password.md) |
| Abilitare il writeback delle password per gestire le password in locale. Si noti che questa operazione richiede versioni specifiche di Azure AD Connect | [Prerequisiti per il writeback delle password](active-directory-passwords-writeback.md) |
| Identificare gli utenti del modello di verifica che useranno questa funzionalità e verificare che siano membri di un gruppo di sicurezza. Gli utenti devono essere non amministratori per presentare completamente la funzionalità | [Personalizzare: Gestione delle password di Azure Active Directory: Limitare l'accesso per la reimpostazione delle password](active-directory-passwords-writeback.md) |


### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Passare al portale di gestione di Azure AD, pagina di reimpostazione delle password | [Portale di gestione di Azure AD: Reimpostazione delle password](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Determinare i criteri di reimpostazione delle password. Ai fini del modello di verifica è possibile usare le chiamate telefoniche e la sezione di domande e risposte. È consigliabile abilitare la registrazione obbligatoria per accedere al Pannello di accesso |  |
| Disconnettersi e accedere di nuovo come Information Worker |  |
| Specificare i dati di reimpostazione self-service delle password come indicato per il passaggio 2 | http://aka.ms/ssprsetup |
| Chiudere il browser |  |
| Ripetere la procedura di accesso come l'Information Worker usato nel passaggio 4 |  |
| Reimpostare la password | [Aggiornare la password: Reimpostare la password](active-directory-passwords-update-your-own-password.md) |
| Provare ad accedere con la nuova password ad Azure AD e anche alle risorse locali |  |

### <a name="considerations"></a>Considerazioni

1. Se si prevede che l'aggiornamento ad Azure AD Connect causi dei problemi, è consigliabile usarlo con gli account nel cloud o trasformarlo in demo per un ambiente separato
2. Gli amministratori usano criteri diversi e reimpostare la password con l'account di amministratore può causare alterazioni del modello di verifica e confusione. Assicurarsi di usare un account utente normale per testare le operazioni di reimpostazione


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Azure Multi-Factor Authentication con chiamate telefoniche

Tempo previsto per il completamento: 10 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Identificare gli utenti del modello di verifica che useranno MFA  |  |
| Telefono con buona ricezione per la richiesta di connessione MFA  | [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Passare al pannello "Utenti e gruppi" nel portale di gestione di Azure AD | [Portale di gestione di Azure AD: Utenti e gruppi](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Scegliere il pannello "Tutti gli utenti" |  |
| Nella barra superiore scegliere il pulsante "Multi-Factor Authentication" | URL diretto per il portale di Azure MFA: https://aka.ms/mfaportal |
| Nelle impostazioni "Utente" selezionare gli utenti del modello di verifica e abilitarli per MFA | [Stati utente in Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) |
| Eseguire l'accesso come utente del modello di verifica e seguire la procedura di registrazione  |  |

### <a name="considerations"></a>Considerazioni

1. I passaggi del modello di verifica in questo blocco predefinito consentono di impostare in modo esplicito MFA per un utente per tutti gli accessi. Sono disponibili altri strumenti, ad esempio l'accesso condizionale e la protezione dell'identità, che usano MFA in scenari più specifici. Questo è un aspetto da considerare quando si passa dal modello di verifica alla produzione.
2. I passaggi del modello di verifica in questo blocco predefinito per comodità usano in modo esplicito le chiamate telefoniche come metodo MFA. Quando si passa dal modello di verifica all'ambiente di produzione, si consiglia di usare il più possibile applicazioni come [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) come secondo fattore.
Altre informazioni: documento [DRAFT NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Accesso condizionale MFA per applicazioni SaaS

Tempo previsto per il completamento: 10 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Identificare gli utenti del modello di verifica a cui destinare i criteri. Questi utenti devono essere in un gruppo di sicurezza per creare l'ambito dei criteri di accesso condizionale | [SaaS: configurazione SSO federato](#saas-federated-sso-configuration) |
| L'applicazione SaaS è già stata configurata |  |
| Gli utenti del modello di verifica sono già stati assegnati all'applicazione |  |
| Le credenziali per l'utente del modello di verifica sono disponibili |  |
| L'utente del modello di verifica è registrato per MFA. Uso di un telefono con buona ricezione | http://aka.ms/ssprsetup |
| Dispositivo nella rete interna. Indirizzo IP configurato nell'intervallo di indirizzi interni | Individuare l'indirizzo IP: https://www.bing.com/search?q=what%27s+my+ip |
| Dispositivo nella rete esterna (può essere un telefono che usa la rete mobile del vettore) |  |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Passare al portale di gestione di Azure AD, pannello Accesso condizionale | [Portale di gestione di Azure AD: Accesso condizionale](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Creare i criteri di accesso condizionale:<br/>- Individuare gli utenti del modello di verifica in "Utenti e gruppi"<br/>- Individuare l'applicazione del modello di verifica in "App cloud"<br/>- Individuare tutte le posizioni tranne quelle attendibili in "Condizioni" -> "Località" **Nota:** gli indirizzi IP attendibili sono configurati nel [portale di MFA](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>- Richiedere l'autenticazione a più fattori nel pannello "Concedi" | [Introduzione all'accesso condizionale in Azure Active Directory: Procedura di configurazione dei criteri](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Accedere all'applicazione dall'interno della rete aziendale | [Introduzione all'accesso condizionale in Azure Active Directory: Test dei criteri](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Accedere all'applicazione dalla rete pubblica | [Introduzione all'accesso condizionale in Azure Active Directory: Test dei criteri](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Considerazioni

Se si usa la federazione, è possibile usare il provider di identità locale (IdP) per comunicare lo stato interno/esterno della rete aziendale con attestazioni. È possibile usare questa tecnica senza dover gestire l'elenco di indirizzi IP che può essere complesso da valutare e gestire nelle organizzazioni di grandi dimensioni. In tale impostazione è necessario un account per lo scenario "roaming di rete" (un utente accede dalla rete interna e mentre è connesso cambia la posizione, ad esempio un bar) e assicurarsi di comprendere le implicazioni. Altre informazioni: [Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS: Indirizzi IP attendibili per utenti federati](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Tempo previsto per il completamento: 15 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Identificare l'amministratore globale che farà parte del modello di verifica per PIM | [Iniziare a usare Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Identificare l'amministratore globale che diventerà amministratore della sicurezza | [Iniziare a usare Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Ruolo amministrativo differente in Azure AD PIM](active-directory-privileged-identity-management-roles.md) |
| Facoltativo: verificare se gli amministratori globali hanno accesso alla posta elettronica per gestire le notifiche via posta elettronica in PIM | [Che cos'è Azure AD Privileged Identity Management?: Configurare le impostazioni di attivazione del ruolo](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Accedere a https://portal.azure.com come amministratore globale (GA) e personalizzare il pannello PIM con bootstrap. L'amministratore globale che esegue questo passaggio viene designato amministratore della sicurezza.  In questo esempio sarà l'attore GA1 | [Uso della procedura guidata relativa alla sicurezza di Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Identificare l'amministratore globale e trasformarlo da permanente a idoneo. Deve essere un amministratore separato da quello usato nel passaggio 1 per maggiore chiarezza. In questo esempio sarà l'attore GA2 | [Azure AD Privileged Identity Management: Come aggiungere o rimuovere un ruolo utente](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Che cos'è Azure AD Privileged Identity Management?: Configurare le impostazioni di attivazione del ruolo](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Accedere come GA2 a https://portal.azure.com e provare a modificare le impostazioni utente. Come si può vedere, alcune opzioni sono disattivate. | |
| In una nuova scheda e nella stessa sessione del passaggio 3, passare a https://portal.azure.com e aggiungere il pannello PIM al dashboard. | [Come attivare o disattivare i ruoli in Azure AD Privileged Identity Management: Aggiungere l'applicazione Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Richiedere l'attivazione al ruolo amministratore globale | [Come attivare o disattivare i ruoli in Azure AD Privileged Identity Management: Attivare un ruolo](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Si noti che se GA2 non si è mai registrato per l'autenticazione a più fattori, sarà necessaria la registrazione per Azure MFA |  |
| Tornare alla scheda originale del passaggio 3 e fare clic sul pulsante di aggiornamento nel browser. Si noti che ora si ha accesso alle impostazioni utente per la modifica | |
| Facoltativamente, se gli amministratori globali hanno abilitato la posta elettronica, è possibile controllare la posta in arrivo di GA1 e GA2 e vedere la notifica dell'attivazione del ruolo |  |
| Controllare la cronologia di controllo e osservare il report per verificare se è visualizzata l'elevazione di GA2. | [Che cos'è Azure AD Privileged Identity Management?: Verificare l'attività del ruolo](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Considerazioni

Questa funzionalità fa parte di Azure AD Premium P2 e/o EMS E5

## <a name="discovering-risk-events"></a>Rilevare eventi di rischio

Tempo previsto per il completamento: 20 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Dispositivo con Tor Browser scaricato e installato | [Download di Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Accedere all'utente del modello di verifica per eseguire l'accesso | [Studio di Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Aprire Tor Browser | [Download di Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Accedere con l'account utente del modello di verifica a https://myapps.microsoft.com | [Studio sulla protezione delle identità di Azure Active Directory: Simulazione sugli eventi di rischio](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Attendere 5-7 minuti |  |
| Accedere come amministratore globale a https://portal.azure.com e aprire il pannello Identity Protection | https://aka.ms/aadipgetstarted |
| Aprire il pannello degli eventi di rischio. Dovrebbe apparire una voce sotto "Accessi da indirizzi IP anonimi"  | [Studio sulla protezione delle identità di Azure Active Directory: Simulazione sugli eventi di rischio](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Considerazioni

Questa funzionalità fa parte di Azure AD Premium P2 e/o EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Distribuire criteri di rischio di accesso

Tempo previsto per il completamento: 10 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Dispositivo con Tor Browser scaricato e installato | [Download di Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Accedere come utente del modello di verifica per eseguire il test di accesso |  |
| L'utente del modello di verifica è registrato per MFA. Assicurarsi di usare un telefono con buona ricezione | Blocco predefinito: [Azure Multi-Factor Authentication con chiamate telefoniche](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Accedere come amministratore globale a https://portal.azure.com e aprire il pannello Identity Protection | https://aka.ms/aadipgetstarted |
| Abilitare un criterio di rischio di accesso come segue:<br/>- Assegnato a: utente del modello di verifica<br/>- Condizioni: rischio di accesso medio o alto (l'accesso da una posizione anonima viene considerato come livello di rischio medio)<br/>- Controlli: richiesto MFA | [Studio di Azure Active Directory Identity Protection: Rischio di accesso](active-directory-identityprotection-playbook.md#sign-in-risk) |
| Aprire Tor Browser | [Download di Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Accedere con l'account utente del modello di verifica a https://myapps.microsoft.com |  |
| Si noti la richiesta dell'autenticazione a più fattori | [Esperienze di accesso con Azure AD Identity Protection: Ripristino di un accesso rischioso](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Considerazioni

Questa funzionalità fa parte di Azure AD Premium P2 e/o EMS E5. Per altre informazioni sugli eventi di rischio, vedere [Eventi di rischio di Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Configurare l'autenticazione basata su certificati

Tempo previsto per il completamento: 20 minuti

### <a name="pre-requisites"></a>Prerequisiti

| Prerequisito. | Risorse |
| --- | --- |
| Dispositivo con certificato utente di cui è stato eseguito il provisioning (Windows, iOS o Android) dall'infrastruttura a chiave pubblica aziendale | [Distribuire i certificati utente](https://msdn.microsoft.com/library/cc770857.aspx) |
| Dominio di Azure AD federato con AD FS | [Azure AD Connect e federazione](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Informazioni generali sui Servizi certificati di Active Directory](https://technet.microsoft.com/library/hh831740.aspx)|
| Per i dispositivi iOS l'app Microsoft Authenticator deve essere installata | [Introduzione all'app Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Passaggi

| Passaggio | Risorse |
| --- | --- |
| Abilitare l'autenticazione del certificato in AD FS | [Configurare i criteri di autenticazione: Per configurare l'autenticazione primaria globale in Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Facoltativo: abilitare l'autenticazione del certificato in Azure AD per i client di Exchange Active Sync | [Introduzione all'autenticazione basata su certificati di Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Passare al Pannello di accesso ed eseguire l'autenticazione usando il certificato utente | https://myapps.microsoft.com |

### <a name="considerations"></a>Considerazioni

Per altre informazioni sugli aspetti di questa distribuzione, visitare il post di blog su [AD FS e autenticazione del certificato con Azure AD e Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Il possesso del certificato utente deve essere protetto, gestendo i dispositivi o con PIN in caso di smart card.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
