---
title: Pianificare e risolvere i problemi relativi alle modifiche del nome principale utente (UPN) di AzurePlan and troubleshoot Azure User Principle name (UPN) changes
description: Comprendere i problemi noti e le attenuazioni per le modifiche UPN
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11be1d971922095d4a1ace1c81c763134b4e58c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743329"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Pianificare e risolvere i problemi relativi alle modifiche al nome dell'entità utente in Azure Active DirectoryPlan and troubleshoot User Principal Name changes in Azure Active Directory

Un nome dell'entità utente (UPN, User Principal Name) è un attributo che è uno standard di comunicazione Internet per gli account utente. Un UPN è costituito da un prefisso UPN (il nome dell'account utente) e un suffisso UPN (nome di dominio DNS). Il prefisso unisce il suffisso utilizzando il simbolo "-". Ad esempio: someone@example.com. Un UPN deve essere univoco tra tutti gli oggetti entità di sicurezza in una foresta di directory. 

> [!NOTE]
> Per gli sviluppatori, è consigliabile usare l'objectID utente come identificatore non modificabile, anziché UPN. Se le applicazioni utilizzano attualmente UPN, è consigliabile impostare l'UPN in modo che corrisponda all'indirizzo di posta elettronica principale dell'utente per migliorare la propria esperienza.<br> **In un ambiente ibrido è importante che l'UPN di un utente sia identico nella directory locale e in Azure Active Directory.**

**In questo articolo si presuppone che si sta utilizzando UPN come identificatore utente. Risolve la pianificazione delle modifiche UPN e il ripristino da problemi che possono derivare da modifiche UPN.**

## <a name="learn-about-upns-and-upn-changes"></a>Informazioni su UPN e modifiche UPN
Le pagine di accesso spesso richiedono agli utenti di immettere il proprio indirizzo di posta elettronica quando il valore richiesto è effettivamente il proprio UPN. Pertanto, è necessario assicurarsi di modificare l'UPN degli utenti ogni volta che cambia l'indirizzo di posta elettronica principale.

Gli indirizzi email principali degli utenti potrebbero cambiare per diversi motivi:

* rebranding aziendale

* dipendenti che si spostano in diverse divisioni aziendali 

* fusioni e acquisizioni

* cambia il nome del dipendente

### <a name="types-of-upn-changes"></a>Tipi di modifiche UPN

È possibile modificare un UPN modificando il prefisso, il suffisso o entrambi.

* **Modifica del prefisso**.

   *  Ad esempio, se il nome di una persona è cambiato, potresti modificarne il nome account:  
BSimon@contoso.com perBJohnson@contoso.com

   * È inoltre possibile modificare lo standard aziendale per i prefissi:You might also change the corporate standard for prefixes:  
Bsimon@contoso.com perBritta.Simon@contoso.com

* **Modifica del suffisso**. <br>

    Ad esempio, se una persona ha modificato le divisioni, potresti modificarne il dominio: 

   * Da Britta.Simon@contoso.com a Britta.Simon@contosolabs.com <br>
     Oppure<br>
    * Da Britta.Simon@corp.contoso.com a Britta.Simon@labs.contoso.com 

Modificare l'UPN dell'utente ogni volta che viene aggiornato l'indirizzo di posta elettronica principale di un utente. Indipendentemente dal motivo della modifica dell'e-mail, l'UPN deve sempre essere aggiornato in modo che corrisponda.

Durante la sincronizzazione iniziale da Active Directory ad Azure AD, verificare che i messaggi di posta elettronica degli utenti siano identici ai relativi UPN.

### <a name="upns-in-active-directory"></a>UPN in Active Directory

In Active Directory, il suffisso UPN predefinito è il nome DNS del dominio in cui è stato creato l'account utente. Nella maggior parte dei casi, si tratta del nome di dominio registrato come dominio aziendale su Internet. Se si crea l'account utente nel dominio contoso.com, l'UPN predefinito

username@contoso.com

 Tuttavia, è possibile [aggiungere più suffissi UPN](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) utilizzando domini e trust di Active Directory. 

Ad esempio, è possibile aggiungere labs.contoso.com e fare in modo che gli UPN e i messaggi di posta elettronica degli utenti lo riflettano. Sarebbero poi diventati

username@labs.contoso.com.

>[!IMPORTANT]
> Se gli UPN in Active Directory e Azure Active Directory non corrispondono, si verificano problemi. Se si [modifica il suffisso in Active Directory,](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)è necessario assicurarsi che sia stato aggiunto e verificato un nome di dominio personalizzato corrispondente [in Azure AD.](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) 

![Schermata dei domini verificati](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN in Azure Active Directory

Gli utenti accedono ad Azure AD con il valore nell'attributo userPrincipalName.Users sign in to Azure AD with the value in their userPrincipalName attribute. 

Quando si usa Azure AD insieme ad Active Directory locale, gli account utente vengono sincronizzati tramite il servizio Azure AD Connect.When you use Azure AD in conjunction with your on-premises Active Directory, user accounts are synchronized by using the Azure AD Connect service. Per impostazione predefinita, la procedura guidata di Azure AD Connect usa l'attributo userPrincipalName di Active Directory locale come UPN in Azure AD. È possibile modificarlo in un attributo diverso in un'installazione personalizzata.

È importante disporre di un processo definito quando si aggiorna un nome dell'entità utente (UPN) di un singolo utente o per l'intera organizzazione. 

Vedere problemi noti e soluzioni alternative in questo documento.

Quando si sincronizzano gli account utente da Active Directory ad Azure AD, verificare che gli UPN in Active Directory eserino a domini verificati in Azure AD.

![Screenshot dei domini verificati](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Se il valore dell'attributo userPrincipalName non corrisponde a un dominio verificato in Azure AD, il processo di sincronizzazione sostituisce il suffisso con un valore onmicrosoft.com predefinito.


### <a name="roll-out-bulk-upn-changes"></a>Implementare modifiche bulk UPN

Seguire le [procedure consigliate per un progetto pilota](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) per le modifiche di tipo UPN di massa. Disporre inoltre di un piano di rollback testato per il ripristino degli UPN se si rilevano problemi che non possono essere risolti rapidamente. Una volta che il progetto pilota è in esecuzione, puoi iniziare a scegliere come target piccoli set di utenti con vari ruoli organizzativi e i relativi set specifici di app o dispositivi.

Passando attraverso questo primo sottoinsieme di utenti vi darà una buona idea di ciò che gli utenti dovrebbero aspettarsi come parte della modifica. Includere queste informazioni nelle comunicazioni degli utenti.

Creare una procedura definita per la modifica degli UPN per singoli utenti come parte delle normali operazioni. Si consiglia di avere una procedura testata che include la documentazione sui problemi noti e soluzioni alternative.

Nelle sezioni seguenti vengono descritti in dettaglio i potenziali problemi noti e le soluzioni alternative quando vengono modificati gli UPN.

## <a name="apps-known-issues-and-workarounds"></a>Problemi noti delle app e soluzioni alternative

Le applicazioni [SaaS (Software as a Service)](https://azure.microsoft.com/overview/what-is-saas/) e Line of Business (LoB) spesso si basano su UPN per trovare utenti e archiviare le informazioni del profilo utente, inclusi i ruoli. Le applicazioni che usano il [provisioning Just-in-Time](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) per creare un profilo utente quando gli utenti accedono all'app per la prima volta possono essere interessate dalle modifiche UPN.

**Problema noto**<br>
La modifica dell'UPN di un utente potrebbe interrompere la relazione tra l'utente di Azure AD e il profilo utente creato nell'applicazione. Se l'applicazione utilizza il [provisioning Just-in-Time,](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)potrebbe creare un nuovo profilo utente. Ciò richiederà all'amministratore dell'applicazione di apportare modifiche manuali per correggere questa relazione.

**Soluzione**<br>
[Il provisioning automatico degli](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) utenti di Azure AD consente di creare, gestire e rimuovere automaticamente le identità utente nelle applicazioni cloud supportate. La configurazione del provisioning automatico degli utenti nelle applicazioni aggiorna automaticamente gli UPN nelle applicazioni. Testare le applicazioni come parte dell'implementazione progressiva per verificare che non siano interessate dalle modifiche UPN.
Se si è uno sviluppatore, è consigliabile [aggiungere il supporto SCIM all'applicazione](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) per abilitare il provisioning automatico degli utenti da Azure Active Directory.If you are a developer, consider adding SCIM support to your application to enable automatic user provisioning from Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Problemi noti dei dispositivi gestiti e soluzioni alternative

Portando i dispositivi in [Azure AD,](https://docs.microsoft.com/azure/active-directory/devices/overview)si massimizza la produttività degli utenti tramite Single Sign-On (SSO) nel cloud e nelle risorse locali.

### <a name="azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD

[I](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) dispositivi aggiunti ad Azure AD vengono aggiunti direttamente ad Azure AD e consentono agli utenti di accedere al dispositivo usando l'identità dell'organizzazione.

**Problemi noti** <br>
Gli utenti potrebbero riscontrare problemi single sign-on con le applicazioni che dipendono da Azure AD per l'autenticazione.

**Soluzione** <br>
Concedere tempo sufficiente per la sincronizzazione della modifica UPN con Azure AD. Dopo aver verificato che il nuovo UPN si rifletta sul portale di Azure AD, chiedere all'utente di selezionare il riquadro "Altro utente" per accedere con il nuovo UPN. È anche possibile verificare tramite [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Dopo aver eseguito l'accesso con il nuovo UPN, i riferimenti al vecchio UPN potrebbero essere ancora visualizzati nell'impostazione di Windows "Accedi all'ambiente aziendale o dell'istituto di istruzione".

![Screenshot dei domini verificati](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti all'identità ibrida di Azure AD

[I](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) dispositivi aggiunti ad Azure AD ibrido vengono aggiunti ad Active Directory e Azure AD. È possibile implementare l'aggiunta ad Azure AD ibrido se l'ambiente ha un footprint di Active Directory locale e si vuole anche trarre vantaggio dalle funzionalità fornite da Azure AD.

**Problemi noti** 

I dispositivi aggiunti ad Azure AD di Azure AD di Windows 10 potrebbero verificarsi riavvii imprevisti e problemi di accesso.

Se gli utenti accedono a Windows prima che il nuovo UPN sia stato sincronizzato con Azure AD o continuino a usare una sessione di Windows esistente, potrebbero riscontrare problemi di Single Sign-On con le applicazioni che usano Azure AD per l'autenticazione se l'accesso condizionale è stato configurato per imporre l'uso di dispositivi aggiunti ibridi per accedere alle risorse. 

Inoltre, verrà visualizzato il seguente messaggio, forzando un riavvio dopo un minuto. 

"Il PC si riavvierà automaticamente in un minuto. Windows ha incontrato un problema e deve essere riavviato. Si dovrebbe chiudere questo messaggio ora e salvare il vostro lavoro".

**Soluzione** 

Il dispositivo deve essere disgiunto da Azure AD e riavviato. Dopo il riavvio, il dispositivo si aggiungerà automaticamente ad Azure AD e l'utente deve accedere usando il nuovo UPN selezionando il riquadro "Altro utente". Per annullare l'aggiunta di un dispositivo da Azure AD, eseguire il comando seguente al prompt dei comandi:To unjoin a device from Azure AD, run the following command at a command prompt:

**dsregcmd /leave**

L'utente dovrà [registrarsi nuovamente](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) per Windows Hello for Business se viene usato. Dispositivi Windows 7 e 8.1 non sono interessati da questo problema dopo le modifiche UPN.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative di Microsoft Authenticator

L'organizzazione potrebbe richiedere l'uso [dell'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) per accedere alle applicazioni e ai dati dell'organizzazione. Anche se nell'app potrebbe essere visualizzato un nome utente, l'account non è configurato per funzionare come metodo di verifica finché l'utente non completa il processo di registrazione.

[L'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) ha quattro funzioni principali:

* Autenticazione a più fattori tramite una notifica push o un codice di verificaMulti-factor authentication via a push notification or verification code

* Agire come broker di autenticazione su dispositivi iOS e Android per fornire single sign-on per le applicazioni che [utilizzano l'autenticazione brokera](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)

* Registrazione del dispositivo (noto anche come aggiunta all'area di lavoro) in Azure AD, che è un requisito per altre funzionalità come Intune App Protection e Device Enrolment/Management,

* Accesso telefonico, che richiede l'autenticazione a più fattori e la registrazione del dispositivo.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authentication con dispositivi Android

L'app Microsoft Authenticator offre un'opzione di verifica fuori banda. Invece di effettuare una telefonata automatica o un SMS all'utente durante l'accesso, [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) invia una notifica all'app Microsoft Authenticator sullo smartphone o tablet dell'utente. L'utente tocca semplicemente Approva (o immette un PIN o biometrico e tocca "Autentica") nell'app per completare l'accesso.

**Problemi noti** 

Quando si modifica l'UPN di un utente, il vecchio UPN viene ancora visualizzato nell'account utente e potrebbe non essere ricevuta una notifica. [I codici di verifica](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) continuano a funzionare.

**Soluzione**

Se viene ricevuta una notifica, indicare all'utente di ignorare la notifica, aprire l'app Authenticator, toccare l'opzione "Controlla notifiche" e approvare il prompt di autenticazione a più fattori. Dopo questo, l'UPN visualizzato sull'account verrà aggiornato. Si noti che l'UPN aggiornato potrebbe essere visualizzato come un nuovo account, ciò è dovuto ad altre funzionalità Authenticator in uso. Per ulteriori informazioni, fare riferimento ai problemi noti aggiuntivi in questo articolo.

### <a name="brokered-authentication"></a>Autenticazione negoziata

Su broker Android e iOS come Microsoft Authenticator abilitare:

* Single Sign-On (SSO): gli utenti non dovranno eseguire l'accesso a ogni applicazione.

* Identificazione del dispositivo: il broker accede al certificato del dispositivo creato nel dispositivo quando è stato aggiunto all'area di lavoro.

* Verifica dell'identificazione dell'applicazione - Quando un'applicazione chiama il broker, passa l'URL di reindirizzamento e il broker lo verifica.

Inoltre, consente alle applicazioni di partecipare a funzionalità più avanzate, ad esempio [l'accesso condizionale,](https://docs.microsoft.com/azure/active-directory/conditional-access/)e supporta gli scenari di [Microsoft Intune.](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)

**Problemi noti**<br>
All'utente vengono presentate richieste di autenticazione più interattive nelle nuove applicazioni che utilizzano l'accesso assistito dal broker a causa di una mancata corrispondenza tra i login_hint passati dall'applicazione e l'UPN archiviato nel broker.

**Soluzione** <br> L'utente deve rimuovere manualmente l'account da Microsoft Authenticator e avviare un nuovo accesso da un'applicazione assistita da broker. L'account verrà aggiunto automaticamente dopo l'autenticazione iniziale.

### <a name="device-registration"></a>Registrazione del dispositivo

L'app Microsoft Authenticator è responsabile della registrazione del dispositivo in Azure AD. La registrazione del dispositivo consente al dispositivo di eseguire l'autenticazione in Azure AD ed è un requisito per gli scenari seguenti:Device registration allows the device to authenticate to Azure AD and is a requirement for the following scenarios:

* Protezione app Intune

* Registrazione dei dispositivi IntuneIntune Device Enrollment

* Accesso telefonico

**Problemi noti**<br>
Quando si modifica l'UPN, un nuovo account con il nuovo UPN viene visualizzato elencato nell'app Microsoft Authenticator, mentre l'account con il vecchio UPN è ancora elencato. Inoltre, il vecchio UPN viene visualizzato nella sezione Registrazione dispositivo nelle impostazioni dell'app. Non vi è alcuna modifica nella normale funzionalità di registrazione del dispositivo o gli scenari dipendenti.

**Soluzione** <br> Per rimuovere tutti i riferimenti al vecchio UPN nell'app Microsoft Authenticator, indicare all'utente di rimuovere manualmente sia gli account vecchi che quelli nuovi da Microsoft Authenticator, registrarsi nuovamente per l'autenticazione a più fattori e aggiungere nuovamente il dispositivo.

### <a name="phone-sign-in"></a>Accesso telefonico

L'accesso telefonico consente agli utenti di accedere ad Azure AD senza una password. To enable phone sign-in, the user needs to register for MFA using the Authenticator app and then enable phone sign-in directly on Authenticator. Come parte della configurazione, il dispositivo si registra con Azure AD.

**Problemi noti** <br>
Gli utenti non sono in grado di utilizzare l'accesso telefonico perché non ricevono alcuna notifica. Se l'utente tocca Controlla notifiche, viene visualizzato un errore.

**Soluzione**<br>
L'utente deve selezionare il menu a discesa nell'account abilitato per l'accesso telefonico e selezionare Disabilita accesso telefonico. Se lo si desidera, l'accesso telefonico può essere abilitato di nuovo.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative della chiave di sicurezza (FIDO2)

**Problemi noti** <br>
Quando più utenti sono registrati sulla stessa chiave, la schermata di accesso mostra una pagina di selezione dell'account in cui viene visualizzato il vecchio UPN. Gli invii che utilizzano le chiavi di sicurezza non sono interessati dalle modifiche UPN.  

**Soluzione**<br>
Per rimuovere i riferimenti ai vecchi UPN, gli utenti devono reimpostare la chiave di [protezione e registrare nuovamente](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative di OneDrive

Gli utenti di OneDrive sono noti per verificarsi problemi dopo le modifiche UPN. Per ulteriori informazioni, vedere [Come le modifiche upN influiscono sull'URL di OneDrive e sulle caratteristiche di OneDrive.](https://docs.microsoft.com/onedrive/upn-changes)

## <a name="next-steps"></a>Passaggi successivi

Vedere queste risorse:See these resources:
* [Azure AD Connect: Concetti relativi alla progettazione](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Popolamento di UserPrincipalName di Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Token ID piattaforma identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
