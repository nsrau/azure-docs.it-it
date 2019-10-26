---
title: Completare una distribuzione di autenticazione senza password con Azure AD
description: Completa una Azure Active Directory distribuzione di autenticazione non con password
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6996b6163c1d5a2a4747093743a937dfd9eb7d4f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933112"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Completare una distribuzione di autenticazione non con password

La maggior parte di attacchi cibernetici inizia con un nome utente e una password rubati da qualcuno in un'organizzazione. 

Le organizzazioni provano a contrastare la minaccia richiedendo agli utenti di usare:

- Password lunghe
- Password complesse
- Modifiche frequenti della password
- Multi-Factor Authentication (MFA)

La ricerca di Microsoft [Mostra](https://aka.ms/passwordguidance) che questi sforzi infastidiscono gli utenti e instradano i costi di supporto. Per ulteriori informazioni, vedere la pagina relativa alla [PA $ $Word non è rilevante](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

La distribuzione dell'autenticazione con password offre i vantaggi seguenti:

- Maggiore sicurezza. Ridurre il rischio di attacchi di phishing e di spray per le password rimuovendo le password come superficie di attacco.
- Migliore esperienza utente. Offri agli utenti un modo pratico per accedere ai dati da qualsiasi luogo e ti permette di accedere facilmente a Outlook, OneDrive, Office e molto altro ancora.
- Informazioni affidabili. Ottieni informazioni dettagliate sulle attività senza password degli utenti con registrazione e controllo affidabili.

La password viene sostituita da un elemento più simile a quello di un utente o a un elemento noto. Ad esempio, in Windows Hello for business un movimento biometrico, ad esempio una faccia o un'impronta digitale, o un PIN specifico del dispositivo che non viene trasmesso in rete.

Microsoft offre tre opzioni di autenticazione con password che coprono molti scenari. Questi metodi possono essere usati in tandem. 

- [Windows Hello for business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) è la scelta migliore per gli utenti nei computer Windows dedicati.
- L'accesso alla chiave di sicurezza con [chiavi di sicurezza FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) è particolarmente utile per gli utenti che effettuano l'accesso a computer condivisi, ad esempio chioschi multimediali, situazioni in cui l'uso di telefoni è limitato e per le identità con privilegi elevati.
- L'accesso tramite telefono con l' [app Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) è utile per fornire un'opzione senza password agli utenti con dispositivi mobili. Ogni telefono iOS o Android viene trasformato in una credenziale complessa e senza password, consentendo agli utenti di accedere a qualsiasi piattaforma o browser. Gli utenti effettuano l'accesso ricevendo una notifica al telefono, abbinando un numero visualizzato sullo schermo a quello sul telefono e quindi usando i dati biometrici o il PIN per confermare.

## <a name="compare-passwordless-authentication-methods"></a>Confrontare metodi di autenticazione con password

I metodi di autenticazione con password di Microsoft abilitano scenari diversi. Prendere in considerazione le esigenze dell'organizzazione, i prerequisiti e le funzionalità di ogni metodo di autenticazione per selezionare la strategia di autenticazione con password. È consigliabile che ogni organizzazione che usa i dispositivi Windows 10 usi Windows Hello for business. Aggiungere quindi l'accesso tramite telefono (con l'app Microsoft Authenticator) o le chiavi di sicurezza per scenari aggiuntivi.

### <a name="passwordless-authentication-scenarios"></a>Scenari di autenticazione con password

| Scenario | Autenticazione tramite telefono | Chiavi di sicurezza | Windows Hello for Business |
| --- | --- | --- | --- |
| **Accesso del computer**: <br> Dal dispositivo Windows 10 assegnato | **No** | **Sì** <br> Con biometrico, PIN | **Sì**<br>con riconoscimento biometrico e PIN |
| **Accesso del computer**: <br> Dal dispositivo Windows 10 condiviso | **No** | **Sì** <br> Con biometrico, PIN  | **No** |
| **Accesso all'app Web**: <br>da un computer dedicato all'utente | **Sì** | **Sì** <br> Il Single Sign-On fornito alle app è abilitato dall'accesso del computer | **Sì**<br> Il Single Sign-On fornito alle app è abilitato dall'accesso del computer |
| **Accesso all'app Web**: <br> da un dispositivo mobile o non Windows | **Sì** | **No** | **No** |
| **Accesso del computer**: <br> Computer non Windows | **No** | **No** | **No** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Considerazioni tecniche per l'app Microsoft Authenticator

**Integrazione di ad FS** : quando un utente abilita le credenziali Microsoft Authenticator con password, l'autenticazione per tale utente viene impostata per impostazione predefinita sull'invio di una notifica per l'approvazione. Agli utenti di un tenant ibrido viene impedito di essere indirizzati ad ADFS per l'accesso, a meno che non selezioni "usa la password". Questo processo ignora anche tutti i criteri di accesso condizionale locali e i flussi di autenticazione pass-through. Tuttavia, se si specifica un login_hint, l'utente verrà inoltrato ad ADFS e ignorerà l'opzione per utilizzare le credenziali con password.

**Server** di autenticazione a più fattori di Azure: gli utenti finali abilitati per l'autenticazione a più fattori tramite il server multi-factor authentication locale di un'organizzazione possono comunque creare e usare una sola credenziale di accesso con telefono senza password Se l'utente tenta di aggiornare più installazioni (più di 5) di Microsoft Authenticator con le credenziali create, questa modifica può determinare un errore.

**Registrazione del dispositivo** : per usare l'app Authenticator per l'autenticazione con password, il dispositivo deve essere registrato nel tenant Azure ad e non può essere un dispositivo condiviso. Un dispositivo può essere registrato solo in un singolo tenant. Questo limite indica che è supportato un solo account aziendale o dell'Istituto di istruzione per l'accesso tramite telefono tramite l'app Authenticator.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare una distribuzione con password non è necessario che le organizzazioni soddisfino i seguenti prerequisiti.

| Prerequisito | App di autenticazione | Chiavi di sicurezza FIDO2 |
| --- | --- | --- |
| [Registrazione combinata per l'autenticazione a più fattori di Azure e la reimpostazione della password self-service (SSPR)](howto-registration-mfa-sspr-combined.md) abilitata (funzionalità di anteprima) | √ | √ |
| [Gli utenti possono eseguire l'autenticazione a più fattori di Azure](howto-mfa-getstarted.md) | √ | √ |
| [Utenti registrati per l'autenticazione a più fattori di Azure e SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Gli utenti hanno registrato i dispositivi mobili in Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 versione 1809 o successiva con un browser supportato come Microsoft Edge o Mozilla Firefox <br> (versione 67 o successiva). <br> *Microsoft consiglia la versione 1903 o una versione successiva per il supporto nativo*. |   | √ |
| Chiavi di sicurezza FIDO2 compatibili. Assicurarsi di usare un dispositivo di sicurezza FIDO2 [testato e verificato da Microsoft](howto-authentication-passwordless-enable.md) o un altro dispositivo di sicurezza FIDO2 compatibile. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Prerequisiti per Windows Hello for business

I prerequisiti per Windows Hello dipendono fortemente dal fatto che la distribuzione sia in una configurazione locale, ibrida o solo cloud. Per ulteriori informazioni, vedere l' [elenco completo dei prerequisiti per Windows Hello for business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Gli utenti registrano il proprio metodo con password come parte del flusso di registrazione dell'autenticazione a più fattori di Azure. L'autenticazione a più fattori con un nome utente e una password insieme a un altro metodo registrato può essere usata come fallback nel caso in cui non possano usare il telefono o la chiave di sicurezza in alcuni scenari.

### <a name="security-key-lifecycle"></a>Ciclo di vita delle chiavi di sicurezza

Le chiavi di sicurezza consentono l'accesso alle risorse ed è necessario pianificare la gestione di tali dispositivi fisici.

1. Distribuzione di chiavi: pianificare come si eseguirà il provisioning delle chiavi per l'organizzazione. È possibile disporre di un processo di provisioning centralizzato o consentire agli utenti finali di acquistare chiavi compatibili con FIDO 2,0.
1. Attivazione chiave: gli utenti finali devono attivare autonomamente la chiave di sicurezza. Gli utenti finali registrano le chiavi di sicurezza in [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) e abilitano il secondo fattore (PIN o biometrico) al primo utilizzo.
1. Disabilitazione di una chiave: mentre la funzionalità chiave di sicurezza si trova nella fase di anteprima, non è possibile che un amministratore rimuova una chiave da un account utente. L'utente deve rimuoverlo. Se un tasto viene smarrito o rubato:
   1. Rimuovere l'utente da qualsiasi gruppo abilitato per l'autenticazione senza password.
   1. Verificare che la chiave sia stata rimossa come metodo di autenticazione.
   1. Rilascia una nuova chiave.
1. Sostituzione della chiave: gli utenti possono abilitare contemporaneamente due chiavi di sicurezza. Quando si sostituisce una chiave di sicurezza, assicurarsi che l'utente abbia rimosso anche la chiave che viene sostituita.

### <a name="enable-windows-10-support"></a>Abilita supporto per Windows 10

Per abilitare l'accesso a Windows 10 con le chiavi di sicurezza di FIDO2 è necessario abilitare la funzionalità del provider di credenziali in Windows 10. Abilitarla in uno dei due modi seguenti:

- [Abilitare il provider di credenziali tramite la distribuzione di Intune di destinazione](howto-authentication-passwordless-security-key.md#enable-targeted-intune-deployment)
   - La distribuzione di Intune è l'opzione consigliata per Azure Active Directory macchine unite in join.
- [Abilitare il provider di credenziali tramite il pacchetto di provisioning](howto-authentication-passwordless-security-key.md#enable-credential-provider-via-provisioning-package)
   - Se non è possibile eseguire la distribuzione di Intune, gli amministratori devono distribuire un pacchetto in ogni computer per abilitare la funzionalità del provider di credenziali. L'installazione del pacchetto può essere eseguita tramite una delle opzioni seguenti:
      - Criteri di gruppo o System Center Configuration Manager (SCCM)
      - Installazione locale in un computer Windows 10

### <a name="register-security-keys"></a>Registrare le chiavi di sicurezza

Gli utenti devono registrare la propria chiave di sicurezza in ogni Azure Active Directory computer Windows 10 aggiunti.

Per altre informazioni, vedere [registrazione e gestione degli utenti delle chiavi di sicurezza FIDO2](howto-authentication-passwordless-security-key.md).

### <a name="licensing-for-passwordless-authentication"></a>Gestione delle licenze per l'autenticazione con password

Non sono previsti costi aggiuntivi per l'autenticazione senza password, sebbene alcuni prerequisiti possano richiedere una sottoscrizione Premium. Vedere le informazioni dettagliate sulle funzionalità e sulle licenze nella [pagina Azure Active Directory Licensing](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="develop-a-plan"></a>Sviluppare un piano

Prendere in considerazione le esigenze aziendali e i casi di utilizzo per ogni metodo di autenticazione. Selezionare quindi il metodo più adatto alle proprie esigenze.

### <a name="use-cases"></a>Casi d'uso

Nella tabella seguente vengono illustrati i casi d'uso da implementare durante il progetto.

| Area | Description |
| --- | --- |
| **Accedere** | L'accesso senza password è disponibile da un dispositivo aziendale o personale all'interno o all'esterno della rete aziendale. |
| **Controllo** | I dati di utilizzo sono disponibili agli amministratori per il controllo quasi in tempo reale. <br> I dati di utilizzo vengono scaricati nei sistemi aziendali almeno ogni 29 giorni oppure viene usato lo strumento SIEM. |
| **Governance** | Il ciclo di vita delle assegnazioni degli utenti al metodo di autenticazione appropriato e ai gruppi associati viene definito e monitorato. |
| **Sicurezza** | L'accesso al metodo di autenticazione appropriato viene controllato tramite le assegnazioni di utenti e gruppi. <br> Solo gli utenti autorizzati possono usare l'accesso senza password. |
| **Prestazioni** | Le sequenze temporali di propagazione dell'assegnazione di accesso vengono documentate e monitorate <br> Il tempo di accesso viene misurato per facilitarne l'uso. |
| **Esperienza utente** | Gli utenti sono consapevoli della compatibilità con i dispositivi mobili. <br> Gli utenti possono configurare l'accesso senza password dell'app Authenticator. |
| **Supporto** | Gli utenti sono consapevoli di come trovare il supporto per i problemi di accesso senza password. |

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, è in genere dovuto a una mancata corrispondenza delle aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) e che i ruoli stakeholder nel progetto siano ben noti.

### <a name="organization-communications"></a>Comunicazioni dell'organizzazione

La comunicazione è fondamentale per il successo di un nuovo servizio. Comunicare in modo proattivo il modo in cui l'esperienza degli utenti cambierà, quando verrà modificata e come ottenere supporto in caso di problemi.

Le comunicazioni agli utenti finali dovranno includere:

- [Abilitare l'esperienza di registrazione di sicurezza combinata](howto-authentication-passwordless-phone.md)
- [Download dell'app Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registrazione nell'app Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Accesso con il telefono](../user-help/user-help-auth-app-sign-in.md)

Microsoft fornisce [modelli di comunicazione](https://aka.ms/mfatemplates)a più fattori, [modelli di comunicazione](https://www.microsoft.com/download/details.aspx?id=56768)self-service per la reimpostazione della password (SSPR) e [documentazione dell'utente finale](../user-help/security-info-setup-signin.md) per facilitare la stesura delle comunicazioni. È possibile inviare utenti a [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) per la registrazione diretta selezionando i collegamenti info di sicurezza nella pagina.

### <a name="testing-passwordless"></a>Test delle password

In ogni fase della distribuzione, verificare che i risultati siano quelli previsti.

#### <a name="testing-the-microsoft-authenticator-app"></a>Test dell'app Microsoft Authenticator

Di seguito sono riportati i test case di esempio per l'autenticazione senza password con l'app Microsoft Authenticator

| Scenario | Risultati previsti |
| --- | --- |
| L'utente può registrare Microsoft Authenticator app | L'utente può registrare l'app da aka.ms/mysecurityinfo |
| L'utente può abilitare l'accesso tramite telefono | Accesso telefonico configurato per l'account di lavoro |
| L'utente può accedere a un'app con l'accesso tramite telefono | L'utente passa attraverso il flusso di accesso tramite telefono e raggiunge l'applicazione designata. |
| Eseguire il test del rollback della registrazione per l'accesso tramite telefono disattivando Microsoft Authenticator accesso senza password nella schermata metodi di autenticazione nel portale di Azure Active Directory | Gli utenti abilitati in precedenza non possono usare l'accesso senza password da Microsoft Authenticator. |
| Rimozione dell'accesso tramite telefono da Microsoft Authenticator app | L'account aziendale non è più disponibile in Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Test delle chiavi di sicurezza

Di seguito sono riportati i test case di esempio per l'autenticazione senza password con chiavi di sicurezza.

**Accesso FIDO senza password ad Azure Active Directory dispositivi Windows 10 aggiunti**

| Scenario | Risultati previsti |
| --- | --- |
| L'utente può registrare il dispositivo FIDO2 (1809) | L'utente può registrare il dispositivo FIDO2 usando le impostazioni > account > Opzioni di accesso > chiave di sicurezza |
| L'utente può reimpostare il dispositivo FIDO2 (1809) | L'utente può reimpostare il dispositivo FIDO2 usando il software del produttore |
| L'utente può accedere con il dispositivo FIDO2 (1809) | L'utente può selezionare la chiave di sicurezza dalla finestra di accesso e accedere correttamente. |
| L'utente può registrare il dispositivo FIDO2 (1903) | L'utente può registrare il dispositivo FIDO2 in Impostazioni > account > Opzioni di accesso > chiave di sicurezza |
| L'utente può reimpostare il dispositivo FIDO2 (1903) | L'utente può reimpostare il dispositivo FIDO2 in Impostazioni > account > Opzioni di accesso > chiave di sicurezza |
| L'utente può accedere con il dispositivo FIDO2 (1903) | L'utente può selezionare la chiave di sicurezza dalla finestra di accesso e accedere correttamente. |

**Accesso FIDO senza password ad app Web Azure AD**

| Scenario | Risultati previsti |
| --- | --- |
| L'utente può registrare il dispositivo FIDO2 in aka.ms/mysecurityinfo tramite Microsoft Edge | La registrazione dovrebbe essere completata |
| L'utente può registrare il dispositivo FIDO2 in aka.ms/mysecurityinfo tramite Firefox | La registrazione dovrebbe essere completata |
| L'utente può accedere a OneDrive online usando il dispositivo FIDO2 con Microsoft Edge | L'accesso dovrebbe avere esito positivo |
| L'utente può accedere a OneDrive online usando il dispositivo FIDO2 con Firefox | L'accesso dovrebbe avere esito positivo |
| Eseguire il rollback della registrazione del dispositivo FIDO2 disattivando le chiavi di sicurezza di FIDO2 nel pannello metodi di autenticazione nel portale di Azure Active Directory | Agli utenti verrà richiesto di eseguire l'accesso con la relativa chiave di sicurezza, che li registrerà correttamente e verrà visualizzato un errore: "i criteri aziendali richiedono l'uso di un metodo diverso per l'accesso". Gli utenti devono quindi selezionare un metodo diverso e accedere correttamente. Chiudere la finestra ed effettuare di nuovo l'accesso per verificare che non venga visualizzato lo stesso messaggio di errore. |

### <a name="auditing-passwordless"></a>Controllo non con password

Azure AD dispone di report che forniscono informazioni tecniche e aziendali. I proprietari delle applicazioni aziendali e tecniche assumono la proprietà di e utilizzano questi report in base ai requisiti dell'organizzazione.

La sezione metodi di autenticazione nel portale di Azure Active Directory è la posizione in cui gli amministratori possono abilitare e gestire le impostazioni per le credenziali con password.

Azure AD aggiunge voci ai log di controllo nei casi seguenti:

- Un amministratore apporta modifiche nella sezione metodi di autenticazione.
- Un utente apporta qualsiasi tipo di modifica alle proprie credenziali all'interno Azure Active Directory.

Nella tabella seguente vengono forniti alcuni esempi di scenari di Reporting tipici.

|   | per gestire i rischi. | Aumentare la produttività | Governance e conformità |
| --- | --- | --- | --- |
| **Tipi di report** | Metodi di autenticazione-utenti registrati per la registrazione di sicurezza combinata | Metodi di autenticazione: utenti registrati per la notifica dell'app | Accessi: verificare chi accede al tenant e come |
| **Azioni potenziali** | Utenti di destinazione non ancora registrati | Guidare l'adozione di Microsoft Authenticator app o chiavi di sicurezza | Revocare l'accesso o applicare criteri di sicurezza aggiuntivi per gli amministratori |

**Azure ad mantiene la maggior parte dei dati di controllo per 30 giorni** e rende disponibili i dati tramite il portale di amministrazione di Azure o l'API da scaricare nei sistemi di analisi. Se l'organizzazione richiede un periodo di conservazione più lungo, i log devono essere esportati e utilizzati in uno strumento SIEM, ad esempio [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk o Sumo Logic. [Scopri di più sulla visualizzazione dei report di accesso e utilizzo](../reports-monitoring/overview-reports.md).

Gli utenti possono registrare e gestire le proprie credenziali passando a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Questo collegamento indirizza gli utenti all'esperienza di gestione delle credenziali dell'utente finale abilitata tramite l'esperienza di registrazione SSPR/autenticazione a più fattori combinata. Qualsiasi registrazione di dispositivi di sicurezza FIDO2 o modifiche ai metodi di autenticazione da parte di un utente viene registrata nei log di controllo Azure Active Directory.

Quando gli utenti abilitano o disabilitano l'account su una chiave di sicurezza o reimpostano il secondo fattore per la chiave di sicurezza nei propri computer Windows 10, viene aggiunta una voce al registro di sicurezza e si trovano sotto gli ID evento seguenti: 4670, 5382.

### <a name="plan-for-rollback"></a>Pianificare per il rollback

Anche se l'autenticazione senza password è una funzionalità leggera con un effetto minimo sugli utenti finali, potrebbe essere necessario eseguire il rollback.

Per eseguire il rollback è necessario che l'amministratore accedi al portale di Azure Active Directory, selezionare i metodi di autenticazione avanzata desiderati e impostare l'opzione Abilita su' No '. Questo processo disattiva la funzionalità con password per tutti gli utenti.

Agli utenti che hanno già registrato dispositivi di sicurezza FIDO2 verrà richiesto di usare il dispositivo di sicurezza al successivo accesso e verrà visualizzato l'errore seguente:

![scegliere un altro modo per accedere](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Pianificare il progetto pilota

Quando si distribuisce l'autenticazione con password, è necessario abilitare prima uno o più gruppi pilota. È possibile [creare gruppi](../fundamentals/active-directory-groups-create-azure-portal.md) in modo specifico per questo scopo. Aggiungere gli utenti che parteciperanno al progetto pilota ai gruppi. Abilitare quindi i nuovi metodi di autenticazione con password per i gruppi selezionati.

I gruppi possono essere sincronizzati da una directory locale o da Azure AD. Quando si è soddisfatti dei risultati del progetto pilota, è possibile attivare l'autenticazione senza password per tutti gli utenti.

Vedere le [procedure consigliate per un progetto pilota](https://aka.ms/deploymentplans) nella pagina piani di distribuzione.

## <a name="deploy-passwordless-authentication"></a>Distribuire l'autenticazione con password

Seguire i passaggi allineati al metodo scelto di seguito.

### <a name="required-administrative-roles"></a>Ruoli amministrativi richiesti

| Ruolo Azure AD | Description |
| --- | --- |
| Amministratore dell'autenticazione | Ruolo con privilegi minimi in grado di implementare e gestire i metodi di autenticazione |
| Utente | Ruolo con privilegi minimi per la configurazione dell'app Authenticator nel dispositivo o per la registrazione del dispositivo chiave di sicurezza per l'accesso Web o Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Distribuire l'accesso tramite telefono con l'app Microsoft Authenticator

Seguire i passaggi descritti nell'articolo [abilitare l'accesso senza password con l'app Microsoft Authenticator](howto-authentication-passwordless-phone.md) per abilitare l'app Microsoft Authenticator come metodo di autenticazione senza password nell'organizzazione.

### <a name="deploy-fido2-security-key-sign-in"></a>Distribuire l'accesso alla chiave di sicurezza FIDO2

Seguire i passaggi descritti nell'articolo [abilitare l'accesso alla chiave di sicurezza senza password per Azure ad](howto-authentication-passwordless-security-key.md) abilitare le chiavi di sicurezza di FIDO2 come metodi di autenticazione senza password nell'organizzazione.
 
### <a name="troubleshoot-phone-sign-in"></a>Risolvere i problemi di accesso telefonico

| Scenario | Soluzione |
| --- | --- |
| L'utente non può eseguire la registrazione combinata | Assicurarsi che la [registrazione combinata](concept-registration-mfa-sspr-combined.md) sia abilitata. |
| L'utente non può abilitare l'app di autenticazione per l'accesso tramite telefono | Verificare che l'utente sia nell'ambito per la distribuzione |
| L'utente non è incluso nell'ambito per l'autenticazione senza password, ma viene visualizzato con l'opzione di accesso senza password, che non è possibile completare. | Questo scenario si verifica quando l'utente ha abilitato l'accesso tramite telefono nell'applicazione prima del criterio creato. <br> Per abilitare l'accesso: aggiungere l'utente all'ambito degli utenti abilitati per l'accesso senza password. <br> Per bloccare l'accesso: chiedere all'utente di rimuovere il modulo di credenziali per l'applicazione. |

### <a name="troubleshoot-security-key-sign-in"></a>Risolvere i problemi di accesso alla chiave di sicurezza

| Scenario | Soluzione |
| --- | --- |
| L'utente non può eseguire la registrazione combinata | Assicurarsi che la [registrazione combinata](concept-registration-mfa-sspr-combined.md) sia abilitata. |
| L'utente non può aggiungere una chiave di sicurezza nelle [impostazioni di sicurezza](https://aka.ms/mysecurityinfo) | Verificare che le [chiavi di sicurezza](howto-authentication-passwordless-security-key.md) siano abilitate. |
| L'utente non può aggiungere la chiave di sicurezza nelle opzioni di accesso a Windows 10 | [Verificare che le chiavi di sicurezza per l'accesso a Windows](howto-authentication-passwordless-enable.md) |
| **Messaggio di errore**: è stato rilevato che il browser o il sistema operativo non supporta le chiavi di sicurezza FIDO2. | I dispositivi di sicurezza FIDO2 con password possono essere registrati solo nei browser supportati (Microsoft Edge, Firefox versione 67) in Windows 10 versione 1809 o successiva. |
| **Messaggio di errore**: per i criteri aziendali è necessario usare un metodo diverso per eseguire l'accesso. | Nel tenant sono abilitate le chiavi di sicurezza non sicure. |
| L'utente non è in grado di gestire la chiave di sicurezza in Windows 10 versione 1809 | La versione 1809 richiede l'uso del software di gestione delle chiavi di sicurezza fornito dal fornitore della chiave FIDO2. Per assistenza, contattare il fornitore. |
| Credo che la chiave di sicurezza di FIDO2 potrebbe essere difettosa, come è possibile testarla | Passare a [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), immettere le credenziali per un account di test, inserire la chiave di sicurezza sospetta, fare clic sul pulsante "+" nella parte superiore destra della schermata, fare clic su Crea e seguire il processo di creazione. Se questo scenario ha esito negativo, il dispositivo potrebbe essere difettoso. |

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare le chiavi di sicurezza senza password per l'accesso per Azure AD](howto-authentication-passwordless-security-key.md)
- [Abilitare l'accesso senza password con l'app Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Altre informazioni sull'utilizzo dei metodi di autenticazione & Insights](howto-authentication-methods-usage-insights.md)
