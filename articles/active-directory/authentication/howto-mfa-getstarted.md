---
title: Pianificare ed eseguire una distribuzione di Azure Multi-Factor Authentication-Azure Active Directory
description: Pianificazione della distribuzione Microsoft Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80ac949dea3c9d6c3d28d2a343c4ed7bad8983
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474348"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Pianificazione di una distribuzione di Azure Multi-Factor Authentication basata sul cloud

Le persone si connettono alle risorse dell'organizzazione in scenari sempre più complessi. Le persone si connettono a dispositivi personali, personali e di proprietà dell'organizzazione in rete aziendale tramite smartphone, tablet, PC e portatili, spesso su più piattaforme. In questo mondo sempre connesso, multidispositivo e multipiattaforma, la sicurezza degli account utente è più importante che mai. Le password, a prescindere dalla loro complessità, utilizzate tra dispositivi, reti e piattaforme non sono più sufficienti per garantire la sicurezza dell'account utente, specialmente quando gli utenti tendono a riutilizzare le password tra gli account. Il phishing sofisticato e altri attacchi di ingegneria sociale possono comportare la pubblicazione e la vendita di nomi utente e password in un sito Web scuro.

[Azure multi-factor authentication (](concept-mfa-howitworks.md) multi-factor authentication) consente di proteggere l'accesso ai dati e alle applicazioni. Fornisce un livello di sicurezza aggiuntivo utilizzando una seconda forma di autenticazione. Le organizzazioni possono usare [l'accesso condizionale](../conditional-access/overview.md) per rendere la soluzione adatta alle proprie esigenze specifiche.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare una distribuzione di Azure Multi-Factor Authentication, è necessario prendere in considerazione elementi prerequisiti.

| Scenario | Prerequisito |
| --- | --- |
| Ambiente di identità **solo cloud** con autenticazione moderna | **Nessuna attività dei prerequisiti aggiuntiva** |
| Scenari di identità **ibrida** | [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) viene distribuita e le identità utente sono sincronizzate o federate con la Active Directory Domain Services locale con Azure Active Directory. |
| Applicazioni legacy locali pubblicate per l'accesso al cloud | Azure AD [proxy di applicazione](../manage-apps/application-proxy.md) viene distribuito. |
| Uso dell'autenticazione a più fattori di Azure con l'autenticazione RADIUS | Viene distribuito un [Server dei criteri di rete](howto-mfa-nps-extension.md) . |
| Gli utenti hanno Microsoft Office 2010 o versioni precedenti o Apple Mail per iOS 11 o versioni precedenti | Eseguire l'aggiornamento a [Microsoft Office 2013 o versione successiva](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e Apple Mail per iOS 12 o versione successiva. L'accesso condizionale non è supportato dai protocolli di autenticazione legacy. |

## <a name="plan-user-rollout"></a>Pianificare l'implementazione utente

Il piano di implementazione di multi-factor authentication deve includere una distribuzione pilota seguita da Waves di distribuzione che si trovano all'interno della capacità di supporto. Iniziare l'implementazione applicando i criteri di accesso condizionale a un piccolo gruppo di utenti pilota. Dopo aver valutato l'effetto sugli utenti pilota, i processi usati e i comportamenti di registrazione, è possibile aggiungere altri gruppi al criterio o aggiungere altri utenti ai gruppi esistenti.

### <a name="user-communications"></a>Comunicazioni utente

È fondamentale informare gli utenti, nelle comunicazioni pianificate, sulle modifiche imminenti, sui requisiti di registrazione dell'autenticazione a più fattori di Azure e sulle azioni utente necessarie. È consigliabile che le comunicazioni siano sviluppate insieme ai rappresentanti all'interno dell'organizzazione, ad esempio le comunicazioni, la gestione delle modifiche o i reparti delle risorse umane.

Microsoft fornisce [modelli di comunicazione](https://aka.ms/mfatemplates) e documentazione per l' [utente finale](../user-help/security-info-setup-signin.md) per facilitare la stesura delle comunicazioni. È possibile inviare utenti a [https://myprofile.microsoft.com](https://myprofile.microsoft.com) per la registrazione diretta selezionando i collegamenti **info di sicurezza** nella pagina.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Azure Multifactor Authentication viene distribuito applicando criteri con accesso condizionale. Un [criterio di accesso condizionale](../conditional-access/overview.md) può richiedere agli utenti di eseguire l'autenticazione a più fattori quando vengono soddisfatti determinati criteri, ad esempio:

* Tutti gli utenti, un utente specifico, un membro di un gruppo o un ruolo assegnato
* Applicazione cloud specifica a cui si accede
* Piattaforma del dispositivo.
* Stato del dispositivo
* Percorso di rete o indirizzo IP geografico
* Applicazioni client
* Rischio di accesso (richiede Identity Protection)
* Dispositivo conforme
* Dispositivo aggiunto all'identità ibrida di Azure AD
* Applicazione client approvata

Usare i poster e i modelli di posta elettronica personalizzabili nei [materiali di implementazione di autenticazione](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) a più fattori per implementare la funzionalità di autenticazione a più fattori per l'organizzazione.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Abilitare Multi-Factor Authentication con l'accesso condizionale

I criteri di accesso condizionale applicano la registrazione, richiedendo agli utenti non registrati di completare la registrazione al primo accesso, una considerazione importante per la sicurezza.

[Azure ad Identity Protection](../identity-protection/howto-configure-risk-policies.md) fornisce un criterio di registrazione per il rilevamento dei rischi automatizzati e i criteri di correzione per la storia di multi-factor authentication di Azure. È possibile creare criteri per forzare le modifiche delle password quando esiste una minaccia di identità compromessa o richiedere l'autenticazione a più fattori quando un accesso viene considerato rischioso dagli [eventi](../reports-monitoring/concept-risk-events.md)seguenti:

* Credenziali perse
* Accessi da indirizzi IP anonimi
* Trasferimento impossibile a posizioni atipiche
* Accessi da posizioni non note
* Accessi da dispositivi infetti
* Accessi da indirizzi IP con attività sospette

Alcuni dei rilevamenti dei rischi rilevati da Azure Active Directory Identity Protection si verificano in tempo reale e altri richiedono l'elaborazione offline. Gli amministratori possono scegliere di bloccare gli utenti che presentano comportamenti rischiosi e correggere manualmente, richiedere una modifica della password o richiedere un'autenticazione a più fattori come parte dei criteri di accesso condizionale.

## <a name="define-network-locations"></a>Definire i percorsi di rete

È consigliabile che le organizzazioni usino l'accesso condizionale per definire la propria rete usando [località denominate](../conditional-access/location-condition.md#named-locations). Se l'organizzazione USA Identity Protection, è consigliabile usare criteri basati sul rischio anziché percorsi denominati.

### <a name="configuring-a-named-location"></a>Configurazione di un percorso denominato

1. Aprire **Azure Active Directory** nella portale di Azure
2. Fare clic su **accesso condizionale**
3. Fare clic su **località denominate**
4. Fare clic su **nuova posizione**
5. Nel campo **nome** specificare un nome significativo
6. Consente di indicare se si sta definendo il percorso usando gli intervalli IP o i paesi/aree geografiche
   1. Se si usano gli intervalli IP
      1. Decidere se contrassegnare il percorso come attendibile. L'accesso da una posizione attendibile riduce il rischio di accesso dell'utente. Contrassegnare questo percorso come attendibile se si è certi che gli intervalli IP immessi siano stabiliti e credibili nell'organizzazione.
      2. Specificare gli intervalli IP
   2. Se si usano paesi/aree geografiche
      1. Espandere il menu a discesa e selezionare i paesi o le aree geografiche che si desidera definire per la località specificata.
      2. Decidere se includere aree sconosciute. Le aree sconosciute sono indirizzi IP di cui non è possibile eseguire il mapping a un paese/area geografica.
7. Fare clic su **Crea**

## <a name="plan-authentication-methods"></a>Pianificare i metodi di autenticazione

Gli amministratori possono scegliere i [metodi di autenticazione](../authentication/concept-authentication-methods.md) che desiderano rendere disponibili per gli utenti. È importante consentire più di un singolo metodo di autenticazione in modo che gli utenti dispongano di un metodo di backup disponibile nel caso in cui il metodo principale non sia disponibile. Per consentire agli amministratori di abilitare, sono disponibili i metodi seguenti:

### <a name="notification-through-mobile-app"></a>Notifica tramite app per dispositivi mobili

Viene inviata una notifica push all'app Microsoft Authenticator nel dispositivo mobile. L'utente Visualizza la notifica e seleziona **approva** per completare la verifica. Le notifiche push tramite un'app per dispositivi mobili offrono l'opzione meno intrusiva per gli utenti. Sono anche l'opzione più affidabile e sicura perché usano una connessione dati invece della telefonia.

> [!NOTE]
> Se l'organizzazione dispone di personale che lavora o viaggia in Cina, la notifica tramite il metodo dell' **app mobile** nei **dispositivi Android** non funziona in quel paese. È necessario rendere disponibili metodi alternativi per tali utenti.

### <a name="verification-code-from-mobile-app"></a>Codice di verifica dall'app per dispositivi mobili

Un'app per dispositivi mobili come l'app Microsoft Authenticator genera un nuovo codice di verifica del GIURAmento ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso. L'opzione app per dispositivi mobili può essere usata indipendentemente dal fatto che il telefono abbia o meno un segnale dati o cellulare.

### <a name="call-to-phone"></a>Chiamata al telefono

Viene effettuata una chiamata vocale automatizzata all'utente. L'utente risponde alla chiamata e preme **#** sul tastierino telefonico per approvare l'autenticazione. La chiamata al telefono è un ottimo metodo di backup per il codice di notifica o di verifica da un'app per dispositivi mobili.

### <a name="text-message-to-phone"></a>SMS al telefono

All'utente viene inviato un messaggio di testo contenente un codice di verifica, all'utente viene richiesto di immettere il codice di verifica nell'interfaccia di accesso.

### <a name="choose-verification-options"></a>Scegliere le opzioni di verifica

1. Passare ad **Azure Active Directory**, **Utenti**, **Multi-Factor Authentication**.

   ![Accesso al portale Multi-Factor Authentication dal pannello Utenti di Azure AD nel portale di Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Nella nuova scheda che si apre passare a **Impostazioni del servizio**.
1. In **Opzioni di verifica** selezionare tutte le caselle per i metodi disponibili per gli utenti.

   ![Configurare i metodi di verifica nella scheda delle impostazioni del servizio Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Fare clic su **Save**.
1. Chiudere la scheda **Impostazioni del servizio**.

## <a name="plan-registration-policy"></a>Pianificare i criteri di registrazione

Gli amministratori devono determinare il modo in cui gli utenti registreranno i loro metodi. Le organizzazioni devono [abilitare la nuova esperienza di registrazione combinata per l'autenticazione a più fattori](howto-registration-mfa-sspr-combined.md) di Azure e la reimpostazione della password self-service (SSPR). SSPR consente agli utenti di reimpostare la password in modo sicuro usando gli stessi metodi usati per l'autenticazione a più fattori. È consigliabile usare questa registrazione combinata, attualmente disponibile in anteprima pubblica, perché si tratta di un'esperienza ottimale per gli utenti, con la possibilità di registrarsi una sola volta per entrambi i servizi. L'abilitazione degli stessi metodi per SSPR e l'autenticazione a più fattori di Azure consentirà agli utenti di essere registrati per usare entrambe le funzionalità.

### <a name="registration-with-identity-protection"></a>Registrazione con Identity Protection

Se l'organizzazione USA Azure Active Directory Identity Protection, [configurare i criteri di registrazione dell'](../identity-protection/howto-mfa-policy.md) autenticazione a più fattori per richiedere agli utenti di registrarsi al successivo accesso interattivo.

### <a name="registration-without-identity-protection"></a>Registrazione senza Identity Protection

Se l'organizzazione non ha licenze che abilitano la protezione delle identità, agli utenti viene richiesto di effettuare la registrazione alla successiva richiesta di autenticazione a più fattori al momento dell'accesso. Gli utenti potrebbero non essere registrati per l'autenticazione a più fattori se non usano applicazioni protette con multi-factor authentication. È importante che tutti gli utenti siano registrati in modo che gli attori malintenzionati non possano indovinare la password di un utente e registrarsi per l'autenticazione a più fattori per loro conto, assumendo effettivamente il controllo dell'account.

#### <a name="enforcing-registration"></a>Applicazione della registrazione

Usando i passaggi seguenti un criterio di accesso condizionale può forzare gli utenti a registrarsi per Multi-Factor Authentication

1. Creare un gruppo, aggiungere tutti gli utenti non attualmente registrati.
2. Con l'accesso condizionale, applicare l'autenticazione a più fattori per questo gruppo per l'accesso a tutte le risorse.
3. Periodicamente, rivalutare l'appartenenza al gruppo e rimuovere gli utenti che hanno eseguito la registrazione dal gruppo.

È possibile identificare gli utenti di Azure multi-factor authentication registrati e non registrati con i comandi di PowerShell che si basano sul [modulo MSOnline di PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificazione degli utenti registrati

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificare utenti non registrati

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertire gli utenti dall'autenticazione a più fattori per utente all'autenticazione a più fattori

Se gli utenti sono stati abilitati con l'abilitazione per l'utente e l'applicazione di Azure Multi-Factor Authentication, il seguente PowerShell può risultare utile per eseguire la conversione in Azure Multi-Factor Authentication basato sull'accesso condizionale.

Eseguire questo PowerShell in una finestra ISE o salvarlo come. File PS1 da eseguire localmente.

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

## <a name="plan-conditional-access-policies"></a>Pianificare i criteri di accesso condizionale

Per pianificare la strategia di criteri di accesso condizionale, che determina quando sono necessari l'autenticazione a più fattori e altri controlli, vedere informazioni sull' [accesso condizionale in Azure Active Directory](../conditional-access/overview.md).

È importante impedire il blocco inavvertitamente del tenant Azure AD. È possibile ridurre l'effetto di questa mancanza accidentale di accesso amministrativo creando [due o più account di accesso di emergenza nel tenant](../users-groups-roles/directory-emergency-access.md) ed escludendo questi ultimi dai criteri di accesso condizionale.

### <a name="create-conditional-access-policy"></a>Creare criteri di accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.
1. Passare ad **Azure Active Directory** , **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
   ![creare un criterio di accesso condizionale per abilitare l'autenticazione a più fattori per portale di Azure utenti del gruppo pilota](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Immettere un nome significativo per i criteri.
1. In **Utenti e gruppi**:
   * Nella scheda **Includere** selezionare il pulsante di opzione **Tutti gli utenti**
   * Nella scheda **Escludi** selezionare la casella per **utenti e gruppi** e scegliere gli account di accesso di emergenza.
   * Fare clic su **Done**.
1. In **App cloud** selezionare il pulsante di opzione **Tutte le app cloud**.
   * FACOLTATIVAMENTE: nella scheda **Escludere** scegliere le app cloud per le quali l'organizzazione non richiede MFA.
   * Fare clic su **Done**.
1. Nella sezione **Condizioni**:
   * FACOLTATIVAMENTE: se è stato abilitato Azure Identity Protection, è possibile scegliere di valutare il rischio di accesso come parte dei criteri.
   * FACOLTATIVAMENTE: se sono state configurate posizioni attendibili o località denominate, è possibile specificare di includere o escludere tali percorsi dai criteri.
1. In **Concedi** assicurarsi che il pulsante di opzione **Concedi accesso** sia selezionato.
    * Selezionare la casella **Richiedi autenticazione a più fattori**.
    * Fare clic su **Seleziona**.
1. Ignorare la sezione **Sessione**.
1. Impostare l'interruttore **Abilita criterio** su **Sì**.
1. Fare clic su **Create**(Crea).

## <a name="plan-integration-with-on-premises-systems"></a>Pianificare l'integrazione con i sistemi locali

Alcune applicazioni legacy e locali che non eseguono l'autenticazione direttamente in Azure AD richiedono passaggi aggiuntivi per l'uso dell'autenticazione a più fattori, tra cui:

* Applicazioni locali legacy, che dovranno usare il proxy di applicazione.
* Applicazioni RADIUS locali, che dovranno usare la scheda autenticazione a più fattori con server NPS.
* Applicazioni AD FS locali, che dovranno usare la scheda multi-factor authentication con AD FS 2016 o versione successiva.

Le applicazioni che eseguono l'autenticazione direttamente con Azure AD e hanno l'autenticazione moderna (WS-Fed, SAML, OAuth, OpenID Connect) possono usare direttamente i criteri di accesso condizionale.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Usare l'autenticazione a più fattori di Azure con Azure AD proxy applicazione

Le applicazioni che risiedono in locale possono essere pubblicate nel tenant di Azure AD tramite [Azure ad proxy di applicazione](../manage-apps/application-proxy.md) e possono sfruttare i vantaggi di Azure multi-factor authentication se sono configurate per l'uso Azure ad pre-autenticazione.

Queste applicazioni sono soggette ai criteri di accesso condizionale che applicano Multi-Factor Authentication di Azure, proprio come qualsiasi altra applicazione integrata Azure AD.

Analogamente, se Azure Multi-Factor Authentication viene applicato per tutti gli accessi degli utenti, le applicazioni locali pubblicate con Azure AD proxy di applicazione verranno protette.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrazione di Multi-Factor Authentication di Azure con il server dei criteri di rete

L'estensione di Server dei criteri di rete (NPS) per Azure MFA aggiunge funzionalità MFA basate su cloud per l'infrastruttura di autenticazione usando i server esistenti. Con l'estensione NPS, è possibile aggiungere una chiamata telefonica, un SMS o una verifica dell'app telefonica al flusso di autenticazione esistente. Questa integrazione presenta le limitazioni seguenti:

* Con il protocollo CHAPv2, sono supportate solo le notifiche push dell'app di autenticazione e la chiamata vocale.
* Non è possibile applicare i criteri di accesso condizionale.

L'estensione server dei criteri di rete funge da adattatore tra RADIUS e l'autenticazione a più fattori di Azure basata sul cloud per fornire un secondo fattore di autenticazione per proteggere la [VPN](howto-mfa-nps-extension-vpn.md), le [connessioni Desktop remoto Gateway](howto-mfa-nps-extension-rdg.md)o altre applicazioni che supportano RADIUS. Gli utenti registrati per l'autenticazione a più fattori di Azure in questo ambiente verranno contestati per tutti i tentativi di autenticazione, la mancanza di criteri di accesso condizionale significa che l'autenticazione a più fattori è sempre obbligatoria

#### <a name="implementing-your-nps-server"></a>Implementazione del server NPS

Se è già stata distribuita un'istanza NPS e in uso, fare riferimento a [integrare l'infrastruttura NPS esistente con multi-factor authentication di Azure](howto-mfa-nps-extension.md). Se si sta configurando Server dei criteri di rete per la prima volta, fare riferimento a [Server dei criteri di rete (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) per le istruzioni. Le linee guida per la risoluzione dei problemi sono disponibili nell'articolo [risolvere i messaggi di errore dall'estensione NPS per Azure multi-factor authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Preparare NPS per utenti che non sono registrati per l'autenticazione a più fattori

Scegliere cosa accade quando gli utenti che non sono registrati con l'autenticazione a più fattori tentano di eseguire l'autenticazione. Usare l'impostazione del registro di sistema `REQUIRE_USER_MATCH` nel percorso del registro di sistema `HKLM\Software\Microsoft\AzureMFA` per controllare il comportamento della funzionalità. Questa impostazione ha un'unica opzione di configurazione.

| Chiave | Value | Predefinito |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE/FALSE | Non impostato (equivalente a VERO) |

Lo scopo di questa impostazione è stabilire cosa fare quando un utente non è registrato per MFA. Gli effetti della modifica di questa impostazione sono elencati nella tabella seguente.

| Impostazioni | Stato dell'autenticazione a più fattori | Effetti |
| --- | --- | --- |
| La chiave non esiste | Non registrato | La richiesta di autenticazione a più fattori non è riuscita |
| Il valore è impostato su true/not set | Non registrato | La richiesta di autenticazione a più fattori non è riuscita |
| Chiave impostata su false | Non registrato | Autenticazione senza autenticazione a più fattori |
| La chiave è impostata su false o true | Registrati | Eseguire l'autenticazione con l'autenticazione a più fattori |

### <a name="integrate-with-active-directory-federation-services"></a>Integrazione con Active Directory Federation Services

Se l'organizzazione è federata con Azure AD, è possibile usare [multi-factor authentication di Azure per proteggere le risorse ad FS](multi-factor-authentication-get-started-adfs.md), sia in locale che nel cloud. Autenticazione a più fattori di Azure consente di ridurre le password e fornire un modo più sicuro per l'autenticazione. A partire da Windows Server 2016, è ora possibile configurare l'autenticazione a più fattori di Azure per l'autenticazione principale.

A differenza di quanto accade con AD FS in Windows Server 2012 R2, l'adapter multi-factor authentication di Azure AD FS 2016 si integra direttamente con Azure AD e non richiede un server di autenticazione a più fattori locale di Azure. La scheda multi-factor authentication di Azure è incorporata in Windows Server 2016 e non è necessaria un'installazione aggiuntiva.

Quando si usa l'autenticazione a più fattori di Azure con AD FS 2016 e l'applicazione di destinazione è soggetta ai criteri di accesso condizionale, esistono ulteriori considerazioni:

* L'accesso condizionale è disponibile quando l'applicazione è un relying party Azure AD, federata con AD FS 2016 o versione successiva.
* L'accesso condizionale non è disponibile quando l'applicazione è un relying party AD FS 2016 o AD FS 2019 e viene gestita o federata con AD FS 2016 o AD FS 2019.
* L'accesso condizionale non è disponibile anche quando AD FS 2016 o AD FS 2019 è configurato per usare l'autenticazione a più fattori di Azure come metodo di autenticazione principale.

#### <a name="ad-fs-logging"></a>Registrazione AD FS

La registrazione standard AD FS 2016 e 2019 nel registro di sicurezza di Windows e nel registro AD FS amministratore contiene informazioni sulle richieste di autenticazione e il relativo esito positivo o negativo. I dati del registro eventi all'interno di questi eventi indicheranno se è stato usato il multi-factor authentication. Ad esempio, un evento di controllo AD FS ID 1200 può contenere:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Rinnovo e gestione dei certificati

In ogni server AD FS, nel computer locale archivio personale, sarà presente un certificato di autenticazione a più fattori di Azure autofirmato denominato OU = Microsoft AD FS Azure multi-factor authentication, che contiene la data di scadenza del certificato. Verificare il periodo di validità del certificato in ogni server AD FS per determinare la data di scadenza.

Se il periodo di validità dei certificati è prossimo alla scadenza, [generare e verificare un nuovo certificato multi-factor authentication in ogni server ad FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Le linee guida seguenti illustrano come gestire i certificati di autenticazione a più fattori di Azure nei server AD FS. Quando si configura AD FS con l'autenticazione a più fattori di Azure, i certificati generati tramite il cmdlet `New-AdfsAzureMfaTenantCertificate` PowerShell sono validi per 2 anni. Rinnovare e installare i certificati rinnovati prima della scadenza per le rotture di ovoidale nel servizio multi-factor authentication.

## <a name="implement-your-plan"></a>Implementare il piano

Ora che è stata pianificata la soluzione, è possibile implementare attenendosi alla procedura seguente:

1. Soddisfare i prerequisiti necessari
   1. Distribuisci [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) per qualsiasi scenario ibrido
   1. Distribuire [Azure ad proxy di applicazione](../manage-apps/application-proxy.md) per le app locali pubblicate per l'accesso al cloud
   1. Distribuire [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) per qualsiasi autenticazione RADIUS
   1. Assicurarsi che gli utenti abbiano eseguito l'aggiornamento alle versioni supportate di Microsoft Office con l'autenticazione moderna abilitata
1. Configura [metodi di autenticazione](#choose-verification-options) scelti
1. Definire i [percorsi di rete denominati](../conditional-access/location-condition.md#named-locations)
1. Selezionare i gruppi per iniziare a implementare l'autenticazione a più fattori.
1. Configurare i [criteri di accesso condizionale](#create-conditional-access-policy)
1. Configurare i criteri di registrazione di autenticazione a più fattori
   1. [Multi-factor authentication e SSPR](howto-registration-mfa-sspr-combined.md)
   1. Con [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Inviare le comunicazioni utente e chiedere agli utenti di registrarsi all' [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Tenere traccia degli utenti registrati](#identify-non-registered-users)

> [!TIP]
> Gli utenti del cloud per enti pubblici possono registrarsi all' [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Gestire la soluzione

Report per l'autenticazione a più fattori di Azure

Azure Multi-Factor Authentication fornisce report tramite i portale di Azure:

| Documentazione | Località | Description |
| --- | --- | --- |
| Avvisi di illecito e utilizzo | Azure AD > Accessi | Fornisce informazioni su utilizzo complessivo, riepilogo utenti e dettagli utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |

## <a name="troubleshoot-mfa-issues"></a>Risolvere i problemi di autenticazione a più fattori

Trovare soluzioni per problemi comuni con l'autenticazione a più fattori di Azure nell' [articolo sulla risoluzione dei problemi di azure multi-factor authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) in supporto tecnico Microsoft Center.

## <a name="next-steps"></a>Passaggi successivi

* [Cosa si intende per metodi di autenticazione?](concept-authentication-methods.md)
* [Abilitare la registrazione convergente per Azure Multi-Factor Authentication e la reimpostazione della password self-service di Azure AD](concept-registration-mfa-sspr-converged.md)
* Perché a un utente è stato richiesto o non è stato richiesto di eseguire MFA? Vedere la sezione [Report sugli accessi ad Azure AD nel documento Report in Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
