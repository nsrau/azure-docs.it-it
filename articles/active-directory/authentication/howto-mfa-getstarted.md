---
title: Considerazioni sulla distribuzione per Azure Multi-Factor Authentication
description: Informazioni sulle considerazioni sulla distribuzione e sulla strategia per la corretta implementazione di Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845a202faccbbe0a604560ac57ae30f87344b95a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451126"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Pianificare una distribuzione di Azure Multi-Factor AuthenticationPlan an Azure Multi-Factor Authentication deployment

Le persone si connettono alle risorse dell'organizzazione in scenari sempre più complicati. Le persone si connettono da dispositivi pubblici, personali e pubblici dell'organizzazione all'aria e fuori dalla rete aziendale utilizzando smartphone, tablet, PC e laptop, spesso su più piattaforme. In questo mondo sempre connesso, multi-dispositivo e multi-piattaforma, la sicurezza degli account utente è più importante che mai. Le password, indipendentemente dalla complessità, utilizzate su dispositivi, reti e piattaforme non sono più sufficienti per garantire la sicurezza dell'account utente, soprattutto quando gli utenti tendono a riutilizzare le password tra gli account. Phishing sofisticato e altri attacchi di social engineering possono comportare nomi utente e password vengono pubblicati e venduti attraverso il dark web.

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) consente di proteggere l'accesso a dati e applicazioni. Fornisce un ulteriore livello di sicurezza utilizzando una seconda forma di autenticazione. Le organizzazioni possono utilizzare [l'accesso condizionale](../conditional-access/overview.md) per adattare la soluzione alle proprie esigenze specifiche.

Questa guida alla distribuzione illustra come pianificare e quindi testare un'implementazione di Azure Multi-Factor Authentication.This deployment guide shows you how to plan and then test an Azure Multi-Factor Authentication roll-out.

Per vedere rapidamente Azure Multi-Factor Authentication in azione e quindi tornare a comprendere ulteriori considerazioni sulla distribuzione:To quickly see Azure Multi-Factor Authentication in action and then torna a comprendere ulteriori considerazioni sulla distribuzione:To quickly see Azure Multi-Factor Authentication in action and then torna a comprendere ulteriori considerazioni sulla distribuzione

> [!div class="nextstepaction"]
> [Abilitare Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di avviare una distribuzione di Azure Multi-Factor Authentication, è necessario considerare gli elementi prerequisiti.

| Scenario | Prerequisito |
| --- | --- |
| **Ambiente** di identità solo cloud con autenticazione moderna | **Nessuna attività aggiuntiva dei prerequisiti** |
| Scenari di identità **ibridaHybrid** identity scenarios | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) viene distribuito e le identità utente vengono sincronizzate o federate con Servizi di dominio Active Directory locale con Azure Active Directory. |
| Applicazioni legacy locali pubblicate per l'accesso cloud | Viene distribuito il [proxy di applicazione](../manage-apps/application-proxy.md) di Azure AD. |
| Uso di Azure MFA con l'autenticazione RADIUSUsing Azure MFA with RADIUS Authentication | Viene distribuito un [Server dei criteri di rete (NPS).](howto-mfa-nps-extension.md) |
| Gli utenti dispongono di Microsoft Office 2010 o versioni precedenti o di Apple Mail per iOS 11 o versioni precedenti | Eseguire l'aggiornamento a [Microsoft Office 2013 o versione successiva](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e a Apple Mail per iOS 12 o versioni successive. L'accesso condizionale non è supportato dai protocolli di autenticazione legacy. |

## <a name="plan-user-rollout"></a>Pianificare l'implementazione degli utenti

Il piano di implementazione dell'autenticazione a più fattori deve includere una distribuzione pilota seguita da ondate di distribuzione che rientrano nella capacità di supporto. Iniziare l'implementazione applicando i criteri di accesso condizionale a un piccolo gruppo di utenti pilota. Dopo aver valutato l'effetto sugli utenti pilota, il processo utilizzato e i comportamenti di registrazione, è possibile aggiungere altri gruppi al criterio o altri utenti ai gruppi esistenti.

### <a name="user-communications"></a>Comunicazioni degli utenti

È fondamentale informare gli utenti, nelle comunicazioni pianificate, le modifiche imminenti, i requisiti di registrazione di Azure MFA e le azioni necessarie per l'utente. È consigliabile sviluppare le comunicazioni di concerto con i rappresentanti dell'organizzazione, ad esempio i reparti Comunicazione, Gestione delle modifiche o Risorse umane.

Microsoft fornisce modelli di [comunicazione](https://aka.ms/mfatemplates) e [la documentazione per l'utente finale](../user-help/security-info-setup-signin.md) per la stesura delle comunicazioni. È possibile inviare gli utenti a [https://myprofile.microsoft.com](https://myprofile.microsoft.com) registrarsi direttamente selezionando i collegamenti Informazioni di **sicurezza** in tale pagina.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Azure Multi-factor Authentication viene distribuito applicando criteri con accesso condizionale. Un [criterio di accesso condizionale](../conditional-access/overview.md) può richiedere agli utenti di eseguire l'autenticazione a più fattori quando vengono soddisfatti determinati criteri, ad esempio:A Conditional Access policy can require users to perform multi-factor authentication when certain certain criteria are met, such as:

* Tutti gli utenti, un utente specifico, un membro di un gruppo o un ruolo assegnato
* Applicazione cloud specifica a cui si accede
* Piattaforma del dispositivo.
* Stato del dispositivo
* Percorso di rete o indirizzo IP geolocalizzato
* Applicazioni client
* Rischio di accesso (richiede la protezione dell'identità)
* Dispositivo conforme
* Dispositivo aggiunto all'identità ibrida di Azure AD
* Applicazione client approvata

Utilizzare i poster e i modelli di messaggio di posta elettronica personalizzabili nei materiali di [implementazione dell'autenticazione](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) a più fattori per implementare l'autenticazione a più fattori nell'organizzazione.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Abilitare Multi-Factor Authentication con l'accesso condizionale

I criteri di accesso condizionale applicano la registrazione, richiedendo agli utenti non registrati di completare la registrazione al primo accesso, un'importante considerazione per la sicurezza.

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) contribuisce sia ai criteri di registrazione per che ai criteri di rilevamento e correzione automatici dei rischi per la storia di Azure Multi-Factor Authentication. È possibile creare criteri per forzare le modifiche delle password quando si verifica una minaccia di identità compromessa o richiedere l'autenticazione a più fattori quando un accesso è considerato rischioso dai seguenti [eventi:](../reports-monitoring/concept-risk-events.md)

* Credenziali perse
* Accessi da indirizzi IP anonimi
* Trasferimento impossibile a posizioni atipiche
* Accessi da posizioni non note
* Accessi da dispositivi infetti
* Accessi da indirizzi IP con attività sospette

Alcuni dei rilevamenti dei rischi rilevati da Azure Active Directory Identity Protection si verificano in tempo reale e richiedono l'elaborazione offline. Gli amministratori possono scegliere di bloccare gli utenti che presentano comportamenti rischiosi e correggere manualmente, richiedere una modifica della password o richiedere un'autenticazione a più fattori come parte dei criteri di accesso condizionale.

## <a name="define-network-locations"></a>Definire i percorsi di rete

È consigliabile che le organizzazioni utilizzino l'accesso condizionale per definire la propria rete utilizzando [percorsi denominati](../conditional-access/location-condition.md#named-locations). Se l'organizzazione utilizza Identity Protection, è consigliabile usare criteri basati sul rischio anziché percorsi denominati.

### <a name="configuring-a-named-location"></a>Configurazione di una posizione denominata

1. Aprire Azure Active Directory nel portale di AzureOpen **Azure Active Directory** in the Azure portal
2. Scegliere **Sicurezza**
3. In **Gestisci**, scegliere **Posizioni denominate**
4. Seleziona **nuova posizione**
5. Nel campo **Nome** specificare un nome significativo
6. Selezionare se si sta definendo la posizione utilizzando *intervalli IP* o *paesi/aree geografiche*
   1. Se si utilizzano *gli intervalli IP*
      1. Decidere se *contrassegnare come percorso attendibile*. L'accesso da una posizione attendibile riduce il rischio di accesso dell'utente. Contrassegnare questa posizione come attendibile solo se si è certi che gli intervalli IP immessi siano stabiliti e credibili nell'organizzazione.
      2. Specificare gli intervalli IP
   2. Utilizzo *di Paesi/Aree geografiche*
      1. Espandere il menu a discesa e selezionare i paesi o le aree geografiche che si desidera definire per questa località denominata.
      2. Decidere se *includere aree sconosciute*. Le aree sconosciute sono indirizzi IP che non possono essere mappati a un paese.
7. Selezionare **Crea**

## <a name="plan-authentication-methods"></a>Pianificare i metodi di autenticazione

Gli amministratori possono scegliere i metodi di [autenticazione](../authentication/concept-authentication-methods.md) che desiderano rendere disponibili per gli utenti. È importante consentire più di un singolo metodo di autenticazione in modo che gli utenti dispongano di un metodo di backup disponibile nel caso in cui il metodo primario non sia disponibile. Per l'abilitazione degli amministratori sono disponibili i seguenti metodi:

### <a name="notification-through-mobile-app"></a>Notifica tramite app per dispositivi mobili

Una notifica push viene inviata all'app Microsoft Authenticator sul dispositivo mobile. L'utente visualizza la notifica e seleziona **Approva** per completare la verifica. Le notifiche push tramite un'app per dispositivi mobili offrono l'opzione meno intrusiva per gli utenti. Sono anche l'opzione più affidabile e sicura perché utilizzano una connessione dati piuttosto che la telefonia.

> [!NOTE]
> Se l'organizzazione ha personale che lavora o viaggia in Cina, il metodo **Di notifica tramite app per dispositivi mobili** su dispositivi **Android** non funziona in quel paese. Per tali utenti devono essere disponibili metodi alternativi.

### <a name="verification-code-from-mobile-app"></a>Codice di verifica dall'app per dispositivi mobili

Un'app per dispositivi mobili come l'app Microsoft Authenticator genera un nuovo codice di verifica OATH ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso. L'opzione dell'app mobile può essere utilizzata indipendentemente dal fatto che il telefono disponga o meno di un segnale dati o cellulare.

### <a name="call-to-phone"></a>Chiamata al telefono

Viene effettuata una chiamata vocale automatica all'utente. L'utente risponde alla chiamata **#** e preme sul tastierino del telefono per approvare l'autenticazione. Call to phone è un ottimo metodo di backup per la notifica o il codice di verifica da un'app per dispositivi mobili.

### <a name="text-message-to-phone"></a>SMS al telefono

Un messaggio di testo che contiene un codice di verifica viene inviato all'utente, all'utente viene richiesto di immettere il codice di verifica nell'interfaccia di accesso.

### <a name="choose-verification-options"></a>Scegliere le opzioni di verifica

1. Passare ad **Azure Active Directory**, **Utenti**, **Multi-Factor Authentication**.

   ![Accesso al portale Multi-Factor Authentication dal pannello Utenti di Azure AD nel portale di Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Nella nuova scheda che si apre passare alle **impostazioni del servizio**.
1. In **Opzioni di verifica** selezionare tutte le caselle per i metodi disponibili per gli utenti.

   ![Configurare i metodi di verifica nella scheda delle impostazioni del servizio Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Fare clic su **Save**.
1. Chiudere la scheda delle impostazioni del **servizio.**

## <a name="plan-registration-policy"></a>Pianificare i criteri di registrazione

Gli amministratori devono determinare la modalità di registrazione dei metodi da parte degli utenti. Le organizzazioni devono [abilitare la nuova esperienza](howto-registration-mfa-sspr-combined.md) di registrazione combinata per Azure MFA e la reimpostazione della password self-service (SSPR). SSPR consente agli utenti di reimpostare la password in modo sicuro utilizzando gli stessi metodi utilizzati per l'autenticazione a più fattori. Consigliamo questa registrazione combinata perché è un'ottima esperienza per gli utenti, con la possibilità di registrarsi una volta per entrambi i servizi. L'abilitazione degli stessi metodi per SSPR e Azure MFA consentirà agli utenti di essere registrati per usare entrambe le funzionalità.

### <a name="registration-with-identity-protection"></a>Registrazione con Identity Protection

Se l'organizzazione usa Azure Active Directory Identity Protection, configurare i criteri di [registrazione dell'autenticazione](../identity-protection/howto-mfa-policy.md) a più fattori per richiedere agli utenti di registrarsi al successivo accesso interattivo.

### <a name="registration-without-identity-protection"></a>Registrazione senza protezione dell'identità

Se l'organizzazione non dispone di licenze che abilitano la protezione dell'identità, agli utenti viene richiesto di registrare al successivo richiesta l'autenticazione a più fattori all'accesso. Gli utenti potrebbero non essere registrati per l'autenticazione a più fattori se non utilizzano applicazioni protette con l'autenticazione a più fattori. È importante ottenere tutti gli utenti registrati in modo che i malintenzionati non possono indovinare la password di un utente e registrarsi per l'autenticazione a più fattori per loro conto, assumendo in modo efficace il controllo dell'account.

#### <a name="enforcing-registration"></a>Applicazione della registrazione

Utilizzando i passaggi seguenti, un criterio di accesso condizionale può imporre agli utenti di registrarsi per Multi-Factor AuthenticationUsing the following steps a Conditional Access policy can force users to register for Multi-Factor Authentication

1. Creare un gruppo, aggiungere tutti gli utenti attualmente non registrati.
2. Utilizzando l'accesso condizionale, applicare l'autenticazione a più fattori per questo gruppo per l'accesso a tutte le risorse.
3. Periodicamente, rivalutare l'appartenenza al gruppo e rimuovere gli utenti registrati dal gruppo.

È possibile identificare gli utenti di Azure MFA registrati e non registrati con comandi di PowerShell che si basano sul [modulo DI MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificare gli utenti registrati

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificare gli utenti non registrati

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertire gli utenti dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionaleConvert users from per-user MFA to Conditional Access based MFA

Se gli utenti sono stati abilitati usando Azure Multi-Factor Authentication abilitato per utente e applicato, PowerShell può essere indicato nella conversione in Azure Multi-Factor Authentication basato sull'accesso condizionale.

Eseguire PowerShell in una finestra di `.PS1` ISE o salvare come file per l'esecuzione in locale.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Di conseguenza, il comportamento e lo script di PowerShell precedenti sono stati modificati in precedenza. In precedenza, lo script salvato i metodi di autenticazione a più fattori, disabilitato MFA e ripristinato i metodi. Questo non è più necessario ora che il comportamento predefinito per disable non cancella i metodi.

## <a name="plan-conditional-access-policies"></a>Pianificare i criteri di accesso condizionalePlan Conditional Access policies

Per pianificare la strategia dei criteri di accesso condizionale, che determinerà quando sono necessari l'autenticazione a più fattori e altri controlli, fare riferimento a [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md).

È importante impedire di essere inavvertitamente bloccati dal tenant di Azure AD. È possibile ridurre l'impatto di questa involontaria mancanza di accesso amministrativo creando due o più account di accesso di [emergenza nel tenant](../users-groups-roles/directory-emergency-access.md) ed escludendoli dai criteri di accesso condizionale.

### <a name="create-conditional-access-policy"></a>Creare criteri di accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
   ![Creare criteri di accesso condizionale per abilitare l'autenticazione a più fattori per gli utenti del portale di Azure nel gruppo pilotaCreate a Conditional Access policy to enable MFA for Azure portal users in pilot group](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Immettere un nome significativo per i criteri.
1. In **Utenti e gruppi**:
   * Nella scheda **Includere** selezionare il pulsante di opzione **Tutti gli utenti**
   * Nella scheda **Escludi** selezionare la casella **Utenti e gruppi** e scegliere gli account di accesso di emergenza.
   * Fare clic su **Done**.
1. In **App cloud**selezionare il pulsante di opzione Tutte le app **cloud.**
   * FACOLTATIVAMENTE: nella scheda **Escludere** scegliere le app cloud per le quali l'organizzazione non richiede MFA.
   * Fare clic su **Done**.
1. In **Condizioni** sezione:
   * FACOLTATIVAMENTE: se è stato abilitato Azure Identity Protection, è possibile scegliere di valutare il rischio di accesso come parte dei criteri.
   * FACOLTATIVAMENTE: se sono state configurate posizioni attendibili o località denominate, è possibile specificare di includere o escludere tali percorsi dai criteri.
1. In **Concedi**verificare che il pulsante di opzione **Concedi accesso** sia selezionato.
    * Selezionare la casella **Richiedi autenticazione a più fattori**.
    * Fare clic su **Seleziona**.
1. Saltare la sezione **Sessione.**
1. Impostare l'interruttore **Abilita criterio** su **Attivato**.
1. Fare clic su **Crea**.

## <a name="plan-integration-with-on-premises-systems"></a>Pianificare l'integrazione con sistemi locali

Alcune applicazioni legacy e locali che non eseguono l'autenticazione direttamente in Azure AD richiedono passaggi aggiuntivi per usare l'autenticazione a più fattori, tra cui:Some legacy and on-premises applications that do not authenticate directly against Azure AD require additional steps to use MFA including:

* Applicazioni locali legacy, che dovranno usare il proxy di applicazione.
* Applicazioni RADIUS locali, che dovranno utilizzare l'adattatore MFA con server dei criteri di rete.
* Applicazioni ADFS locali, che dovranno usare la scheda MFA con AD FS 2016 o versione più recente.

Le applicazioni che eseguono l'autenticazione diretta con Azure AD e che dispongono di autenticazione moderna (WS-Fed, SAML, OAuth, OpenID Connect) possono utilizzare direttamente i criteri di accesso condizionale.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Usare Azure MFA con il proxy di applicazione di Azure ADUse Azure MFA with Azure AD Application Proxy

Le applicazioni che risiedono in locale possono essere pubblicate nel tenant di Azure AD tramite il proxy di applicazione di [Azure AD](../manage-apps/application-proxy.md) e possono sfruttare l'autenticazione a più fattori di Azure se sono configurate per l'uso della preautenticazione di Azure AD.

Queste applicazioni sono soggette acriteri di accesso condizionale che applicano Azure Multi-Factor Authentication, come qualsiasi altra applicazione integrata in Azure AD.

Analogamente, se Azure Multi-Factor Authentication viene applicato per tutti gli accessi utente, le applicazioni locali pubblicate con il proxy di applicazione di Azure AD verranno protette.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrazione di Azure Multi-Factor Authentication con Server dei criteri di reteIntegrating Azure Multi-Factor Authentication with Network Policy Server

L'estensione di Server dei criteri di rete (NPS) per Azure MFA aggiunge funzionalità MFA basate su cloud per l'infrastruttura di autenticazione usando i server esistenti. Con l'estensione Server dei criteri di rete, è possibile aggiungere la verifica della telefonata, del messaggio di testo o dell'app telefono al flusso di autenticazione esistente. Questa integrazione presenta le limitazioni seguenti:This integration has the following limitations:

* Con il protocollo CHAPv2, sono supportate solo le notifiche push dell'app di autenticazione e la chiamata vocale.
* Non è possibile applicare criteri di accesso condizionale.

L'estensione Server dei criteri di rete funge da adapter tra RADIUS e l'autenticazione a più fattori basata su cloud per fornire un secondo fattore di autenticazione per proteggere [VPN,](howto-mfa-nps-extension-vpn.md)le [connessioni di Gateway Desktop remoto](howto-mfa-nps-extension-rdg.md)o altre applicazioni compatibili con RADIUS. Gli utenti che si registrano per Azure MFA in questo ambiente verranno richiesti per tutti i tentativi di autenticazione, la mancanza di criteri di accesso condizionale significa che l'autenticazione a più fattori è sempre necessaria.

#### <a name="implementing-your-nps-server"></a>Implementazione del server dei criteri di rete

Se si dispone di un'istanza di Server dei criteri di rete già distribuita e in uso, fare riferimento [a Integrare l'infrastruttura dei criteri](howto-mfa-nps-extension.md)di rete esistente con Azure Multi-Factor Authentication . Se si configura Server dei criteri di rete per la prima volta, fare riferimento a Server dei criteri di rete [(NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) per istruzioni. Le indicazioni per la risoluzione dei problemi sono disponibili nell'articolo Risolvere i messaggi di errore dall'estensione Server dei criteri di rete [per Azure Multi-Factor Authentication.](howto-mfa-nps-extension-errors.md)

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Preparare server dei criteri di rete per gli utenti non registrati per l'autenticazione a più fattoriPrepare NPS for users that aren't enrolled for MFA

Scegliere cosa accade quando gli utenti non registrati con l'autenticazione a più fattori tentano di eseguire l'autenticazione. Utilizzare l'impostazione del Registro di sistema `REQUIRE_USER_MATCH` nel percorso `HKLM\Software\Microsoft\AzureMFA` del Registro di sistema per controllare il comportamento della funzionalità. Questa impostazione ha una singola opzione di configurazione.

| Chiave | valore | Predefinito |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | VERO / FALSO | Non impostato (equivalente a VERO) |

Lo scopo di questa impostazione è stabilire cosa fare quando un utente non è registrato per MFA. Gli effetti della modifica di questa impostazione sono elencati nella tabella seguente.

| Impostazioni | Stato autenticazione a più fattori utente | Effetti |
| --- | --- | --- |
| La chiave non esiste | Non registrato | La sfida dell'AMF non ha successo |
| Valore impostato su True / non impostato | Non registrato | La sfida dell'AMF non ha successo |
| Chiave impostata su FalseKey set to False | Non registrato | Autenticazione senza autenticazione a più fattori |
| Chiave impostata su False o True | Enrolled | Deve eseguire l'autenticazione con l'autenticazione a più fattori |

### <a name="integrate-with-active-directory-federation-services"></a>Integrazione con Active Directory Federation Services

Se l'organizzazione è federata con Azure AD, è possibile usare [Azure Multi-Factor Authentication per proteggere le risorse di ADFS,](multi-factor-authentication-get-started-adfs.md)sia in locale che nel cloud. Azure MFA consente di ridurre le password e fornire un modo più sicuro per l'autenticazione. A partire da Windows Server 2016, è possibile configurare autenticazione a più Fattori di Azure per l'autenticazione principale.

A differenza di ADFS in Windows Server 2012 R2, l'adattatore AD FS 2016 Azure MFA si integra direttamente con Azure AD e non richiede un server Azure MFA locale. L'adattatore Azure MFA è integrato in Windows Server 2016 e non è necessaria un'installazione aggiuntiva.

Quando si usa Azure MFA con AD FS 2016 e l'applicazione di destinazione è soggetta ai criteri di accesso condizionale, esistono considerazioni aggiuntive:When using Azure MFA with AD FS 2016 and the target application is subject to Conditional Access policy, there are additional considerations:

* L'accesso condizionale è disponibile quando l'applicazione è una relying party per Azure AD, federata con AD FS 2016 o versione più recente.
* Accesso condizionale non è disponibile quando l'applicazione è una relying party di AD FS 2016 o AD FS 2019 ed è gestito o federato con AD FS 2016 o AD FS 2019.
* Accesso condizionale non è disponibile anche quando AD FS 2016 o 2019 AD è configurato per utilizzare Azure MFA come metodo di autenticazione principale.

#### <a name="ad-fs-logging"></a>Registrazione di ADFSAD FS logging

Standard AD FS 2016 e 2019 registrazione nel registro di sicurezza di Windows e il registro di amministrazione di ADFS, contiene informazioni sulle richieste di autenticazione e il relativo esito positivo o negativo. I dati del log eventi all'interno di questi eventi indicheranno se è stato usato Azure MFA. Ad esempio, un ID evento di controllo di ADFS 1200 può contenere:For example, an AD FS Auditing Event ID 1200 may contain:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Rinnovare e gestire i certificati

In ogni server ADFS, nell'archivio personale del computer locale, sarà presente un certificato di Azure MFA autofirmato denominato OU- Microsoft AD FS Azure MFA, che contiene la data di scadenza del certificato. Controllare il periodo di validità del certificato in ogni server ADFS per determinare la data di scadenza.

Se il periodo di validità dei certificati sta per scadere, [generare e verificare un nuovo certificato MFA in ogni server ADFS.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)

Le indicazioni seguenti illustrano in dettaglio come gestire i certificati Azure MFA nei server ADFS. Quando si configura ADFS con Azure MFA, `New-AdfsAzureMfaTenantCertificate` i certificati generati tramite il cmdlet PowerShell sono validi per due anni. Rinnovare e installare i certificati rinnovati prima della scadenza per ovoid interruzioni nel servizio Di mandare a più fattori.

## <a name="implement-your-plan"></a>Implementare il piano

Dopo aver pianificato la soluzione, è possibile implementarla attenendosi alla procedura seguente:

1. Soddisfare tutti i prerequisiti necessari
   1. Distribuire Azure AD Connect per qualsiasi scenario ibridoDeploy [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) for any hybrid scenarios
   1. Distribuire il proxy di applicazione di Azure AD per tutte le app locali pubblicate per l'accesso cloudDeploy [Azure AD Application Proxy](../manage-apps/application-proxy.md) for on any on-premises apps published for cloud access
   1. Distribuire Server dei criteri di rete per qualsiasi autenticazione RADIUSDeploy [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) for any RADIUS authentication
   1. Verificare che gli utenti abbiano eseguito l'aggiornamento alle versioni supportate di Microsoft Office con l'autenticazione moderna abilitataEnsure users have upgraded to supported versions of Microsoft Office with modern authentication enabled
1. Configurare i [metodi di autenticazione](#choose-verification-options) scelti
1. Definire i [percorsi di rete denominati](../conditional-access/location-condition.md#named-locations)
1. Selezionare i gruppi per avviare l'implementazione dell'autenticazione a più fattori.
1. Configurare i [criteri di accesso condizionaleConfigure](#create-conditional-access-policy) your Conditional Access policies
1. Configurare i criteri di registrazione dell'autenticazione a più fattoriConfigure your MFA registration policy
   1. [Combinato MFA e SSPR](howto-registration-mfa-sspr-combined.md)
   1. Con [la protezione dell'identità](../identity-protection/howto-mfa-policy.md)
1. Inviare le comunicazioni degli utenti e convincere gli utenti a iscriversi all'utente[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Tenere traccia dell'iscrizione](#identify-non-registered-users)

> [!TIP]
> Gli utenti cloud governativi possono iscriversi a[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Gestire la soluzione

Report per Azure MFA

Azure Multi-Factor Authentication fornisce report tramite il portale di Azure:Azure Multi-Factor Authentication provides reports through the Azure portal:

| Report | Location | Descrizione |
| --- | --- | --- |
| Avvisi di illecito e utilizzo | Azure AD > Accessi | Fornisce informazioni su utilizzo complessivo, riepilogo utenti e dettagli utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |

## <a name="troubleshoot-mfa-issues"></a>Risolvere i problemi relativi all'autenticazione a più fattoriTroubleshoot MFA

Per trovare soluzioni aproblemi comuni relativi all'autenticazione a più fattori di Azure, vedere l'articolo Risoluzione dei problemi di Azure Multi-Factor Authentication nel Centro supporto tecnico Microsoft.Find solutions for common issues with Azure MFA at the [Troubleshooting Azure Multi-Factor Authentication article](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) on the Microsoft Support Center.

## <a name="next-steps"></a>Passaggi successivi

Per vedere Azure Multi-Factor Authentication in azione, completare l'esercitazione seguente:To see Azure Multi-Factor Authentication in action, complete the following tutorial:

> [!div class="nextstepaction"]
> [Abilitare Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)
