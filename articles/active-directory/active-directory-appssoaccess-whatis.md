---
title: Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory | Documentazione Microsoft
description: Utilizzare Azure Active Directory per abilitare Single Sign-On su tutte le applicazioni Web e SaaS necessarie per l'azienda.
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 75d1a3fd-b3c5-4495-a5c8-c4c24145ff00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: curyand
ms.translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: cb41fbd7ca931df52ba8e5304459bb04298a108c
ms.contentlocale: it-it
ms.lasthandoff: 02/16/2017

---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory
Single Sign-On indica la possibilità di accedere a tutte le applicazioni e risorse necessarie per svolgere attività commerciali, effettuando l'accesso solo una volta con un singolo account utente. Una volta effettuato l'accesso, è possibile accedere a tutte le applicazioni senza dover effettuare l’autenticazione (ad esempio, digitare una password) una seconda volta.

Molte organizzazioni si basano sul software come un servizio (SaaS), ad esempio Office 365, Box e Salesforce, per la produttività dell'utente finale. In passato, il personale IT doveva creare e aggiornare singolarmente gli account utente in ciascuna applicazione SaaS e gli utenti dovevano ricordare una password per ogni applicazione SaaS.

Azure Active Directory estende Active Directory locale nel cloud, consentendo agli utenti di utilizzare il proprio account aziendale principale non solo per eseguire l'accesso ai loro dispositivi appartenenti a un dominio e alle risorse della società, ma anche a tutte le applicazioni Web e SaaS necessarie per il proprio lavoro.

Quindi non solo gli utenti non devono più gestire diversi set di nomi utente e password, per l’accesso alle loro applicazioni il provisioning o deprovisioning può essere eseguito automaticamente in base ai membri del gruppo della relativa organizzazione e anche al relativo stato come dipendenti. Azure Active Directory introduce i controlli della governance di sicurezza e accesso che consentono di gestire in modo centralizzato l'accesso degli utenti tra le applicazioni SaaS.

Azure AD consente un’integrazione semplice con molte applicazioni SaaS diffuse attualmente; offre la gestione delle identità e degli accessi e consente agli utenti di utilizzare Single Sign-On per le applicazioni direttamente o di individuarle e avviarle da un portale, ad esempio Office 365 o il pannello di accesso di Azure AD.

L'architettura dell'integrazione è costituita dai quattro principali blocchi predefiniti seguenti:

* Single Sign-On consente agli utenti di accedere alle applicazioni SaaS in base al proprio account aziendale in Azure AD. Single Sign-On consente agli utenti di eseguire l'autenticazione a un'applicazione utilizzando il proprio account aziendale singolo.
* Il provisioning dell'utente permette il provisioning e il deprovisioning dell’utente nei servizi SaaS di destinazione in base alle modifiche effettuate in Windows Server Active Directory e/o in Azure AD. Un account con provisioning consente all'utente di essere autorizzato a utilizzare un'applicazione, dopo l'autenticazione tramite Single Sign-On.
* La gestione centralizzata dell’accesso alle applicazioni nel portale di gestione di Azure consente l'accesso alle applicazioni SaaS e la loro gestione da un unico punto, con la possibilità di delegare i processi decisionali e le approvazioni dell’accesso alle applicazioni a tutti gli utenti dell'organizzazione
* Segnalazione e monitoraggio unificati delle attività dell'utente in Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Come funziona Single Sign-On con Azure Active Directory (PHP)?
Quando un utente "accede" a un'applicazione, passa attraverso un processo di autenticazione in cui viene richiesto di dimostrare di essere chi dice di essere. Senza Single Sign-On, questa operazione viene in genere eseguita immettendo una password archiviata nell'applicazione e l'utente è tenuto a conoscere la password.

Azure AD supporta tre modi diversi per accedere alle applicazioni:

* **Single Sign-On federato** consente alle applicazioni di reindirizzare ad Azure AD per l'autenticazione dell’utente anziché richiedere la propria password. È supportato per le applicazioni che supportano protocolli quali SAML 2.0, WS-Federation oppure OpenID Connect ed è la modalità più ricca di Single Sign-On.
* **Single Sign-On basato su password** consente l’archiviazione e la riproduzione delle password delle applicazioni protette utilizzando un'estensione del browser Web o un’app mobile. Questo sfrutta il processo di accesso esistente fornito dall'applicazione, ma consente all'amministratore di gestire le password e non richiede all'utente di conoscerle.
* **Single Sign-On esistente** consente ad Azure AD di sfruttare qualunque Single Sign-On esistente configurato per l'applicazione, ma consente a queste applicazioni di essere collegate ai portali del pannello di accesso di Office 365 o di Azure AD e abilita il reporting aggiuntivo in Azure AD quando le applicazioni vengono avviate da questa posizione.

Una volta che un utente è stato autenticato con un'applicazione, deve disporre anche di un record di account con provisioning all'applicazione che indica all'applicazione dove si trovano le autorizzazioni e il livello di accesso all'interno dell'applicazione. Il provisioning di questo record di account può verificarsi automaticamente o può essere effettuato manualmente da un amministratore prima che all'utente venga fornito l'accesso Single Sign-On.

 Ulteriori informazioni su queste modalità Single Sign-On e sul provisioning sono riportate di seguito.

### <a name="federated-single-sign-on"></a>Single Sign-On federato
Single Sign-On federato consente agli utenti dell'organizzazione di accedere automaticamente a un'applicazione SaaS di terze parti tramite Azure AD utilizzando le informazioni dell’account utente di Azure AD.

In questo scenario, quando è già stato effettuato l’accesso ad Azure AD e si desidera accedere alle risorse controllate da un'applicazione SaaS di terze parti, la federazione elimina la necessità per un utente di ripetere l'autenticazione.

Azure AD supporta il Single Sign-On federato con applicazioni che supportano i protocolli SAML 2.0, WS-Federation oppure OpenID Connect.

Vedere anche: [Gestione dei certificati per Single Sign-On federato](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>Single Sign-On basato su password
La configurazione di Single Sign-On basato su password consente agli utenti dell'organizzazione di accedere automaticamente a un'applicazione SaaS di terze parti tramite Azure AD utilizzando le informazioni dell’account utente dall’applicazione SaaS di terze parti. Quando si abilita questa funzionalità, Azure AD raccoglie e archivia in modo sicuro le informazioni sull'account utente e la relativa password.

Azure AD supporta Single Sign-On basato su password per qualunque applicazione basata su cloud con pagina di accesso basata su HTML. Utilizzando un plug-in del browser personalizzato, AAD automatizza il processo di accesso dell’utente recuperando in modo sicuro le credenziali dell'applicazione come il nome utente e la password dalla directory e immettendole nella pagina di accesso dell’applicazione per conto dell'utente. Esistono due casi di utilizzo:

1. **L’amministratore gestisce le credenziali** : gli amministratori possono creare e gestire le credenziali dell'applicazione e assegnare le credenziali a utenti o gruppi che devono accedere all'applicazione. In questi casi, l'utente finale non deve necessariamente conoscere le credenziali, ma comunque ottiene l’accesso Single Sign-On all'applicazione semplicemente facendo clic su di esso nel proprio pannello di accesso o tramite un collegamento fornito. Questo consente sia la gestione del ciclo di vita delle credenziali da parte dell’amministratore, che la comodità per gli utenti finali che non devono necessariamente ricordare o gestire le password specifiche dell’app. Le credenziali vengono nascoste all'utente finale durante il processo di accesso automatico; tuttavia, esse sono tecnicamente individuabili dall'utente utilizzando strumenti di debug Web; gli utenti e gli amministratori devono seguire gli stessi criteri di sicurezza utilizzati se le credenziali fossero presentate direttamente all'utente. Le credenziali fornite dall’amministratore sono molto utili quando si fornisce un accesso all'account condiviso tra più utenti, ad esempio i social media o applicazioni di condivisione dei documenti.
2. **L’utente gestisce le credenziali** : gli amministratori possono assegnare le applicazioni agli utenti finali o ai gruppi e consentire agli utenti finali di immettere le proprie credenziali direttamente quando accedono all'applicazione per la prima volta nel proprio pannello di accesso. Questo crea una comodità per gli utenti finali poiché essi non devono immettere continuamente le password specifiche delle applicazioni ogni volta che accedono ad esse. Questo caso di utilizzo può essere usato anche come tappa della gestione amministrativa delle credenziali, poiché l'amministratore può impostare nuove credenziali per l'applicazione in un secondo momento senza modificare l'esperienza di accesso dell'utente finale all’app.

In entrambi i casi, le credenziali vengono archiviate in formato crittografato nella directory e vengono trasferite solo tramite HTTPS durante il processo di accesso automatico. Utilizzando Single Sign-On basato su password, Azure AD offre una soluzione di gestione dell’accesso all’identità pratica per le applicazioni che non sono in grado di supportare i protocolli di federazione.

Single Sign-On basato su password si basa su un'estensione del browser per recuperare in modo sicuro le informazioni specifiche dell’applicazione e dell'utente da Azure AD e applicarle al servizio. La maggior parte delle applicazioni SaaS di terze parti supportate da Azure AD supportano questa funzionalità.

Per l'accesso Single Sign-On basato su password il browser dell'utente finale può essere uno dei seguenti:

* Internet Explorer 8, 9, 10 e 11 -- su Windows 7 o versione successiva (vedere anche [Guida alla distribuzione dell'estensione di Internet Explorer](active-directory-saas-ie-group-policy.md))
* Chrome in Windows 7 o versione successiva e MacOS X o versione successiva
* Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva e in Mac OS X 10.6 o versione successiva

**Nota:** l'estensione SSO basata su password sarà disponibile per Edge in Windows 10, quando saranno supportate le estensioni del browser per Edge.

### <a name="existing-single-sign-on"></a>Single Sign-On esistente
Quando si configura Single Sign-On per un'applicazione, il portale di gestione di Azure fornisce una terza opzione di "Single Sign-On esistente". Questa opzione consente semplicemente all'amministratore di creare un collegamento a un'applicazione e di posizionarlo nel pannello di accesso per gli utenti selezionati.

Ad esempio, se è presente un'applicazione configurata per l'autenticazione degli utenti tramite Active Directory Federation Services 2.0, un amministratore può utilizzare l'opzione "Single Sign-On esistente" per creare un collegamento al pannello di accesso. Quando gli utenti accedono al collegamento, vengono autenticati utilizzando Active Directory Federation Services 2.0 o qualunque soluzione Single Sign-On esistente fornita dall'applicazione.

### <a name="user-provisioning"></a>Provisioning utente
Per selezionare le applicazioni, Azure AD abilita il provisioning e il deprovisioning automatizzato degli utenti degli account nelle applicazioni SaaS di terze parti dal portale di gestione di Azure, utilizzando le informazioni dell’identità di Windows Server Active Directory o Azure AD. Quando a un utente vengono concesse le autorizzazioni in Azure AD per una di queste applicazioni, un account può essere creato automaticamente (provisioning) nell'applicazione SaaS di destinazione.

Quando un utente viene eliminato o le relative informazioni cambiano in Azure AD, tali modifiche si riflettono anche nell'applicazione SaaS. Ciò significa che la configurazione della gestione automatizzata del ciclo di vita dell’identità consente agli amministratori di controllare e fornire provisioning e deprovisioning automatizzato dalle applicazioni SaaS. In Azure AD questa automazione della gestione del ciclo di vita dell’identità è abilitata dal provisioning dell'utente.

Per altre informazioni, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Iniziare a utilizzare la raccolta di applicazioni di Azure AD
Informazioni introduttive Per distribuire il servizio Single Sign-On tra Azure AD e le applicazioni SaaS utilizzate dall'organizzazione, seguire queste linee guida.

### <a name="using-the-azure-ad-application-gallery"></a>Utilizzo della raccolta di applicazioni di Azure AD
La [raccolta delle applicazioni di Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) fornisce un elenco di applicazioni che supportano una forma di Single Sign-On con Azure Active Directory.

![][1]

Di seguito sono riportati alcuni suggerimenti per la ricerca delle applicazioni in base alle funzionalità che supportano:

* Azure AD supporta il provisioning e deprovisioning automatico per tutte le applicazioni “In primo piano” nella [raccolta di applicazioni di Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/).
* Un elenco di applicazioni federate che supportano in particolare il Single Sign-On federato tramite un protocollo, ad esempio SAML, WS-Federation oppure OpenID Connect, è disponibile [qui](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Dopo aver trovato l'applicazione, è possibile iniziare seguendo le istruzioni dettagliate presentate nella raccolta delle applicazioni e nel portale di gestione di Azure per abilitare Single Sign-On.

### <a name="application-not-in-the-gallery"></a>L’applicazione non è presente nella raccolta?
Se l'applicazione non è presente nella raccolta di applicazioni Azure AD, sono disponibili queste opzioni:

* **Aggiungere un'app non pubblicata attualmente in uso** : per connettere un'applicazione non in elenco usata dall'organizzazione, usare la categoria personalizzata nella raccolta di app del portale di gestione di Azure. È possibile aggiungere qualunque applicazione che supporta SAML 2.0 come applicazione federata o qualunque applicazione che dispone di una pagina di accesso basata su HTML come applicazione Single Sign-On con password. Per altre informazioni, vedere il blog sull' [aggiunta di un'applicazione personalizzata](active-directory-saas-custom-apps.md).
* **Aggiungere l'app personalizzata che si sta sviluppando** : se l'applicazione è stata sviluppata personalmente, per implementare il Single Sign-On federato o il provisioning con l'API Graph di Azure AD, seguire le linee guida nella documentazione per sviluppatori di Azure AD. Per ulteriori informazioni, vedere le risorse:
  
  * [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Richiedere l'integrazione di un'app** : richiedere il supporto per l'applicazione necessaria tramite il [forum dei commenti di Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-management-portal"></a>Utilizzo del portale di gestione di Azure
È possibile utilizzare l'estensione di Active Directory nel portale di gestione di Azure per configurare l'applicazione Single Sign-On. Innanzitutto, è necessario selezionare una directory dalla sezione Active Directory nel portale:

![][2]

Per gestire le applicazioni SaaS di terze parti, è possibile passare alla scheda Applicazioni della directory selezionata. Questa vista consente agli amministratori di:

* Aggiungere dalla raccolta di Azure AD nuove applicazioni, nonché le applicazioni che si stanno sviluppando
* Eliminare applicazioni integrate
* Gestire le applicazioni già integrate

Le attività amministrative tipiche per un'applicazione SaaS di terze parti sono:

* Abilitazione di Single Sign-On con Azure AD, utilizzando Single Sign-On con password o, se disponibile per il SaaS di destinazione, Single Sign-On federato
* Facoltativamente, abilitare il provisioning e deprovisioning per l'utente (gestione del ciclo dell’identità)
* Per le applicazioni in cui il provisioning dell'utente è abilitato, selezionare gli utenti che hanno accesso all'applicazione

Per le applicazioni della raccolta che supportano Single Sign-On federato, la configurazione in genere richiede di fornire impostazioni di configurazione aggiuntive, ad esempio certificati e metadati per creare una relazione di trust federativa tra l'applicazione di terze parti e Azure AD. La configurazione guidata illustra i dettagli e fornisce l’accesso facile ai dati specifici dell'applicazione SaaS e istruzioni.

Per le applicazioni della raccolta che supportano il provisioning automatico dell’utente, ciò richiede di concedere le autorizzazioni di Azure AD per gestire gli account nell'applicazione SaaS. Come minimo, è necessario fornire le credenziali che Azure AD deve utilizzare per l'autenticazione attraverso l'applicazione di destinazione. Se è necessario specificare ulteriori impostazioni di configurazione dipende dai requisiti dell'applicazione.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Distribuzione di applicazioni integrate di Azure AD agli utenti
Azure AD fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione:

* Pannello di accesso di Azure AD
* Applicazione di avvio di Office 365
* Accesso diretto alle applicazioni federate
* Collegamenti diretti per applicazioni federate, basate su password o esistenti

I metodi che è possibile scegliere per la distribuzione nell'organizzazione sono a discrezione dell'utente.

### <a name="azure-ad-access-panel"></a>Pannello di accesso di Azure AD
Il pannello di accesso in https://myapps.microsoft.com è un portale basato sul Web che consente a un utente finale con un account aziendale in Azure Active Directory di visualizzare e avviare applicazioni basate sul cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Se si è un utente finale con [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), è anche possibile utilizzare le funzionalità di gestione di gruppi in modalità self-service tramite il riquadro di accesso.

![][3]

Il riquadro di accesso non fa parte del portale di gestione di Azure e non richiede agli utenti di avere una sottoscrizione di Azure o di Office 365.

Per altre informazioni sul riquadro di accesso di Azure AD, vedere [Introduzione al riquadro di accesso](active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Applicazione di avvio di Office 365
Per le organizzazioni che hanno distribuito Office 365, le applicazioni assegnate agli utenti con Azure AD vengono visualizzate anche nel portale di Office 365 all'indirizzo https://portal.office.com/myapps. Questo rende facile e comodo per gli utenti all’interno di un'organizzazione avviare le proprie applicazioni senza dover utilizzare un secondo portale ed è la soluzione di avvio delle applicazioni consigliata per le organizzazioni che utilizzano Office 365.

![][4]

Per ulteriori informazioni sull'avvio di applicazioni di Office 365, vedere [Visualizzazione dell'applicazione nell’applicazione di avvio di Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Accesso diretto alle applicazioni federate
La maggior parte delle applicazioni federate che supportano OpenID Connect, WS-Federation o SAML 2.0 supportano anche la possibilità per gli utenti di avviare l'applicazione e quindi ottenere l’accesso tramite Azure AD con il reindirizzamento automatico oppure facendo clic su un collegamento per accedere. Questo è noto come accesso avviato dal provider di servizi e la maggior parte delle applicazioni federate nella raccolta di applicazioni di Azure AD lo supporta (vedere la documentazione collegata dalla configurazione guidata di configurazione di Single Sign-On dell'app nel portale di gestione di Azure per i dettagli).

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Collegamenti diretti di accesso per le applicazioni federate, basate su password o esistenti
Azure AD supporta anche collegamenti diretti Single Sign-On alle singole applicazioni che supportano Single Sign-On basato su password, Single Sign-On esistente e qualunque forma di Single Sign-On federato.

Questi collegamenti sono URL realizzati appositamente che inviano un utente attraverso la procedura di accesso di Azure AD per un'applicazione specifica senza richiedere che la avvii dal pannello di accesso di Azure AD o Office 365. Questi URL Single Sign-On sono disponibili nella scheda Dashboard di qualunque applicazione già integrata nella sezione Active Directory del portale di gestione di Azure, come illustrato nella schermata riportata di seguito.

![][6]

Questi collegamenti possono essere copiati e incollati ovunque per fornire un collegamento di accesso all'applicazione selezionata. Questo potrebbe essere all’interno di un messaggio di posta elettronica o in qualsiasi portale personalizzato basato sul Web configurato per l'accesso alle applicazioni dell’utente. Di seguito è riportato un esempio di URL Single Sign-On diretto di Azure AD per Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Analogamente agli URL specifici dell'organizzazione per il pannello di accesso, è possibile personalizzare ulteriormente questo URL mediante l'aggiunta di uno dei domini attivi o verificati per la directory dopo il dominio myapps.microsoft.com. Questo assicura che eventuali personalizzazioni dell’organizzazione vengano caricate immediatamente nella pagina di accesso senza che l'utente debba immettere prima l'ID utente:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando un utente autorizzato fa clic su uno di questi collegamenti specifici dell'applicazione, per prima cosa vede le pagina di accesso aziendale (presupponendo che non abbia già effettuato l’accesso) e dopo l’accesso viene reindirizzato alla propria applicazione senza passare prima dal pannello di accesso. Se l'utente non dispone dei prerequisiti per l'accesso all'applicazione, ad esempio l'estensione browser di accesso singolo basato su password, il collegamento richiederà all'utente di installare l'estensione mancante. L'URL del collegamento rimane costante se la configurazione Single Sign-On per l'applicazione viene modificata.

Questi collegamenti utilizzano gli stessi meccanismi di controllo dell’accesso come il pannello di accesso e Office 365 e solo gli utenti o i gruppi che sono stati assegnati all'applicazione nel portale di gestione di Azure potranno eseguire l'autenticazione. Tuttavia, gli utenti non autorizzati vedranno un messaggio che indica che non sono autorizzati all’accesso e verrà fornito loro un collegamento per caricare il pannello di accesso per visualizzare le applicazioni disponibili per cui dispongono dell’accesso.

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Ricerca di applicazioni cloud non autorizzate con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
* [Introduzione alla gestione dell'accesso alle app](active-directory-managing-access-to-apps.md)
* [Confronto tra le funzionalità per la gestione di identità esterne con Azure AD](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png

