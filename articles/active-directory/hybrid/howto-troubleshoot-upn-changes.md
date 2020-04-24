---
title: Pianificare e risolvere i problemi delle modifiche all'UPN (User Principal Name) di Azure
description: Informazioni sui problemi noti e sulle mitigazioni per le modifiche UPN
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
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Pianificare e risolvere i problemi relativi alle modifiche al nome dell'entità utente in Azure Active Directory

Un nome dell'entità utente (UPN) è un attributo che è uno standard di comunicazione Internet per gli account utente. Un UPN è costituito da un prefisso UPN (il nome dell'account utente) e un suffisso UPN (nome di dominio DNS). Il prefisso viene unito al suffisso usando il simbolo "@". Ad esempio, someone@example.com Un UPN deve essere univoco tra tutti gli oggetti entità di sicurezza in una foresta di directory. 

> [!NOTE]
> Per gli sviluppatori, è consigliabile usare l'objectID utente come identificatore non modificabile, anziché UPN. Se le applicazioni usano attualmente UPN, è consigliabile impostare l'UPN in modo che corrisponda all'indirizzo di posta elettronica primario dell'utente per migliorare l'esperienza.<br> **In un ambiente ibrido è importante che l'UPN per un utente sia identico nella directory locale e in Azure Active Directory**.

**Questo articolo presuppone che si usi UPN come identificatore utente. Si occupa della pianificazione delle modifiche UPN e del ripristino da problemi che possono derivare da modifiche UPN.**

## <a name="learn-about-upns-and-upn-changes"></a>Informazioni sulle modifiche di UPN e UPN
Le pagine di accesso spesso richiedono agli utenti di immettere l'indirizzo di posta elettronica quando il valore richiesto è effettivamente il nome UPN. Pertanto, è necessario assicurarsi di modificare l'UPN degli utenti in qualsiasi momento in cui viene modificato l'indirizzo di posta elettronica primario.

Gli indirizzi di posta elettronica primari degli utenti possono cambiare per diversi motivi:

* personalizzazione della società

* dipendenti che passano a diverse divisioni aziendali 

* fusioni e acquisizioni

* modifiche al nome dei dipendenti

### <a name="types-of-upn-changes"></a>Tipi di modifiche UPN

È possibile modificare un UPN cambiando il prefisso, il suffisso o entrambi.

* **Modifica del prefisso**.

   *  Ad esempio, se il nome di una persona è cambiato, è possibile modificare il nome dell'account:  
BSimon@contoso.com aBJohnson@contoso.com

   * È anche possibile modificare lo standard aziendale per i prefissi:  
Bsimon@contoso.com aBritta.Simon@contoso.com

* **Modifica del suffisso**. <br>

    Ad esempio, se un utente ha modificato le divisioni, è possibile modificare il dominio: 

   * Da Britta.Simon@contoso.com a Britta.Simon@contosolabs.com <br>
     Oppure<br>
    * Da Britta.Simon@corp.contoso.com a Britta.Simon@labs.contoso.com 

Modificare l'UPN dell'utente ogni volta che viene aggiornato l'indirizzo di posta elettronica principale di un utente. Indipendentemente dal motivo della modifica del messaggio di posta elettronica, è necessario aggiornare sempre il nome UPN in modo che corrisponda.

Durante la sincronizzazione iniziale da Active Directory a Azure AD, assicurarsi che i messaggi di posta elettronica degli utenti siano identici a quelli di UPN.

### <a name="upns-in-active-directory"></a>UPN in Active Directory

In Active Directory il suffisso UPN predefinito è il nome DNS del dominio in cui è stato creato l'account utente. Nella maggior parte dei casi, si tratta del nome di dominio registrato come dominio dell'organizzazione su Internet. Se si crea l'account utente nel dominio contoso.com, l'UPN predefinito è

username@contoso.com

 Tuttavia, è possibile [aggiungere altri suffissi UPN](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) usando Active Directory domini e trust. 

Ad esempio, è possibile aggiungere labs.contoso.com e fare in modo che i UPN e i messaggi di posta elettronica degli utenti riflettano. Quindi, diventeranno

username@labs.contoso.com.

>[!IMPORTANT]
> Se UPN in Active Directory e Azure Active Directory non corrispondono, si verificano problemi. Se si [modifica il suffisso in Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain), è necessario assicurarsi che sia stato [aggiunto e verificato](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)un nome di dominio personalizzato corrispondente nel Azure ad. 

![Screenshot dei domini verificati](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN in Azure Active Directory

Gli utenti possono accedere a Azure AD con il valore nell'attributo userPrincipalName. 

Quando si usa Azure AD insieme al Active Directory locale, gli account utente vengono sincronizzati tramite il servizio Azure AD Connect. Per impostazione predefinita, la procedura guidata Azure AD Connect usa l'attributo userPrincipalName della Active Directory locale come UPN in Azure AD. È possibile modificarlo in un attributo diverso in un'installazione personalizzata.

È importante disporre di un processo definito quando si aggiorna un nome dell'entità utente (UPN) di un singolo utente o per l'intera organizzazione. 

Vedere i problemi noti e le soluzioni alternative in questo documento.

Quando si esegue la sincronizzazione degli account utente da Active Directory a Azure AD, assicurarsi che UPN in Active Directory venga eseguito il mapping ai domini verificati in Azure AD.

![Screenshot dei domini verificati](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Se il valore dell'attributo userPrincipalName non corrisponde a un dominio verificato in Azure AD, il processo di sincronizzazione sostituisce il suffisso con un valore default. onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Modifiche bulk UPN di rollup

Seguire le [procedure consigliate per un progetto pilota](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) per le modifiche bulk UPN. Eseguire anche un piano di rollback testato per ripristinare UPN se si riscontrano problemi che non possono essere risolti rapidamente. Una volta eseguito il progetto pilota, è possibile iniziare a usare piccoli set di utenti con vari ruoli aziendali e con i relativi set di app o dispositivi specifici.

Il primo sottoinsieme di utenti fornirà una panoramica di ciò che gli utenti dovrebbero aspettarsi come parte della modifica. Includere queste informazioni sulle comunicazioni utente.

Creare una procedura definita per la modifica di UPN nei singoli utenti come parte delle normali operazioni. Si consiglia di eseguire una procedura testata che includa la documentazione relativa ai problemi noti e alle soluzioni alternative.

Le sezioni seguenti illustrano in dettaglio i potenziali problemi noti e le soluzioni alternative quando UPN vengono modificati.

## <a name="apps-known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative per le app

Le applicazioni [SaaS (software as a Service)](https://azure.microsoft.com/overview/what-is-saas/) e line-of-business (LOB) si basano spesso su UPN per trovare gli utenti e archiviare le informazioni sul profilo utente, inclusi i ruoli. Applicazioni che usano il [provisioning just-in-Time](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) per creare un profilo utente quando l'utente accede all'app per la prima volta può essere influenzato da modifiche UPN.

**Problema noto**<br>
La modifica dell'UPN di un utente potrebbe interrompere la relazione tra l'utente Azure AD e il profilo utente creato nell'applicazione. Se l'applicazione usa il [provisioning just-in-Time](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning), potrebbe creare un nuovo profilo utente. Questa operazione richiederà all'amministratore dell'applicazione di apportare modifiche manuali per correggere questa relazione.

**Soluzione alternativa**<br>
[Azure ad provisioning utenti automatizzato](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) consente di creare, gestire e rimuovere automaticamente le identità utente nelle applicazioni cloud supportate. La configurazione del provisioning utenti automatizzato nelle applicazioni aggiorna automaticamente UPN nelle applicazioni. Testare le applicazioni come parte dell'implementazione progressiva per verificare che non siano interessate dalle modifiche UPN.
Se si è uno sviluppatore, è consigliabile [aggiungere il supporto scim all'applicazione](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) per abilitare il provisioning utenti automatico dal Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative per i dispositivi gestiti

Grazie [alla Azure ad dei dispositivi](https://docs.microsoft.com/azure/active-directory/devices/overview), è possibile massimizzare la produttività degli utenti tramite Single Sign-on (SSO) tra le risorse cloud e locali.

### <a name="azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD

[Azure ad](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) i dispositivi aggiunti vengono aggiunti direttamente al Azure ad e consentono agli utenti di accedere al dispositivo usando l'identità dell'organizzazione.

**Problemi noti** <br>
È possibile che gli utenti riscontrino Single Sign-On problemi con le applicazioni che dipendono da Azure AD per l'autenticazione.

**Soluzione alternativa** <br>
Attendere un tempo sufficiente per la sincronizzazione della modifica dell'UPN con Azure AD. Una volta verificato che il nuovo UPN viene riflesso nel portale di Azure AD, richiedere all'utente di selezionare il riquadro "altro utente" per accedere con il nuovo UPN. È anche possibile verificare tramite [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Dopo aver eseguito l'accesso con il nuovo UPN, i riferimenti all'UPN precedente potrebbero essere ancora visualizzati nell'impostazione di Windows "accedi all'ufficio o all'Istituto di istruzione".

![Screenshot dei domini verificati](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti all'identità ibrida di Azure AD

[Azure ad ibrido dispositivi aggiunti](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) vengono aggiunti a Active Directory e Azure ad. È possibile implementare Azure AD ibrido join se l'ambiente ha un footprint di Active Directory locale e si vuole anche sfruttare le funzionalità fornite da Azure AD.

**Problemi noti** 

Per i dispositivi Windows 10 Azure AD ibrido aggiunti è probabile che si verifichino riavvii imprevisti e problemi di accesso.

Se gli utenti accedono a Windows prima che il nuovo UPN venga sincronizzato con Azure AD o continuino a usare una sessione di Windows esistente, potrebbero verificarsi Single Sign-On problemi con le applicazioni che usano Azure AD per l'autenticazione se l'accesso condizionale è stato configurato per imporre l'uso di dispositivi aggiunti ibridi per accedere alle risorse. 

Viene inoltre visualizzato il messaggio seguente che impone un riavvio dopo un minuto. 

"Il PC verrà riavviato automaticamente in un minuto. Windows ha avuto un problema e deve essere riavviato. È necessario chiudere il messaggio ora e salvare il lavoro ".

**Soluzione alternativa** 

È necessario separare il dispositivo dal Azure AD e riavviarlo. Dopo il riavvio, il dispositivo si unirà automaticamente Azure AD e l'utente deve eseguire l'accesso con il nuovo UPN selezionando il riquadro "altro utente". Per separare un dispositivo da Azure AD, eseguire il comando seguente al prompt dei comandi:

**/Leave dsregcmd**

Se è in uso, l'utente dovrà eseguire di [nuovo la registrazione](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) a Windows Hello for business. I dispositivi Windows 7 e 8,1 non sono interessati da questo problema dopo le modifiche UPN.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator problemi noti e soluzioni alternative

L'organizzazione potrebbe richiedere l'uso dell' [app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) per accedere alle applicazioni e ai dati aziendali. Sebbene un nome utente possa essere visualizzato nell'app, l'account non è configurato per funzionare come metodo di verifica fino a quando l'utente non completa il processo di registrazione.

L' [app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) dispone di quattro funzioni principali:

* Autenticazione a più fattori tramite una notifica push o un codice di verifica

* Fungere da broker di autenticazione nei dispositivi iOS e Android per fornire Single Sign-On per le applicazioni che usano [l'autenticazione negoziata](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)

* Registrazione del dispositivo (anche nota come Workplace Join) per Azure AD, che è un requisito per altre funzionalità, ad esempio Protezione app di Intune e l'iscrizione/gestione del dispositivo,

* Accesso tramite telefono, che richiede l'autenticazione a più fattori e la registrazione del dispositivo.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authentication con dispositivi Android

L'app Microsoft Authenticator offre un'opzione di verifica fuori banda. Invece di effettuare una telefonata o un SMS automatizzato all'utente durante l'accesso, [multi-factor authentication (](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) multi-factor authentication) Invia una notifica all'app Microsoft Authenticator sullo smartphone o sul tablet dell'utente. L'utente tocca semplicemente approva (o immette un PIN o biometrico e tocca "Esegui autenticazione") nell'app per completare l'accesso.

**Problemi noti** 

Quando si modifica l'UPN di un utente, l'UPN precedente viene comunque visualizzato nell'account utente ed è possibile che non venga ricevuta una notifica. I [codici di verifica](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) continuano a funzionare.

**Soluzione alternativa**

Se viene ricevuta una notifica, indicare all'utente di ignorare la notifica, aprire l'app Authenticator, toccare l'opzione "Verifica le notifiche" e approvare la richiesta di autenticazione a più fattori. Successivamente, l'UPN visualizzato nell'account verrà aggiornato. Si noti che il nome UPN aggiornato potrebbe essere visualizzato come un nuovo account, questo è dovuto all'utilizzo di altre funzionalità di autenticazione. Per ulteriori informazioni, vedere i problemi noti aggiuntivi in questo articolo.

### <a name="brokered-authentication"></a>Autenticazione negoziata

Nei broker Android e iOS come Microsoft Authenticator abilitare:

* Single Sign-on (SSO): gli utenti non dovranno accedere a ogni applicazione.

* Identificazione del dispositivo: il broker accede al certificato del dispositivo creato sul dispositivo quando è stato aggiunto all'area di lavoro.

* Verifica dell'identificazione dell'applicazione: quando un'applicazione chiama il broker, passa l'URL di reindirizzamento e il broker lo verifica.

Consente inoltre alle applicazioni di partecipare a funzionalità più avanzate, ad esempio [l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/), e supporta [scenari Microsoft Intune](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps).

**Problemi noti**<br>
Viene visualizzato un messaggio di richiesta di autenticazione più interattivo per le nuove applicazioni che utilizzano l'accesso assistito da broker a causa di una mancata corrispondenza tra il login_hint passato dall'applicazione e il nome UPN archiviato nel Broker.

**Soluzione alternativa** <br> L'utente deve rimuovere manualmente l'account dal Microsoft Authenticator e avviare un nuovo accesso da un'applicazione assistita da broker. L'account verrà aggiunto automaticamente dopo l'autenticazione iniziale.

### <a name="device-registration"></a>Registrazione del dispositivo

L'app Microsoft Authenticator è responsabile della registrazione del dispositivo a Azure AD. La registrazione del dispositivo consente al dispositivo di eseguire l'autenticazione a Azure AD ed è un requisito per gli scenari seguenti:

* Protezione app Intune

* Registrazione del dispositivo Intune

* Accesso tramite telefono

**Problemi noti**<br>
Quando si modifica il nome UPN, viene visualizzato un nuovo account con il nuovo UPN nell'app Microsoft Authenticator, mentre l'account con l'UPN precedente è ancora elencato. Inoltre, l'UPN precedente viene visualizzato nella sezione registrazione del dispositivo nelle impostazioni dell'app. Non sono state apportate modifiche alla normale funzionalità di registrazione del dispositivo o agli scenari dipendenti.

**Soluzione alternativa** <br> Per rimuovere tutti i riferimenti all'UPN precedente nell'app Microsoft Authenticator, indicare all'utente di rimuovere manualmente i vecchi e i nuovi account da Microsoft Authenticator, ripetere la registrazione per l'autenticazione a più fattori e aggiungere nuovamente il dispositivo.

### <a name="phone-sign-in"></a>Accesso tramite telefono

L'accesso tramite telefono consente agli utenti di accedere a Azure AD senza password. Per abilitare l'accesso tramite telefono, l'utente deve registrarsi per l'autenticazione a più fattori usando l'app Authenticator e quindi abilitare l'accesso tramite telefono direttamente sull'autenticatore. Come parte della configurazione, il dispositivo esegue la registrazione con Azure AD.

**Problemi noti** <br>
Gli utenti non sono in grado di usare l'accesso tramite telefono perché non ricevono alcuna notifica. Se l'utente tocca Controlla le notifiche, viene ricevuto un errore.

**Soluzione alternativa**<br>
L'utente deve selezionare il menu a discesa nell'account abilitato per l'accesso tramite telefono e selezionare Disabilita l'accesso tramite telefono. Se lo si desidera, è possibile abilitare di nuovo l'accesso tramite telefono.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative per la chiave di sicurezza (FIDO2)

**Problemi noti** <br>
Quando più utenti sono registrati sulla stessa chiave, la schermata di accesso Mostra una pagina di selezione dell'account in cui viene visualizzato il nome UPN precedente. Gli accessi con chiavi di sicurezza non sono interessati dalle modifiche UPN.  

**Soluzione alternativa**<br>
Per rimuovere i riferimenti a UPN precedenti, gli utenti devono [reimpostare la chiave di sicurezza e ripetere la registrazione](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative OneDrive

Si noti che gli utenti di OneDrive riscontrano problemi dopo le modifiche UPN. Per ulteriori informazioni, vedere [come le modifiche UPN influiscono sull'URL OneDrive e sulle funzionalità di OneDrive](https://docs.microsoft.com/onedrive/upn-changes).

## <a name="next-steps"></a>Passaggi successivi

Vedere le risorse seguenti:
* [Azure AD Connect: Concetti relativi alla progettazione](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Popolamento di UserPrincipalName di Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Token ID piattaforma Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
