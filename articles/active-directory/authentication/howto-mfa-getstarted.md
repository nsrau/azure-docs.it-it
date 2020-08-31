---
title: Considerazioni sulla distribuzione di Azure Multi-Factor Authentication
description: Informazioni sulle considerazioni e sulla strategia di distribuzione per una corretta implementazione di Azure Multi-Factor Authentication
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
ms.openlocfilehash: da9a0fe30721a2f55db2c4771f54716868f9972f
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055074"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Pianificare una distribuzione di Azure Multi-Factor Authentication

Le persone si connettono alle risorse dell'organizzazione in scenari sempre più complessi. Si connettono e disconnettono alla e dalla rete aziendale da dispositivi di proprietà dell'organizzazione, personali e pubblici, usando smartphone, tablet, PC e portatili, spesso su più piattaforme. In questo mondo sempre connesso, su più dispositivi e più piattaforme, la sicurezza degli account utente è più importante che mai. Le password, a prescindere dalla loro complessità, usate su dispositivi, reti e piattaforme non sono più sufficienti per garantire la sicurezza dell'account utente, specialmente quando gli utenti tendono a usare le password per più account. Il phishing sofisticato e altri attacchi di ingegneria sociale possono causare la pubblicazione e la vendita di nomi utente e password nel dark web.

[Azure Multi-Factor Authentication](concept-mfa-howitworks.md) consente di proteggere l'accesso ai dati e alle applicazioni. Offre un livello di sicurezza aggiuntivo usando una seconda forma di autenticazione. Le organizzazioni possono usare l'[accesso condizionale](../conditional-access/overview.md) per adeguare la soluzione alle proprie esigenze specifiche.

Questa guida alla distribuzione illustra come pianificare e quindi testare un'implementazione di Azure Multi-Factor Authentication.

Per vedere rapidamente Azure Multi-Factor Authentication in azione e quindi tornare a esaminare ulteriori considerazioni sulla distribuzione:

> [!div class="nextstepaction"]
> [Abilitare Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare una distribuzione di Azure Multi-Factor Authentication, è necessario prendere in considerazione alcuni prerequisiti.

| Scenario | Prerequisito |
| --- | --- |
| Ambiente di gestione delle identità **solo cloud** con autenticazione moderna | **Nessuna attività essenziale aggiuntiva** |
| Scenari con identità **ibride** | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) viene distribuito e le identità utente vengono sincronizzate o federate con l'istanza locale di Active Directory Domain Services con Azure Active Directory. |
| Applicazioni legacy locali pubblicate per l'accesso al cloud | Il [proxy di applicazione](../manage-apps/application-proxy.md) di Azure AD viene distribuito. |
| Uso di Azure MFA con l'autenticazione RADIUS | Viene distribuito un [server dei criteri di rete](howto-mfa-nps-extension.md). |
| Gli utenti hanno Microsoft Office 2010 o versioni precedenti o Apple Mail per iOS 11 o versioni precedenti | Aggiornamento a [Microsoft Office 2013 o versioni successive](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e Apple Mail per iOS 12 o versioni successive. L'accesso condizionale non è supportato dai protocolli di autenticazione legacy. |

## <a name="plan-user-rollout"></a>Pianificare l'implementazione per gli utenti

Il piano di implementazione di MFA deve includere una distribuzione pilota seguita da cicli di distribuzione che rientrano nella capacità di supporto. Per iniziare l'implementazione, applicare i criteri di accesso condizionale a un piccolo gruppo di utenti pilota. Dopo aver valutato l'effetto sugli utenti pilota, i processi usati e i comportamenti di registrazione, è possibile aggiungere altri gruppi ai criteri o aggiungere altri utenti ai gruppi esistenti.

### <a name="user-communications"></a>Comunicazioni per gli utenti

È fondamentale informare gli utenti, con comunicazioni pianificate, riguardo alle modifiche imminenti, ai requisiti di registrazione di Azure MFA e a eventuali azioni utente necessarie. È consigliabile che le comunicazioni vengano elaborate insieme a rappresentanti della propria organizzazione, ad esempio dei reparti addetti a comunicazioni, gestione del cambiamento o risorse umane.

Microsoft offre [modelli di comunicazione](https://aka.ms/mfatemplates) e [documentazione per l'utente finale](../user-help/security-info-setup-signin.md) per facilitare la stesura delle comunicazioni. È possibile indirizzare gli utenti a [https://myprofile.microsoft.com](https://myprofile.microsoft.com) in modo che eseguano la registrazione direttamente selezionando i collegamenti alle **informazioni di sicurezza** nella pagina.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Azure Multi-Factor Authentication viene distribuito applicando i criteri con accesso condizionale. Un criterio di accesso condizionale può richiedere agli utenti di eseguire l'autenticazione a più fattori quando vengono soddisfatti determinati criteri, ad esempio:

* Tutti gli utenti, un utente specifico, un membro di un gruppo o un ruolo assegnato
* Applicazione cloud specifica a cui si accede
* Piattaforma del dispositivo.
* Stato del dispositivo
* Percorso di rete o indirizzo IP geograficamente localizzato
* Applicazioni client
* Rischio di accesso (richiede Identity Protection)
* Dispositivo conforme
* Dispositivo aggiunto all'identità ibrida di Azure AD
* Applicazione client approvata

Usare i poster personalizzabili e i modelli di messaggi di posta elettronica nei [materiali di implementazione dell'autenticazione a più fattori](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) per implementare l'autenticazione a più fattori nell'organizzazione.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Abilitare Multi-Factor Authentication con l'accesso condizionale

I criteri di accesso condizionale applicano la registrazione, richiedendo agli utenti non registrati di completare la registrazione al primo accesso, una considerazione importante per la sicurezza.

[Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) offre sia criteri di registrazione che criteri di rilevamento e correzione automatici dei rischi per Azure Multi-Factor Authentication. È possibile creare criteri per forzare la modifica delle password quando esiste una minaccia di identità compromessa o per richiedere l'autenticazione a più fattori quando un accesso viene considerato rischioso in base agli [eventi](../identity-protection/overview-identity-protection.md) seguenti:

* Credenziali perse
* Accessi da indirizzi IP anonimi
* Trasferimento impossibile a posizioni atipiche
* Accessi da posizioni non note
* Accessi da dispositivi infetti
* Accessi da indirizzi IP con attività sospette

Alcuni dei rilevamenti dei rischi eseguiti da Azure Active Directory Identity Protection si verificano in tempo reale e altri richiedono l'elaborazione offline. Gli amministratori possono scegliere di bloccare gli utenti con comportamenti rischiosi e correggere il problema manualmente, richiedere una modifica della password o richiedere un'autenticazione a più fattori come parte dei criteri di accesso condizionale.

## <a name="define-network-locations"></a>Definire i percorsi di rete

È consigliabile che le organizzazioni usino l'accesso condizionale per definire la propria rete usando le [località denominate](../conditional-access/location-condition.md#named-locations). Se l'organizzazione usa Identity Protection, considerare la possibilità di usare criteri basati sul rischio anziché località denominate.

### <a name="configuring-a-named-location"></a>Configurazione di una località denominata

1. Aprire **Azure Active Directory** nel portale di Azure
2. Scegliere **Sicurezza**
3. In **Gestisci** scegliere **Località denominate**
4. Selezionare **Nuova località**
5. Specificare un nome significativo nel campo **Nome**
6. Indicare se si sta definendo la località usando *Intervalli IP* o *Paesi/aree geografiche*
   1. Se si usano gli *intervalli IP*
      1. Decidere se *contrassegnare come posizione attendibile*. L'accesso da una posizione attendibile riduce il rischio di accesso dell'utente. Contrassegnare questa posizione come attendibile solo se si è certi che gli intervalli IP immessi siano stabiliti e credibili nell'organizzazione.
      2. Specificare gli intervalli IP
   2. Se si usa *Paesi/aree geografiche*
      1. Espandere il menu a discesa e selezionare i paesi o le aree geografiche che si vuole definire per la località denominata.
      2. Decidere se *includere le aree sconosciute*. Le aree sconosciute sono indirizzi IP di cui non è possibile eseguire il mapping a un paese o un'area geografica.
7. Selezionare **Crea**

## <a name="plan-authentication-methods"></a>Pianificare i metodi di autenticazione

Gli amministratori possono scegliere i [metodi di autenticazione](../authentication/concept-authentication-methods.md) che vogliono mettere a disposizione degli utenti. È importante consentire più di un singolo metodo di autenticazione in modo che gli utenti possano contare su un metodo di backup nel caso in cui il metodo principale non sia disponibile. I metodi seguenti sono disponibili per l'abilitazione da parte degli amministratori:

> [!TIP]
> Microsoft consiglia di usare il Microsoft Authenticator (app per dispositivi mobili) come metodo principale per Azure Multi-Factor Authentication per un'esperienza utente più sicura e migliorata. L'app Microsoft Authenticator inoltre [soddisfa](https://azure.microsoft.com/resources/microsoft-nist/) i livelli di garanzia del National Institute of Standards and Technology Authenticator. 

### <a name="notification-through-mobile-app"></a>Notifica tramite app per dispositivi mobili

Viene inviata una notifica push all'app Microsoft Authenticator sul dispositivo mobile. L'utente visualizza la notifica e seleziona **Approva** per completare la verifica. Le notifiche push a un'app per dispositivi mobili sono l'opzione meno intrusiva per gli utenti. Sono anche l'opzione più affidabile e sicura perché usano una connessione dati anziché la telefonia.

> [!NOTE]
> Se il personale dell'organizzazione lavora o viaggia verso la Cina, il metodo di **notifica all'app per dispositivi mobili** nei **dispositivi Android** non funziona in quel paese o in quell'area geografica. Per questi utenti è necessario rendere disponibili metodi alternativi.

### <a name="verification-code-from-mobile-app"></a>Codice di verifica dall'app per dispositivi mobili

Un'app per dispositivi mobili come Microsoft Authenticator genera un nuovo codice di verifica OATH ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso. L'app per dispositivi mobili può essere usata indipendentemente dal fatto che il telefono abbia o meno un segnale dati o cellulare.

### <a name="call-to-phone"></a>Chiamata al telefono

Viene inviata all'utente una chiamata vocale automatizzata. Per approvare l'autenticazione, l'utente risponde alla chiamata e preme **#** sul tastierino telefonico. La chiamata al telefono è un ottimo metodo di backup per la notifica o il codice di verifica da un'app per dispositivi mobili.

### <a name="text-message-to-phone"></a>SMS al telefono

Viene inviato all'utente un SMS con il codice di verifica e viene chiesto all'utente di immettere il codice di verifica nell'interfaccia di accesso.

### <a name="choose-verification-options"></a>Scegliere le opzioni di verifica

1. Passare ad **Azure Active Directory**, **Utenti**, **Multi-Factor Authentication**.

   ![Accesso al portale Multi-Factor Authentication dal pannello Utenti di Azure AD nel portale di Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Nella nuova scheda che si apre passare a **Impostazioni del servizio**.
1. In **Opzioni di verifica** selezionare tutte le caselle per i metodi disponibili per gli utenti.

   ![Configurare i metodi di verifica nella scheda delle impostazioni del servizio Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Fare clic su **Save**.
1. Chiudere la scheda **Impostazioni del servizio**.

## <a name="plan-registration-policy"></a>Pianificare i criteri di registrazione

Gli amministratori devono determinare il modo in cui gli utenti registreranno i propri metodi. Le organizzazioni devono [abilitare la nuova esperienza di registrazione combinata](howto-registration-mfa-sspr-combined.md) per Azure MFA e la reimpostazione della password self-service (SSPR). SSPR consente agli utenti di reimpostare la password in modo sicuro usando gli stessi metodi usati per l'autenticazione a più fattori. Questa registrazione combinata è consigliabile perché si tratta di un'esperienza ottimale per gli utenti, con la possibilità di registrarsi una sola volta per entrambi i servizi. L'abilitazione degli stessi metodi per SSPR e Azure MFA consentirà agli utenti di essere registrati per usare entrambe le funzionalità.

### <a name="registration-with-identity-protection"></a>Registrazione con Identity Protection

Se l'organizzazione usa Azure Active Directory Identity Protection, [configurare i criteri di registrazione MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) per richiedere agli utenti di registrarsi al successivo accesso interattivo.

### <a name="registration-without-identity-protection"></a>Registrazione senza Identity Protection

Se l'organizzazione non ha licenze che abilitano Identity Protection, agli utenti viene richiesto di effettuare la registrazione la volta successiva che per accedere è necessaria l'autenticazione a più fattori. Gli utenti potrebbero non essere registrati per MFA se non usano applicazioni protette con l'autenticazione a più fattori. È importante che tutti gli utenti siano registrati in modo che eventuali malintenzionati non possano indovinare la password di un utente e registrarsi per MFA a suo nome, assumendo di fatto il controllo dell'account.

#### <a name="enforcing-registration"></a>Applicazione della registrazione

Usando i passaggi seguenti, un criterio di accesso condizionale può obbligare gli utenti a registrarsi per MFA

1. Creare un gruppo, aggiungere tutti gli utenti non attualmente registrati.
2. Usando l'accesso condizionale, applicare l'autenticazione a più fattori per questo gruppo per l'accesso a tutte le risorse.
3. Periodicamente, riconsiderare l'appartenenza al gruppo e rimuovere gli utenti che hanno eseguito la registrazione dal gruppo.

È possibile identificare gli utenti registrati e non registrati di Azure MFA con i comandi di PowerShell che si basano sul [modulo di PowerShell MSOnline](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificare gli utenti registrati

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificare gli utenti non registrati

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertire gli utenti dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionale

Se gli utenti sono stati abilitati usando Azure Multi-Factor Authentication abilitata e applicata per utente, il seguente comando di PowerShell può risultare utile per eseguire la conversione in Azure Multi-Factor Authentication basata sull'accesso condizionale.

Eseguire questo comando PowerShell in una finestra ISE o salvarlo come file `.PS1` da eseguire localmente.

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
> Il comportamento e lo script PowerShell sono stati modificati di recente di conseguenza. In precedenza, lo script salvava i metodi di MFA, disabilitava MFA e ripristinava i metodi. Questo non è più necessario, ora che il comportamento predefinito per la disabilitazione non cancella i metodi.

## <a name="plan-conditional-access-policies"></a>Pianificare i criteri di accesso condizionale

Per pianificare la strategia dei criteri di accesso condizionale, che determina quando sono necessari MFA e altri controlli, vedere [Criteri comuni di accesso condizionale](../conditional-access/concept-conditional-access-policy-common.md).

È importante evitare di essere inavvertitamente bloccati fuori dal tenant di Azure AD. È possibile ridurre l'impatto di questa accidentale mancanza di accesso amministrativo [creando due o più account di accesso di emergenza nel tenant](../users-groups-roles/directory-emergency-access.md) ed escludendoli dai criteri di accesso condizionale.

### <a name="create-conditional-access-policy"></a>Creare criteri di accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
   ![Creare un criterio di accesso condizionale per abilitare MFA per gli utenti del portale di Azure nel gruppo pilota](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Immettere un nome significativo per i criteri.
1. In **Utenti e gruppi**:
   * Nella scheda **Includere** selezionare il pulsante di opzione **Tutti gli utenti**
   * Nella scheda **Escludere** selezionare la casella per **Utenti e gruppi** e scegliere gli account di accesso di emergenza.
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
1. Fare clic su **Crea**.

## <a name="plan-integration-with-on-premises-systems"></a>Pianificare l'integrazione con i sistemi locali

Alcune applicazioni legacy e locali che non eseguono l'autenticazione direttamente in Azure AD richiedono passaggi aggiuntivi per l'uso di MFA, tra cui:

* Applicazioni legacy locali, che dovranno usare il proxy dell'applicazione.
* Applicazioni RADIUS locali, che dovranno usare la scheda MFA con Server dei criteri di rete.
* Applicazioni AD FS locali, che dovranno usare la scheda MFA con AD FS 2016 o versione successiva.

Le applicazioni che eseguono l'autenticazione direttamente con Azure AD e hanno l'autenticazione moderna (WS-Fed, SAML, OAuth, OpenID Connect) possono usare direttamente i criteri di accesso condizionale.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Usare Azure MFA con il proxy di applicazione di Azure AD

Le applicazioni che risiedono in locale possono essere pubblicate nel tenant di Azure AD usando il [proxy di applicazione di Azure AD](../manage-apps/application-proxy.md) e possono sfruttare i vantaggi offerti da Azure Multi-Factor Authentication se sono configurate per l'uso della preautenticazione di Azure AD.

Queste applicazioni sono soggette ai criteri di accesso condizionale che applicano Azure Multi-Factor Authentication, proprio come qualsiasi altra applicazione integrata in Azure AD.

Analogamente, se Azure Multi-Factor Authentication viene applicata per tutti gli accessi degli utenti, le applicazioni locali pubblicate con il proxy di applicazione di Azure AD verranno protette.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrazione di Azure Multi-Factor Authentication con Server dei criteri di rete

L'estensione di Server dei criteri di rete (NPS) per Azure MFA aggiunge funzionalità MFA basate su cloud per l'infrastruttura di autenticazione usando i server esistenti. Con l'estensione NPS è possibile aggiungere la verifica con telefonata, SMS o app telefonica al flusso di autenticazione esistente. Questa integrazione presenta le limitazioni seguenti:

* Con il protocollo CHAPv2, sono supportate solo le notifiche push dell'app di autenticazione e la chiamata vocale.
* I criteri di accesso condizionale non possono essere applicati.

L'estensione NPS funge da adattatore tra RADIUS e Azure MFA basata sul cloud per offrire un secondo fattore di autenticazione per proteggere la rete [VPN](howto-mfa-nps-extension-vpn.md), le [connessioni per Gateway Desktop remoto](howto-mfa-nps-extension-rdg.md) o altre applicazioni con supporto RADIUS. Per gli utenti che si registrano per Azure MFA in questo ambiente verranno verificati tutti i tentativi di autenticazione, la mancanza di criteri di accesso condizionale significa che l'autenticazione a più fattori è sempre obbligatoria.

#### <a name="implementing-your-nps-server"></a>Implementazione del server NPS

Se è già stata distribuita ed è in uso un'istanza di NPS, vedere [Integrare l'infrastruttura NPS esistente con Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). Se si sta configurando NPS per la prima volta, fare riferimento a [Server dei criteri di rete (NPS)](/windows-server/networking/technologies/nps/nps-top) per le istruzioni. Indicazioni utili per la risoluzione dei problemi sono contenute nell'articolo [Risolvere i messaggi di errore dall'estensione NPS per Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Impostazioni di NPS per gli utenti che non sono registrati per MFA

Scegliere cosa accade quando gli utenti non registrati per MFA tentano di eseguire l'autenticazione. Usare l'impostazione del registro `REQUIRE_USER_MATCH` nel percorso del registro `HKLM\Software\Microsoft\AzureMFA` per controllare il comportamento della funzionalità. Questa impostazione ha un'unica opzione di configurazione.

| Chiave | valore | Predefinito |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | Non impostato (equivalente a VERO) |

Lo scopo di questa impostazione è stabilire cosa fare quando un utente non è registrato per MFA. Gli effetti della modifica di questa impostazione sono elencati nella tabella seguente.

| Impostazioni | Stato MFA utente | Effetti |
| --- | --- | --- |
| Chiave inesistente | Non registrato | Richiesta di verifica MFA non riuscita |
| Valore impostato su True/non impostato | Non registrato | Richiesta di verifica MFA non riuscita |
| Chiave impostata su False. | Non registrato | Autenticazione senza MFA |
| Chiave impostata su False o True | Registrato | Deve eseguire l'autenticazione con MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrare con Active Directory Federation Services

Se l'organizzazione è federata con Azure AD, è possibile usare [Azure Multi-Factor Authentication per proteggere le risorse AD FS](multi-factor-authentication-get-started-adfs.md), sia in locale che nel cloud. Azure MFA consente di ridurre le password e di usare un metodo più sicuro per l'autenticazione. A partire da Windows Server 2016 è possibile configurare Azure MFA per l'autenticazione primaria.

A differenza di quanto accade con AD FS in Windows Server 2012 R2, l'adapter MFA di Azure per AD FS 2016 si integra direttamente con Azure AD e non richiede un server di Azure MFA locale. L'adapter MFA di Azure è incorporato in Windows Server 2016 e non è necessaria un'installazione aggiuntiva.

Quando si usa Azure MFA con AD FS 2016 e l'applicazione di destinazione è soggetta ai criteri di accesso condizionale, esistono ulteriori considerazioni:

* L'accesso condizionale è disponibile quando l'applicazione è una relying party per Azure AD, federata con AD FS 2016 o versione successiva.
* L'accesso condizionale non è disponibile quando l'applicazione è una relying party per AD FS 2016 o AD FS 2019 ed è gestita o federata con AD FS 2016 o AD FS 2019.
* L'accesso condizionale non è disponibile anche quando AD FS 2016 o AD FS 2019 è configurato per usare Azure MFA come metodo di autenticazione principale.

#### <a name="ad-fs-logging"></a>Registrazione di AD FS

La registrazione standard di AD FS 2016 e 2019 nel log di sicurezza di Windows e nel log di amministrazione di AD FS contiene informazioni sulle richieste di autenticazione e il relativo esito positivo o negativo. I dati del log eventi all'interno di questi eventi indicheranno se è stato usato Azure MFA. Ad esempio, un evento di controllo AD FS ID 1200 può contenere:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Rinnovare e gestire i certificati

In ogni server AD FS, nell'archivio personale del computer locale, sarà presente un certificato di Azure MFA autofirmato dal titolo OU=Microsoft AD FS Azure MFA, che contiene la data di scadenza del certificato. Verificare il periodo di validità del certificato in ogni server AD FS per determinare la data di scadenza.

Se il periodo di validità dei certificati è prossimo alla scadenza, [generare e verificare un nuovo certificato MFA in ogni server AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Le linee guida seguenti illustrano come gestire i certificati di Azure MFA nei server AD FS. Quando si configura AD FS con Azure MFA, i certificati generati usando il cmdlet `New-AdfsAzureMfaTenantCertificate` di PowerShell sono validi per due anni. Rinnovare e installare i certificati rinnovati prima della scadenza per evitare interruzioni del servizio MFA.

## <a name="implement-your-plan"></a>Implementare il piano

Ora che è stata pianificata la soluzione, è possibile implementarla attenendosi alla procedura seguente:

1. Soddisfare eventuali prerequisiti necessari
   1. Distribuire [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) per tutti gli scenari ibridi
   1. Distribuire il [proxy di applicazione di Azure AD](../manage-apps/application-proxy.md) per tutte le app locali pubblicate per l'accesso al cloud
   1. Distribuire [NPS](/windows-server/networking/technologies/nps/nps-top) per l'autenticazione RADIUS
   1. Verificare che gli utenti abbiano eseguito l'aggiornamento alle versioni supportate di Microsoft Office con l'autenticazione moderna abilitata
1. Configurare i [metodi di autenticazione](#choose-verification-options) scelti
1. Definire i [percorsi di rete denominati](../conditional-access/location-condition.md#named-locations)
1. Selezionare i gruppi per iniziare a implementare MFA.
1. Configurare i [criteri di accesso condizionale](#create-conditional-access-policy)
1. Configurare i criteri di registrazione MFA
   1. [Combinazione di MFA e SSPR](howto-registration-mfa-sspr-combined.md)
   1. Con [Identity Protection](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. Inviare le comunicazioni agli utenti e invitarli a registrarsi all'indirizzo [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Tenere traccia degli utenti registrati](#identify-non-registered-users)

> [!TIP]
> Gli utenti del cloud per enti pubblici possono registrarsi su [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Gestire la soluzione

Report per Azure MFA

Azure Multi-Factor Authentication mette a disposizione dei report dal portale di Azure:

| Report | Location | Descrizione |
| --- | --- | --- |
| Avvisi di illecito e utilizzo | Azure AD > Accessi | Fornisce informazioni su utilizzo complessivo, riepilogo utenti e dettagli utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |

## <a name="troubleshoot-mfa-issues"></a>Risolvere i problemi di MFA

Trovare soluzioni per problemi comuni di Azure MFA nell'articolo relativo alla [risoluzione dei problemi di Azure Multi-Factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) del centro di supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per vedere Azure Multi-Factor Authentication in azione, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Abilitare Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)
