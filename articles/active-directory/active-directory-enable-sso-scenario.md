<properties 
    pageTitle="Usare Azure AD per abilitare l'accesso Single Sign-On a tutte le applicazioni | Microsoft Azure" 
    description="Informazioni su come Azure Active Directory consente di estendere l'ambito di un'identità e gestirla." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/12/2015" 
    ms.author="markvi" />


# Usare Azure AD per abilitare l'accesso Single Sign-On per tutte le applicazioni
Questo articolo è destinato ai responsabili dell'IT che desiderano approfondire il valore aziendale fornito da Azure Active Directory alle applicazioni in uso nell'organizzazione.

## Esecuzione di app in un ambiente basato sul cloud  

Con l'attuale diffusione di sistemi basati sul cloud, è possibile disporre di migliaia di app in grado di svolgere determinate attività. I reparti IT spesso non sono a conoscenza di tutte le app SaaS in uso nelle proprie organizzazioni. In molti casi l'uso delle app è noto, ma ottenere un certo livello di sicurezza e controllo è un'attività costosa in termini di tempo e denaro. In molti casi, inoltre, le applicazioni non supportano l'integrazione con sistemi di identità aziendale e in altri le integrazioni d'uso occasionale vengono evitate a causa dei costi e del livello di complessità correlati.

Di conseguenza,

- l'accesso non autorizzato ai dati aziendali, le possibili perdite di dati e altri rischi di sicurezza impliciti introdotti da applicazioni non gestite rappresentano una preoccupazione per molte organizzazioni. Poiché le organizzazioni non sono a conoscenza né del numero né del tipo di app usate, risulta difficile predisporre un piano per affrontare questi rischi in modo efficace.
- Gli amministratori devono gestire singolarmente tutti i provider di servizio IAM introdotti da app gestite nel proprio ambiente. Sono incluse attività quali la creazione e l'eliminazione di utenti e gruppi, nonché la concessione o la revoca dell'accesso a tali app.
- Gli utenti devono memorizzare varie credenziali per accedere alle app che desiderano usare. I casi di dimenticanza delle password rappresentano un enorme ostacolo per i costi operativi di molte organizzazioni. L'elemento in comune a tutti questi problemi è l'impatto negativo sulla produttività degli utenti e sui costi operativi.  
 
## Quale contributo può offrire Azure Active Directory?
Azure Active Directory consente di risolvere questi problemi grazie alla possibilità di estendere facilmente nel cloud l'infrastruttura di identità esistente e di conseguenza:

- Estendere la copertura degli utenti mobili a qualsiasi app 
- Estendere la copertura della gestione dell'accesso a qualsiasi app cloud 
- Rilevare le app in uso a cui gli utenti hanno eseguito l'accesso


### Estensione della copertura della propria identità a qualsiasi app 

Per impostazione predefinita, Azure Active Directory fornisce accesso Single Sign-On alle app cloud ospitate in Azure e altri servizi online Microsoft quali Office 365, CRM Online e Intune.

Inoltre, può svolgere la funzione di broker di identità per l'accesso Single Sign-On alle app in qualsiasi altro cloud pubblico, ad esempio Google, Amazon, IBM e altri. <br> Se un'app è ampiamente diffusa e nota, è possibile pre-integrarla nella raccolta di app di Azure Active Directory. Al giorno d'oggi sono disponibili circa 2500 app pre-integrate nella raccolta di app e il numero è in continua crescita. <br> Per eseguire la configurazione dell'accesso Single Sign-On per le app pre-integrate nella raccolta di app, sono sufficienti poche operazioni. Come comportarsi se l'app non è ancora presente nella raccolta di app? È possibile integrare qualsiasi app pubblica in Azure Active Directory tramite una procedura guidata e abilitare in questo modo l'accesso Single Sign-On per tali app. Per altre informazioni, vedere l'argomento relativo alla [distribuzione di Single Sign-On tramite Azure Active Directory per le app SaaS appena acquisite](active-directory-single-sign-on-newly-acquired-saas-apps.md) e l'argomento relativo all'[integrazione dell'accesso Single Sign-On di Azure Active Directory con app esistenti](active-directory-sso-integrate-existing-apps.md)<br> E per quanto riguarda le app sviluppate dall'organizzazione? Viene fornita la relativa documentazione che consente agli sviluppatori di integrare facilmente le app sviluppate dall'organizzazione in Azure Active Directory. Per altre informazioni, vedere l'argomento relativo ad [Azure AD e alle applicazioni della guida per gli sviluppatori](active-directory-applications-guiding-developers-for-lob-applications.md).

Grazie al supporto per le app pre-integrate, le app pubbliche non presenti nella raccolta di app e le app sviluppate dall'organizzazione, Azure Active Directory consente di fornire l'accesso Single Sign-On per tutte le app cloud.

Tuttavia, Azure Active Directory risulta vantaggioso non solo per le app cloud. Con Azure Active Directory, è possibile estendere la copertura delle identità Azure fornendo accesso remoto sicuro alle app locali tramite Single Sign-On. Ciò significa che è possibile eseguire l'accesso remoto, che non richiede tecnologie quali reti VPN o altre infrastrutture tradizionali di accesso remoto, alle app Web locali. Azure Active Directory offre questa opzione tramite le funzionalità del proxy di applicazione.

Il modello tradizionale di accesso Single Sign-On si basa sul mapping di due account singoli in due archivi di identità. Con Azure Active Directory, è possibile eseguire il mapping anche di un account singolo con un account aziendale condiviso, ad esempio l'account Twitter aziendale. Implementando l'accesso Single Sign-On per gli account aziendali condivisi, non è necessario condividere le credenziali dell'account con gli utenti per migliorare la protezione di tali account. Per altre informazioni, vedere [Condivisione di account con Azure AD](active-directory-sharing-accounts.md).

Se si estende la copertura delle identità, con una sola password è possibile accedere a migliaia di app.



### Estensione della copertura della gestione dell'accesso a qualsiasi app cloud

La gestione dell'accesso alle app cloud è un'attività costosa se è necessario eseguirla manualmente su ogni app. Con Azure Active Directory è possibile gestire l'accesso alle app cloud in base a gruppi da un punto centrale: il portale di Azure. È possibile assegnare l'accesso a utenti o gruppi singoli. Per altre informazioni, vedere [Gestione dell'accesso alle app](active-directory-managing-access-to-apps.md).

Alcune app, ad esempio Salesforce, Box, Google Apps e Concur offrono le interfacce di automazione per la creazione e la rimozione (o disattivazione) di account. Se un provider offre un'interfaccia di questo tipo, viene usata da Azure Active Directory. In altri termini, Azure Active Directory fornisce supporto per il provisioning automatizzato degli utenti per le app che offrono un'interfaccia di automazione correlata.

Provisioning automatizzato degli utenti:

- Con un utente che dispone dell'accesso a un'app correlata, Azure Active Directory esegue automaticamente il provisioning dell'account Single Sign-On richiesto all'app.
- Quando un account utente in Azure Active Directory è stato eliminato, l'account nell'app viene disattivato o eliminato. La configurazione di provider di provisioning automatizzato degli utenti offre i vantaggi seguenti:
- 	Riduce i costi operativi perché consente di automatizzare attività amministrative che dovrebbero essere eseguite dal personale IT.
- Migliora la protezione dell'ambiente poiché riduce la probabilità di accessi non necessari alle app.

Per altre informazioni sul provisioning automatizzato degli utenti, vedere l'argomento relativo al [provisioning e deprovisioning automatizzato degli utenti alle app SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).


### Rilevamento delle app in uso a cui gli utenti hanno eseguito l'accesso

Con l'accesso Single Sign-On e il provisioning dell'account, Azure Active Directory offre potenti meccanismi per migliorare l'uso delle app da parte di utenti e amministratori nell'ambiente aziendale. Tuttavia, nelle aziende moderne, i reparti IT non sono a conoscenza di tutte le app cloud usate. Con Cloud App Discovery, Azure Active Directory fornisce anche una soluzione per individuare tali app. Cloud App Discovery permette di:

- Individuare le app in uso e misurarne l'utilizzo in base al numero di utenti, al volume di traffico o al numero di richieste Web all'app.
- Identificare gli utenti che usano un'app.
- Esportare i dati per ulteriori analisi offline.
- Assegnare priorità alle app in modo che il reparto IT possa tenerle sotto controllo e integrarle facilmente per abilitare l'accesso Single Sign-On e la gestione degli utenti.

Per altre informazioni su Cloud App Discovery, vedere [Come individuare app cloud non autorizzate usate nell'organizzazione](active-directory-cloudappdiscovery-whatis.md).


## Operazioni iniziali

Innanzitutto, un amministratore IT che non usa già Azure AD:

- [Può richiedere una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory): è possibile ottenere subito gratuitamente una versione di valutazione di 30 giorni e distribuire la prima soluzione cloud in meno di 5 minuti, usando il collegamento seguente

<!---HONumber=Oct15_HO3-->