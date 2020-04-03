---
title: Pianificare una distribuzione dell'autenticazione senza password con Azure ADPlan a passwordless authentication deployment with Azure AD
description: Informazioni su come pianificare e distribuire un'implementazione dell'autenticazione senza password di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c9933e75a39af43af9e2745d5f7732d40027b34
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582482"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Pianificare una distribuzione dell'autenticazione senza password in Azure Active DirectoryPlan a passwordless authentication deployment in Azure Active Directory

> [!NOTE]
> Per creare una versione offline di questo piano di distribuzione, utilizzare la funzionalità Stampa su PDF del browser.

La maggior parte degli attacchi informatici inizia con un nome utente e una password compromessi. Le organizzazioni cercano di contrastare la minaccia richiedendo agli utenti di utilizzare uno dei seguenti approcci:

- Password lunghe
- Password complesse
- Modifiche frequenti delle password
- Multi-Factor Authentication (MFA)

La ricerca di Microsoft [mostra](https://aka.ms/passwordguidance) che questi sforzi infastidiscono gli utenti e aumentano i costi di supporto. Per ulteriori informazioni, consultate [Il tuo $word non ha importanza.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

### <a name="benefits-of-passwordless-authentication"></a>Vantaggi dell'autenticazione senza password

- **Maggiore sicurezza**. Riduci il rischio di attacchi di phishing e spray password rimuovendo le password come superficie di attacco.
-  **Migliore esperienza utente**. Offri agli utenti un modo pratico per accedere ai dati da qualsiasi luogo. Fornire un facile accesso ad applicazioni e servizi come Outlook, OneDrive o Office mentre è mobile.
-  **Solide intuizioni**. Ottieni informazioni dettagliate sulle attività senza password degli utenti con una registrazione e un controllo affidabili.

Con passwordless, la password viene sostituita con qualcosa che hai più qualcosa che sei o qualcosa che conosci. Ad esempio, Windows Hello for Business può usare un gesto biometrico come un volto o un'impronta digitale o un PIN specifico del dispositivo che non viene trasmesso in rete.

## <a name="passwordless-authentication-methods"></a>Metodi di autenticazione senza password
Microsoft offre tre opzioni di autenticazione senza password che coprono molti scenari. Questi metodi possono essere utilizzati in tandem:

- [Windows Hello for Business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) è ideale per gli utenti nei computer Windows dedicati.
- L'accesso con chiave di sicurezza con le chiavi di sicurezza [FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) è particolarmente utile per gli utenti che accedono a computer condivisi come i chioschi multimediali, in situazioni in cui l'uso dei telefoni è limitato e per le identità con privilegi elevati.
- L'accesso telefonico con [l'app Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) è utile per fornire un'opzione senza password agli utenti con dispositivi mobili. L'app Authenticator trasforma qualsiasi telefono iOS o Android in una credenziale forte e senza password consentendo agli utenti di accedere a qualsiasi piattaforma o browser. Gli utenti accedono ricevendo una notifica al telefono, che corrisponde a un numero visualizzato sullo schermo con quello del telefono e quindi utilizzando i dati biometrici o il PIN per confermare.

### <a name="passwordless-authentication-scenarios"></a>Scenari di autenticazione senza password

I metodi di autenticazione senza password di Microsoft consentono scenari diversi. Considerare le esigenze dell'organizzazione, i prerequisiti e le funzionalità di ogni metodo di autenticazione per selezionare la strategia di autenticazione senza password. È consigliabile che ogni organizzazione che usa dispositivi Windows 10 usi Windows Hello for Business. Aggiungere quindi l'accesso tramite telefono (con l'app Microsoft Authenticator) o le chiavi di sicurezza per altri scenari.

| Scenario | Autenticazione telefonica | Chiavi di sicurezza | Windows Hello for Business |
| --- | --- | --- | --- |
| **Accesso al computer:** <br> Dal dispositivo Windows 10 assegnato | **No** | **Sì** <br> Con il PIN biometrico, | **Sì**<br>con riconoscimento biometrico e o PIN |
| **Accesso al computer:** <br> Dal dispositivo Windows 10 condiviso | **No** | **Sì** <br> Con il PIN biometrico,  | **No** |
| **Accesso all'app Web**: <br>da un computer dedicato all'utente | **Sì** | **Sì** <br> L'accesso Single Sign-On fornito alle app è abilitato dall'accesso al computer | **Sì**<br> L'accesso Single Sign-On fornito alle app è abilitato dall'accesso al computer |
| **Accesso all'app Web**: <br> da un dispositivo mobile o non Windows | **Sì** | **No** | **No** |
| **Accesso al computer:** <br> Computer non Windows | **No** | **No** | **No** |

Per informazioni sulla selezione del metodo migliore per l'organizzazione, vedere [Decidere un metodo senza password](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#choose-a-passwordless-method).

## <a name="prerequisites"></a>Prerequisiti

Le organizzazioni devono soddisfare i prerequisiti seguenti prima di iniziare una distribuzione senza password:Organizations must meet the following prerequisites before beginning a passwordless deployment:

| Prerequisito | App di autenticazione | Chiavi di protezione FIDO2 |
| --- | --- | --- |
| [La registrazione combinata per l'autenticazione a più fattori di Azure e la reimpostazione della password self-service (SSPR)](howto-registration-mfa-sspr-combined.md) è abilitata (funzionalità di anteprima)Combined registration for Azure Multi-factor authentication and self-service password reset (SSPR) is enabled (preview feature) | √ | √ |
| [Gli utenti possono eseguire l'autenticazione a più fattori di AzureUsers can perform Azure Multi-factor authentication](howto-mfa-getstarted.md) | √ | √ |
| [Gli utenti sono stati registrati per l'autenticazione a più fattori di Azure e SSPRUsers have registered for Azure Multi-factor authentication and SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Gli utenti hanno registrato i propri dispositivi mobili in Azure Active DirectoryUsers have registered their mobile devices to Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 versione 1809 o successiva utilizzando un browser supportato come Microsoft Edge o Mozilla Firefox <br> (versione 67 o successiva). <br> Microsoft consiglia la *versione 1903 o successiva per*il supporto nativo . |   | √ |
| Chiavi di sicurezza FIDO2 compatibili. Assicurarsi di utilizzare un dispositivo di sicurezza FIDO2 [testato da Microsoft e verificato](howto-authentication-passwordless-enable.md) o un altro dispositivo di sicurezza FIDO2 compatibile. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Prerequisiti per Windows Hello for Business

I prerequisiti per Windows Hello dipendono fortemente dal fatto che si stia distribuendo in una configurazione locale, ibrida o solo cloud. Per ulteriori informazioni, vedere [l'elenco completo dei prerequisiti per Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Gli utenti registrano il metodo senza password come parte del flusso di registrazione dell'autenticazione a più fattori di Azure.Users register their passwordless method as a part of the Azure Multi-factor authentication registration flow. L'autenticazione a più fattori con un nome utente e una password insieme a un altro metodo registrato può essere utilizzata come fallback nel caso in cui non possa usare il telefono o la chiave di sicurezza in alcuni scenari.

### <a name="licensing"></a>Gestione delle licenze 
Non sono previsti costi aggiuntivi per l'autenticazione senza password, anche se alcuni prerequisiti possono richiedere un abbonamento premium. Per informazioni dettagliate sulle funzionalità e sulle licenze nella pagina delle licenze di [Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Sviluppare un piano

Considerare le esigenze aziendali e i casi d'uso per ogni metodo di autenticazione. Quindi selezionare il metodo che meglio si adatta alle proprie esigenze.

### <a name="use-cases"></a>Casi d'uso

Nella tabella seguente vengono descritti i casi d'uso da implementare durante questo progetto.

| Area | Descrizione |
| --- | --- |
| **Accesso** | L'accesso senza password è disponibile da un dispositivo aziendale o personale all'interno o all'esterno della rete aziendale. |
| **Controllo** | I dati di utilizzo sono a disposizione degli amministratori per il controllo in tempo quasi reale. <br> I dati di utilizzo vengono scaricati nei sistemi aziendali almeno ogni 29 giorni oppure viene utilizzato lo strumento SIEM. |
| **Governance** | Il ciclo di vita delle assegnazioni utente al metodo di autenticazione appropriato e ai gruppi associati viene definito e monitorato. |
| **Sicurezza** | L'accesso al metodo di autenticazione appropriato è controllato tramite assegnazioni di utenti e gruppi. <br> Solo gli utenti autorizzati possono utilizzare l'accesso senza password. |
| **Prestazioni** | Le sequenze temporali di propagazione delle assegnazioni di accesso sono documentate e monitorate. <br> I tempi di accesso vengono misurati per facilità d'uso. |
| **Esperienza utente** | Gli utenti sono a conoscenza della compatibilità mobile. <br> Gli utenti possono configurare l'accesso senza password dell'app Authenticator. |
| **Supporto** | Gli utenti sono a conoscenza di come trovare il supporto per i problemi di accesso senza password. |

### <a name="engage-the-right-stakeholders"></a>Coinvolgi le parti interessate giuste

Quando i progetti tecnologici falliscono, è in genere a causa di aspettative non corrispondenti su impatto, risultati e responsabilità. Per evitare queste insidie, [assicurati di coinvolgere le parti interessate giuste](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) e che i ruoli degli stakeholder nel progetto siano ben compresi.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di ogni nuovo servizio. Comunica in modo proattivo come cambieranno l'esperienza degli utenti, quando cambierà e come ottenere supporto in caso di problemi.

Le comunicazioni agli utenti finali devono includere le seguenti informazioni:

- [Abilitazione dell'esperienza combinata di registrazione della sicurezza](howto-authentication-passwordless-phone.md)
- [Download dell'app Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registrazione nell'app Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Accesso con il telefono](../user-help/user-help-auth-app-sign-in.md)

Microsoft fornisce modelli di [comunicazione](https://aka.ms/mfatemplates)per l'autenticazione a più fattori, [modelli](https://www.microsoft.com/download/details.aspx?id=56768)di comunicazione SSPR (Self-Service Password Reset) e la documentazione per [l'utente](../user-help/security-info-setup-signin.md) finale per la stesura delle comunicazioni. È possibile inviare gli utenti a [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) registrarsi direttamente selezionando i collegamenti Informazioni di **sicurezza** in tale pagina.

### <a name="plan-to-pilot"></a>Pianificare il pilotaggio

Quando si distribuisce l'autenticazione senza password, è necessario abilitare uno o più gruppi pilota. È possibile [creare gruppi](../fundamentals/active-directory-groups-create-azure-portal.md) in modo specifico per questo scopo. Aggiungere gli utenti che parteciperanno al progetto pilota ai gruppi. Quindi, abilitare nuovi metodi di autenticazione senza password per i gruppi selezionati.

I gruppi possono essere sincronizzati da una directory locale o da Azure AD. Quando si è soddisfatti dei risultati del progetto pilota, è possibile attivare l'autenticazione senza password per tutti gli utenti.

Vedere [Procedure consigliate per un progetto pilota](https://aka.ms/deploymentplans) nella pagina piani di distribuzione.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Pianificare l'autenticazione senza password con l'app Microsoft Authenticator

L'app Microsoft Authenticator è scaricabile gratuitamente da Google Play o dall'App Store di Apple. [Ulteriori informazioni sul download dell'app Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Chiedi agli utenti di scaricare l'app Microsoft Authenticator. e seguire le istruzioni per abilitare l'accesso al telefono. 

Trasforma qualsiasi telefono iOS o Android in una forte credenziale senza password. Gli utenti accedono a qualsiasi piattaforma o browser ricevendo una notifica al proprio telefono, che corrisponde a un numero visualizzato sullo schermo con quello del telefono e quindi utilizzando la biometria o un PIN per confermare. [Vedere i dettagli sul funzionamento dell'app Microsoft Authenticator.](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#microsoft-authenticator-app)

![accedere con l'app Authenticator](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Considerazioni tecniche per l'app Microsoft Authenticator

**Integrazione** con ADFS: quando un utente abilita le credenziali senza password di Microsoft Authenticator, l'autenticazione per tale utente viene utilizzata per impostazione predefinita per l'invio di una notifica per l'approvazione. Agli utenti in un tenant ibrido viene impedito di essere indirizzati ad ADFS per l'accesso, a meno che non selezionino "Usa la password". Questo processo ignora inoltre tutti i criteri di accesso condizionale locali e i flussi di autenticazione pass-through. Tuttavia, se viene specificato un *login_hint,* l'utente viene inoltrato ad ADFS e ignorare l'opzione per utilizzare le credenziali senza password.

Server di autenticazione a più fattori di **Azure:** gli utenti finali abilitati per l'autenticazione a più fattori tramite il server Azure MFA locale di un'organizzazione possono creare e usare una singola credenziale di accesso del telefono senza password. Se l'utente tenta di aggiornare più installazioni (5 o più) di Microsoft Authenticator con la credenziale, questa modifica potrebbe generare un errore.

**Registrazione del dispositivo:** per usare l'app Authenticator per l'autenticazione senza password, il dispositivo deve essere registrato nel tenant di Azure AD e non può essere un dispositivo condiviso. Un dispositivo può essere registrato solo in un singolo tenant. Questo limite indica che per l'accesso tramite l'autenticatore è supportato un solo account aziendale o dell'istituto di istruzione.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Pianificare l'autenticazione senza password con le chiavi di protezione FIDO2
Sono disponibili tre tipi di distribuzioni di accesso senza password con le chiavi di sicurezza:There are three types of passwordless sign-in deployments available with security keys:

-    App Web di Azure Active Directory in un browser supportato
-    Dispositivi Windows 10 aggiunti ad Azure Active DirectoryAzure Active Directory Joined Windows 10 devices
-    Dispositivi Windows 10 aggiunti ad Azure Active Directory ibrido (anteprima)Hybrid Azure Active Directory Joined Windows 10 devices (preview)
     -    Fornisce l'accesso alle risorse basate su cloud e locali. Per altre informazioni sull'accesso alle risorse locali, vedere SSO alle risorse locali usando le chiavi FIDOP2For more information about access [to on-premises resources, see SSO to on-premises resources using FIDOP2 keys](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

È necessario attivare le chiavi di **protezione FIDO2 compatibili**. Microsoft ha annunciato [partnership chiave con i fornitori chiave FIDO2](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

Per le app Web di Azure AD e i **dispositivi aggiunti a Windows di Azure AD:**

-    Windows 10 versione 1809 o successiva utilizzando un browser supportato come Microsoft Edge o Mozilla Firefox (versione 67 o successiva). 
-    Windows 10 versione 1809 supporta l'accesso FIDO2 e potrebbe richiedere la distribuzione del software del produttore della chiave FIDO2. Si consiglia di utilizzare la versione 1903 o successiva. 

**Per i dispositivi aggiunti al dominio di Azure Active Directory ibrido:For Hybrid Azure Active Directory Domain Joined devices:** 
-    Windows 10 Insider build 18945 o versione successiva
-    Server di dominio completamente con patch che eseguono Windows Server 2016 o 2019.
-    Versione più recente di Azure AD Connect

Per un elenco completo dei requisiti, vedere Abilitare l'accesso alla chiave di sicurezza senza password per i [dispositivi Windows 10 con Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)


### <a name="security-key-life-cycle"></a>Ciclo di vita della chiave di sicurezza

Le chiavi di sicurezza consentono l'accesso alle risorse ed è consigliabile pianificare la gestione di tali dispositivi fisici.

1. **Distribuzione delle chiavi**: pianificare come eseguire il provisioning delle chiavi nell'organizzazione. È possibile disporre di un processo di provisioning centralizzato o consentire agli utenti finali di acquistare chiavi compatibili con FIDO 2.0.
1. **Attivazione chiave**: Gli utenti finali devono attivare automaticamente la chiave di sicurezza. Gli utenti finali registrano le loro chiavi di sicurezza [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) al primo utilizzo e abilitano il secondo fattore (PIN o biometrico).
1. **Disabilitazione di una chiave**: Mentre la funzionalità della chiave di protezione è nella fase di anteprima, non è possibile per un amministratore rimuovere una chiave da un account utente. L'utente deve rimuoverlo. In caso di smarrimento o furto di una chiave:
   1. Rimuovere l'utente da qualsiasi gruppo abilitato per l'autenticazione senza password.
   1. Verificare di aver rimosso la chiave come metodo di autenticazione.
   1. Emettere una nuova chiave. **Sostituzione delle chiavi**: Gli utenti possono abilitare due chiavi di sicurezza contemporaneamente. Quando si sostituisce una chiave di protezione, assicurarsi che l'utente abbia anche rimosso la chiave da sostituire.

### <a name="enable-windows-10-support"></a>Abilitare il supporto per Windows 10Enable Windows 10 support

L'abilitazione dell'accesso a Windows 10 con le chiavi di sicurezza FIDO2 richiede l'abilitazione della funzionalità del provider di credenziali in Windows 10. Selezionare una delle opzioni seguenti:

- [Abilitare il provider di credenziali con IntuneEnable credential provider with Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - La distribuzione di Intune è l'opzione consigliata.
- [Abilitare il provider di credenziali con un pacchetto di provisioningEnable credential provider with a provisioning package](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Se la distribuzione di Intune non è possibile, gli amministratori devono distribuire un pacchetto in ogni computer per abilitare la funzionalità del provider di credenziali. L'installazione del pacchetto può essere eseguita da una delle seguenti opzioni:
      - Criteri di gruppo o Gestione configurazione
      - Installazione locale in un computer Windows 10
- [Abilitare il provider di credenziali con Criteri di gruppoEnable credential provider with Group Policy](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Supportato solo per i dispositivi aggiunti ad Azure AD ibridi.

#### <a name="enable-on-premises-integration"></a>Abilitare l'integrazione localeEnable on-premises integration

Per abilitare l'accesso alle risorse locali, seguire la procedura descritta in Abilitare l'accesso alla chiave di sicurezza senza password per le [risorse locali (anteprima).](howto-authentication-passwordless-security-key-on-premises.md)

> [!IMPORTANT]
> Questi passaggi devono essere completati anche per tutti i dispositivi ibridi aggiunti ad Azure AD per usare le chiavi di sicurezza FIDO2 per l'accesso a Windows 10.These steps must also be completed for any hybrid Azure AD joined devices to use FIDO2 security keys for Windows 10 sign in.

### <a name="register-security-keys"></a>Registrare le chiavi di sicurezza

Gli utenti devono registrare la chiave di sicurezza in ogni computer Windows 10 aggiunto ad Azure Active Directory.

Per ulteriori informazioni, vedere [Registrazione utente e gestione delle chiavi di protezione FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Pianificare il controllo, la sicurezza e i test
La pianificazione del controllo che soddisfi i framework organizzativi e di conformità è una parte essenziale della distribuzione.

### <a name="auditing-passwordless"></a>Controllo senza password

Azure AD include report che forniscono informazioni tecniche e aziendali. Fai in modo che i proprietari di applicazioni aziendali e tecniche assumano la proprietà e utilizzino questi report in base ai requisiti dell'organizzazione.

La sezione Metodi di **autenticazione** all'interno del portale di Azure Active Directory consente agli amministratori di abilitare e gestire le impostazioni per le credenziali senza password.

Azure AD aggiunge voci ai log di controllo quando:Azure AD adds entries to the audit logs when:

- Un amministratore apporta modifiche nella sezione Metodi di autenticazione.
- Un utente apporta qualsiasi tipo di modifica alle proprie credenziali all'interno di Azure Active Directory.A user makes any kind of change to their credentials within Azure Active Directory.

Nella tabella seguente vengono forniti alcuni esempi di scenari di report tipici:The following table provides some examples of typical reporting scenarios:

|   | per gestire i rischi. | Aumentare la produttività | Governance e conformità |
| --- | --- | --- | --- |
| **Tipi di report** | Metodi di autenticazione: utenti registrati per la registrazione combinata della sicurezza | Metodi di autenticazione: utenti registrati per la notifica dell'app | Accessi: esaminare chi accede al tenant e come |
| **Azioni potenziali** | Utenti di destinazione non ancora registrati | Promuovere l'adozione dell'app Microsoft Authenticator o delle chiavi di sicurezza | Revocare l'accesso o applicare criteri di sicurezza aggiuntivi per gli amministratoriRevoke access or enforce additional security policies for admins |

**Azure AD mantiene la maggior parte dei dati** di controllo per 30 giorni e rende i dati disponibili tramite il portale di amministrazione di Azure o l'API da scaricare nei sistemi di analisi. Se è necessaria una conservazione più lunga, esportare e utilizzare i log in uno strumento SIEM, ad esempio [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk o Sumo Logic. [Ulteriori informazioni sulla visualizzazione dei report](../reports-monitoring/overview-reports.md)di accesso e utilizzo .

Gli utenti possono registrare e gestire [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)le proprie credenziali passando a . Questo collegamento indirizza gli utenti all'esperienza di gestione delle credenziali dell'utente finale abilitata tramite l'esperienza combinata di registrazione sSPR/Autenticazione a più fattori. Azure AD registra la registrazione dei dispositivi di sicurezza FIDO2 e le modifiche ai metodi di autenticazione da parte di un utente.

### <a name="plan-security"></a>Pianificare la sicurezza
Come parte di questo piano di implementazione, Microsoft consiglia di abilitare l'autenticazione senza password per tutti gli account amministratore con privilegi.

Quando gli utenti abilitano o disabilitano l'account su una chiave di sicurezza o reimpostano il secondo fattore per la chiave di sicurezza nei computer Windows 10, viene aggiunta una voce al registro di protezione e si trovano sotto i seguenti ID evento: *4670* e *5382*.

### <a name="plan-testing"></a>Pianificare i test

In ogni fase della distribuzione durante il test degli scenari e dell'adozione, verificare che i risultati siano quelli previsti.

#### <a name="testing-the-microsoft-authenticator-app"></a>Test dell'app Microsoft Authenticator

Di seguito sono riportati i test case di esempio per l'autenticazione senza password con l'app Microsoft Authenticator:

| Scenario | Risultati previsti |
| --- | --- |
| L'utente può registrare l'app Microsoft Authenticator | L'utente può registrare l'app da aka.ms/mysecurityinfo |
| L'utente può abilitare l'accesso telefonico | Accesso telefonico configurato per l'account di lavoro |
| L'utente può accedere a un'app con l'accesso telefonico | L'utente passa attraverso il flusso di accesso telefonico e raggiunge l'applicazione. |
| Testare la registrazione dell'accesso al telefono con rollback disattivando l'accesso senza password Microsoft Authenticator all'interno della schermata Metodi di autenticazione nel portale di Azure Active Directory | Gli utenti abilitati in precedenza non possono utilizzare l'accesso senza password da Microsoft Authenticator.Previously enabled users unable to use password-in from Microsoft Authenticator. |
| Rimozione dell'accesso al telefono dall'app Microsoft Authenticator | Account di lavoro non più disponibile in Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Test delle chiavi di sicurezzaTesting security keys

Di seguito sono riportati test case di esempio per l'autenticazione senza password con chiavi di sicurezza.

**Accesso FIDO senza password ai dispositivi Windows 10 aggiunti ad Azure Active Directory**

| Scenario | Risultati previsti |
| --- | --- |
| L'utente può registrare il dispositivo FIDO2 (1809) | L'utente può registrare il dispositivo FIDO2 utilizzando le opzioni di accesso > Impostazioni > Account > Chiave di protezione |
| L'utente può reimpostare il dispositivo FIDO2 (1809) | L'utente può reimpostare il dispositivo FIDO2 utilizzando il software del produttore |
| L'utente può accedere con il dispositivo FIDO2 (1809) | L'utente può selezionare Chiave di sicurezza nella finestra di accesso e accedere correttamente. |
| L'utente può registrare il dispositivo FIDO2 (1903) | L'utente può registrare il dispositivo FIDO2 nelle opzioni di accesso Impostazioni > Account > > Chiave di protezione |
| L'utente può reimpostare il dispositivo FIDO2 (1903) | L'utente può reimpostare il dispositivo FIDO2 in Impostazioni > Account > le opzioni di accesso > Chiave di protezione |
| L'utente può accedere con il dispositivo FIDO2 (1903) | L'utente può selezionare Chiave di sicurezza nella finestra di accesso e accedere correttamente. |

**Accesso FIDO senza password alle app Web di Azure AD**

| Scenario | Risultati previsti |
| --- | --- |
| L'utente può registrare il dispositivo FIDO2 presso aka.ms/mysecurityinfo utilizzando Microsoft Edge | La registrazione dovrebbe avere esito positivo |
| L'utente può registrare il dispositivo FIDO2 aka.ms/mysecurityinfo utilizzando Firefox | La registrazione dovrebbe avere esito positivo |
| L'utente può accedere a OneDrive online utilizzando il dispositivo FIDO2 utilizzando Microsoft Edge | L'accesso dovrebbe avere esito positivo |
| L'utente può accedere a OneDrive online utilizzando il dispositivo FIDO2 utilizzando Firefox | L'accesso dovrebbe avere esito positivo |
| Testare la registrazione del dispositivo FIDO2 eseguendo le chiavi di sicurezza FIDO2 all'interno della finestra Metodo di autenticazione nel portale di Azure Active DirectoryTest rolling FIDO2 device registration by turning off FIDO2 Security Keys within the Authentication method window in the Azure Active Directory portal | Agli utenti verrà richiesto di accedere utilizzando la chiave di sicurezza. Gli utenti accederanno correttamente e verrà visualizzato un errore: "I criteri aziendali richiedono l'utilizzo di un metodo diverso per l'accesso". Gli utenti devono quindi essere in grado di selezionare un metodo diverso e accedere correttamente. Chiudere la finestra e accedere di nuovo per verificare che non visualizzino lo stesso messaggio di errore. |

### <a name="plan-for-rollback"></a>Pianificare per il rollback

Anche se l'autenticazione senza password è una funzionalità leggera con un impatto minimo sugli utenti finali, potrebbe essere necessario eseguire il rollback.

Il rollback richiede che l'amministratore acceda al portale di Azure Active Directory, selezioni i metodi di autenticazione avanzata desiderati e modifichi l'opzione abilita **su No.** Questo processo disattiva la funzionalità senza password per tutti gli utenti.

Agli utenti che hanno già registrato dispositivi di sicurezza FIDO2 viene richiesto di utilizzare il dispositivo di sicurezza al successivo accesso e quindi viene visualizzato il seguente errore:

![scegliere un modo diverso per accedere](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Distribuire e risolvere i problemi relativi all'autenticazione senza password

Seguire i passaggi allineati al metodo scelto di seguito.

### <a name="required-administrative-roles"></a>Ruoli amministrativi necessari

| Azure AD Role | Descrizione |
| --- | --- |
| Amministratore globale|Ruolo con privilegi minimi in grado di implementare l'esperienza di registrazione combinata. |
| Amministratore dell'autenticazione | Ruolo con privilegi minimi in grado di implementare e gestire i metodi di autenticazione. |
| Utente | Ruolo con privilegi minimi per configurare l'app Authenticator nel dispositivo o per registrare il dispositivo con chiave di sicurezza per l'accesso Web o Windows 10.Least privileged role to configure Authenticator app on device, or to enroll security key device for web or Windows 10 sign-in. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Distribuire l'accesso telefonico con l'app Microsoft Authenticator

Seguire i passaggi descritti nell'articolo [Abilitare l'accesso senza password con l'app Microsoft Authenticator](howto-authentication-passwordless-phone.md) per abilitare l'app Microsoft Authenticator come metodo di autenticazione senza password nell'organizzazione.

### <a name="deploy-fido2-security-key-sign-in"></a>Distribuire l'accesso alla chiave di sicurezza FIDO2Deploy FIDO2 security key sign-in

Seguire i passaggi nell'articolo [Abilitare l'accesso alla chiave](howto-authentication-passwordless-security-key.md) di sicurezza senza password per Azure AD per abilitare le chiavi di sicurezza FIDO2 come metodi di autenticazione senza password.

### <a name="troubleshoot-phone-sign-in"></a>Risolvere i problemi di accesso al telefonoTroubleshoot phone sign-in

| Scenario | Soluzione |
| --- | --- |
| L'utente non può eseguire la registrazione combinata. | Assicurarsi che [la registrazione combinata](concept-registration-mfa-sspr-combined.md) sia abilitata. |
| L'utente non può abilitare l'app di autenticazione dell'accesso telefonico. | Verificare che l'utente sia nell'ambito della distribuzione. |
| L'utente NON rientra nell'ambito dell'autenticazione senza password, ma viene visualizzata l'opzione di accesso senza password, che non può completare. | Questo scenario si verifica quando l'utente ha abilitato l'accesso telefonico nell'applicazione prima della creazione del criterio. <br> *Per abilitare l'accesso*: Aggiungere l'utente all'ambito degli utenti abilitati per l'accesso senza password. <br> *Per bloccare l'accesso:* chiedi all'utente di rimuovere le credenziali dall'applicazione. |

### <a name="troubleshoot-security-key-sign-in"></a>Risolvere i problemi di accesso alla chiave di sicurezzaTroubleshoot security key sign-in

| Scenario | Soluzione |
| --- | --- |
| L'utente non può eseguire la registrazione combinata. | Assicurarsi che [la registrazione combinata](concept-registration-mfa-sspr-combined.md) sia abilitata. |
| L'utente non può aggiungere una chiave di sicurezza nelle impostazioni di [sicurezza.](https://aka.ms/mysecurityinfo) | Verificare che [le chiavi](howto-authentication-passwordless-security-key.md) di sicurezza siano abilitate. |
| L'utente non può aggiungere chiave di sicurezza nelle opzioni di accesso di Windows 10.User can't add security key in Windows 10 sign-in options. | [Verificare che le chiavi di sicurezza per l'accesso a Windows](howto-authentication-passwordless-enable.md) |
| **Messaggio di errore:** È stato rilevato che questo browser o sistema operativo non supporta le chiavi di protezione FIDO2. | I dispositivi di sicurezza FIDO2 senza password possono essere registrati solo nei browser supportati (Microsoft Edge, Firefox versione 67) su Windows 10 versione 1809 o successiva. |
| **Messaggio di errore:** I criteri aziendali richiedono l'utilizzo di un metodo diverso per l'accesso. | Le chiavi di sicurezza non sono atso che sono abilitate nel tenant. |
| Utente non è in grado di gestire la chiave di sicurezza in Windows 10 versione 1809 | La versione 1809 richiede l'utilizzo del software di gestione delle chiavi di protezione fornito dal fornitore della chiave FIDO2. Contattare il fornitore per assistenza. |
| Penso che la mia chiave di sicurezza FIDO2 potrebbe essere difettosa, come posso testarlo. | Passare [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)a , immettere le credenziali per un account di **+** test, collegare la chiave di sicurezza sospetto, selezionare il pulsante nella parte superiore destra dello schermo, fare clic su crea e passare attraverso il processo di creazione. Se si verifica un errore in questo scenario, il dispositivo potrebbe essere difettoso. |

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare le chiavi di sicurezza senza password per l'accesso per Azure ADEnable passwordless security keys for sign in for Azure AD](howto-authentication-passwordless-security-key.md)
- [Abilitare l'accesso senza password con l'app Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Altre informazioni sui metodi di autenticazione & informazioni dettagliate](howto-authentication-methods-usage-insights.md)

