---
title: Pianificare ed eseguire una distribuzione di Azure multi-Factor Authentication - Azure Active Directory
description: Pianificazione della distribuzione di Microsoft Azure multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b91af553c402cc1cb241e51e2bb2289bf45b1825
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269028"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication"></a>Pianificazione di un server basato su cloud Azure multi-Factor Authentication

Gli utenti si connettono a risorse aziendali in scenari sempre più complessi. Gli utenti di connettersi da dispositivi di proprietà dell'organizzazione, personali e pubblici in e connessi alla rete aziendale usando gli Smartphone, Tablet, PC e portatili, spesso su più piattaforme. In questo mondo sempre connesso, multidispositivo e multipiattaforma, la sicurezza degli account utente è più importante che mai. Le password, indipendentemente dalla loro complessità, usati nei dispositivi, reti e piattaforme non sono più sufficienti per garantire la sicurezza dell'account utente, specialmente quando gli utenti tendono a riutilizzare le password degli account. Sofisticati phishing e altri attacchi possono comportare i nomi utente e password viene registrato e venduti attraverso il dark web l'ingegneria sociale.

[Azure multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) consente di proteggere l'accesso ai dati e applicazioni. Fornisce un ulteriore livello di sicurezza tramite una seconda forma di autenticazione. Le organizzazioni possono usare [accesso condizionale](../conditional-access/overview.md) per la soluzione adatta alle esigenze specifiche.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare una distribuzione di Azure multi-Factor Authentication, ci sono elementi dei prerequisiti che devono essere considerati.

| Scenario | Prerequisito |
| --- | --- |
| **Solo cloud** ambiente identità con l'autenticazione moderna | **Nessuna attività prerequisita aggiuntiva** |
| **Hybrid** scenari relativi alle identità | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) viene distribuito e le identità degli utenti vengono sincronizzate o federate con i servizi di dominio locali Active Directory con Azure Active Directory. |
| Le applicazioni legacy pubblicate per l'accesso cloud in locale | Azure AD [Proxy di applicazione](../manage-apps/application-proxy.md) viene distribuito. |
| Uso di Azure MFA con l'autenticazione RADIUS | Oggetto [Strumentazione gestione Windows (NPS, Network Policy Server)](howto-mfa-nps-extension.md) viene distribuito. |
| Gli utenti con Microsoft Office 2010 o versioni precedenti, o posta elettronica di Apple per iOS 11 o versione precedente | Eseguire l'aggiornamento a [Microsoft Office 2013 o versioni successive](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e posta elettronica di Apple per iOS 12 o versione successiva. Accesso condizionale non è supportato dai protocolli di autenticazione legacy. |

## <a name="plan-user-rollout"></a>Pianificare la distribuzione utente

Il piano di implementazione di autenticazione a più fattori deve includere una distribuzione pilota, seguita da diverse fasi di distribuzione che si trovano all'interno della capacità di supporto. Iniziare l'implementazione applicando i criteri di accesso condizionale per un piccolo gruppo di utenti pilota. Dopo aver valutato l'effetto su di utenti pilota, processo usato e i comportamenti di registrazione, è possibile aggiungere altri gruppi al criterio o aggiungere altri utenti ai gruppi esistenti.

### <a name="user-communications"></a>Comunicazioni degli utenti

È fondamentale per notificare agli utenti, nelle comunicazioni pianificate, le modifiche imminenti, i requisiti di registrazione di Azure MFA e tutte le azioni utente necessarie. È consigliabile che le comunicazioni vengono sviluppate in concerto con i rappresentanti all'interno dell'organizzazione, ad esempio un reparti delle risorse umane, la gestione delle modifiche o le comunicazioni.

Microsoft fornisce [modelli di comunicazione](https://aka.ms/mfatemplates) e [documentazione dell'utente finale](../user-help/security-info-setup-signin.md) per consentire le comunicazioni in versione bozza. È possibile inviare agli utenti [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) per registrare direttamente selezionando la **Info di sicurezza** collegamenti presenti in tale pagina.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Azure multi-factor Authentication viene distribuito tramite l'applicazione dei criteri di accesso condizionale. Oggetto [criteri di accesso condizionale](../conditional-access/overview.md) possono richiedere agli utenti di eseguire l'autenticazione a più fattori quando vengono soddisfatti determinati criteri, ad esempio:

* Tutti gli utenti, un utente specifico, membro di un ruolo, gruppo o assegnato
* Applicazione cloud specifico a cui si accede
* Piattaforma del dispositivo.
* Stato del dispositivo
* Percorso di rete o indirizzo IP geograficamente localizzati
* Applicazioni client
* Rischio di accesso (richiede Identity Protection)
* Dispositivo conforme
* Dispositivo aggiunto all'identità ibrida di Azure AD
* Applicazioni client approvate
 

Usare i modelli di posta elettronica e il poster personalizzabile in [materiali di implementazione di multi-factor authentication] per implementare l'autenticazione a più fattori per l'organizzazione. (https://www.microsoft.com/en-us/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Abilitare Multi-Factor Authentication con l'accesso condizionale

Criteri di accesso condizionale impongono la registrazione, richiedendo agli utenti di annullare la registrazione completare la registrazione al primo accesso, un aspetto importante della sicurezza.


[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) contribuisce sia un criterio di registrazione per i criteri di rilevamento e correzione rischio automatico per la storia di Azure multi-Factor Authentication. I criteri possono essere creati per forzare le modifiche delle password quando si presenta una minaccia di identità compromessa o richiedere l'autenticazione MFA quando accesso viene considerato rischioso dalle condizioni seguenti [eventi](../reports-monitoring/concept-risk-events.md):

* Credenziali perse
* Accessi da indirizzi IP anonimi
* Trasferimento impossibile a posizioni atipiche
* Accessi da posizioni non note
* Accessi da dispositivi infetti
* Accessi da indirizzi IP con attività sospette

Alcuni degli eventi di rischio rilevati da Azure Active Directory Identity Protection si verificano in tempo reale e alcune richiedono l'elaborazione offline. Gli amministratori possono scegliere di bloccare gli utenti che presentano comportamenti rischiosi e correggere manualmente, richiedono una modifica della password o richiedono autenticazione a più fattori come parte del loro criteri di accesso condizionale.

## <a name="define-network-locations"></a>Definire i percorsi di rete

Si consiglia alle organizzazioni di usare l'accesso condizionale per definire la rete utilizzando [località denominate](../conditional-access/location-condition.md#named-locations). Se l'organizzazione Usa la protezione dell'identità, può provare a usare i criteri basati sul rischio invece località denominate.

### <a name="configuring-a-named-location"></a>Configurazione di una posizione denominata

1. Aprire **Azure Active Directory** nel portale di Azure
2. Fare clic su **accesso condizionale**
3. Fare clic su **località denominate**
4. Fare clic su **nuovo percorso**
5. Nel **nome** campo, specificare un nome significativo
6. Consente di indicare se si sta definendo il percorso usando intervalli di indirizzi IP o paesi/aree geografiche
   1. Se si usa intervalli di indirizzi IP
      1. Decidere se indicano la posizione come attendibile. L'accesso da una posizione attendibile riduce il rischio di accesso dell'utente. Contrassegnare questa posizione come attendibile solo se si conoscono gli intervalli IP immessi siano stabiliti e credibili nell'organizzazione.
      2. Specificare gli intervalli IP
   2. Se si usa paesi/aree geografiche
      1. Espandere il menu a discesa e selezionare i paesi o aree geografiche da definire per questa località denominata.
      2. Decidere se Includi aree sconosciute. Le aree sconosciute sono indirizzi IP di cui non è possibile eseguire il mapping a un paese/area geografica.
7. Fare clic su **Crea**

## <a name="plan-authentication-methods"></a>Pianificare i metodi di autenticazione

Gli amministratori possono scegliere la [metodi di autenticazione](../authentication/concept-authentication-methods.md) che intendono rendere disponibili per gli utenti. È importante consentire più di un singolo metodo di autenticazione in modo che gli utenti hanno un metodo di backup disponibile nel caso in cui il metodo primario non è disponibile. Sono disponibili per gli amministratori abilitare i metodi seguenti:

### <a name="notification-through-mobile-app"></a>Notifica tramite app per dispositivi mobili

Viene inviata una notifica push all'app Microsoft Authenticator nel dispositivo mobile. L'utente visualizza la notifica e seleziona **Approva** per completare la verifica. Le notifiche push tramite un'app per dispositivi mobili forniscono l'opzione meno intrusiva per gli utenti. Sono anche l'opzione più affidabile e sicura perché usano una connessione dati, anziché di telefonia.

### <a name="verification-code-from-mobile-app"></a>Codice di verifica dall'app per dispositivi mobili

Un'app per dispositivi mobili, ad esempio l'app Microsoft Authenticator genera un nuovo codice di verifica OATH ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso. L'opzione di app per dispositivi mobili è utilizzabile se il telefono è un segnale cellulare o dati.

### <a name="call-to-phone"></a>Chiamata al telefono

Viene inserita una chiamata vocale automatizzata all'utente. L'utente risponde la chiamata e preme **#** sul tastierino telefonico per approvare l'autenticazione. Chiamata al telefono è un ottimo metodo backup per il codice di notifica o verifica da un'app per dispositivi mobili.

### <a name="text-message-to-phone"></a>SMS al telefono

Viene inviato un SMS contenente un codice di verifica all'utente, l'utente viene richiesto di immettere il codice di verifica nell'interfaccia di accesso.

### <a name="choose-verification-options"></a>Scegliere le opzioni di verifica

1. Passare ad **Azure Active Directory**, **Utenti**, **Multi-Factor Authentication**.

   ![Accesso al portale Multi-Factor Authentication dal pannello Utenti di Azure AD nel portale di Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Nella nuova scheda che si apre passare a **Impostazioni del servizio**.
1. In **Opzioni di verifica** selezionare tutte le caselle per i metodi disponibili per gli utenti.

   ![Configurare i metodi di verifica nella scheda delle impostazioni del servizio Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Fare clic su **Save**.
1. Chiudere la scheda **Impostazioni del servizio**.

## <a name="plan-registration-policy"></a>Pianificare i criteri di registrazione

Gli amministratori devono determinare come gli utenti verranno registrati i relativi metodi. Le organizzazioni devono [abilitare la nuova esperienza di registrazione combinata](howto-registration-mfa-sspr-combined.md) per Azure MFA e la password self-service (SSPR) di ripristino. SSPR consente agli utenti di reimpostare la password in modo sicuro usando gli stessi metodi che usano multi-factor Authentication. È consigliabile che questa combinazione di registrazione, attualmente in anteprima pubblica, perché è un'esperienza ottimale per gli utenti, con la possibilità di registrare una sola volta per entrambi i servizi. Abilita gli stessi metodi per SSPR e Azure MFA, si consentono agli utenti di essere registrata per l'uso di entrambe le funzionalità.

### <a name="registration-with-identity-protection"></a>Registrazione con Identity Protection

Se l'organizzazione Usa Azure Active Directory Identity Protection [configurare i criteri di registrazione MFA](../identity-protection/howto-mfa-policy.md) per richiedere agli utenti di registrazione al successivo accesso in modo interattivo.

### <a name="registration-without-identity-protection"></a>Registrazione senza identity Protection

Se l'organizzazione non hanno licenze che abilitare Identity Protection, gli utenti vengono chiesto di registrare la volta successiva che MFA è richiesto al momento dell'accesso. Gli utenti non possono essere registrati per MFA, se non usano le applicazioni protette con MFA. È importante ottenere tutti gli utenti registrati in modo che gli attori dannosi non è possibile indovinare la password di un utente e registrazione a MFA per suo conto, in modo efficace assume il controllo dell'account.

#### <a name="enforcing-registration"></a>Implementazione della registrazione

I passaggi seguenti criteri di accesso condizionale possono imporre agli utenti di registrarsi per multi-Factor Authentication

1. Creare un gruppo, aggiungere tutti gli utenti non attualmente registrati.
2. Uso dell'accesso condizionale, imporre l'autenticazione a più fattori per questo gruppo per l'accesso a tutte le risorse.
3. Periodicamente, rivalutare l'appartenenza al gruppo e rimuovere utenti che hanno registrato dal gruppo.

È possibile identificare gli utenti di Azure MFA registrati e non registrati con i comandi di PowerShell che si basano sul [modulo di MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificare gli utenti registrati

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificare gli utenti non registrati

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

## <a name="plan-conditional-access-policies"></a>Pianificare i criteri di accesso condizionale

Per pianificare la strategia di criteri di accesso condizionale, che determina quando sono necessari altri controlli e autenticazione a più fattori, fare riferimento a [qual è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md).

È importante evitare che venga inavvertitamente bloccato tenant di Azure AD. È possibile ridurre l'impatto di questo accidentale mancanza di accesso amministrativo da [creazione di due o più account di accesso di emergenza nel tenant](../users-groups-roles/directory-emergency-access.md) ed esclusi dai criteri di accesso condizionale.

### <a name="create-conditional-access-policy"></a>Creare criteri di accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.
1. Passare a **Azure Active Directory**, **accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Immettere un nome significativo per i criteri.
1. In **Utenti e gruppi**:
   * Nella scheda **Includere** selezionare il pulsante di opzione **Tutti gli utenti**
   * Nel **escludere** scheda, selezionare la casella per **utenti e gruppi** e scegliere gli account di accesso di emergenza.
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

![Creare un criterio di accesso condizionale per abilitare MFA per gli utenti del portale di Azure nel gruppo pilota](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>Pianificare l'integrazione con sistemi locali

Alcune applicazioni legacy e in locale che non eseguono l'autenticazione direttamente con Azure AD richiedono passaggi aggiuntivi per usare autenticazione a più fattori, ad esempio:

* Legacy applicazioni locali, che sarà necessario usare il proxy di applicazione.
* Applicazioni di RADIUS in locale, che saranno necessario utilizzare la scheda di autenticazione a più fattori con server NPS.
* In locale applicazioni di ADFS, che dovranno usare scheda MFA con AD FS 2016.

Possono rendere le applicazioni che l'autenticazione direttamente con Azure AD e l'autenticazione moderna (WS-Fed, SAML, OAuth, OpenID Connect) usare direttamente i criteri di accesso condizionale.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Usare Azure MFA con Azure AD Application Proxy

Le applicazioni che risiedono in locale possono essere pubblicata in Azure AD del tenant tramite [Azure AD Application Proxy](../manage-apps/application-proxy.md) e può sfruttare i vantaggi di Azure multi-Factor Authentication se sono configurati per usare Azure AD pre-autenticazione.

Queste applicazioni sono soggetti a criteri di accesso condizionale che l'applicazione di Azure multi-Factor Authentication, proprio come qualsiasi altra integrate in AD applicazione Azure.

Analogamente, se Azure multi-Factor Authentication viene applicata per tutti gli accessi utente, in locale verranno protetto le applicazioni pubblicate con Proxy applicazione Azure AD.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>L'integrazione di Azure multi-Factor Authentication con Server dei criteri di rete

L'estensione di Server dei criteri di rete (NPS) per Azure MFA aggiunge funzionalità MFA basate su cloud per l'infrastruttura di autenticazione usando i server esistenti. Con l'estensione NPS, è possibile aggiungere una telefonata, SMS o verifica app telefonica al flusso di autenticazione esistente. Questa integrazione presenta le limitazioni seguenti:

* Con il protocollo CHAPv2, sono supportati solo notifiche push dell'app authenticator e chiamata vocale.
* Non è possibile applicare i criteri di accesso condizionale.

L'estensione NPS funge da adattatore tra RADIUS e Azure MFA basato su cloud per fornire un secondo fattore di autenticazione per proteggere [VPN](howto-mfa-nps-extension-vpn.md), [connessioni Gateway Desktop remoto](howto-mfa-nps-extension-rdg.md), o altri RADIUS in grado di supportare applicazioni. Gli utenti che dovrà fornire registrazione per Azure MFA in questo ambiente per tutti i tentativi di autenticazione, la mancanza di mean di criteri di accesso condizionale MFA è sempre obbligatoria.

#### <a name="implementing-your-nps-server"></a>Implementazione di Server dei criteri di rete

Se si dispone di un'istanza dei criteri di rete distribuita e in uso, fare riferimento a [integrare l'infrastruttura NPS esistente con Azure multi-Factor Authentication](howto-mfa-nps-extension.md). Se si imposta criteri di rete per la prima volta, fare riferimento a [Strumentazione gestione Windows (NPS, Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) per le istruzioni. Risoluzione dei problemi relativi a informazioni aggiuntive sono reperibili nell'articolo [risolvere i messaggi di errore dall'estensione NPS per Azure multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Impostazioni dei criteri di rete per gli utenti che non sono registrati per MFA

Scegliere cosa succede quando gli utenti che non sono registrati con supporto MFA tentano di eseguire l'autenticazione. Usare l'impostazione del Registro di sistema `REQUIRE_USER_MATCH` nel percorso del Registro di sistema `HKLM\Software\Microsoft\AzureMFA` per controllare il comportamento della funzionalità. Questa impostazione non ha una singola opzione di configurazione.

| Chiave | Valore | Predefinito |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | Non impostato (equivalente a VERO) |

Lo scopo di questa impostazione è stabilire cosa fare quando un utente non è registrato per MFA. Nella tabella seguente sono elencati gli effetti della modifica di questa impostazione.

| Impostazioni | Stato MFA dell'utente | Effetti |
| --- | --- | --- |
| Chiave non esiste | Non è registrato | Richiesta di verifica MFA ha esito negativo |
| Valore impostato su True o non impostata | Non è registrato | Richiesta di verifica MFA ha esito negativo |
| Chiave impostata su False | Non è registrato | Autenticazione senza autenticazione a più fattori |
| Chiave impostata su False o True | Registrati | È necessario eseguire l'autenticazione con MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrazione con Active Directory Federation Services

Se l'organizzazione è federata con Azure AD, è possibile usare [Azure multi-Factor Authentication per proteggere le risorse di AD FS](multi-factor-authentication-get-started-adfs.md), sia in locale e nel cloud. Azure MFA consente di ridurre le password e fornire un modo più sicuro per l'autenticazione. A partire da Windows Server 2016, è ora possibile configurare Azure MFA per l'autenticazione principale.

A differenza con AD FS in Windows Server 2012 R2, la scheda Azure MFA di AD FS 2016 si integra direttamente con Azure AD e non richiede un server Azure MFA in locale. La scheda Azure MFA è incorporata in Windows Server 2016 e non è necessario per un'installazione aggiuntiva.

Quando si usa Azure MFA con AD FS 2016 e l'applicazione di destinazione è soggetta ai criteri di accesso condizionale, esistono altre considerazioni:

* Accesso condizionale è disponibile quando l'applicazione relying party da Azure AD federata con AD FS 2016.
* Accesso condizionale non è disponibile quando l'applicazione relying party in AD FS 2016 e viene gestita o federata con AD FS 2016.
* Accesso condizionale non è inoltre disponibile quando AD FS 2016 è configurato per usare Azure MFA come metodo di autenticazione principale.

#### <a name="ad-fs-logging"></a>Registrazione di AD FS

Standard AD FS 2016 la registrazione in sia nel Registro di protezione di Windows e log di amministrazione di AD FS, contiene informazioni sull'autenticazione delle richieste e loro esito positivo o negativo. Dati dei registri eventi all'interno di questi eventi indicherà se è stata utilizzata l'autenticazione a più fattori di Azure. Ad esempio, può contenere un ID di evento di controllo di ADFS AD 1200:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Il rinnovo e la gestione dei certificati

In ogni server AD FS, nel computer locale personale Store, esisterà un self-signed di Azure MFA certificato intitolato OU = Microsoft AD FS Azure MFA, che contiene la data di scadenza del certificato. Controllare il periodo di validità del certificato in ogni server AD FS per determinare la data di scadenza.

Se il periodo di validità dei certificati è prossima alla scadenza [generare e verificare un nuovo certificato di autenticazione a più fattori in ogni server AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Il materiale sussidiario seguente illustra come gestire i certificati di autenticazione a più fattori di Azure nei server AD FS. Quando si configura ADFS con Azure MFA, i certificati generati tramite il `New-AdfsAzureMfaTenantCertificate` cmdlet di PowerShell sono valide per 2 anni. Rinnovare e installare i certificati rinnovati prima della scadenza ovoid interruzioni nel servizio di autenticazione a più fattori.

## <a name="implement-your-plan"></a>Implementare il piano

Ora che è stata pianificata la soluzione, è possibile implementare seguendo i passaggi seguenti:

1. Soddisfare i prerequisiti necessari
   1. Distribuire [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) per tutti gli scenari ibridi
   1. Distribuire [Azure AD Application Proxy](../manage-apps/application-proxy.md) per in qualsiasi App locali pubblicate per l'accesso cloud
   1. Distribuire [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) per qualsiasi tipo di autenticazione RADIUS
   1. Verificare che gli utenti sono aggiornati a versioni supportate di Microsoft Office usano l'autenticazione moderna abilitata
1. Configurare scelte [metodi di autenticazione](#choose-verification-options)
1. Definire il [denominato percorsi di rete](../conditional-access/location-condition.md#named-locations)
1. Selezionare i gruppi per iniziare l'implementazione di autenticazione a più fattori.
1. Configurare il [criteri di accesso condizionale](#create-conditional-access-policy)
1. Configurare i criteri di registrazione MFA
   1. [MFA combinato e SSPR](howto-registration-mfa-sspr-combined.md)
   1. Con [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Invio di comunicazioni degli utenti e agli utenti di registrare in [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Tenere traccia di chi è registrato](#identify-non-registered-users)

## <a name="manage-your-solution"></a>Gestire la soluzione

Report per Azure MFA

Azure multi-Factor Authentication fornisce report tramite il portale di Azure:

| Report | Località | DESCRIZIONE |
| --- | --- | --- |
| Avvisi di illecito e utilizzo | Azure AD > Accessi | Fornisce informazioni su utilizzo complessivo, riepilogo utenti e dettagli utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |

## <a name="troubleshoot-mfa-issues"></a>Risolvere i problemi di autenticazione a più fattori

Trovare soluzioni per problemi comuni con Azure MFA nel [articolo di risoluzione dei problemi di Azure multi-Factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) di Microsoft Support Center.

## <a name="next-steps"></a>Passaggi successivi

* [Cosa si intende per metodi di autenticazione?](concept-authentication-methods.md)
* [Abilitare la registrazione per la convergenza per la reimpostazione password self-service di Azure multi-Factor Authentication e Azure AD](concept-registration-mfa-sspr-converged.md)
* Perché a un utente è stato richiesto o non è stato richiesto di eseguire MFA? Vedere la sezione [Report sugli accessi ad Azure AD nel documento Report in Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
