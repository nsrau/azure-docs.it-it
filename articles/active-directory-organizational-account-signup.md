<properties umbracoNaviHide="0" pageTitle="How To Configure Cloud Services" metaKeywords="Azure cloud services, cloud service, configure cloud service" description="Learn how to configure Azure cloud services." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Sign up for Azure as an organization" authors="terrylan" manager="terrylan" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan"></tags>

# Iscrizione ad Azure come organizzazione

[WACOM.INCLUDE [disclaimer][disclaimer]]

Fino a poco tempo fa, era possibile richiedere una nuova sottoscrizione di Azure solo tramite un account Microsoft (Windows Live ID). Azure supporta ora l'uso di uno dei due metodi di iscrizione tramite account seguenti:

-   **Account Microsoft** (creati dall'utente per uso personale): garantiscono l'accesso a tutti i prodotti e servizi cloud Microsoft destinati ai consumatori, ad esempio Outlook (Hotmail), Messenger, SkyDrive, MSN, Xbox LIVE o Office Live. Con l'iscrizione a Outlook viene creato automaticamente un account Microsoft con un indirizzo @Outlook.com. Un volta creato, l'account Microsoft può essere usato per accedere a servizi cloud Microsoft per consumatori e/o ad Azure. [Altre informazioni][Altre informazioni]

-   **Account aziendali** (rilasciati da un amministratore per uso aziendale/accademico): garantiscono l'accesso a tutti i servizi cloud Microsoft per piccole, medie e grandi aziende, ad esempio Azure, Windows Intune o Office 365. Quando ci si iscrive per uno di questi servizi come organizzazione, in Azure Active Directory viene eseguito automaticamente il provisioning di un tenant basato sul cloud per rappresentare l'organizzazione. [Altre informazioni][1]

    Dopo aver creato il tenant, un amministratore può quindi rilasciare gli account aziendali per ciascun dipendente/studente e assegnare le licenze agli account in base al servizio cloud a cui è necessario accedere, ad esempio Azure.

    *Per iscriversi ad Azure come organizzazione* **Effettua l'iscrizione ora**

## Informazioni su Azure Active Directory

Analogamente ad Active Directory, che è un servizio reso disponibile ai clienti tramite il sistema operativo Windows Server per la gestione della directory locale, Azure Active Directory (Azure AD) è un servizio reso disponibile tramite Azure per la gestione della directory dell'organizzazione nel cloud. [Altre informazioni][2]

Poiché si tratta della directory cloud dell'organizzazione, sono i clienti stessi a decidere chi sono gli utenti, quali informazioni conservare nel cloud, chi può usare le informazioni o gestirle e a quali applicazioni o servizi è consentito l'accesso a tali informazioni. Quando si usa Azure AD, è responsabilità di Microsoft mantenere Active Directory in esecuzione nel cloud con caratteristiche di scalabilità elevata, disponibilità elevata e ripristino di emergenza integrato, nel pieno rispetto dei requisiti di privacy e sicurezza delle informazioni aziendali.

### Integrazione con Active Directory in locale

Se l'organizzazione usa già Active Directory in locale, è possibile usare le funzionalità di integrazione di directory di Azure AD, ad esempio la sincronizzazione della directory e Single Sign-On, per estendere ulteriormente l'ambito d'uso delle identità locali esistenti nel cloud, ottenendo un ambiente migliore per gli amministratori e gli utenti finali. [Altre informazioni][3]

  [disclaimer]: ../includes/disclaimer.md
  [Altre informazioni]: http://windows.microsoft.com/en-US/windows-live/sign-in-what-is-microsoft-account
  [1]: http://technet.microsoft.com/it-it/library/jj573650
  [2]: http://technet.microsoft.com/it-it/library/hh967619
  [3]: http://technet.microsoft.com/it-it/library/jj573653
