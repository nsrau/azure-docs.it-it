---
title: Risoluzione dei problemi Azure AD Azure Active Directory estensione NPS server
description: Guida alla risoluzione dei problemi relativi all'estensione NPS per Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 03736f468148ee633aff22718dc000220ab7efe4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839030"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-ad-multi-factor-authentication"></a>Risolvere i messaggi di errore dall'estensione NPS per Azure AD Multi-Factor Authentication

Se si verificano errori con l'estensione NPS per Azure AD Multi-Factor Authentication, usare questo articolo per ottenere una risoluzione più rapida. I log di estensione server dei criteri di rete sono disponibili in Visualizzatore eventi in ruoli del server **visualizzazioni personalizzate**  >  **Server Roles**  >  **servizi di accesso e criteri di rete** nel server in cui è installata l'estensione NPS.

## <a name="troubleshooting-steps-for-common-errors"></a>Procedura per la risoluzione di errori comuni

| Codice errore | Passaggi per la risoluzione dei problemi |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Contattare il supporto tecnico](#contact-microsoft-support) e segnalare l'elenco dei passaggi per la raccolta dei log. Fornire quante più informazioni possibile sugli eventi che si sono verificati prima dell'errore, inclusi l'ID tenant e il nome dell'entità utente (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Potrebbe esserci un problema con la modalità di installazione del certificato client o con la modalità di associazione al tenant. Per analizzare i problemi del certificato client seguire le istruzioni in [Risoluzione dei problemi dell'estensione NPS MFA](howto-mfa-nps-extension.md#troubleshooting). |
| **ESTS_TOKEN_ERROR** | Per analizzare i problemi del certificato client e del token ADAL seguire le istruzioni in [Risoluzione dei problemi dell'estensione NPS MFA](howto-mfa-nps-extension.md#troubleshooting). |
| **HTTPS_COMMUNICATION_ERROR** | Il server NPS non è in grado di ricevere risposte da Azure AD autenticazione a più fattori. Verificare che i firewall siano aperti in modalità bidirezionale per il traffico da e verso https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Nel server che esegue l'estensione NPS, verificare la possibilità di raggiungere `https://adnotifications.windowsazure.com` e `https://login.microsoftonline.com/`. Se tali siti non vengono caricati, risolvere i problemi di connettività sul server. |
| **Estensione NPS per Azure AD autenticazione a più fattori:** <br> L'estensione NPS per Azure AD multi-factor authentication esegue l'autenticazione secondaria solo per le richieste RADIUS nello stato AccessAccept. La richiesta ricevuta per l'utente nomeutente con stato della risposta AccessReject, sta ignorando la richiesta. | Questo errore indica in genere un problema di autenticazione in Active Directory o che NPS non è in grado di ricevere risposte da Azure AD. Verificare che i firewall siano aperti in modalità bidirezionale per il traffico da e verso `https://adnotifications.windowsazure.com` e `https://login.microsoftonline.com` tramite le porte 80 e 443. È importante anche verificare che nella scheda Chiamate in ingresso di Autorizzazione di accesso alla rete l'impostazione sia Controlla accesso tramite criteri di rete del Server dei criteri di rete. Questo errore può anche essere attivato se all'utente non è assegnata una licenza. |
| **REGISTRY_CONFIG_ERROR** | Manca una chiave nel registro per l'applicazione. Questa potrebbe essere la causa per cui lo [script di PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) non viene eseguito dopo l'installazione. Il messaggio di errore deve includere la chiave mancante. Assicurarsi che la chiave si trovi in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Nella richiesta RADIUS manca l'attributo RADIUS userName\Identifier attributo obbligatorio. Verificare che NPS riceva le richieste RADIUS | Questo errore indica in genere un problema di installazione. L'estensione NPS deve essere installata nei server NPS che possono ricevere le richieste RADIUS. I server NPS installati come dipendenze dei servizi come RDG e RRAS non ricevono le richieste RADIUS. L'estensione NPS non funziona quando è installata su tali installazioni e genera degli errori poiché è impossibile leggere i dettagli della richiesta di autenticazione. |
| **REQUEST_MISSING_CODE** | Verificare che il protocollo di crittografia delle password tra i server dei criteri di rete e di accesso alla rete supporti il metodo di autenticazione secondario usato. **PAP** supporta tutti i metodi di autenticazione di Azure ad multi-factor authentication nel cloud: telefonata, SMS unidirezionale, notifica dell'app per dispositivi mobili e codice di verifica dell'app per dispositivi mobili. **CHAPV2** e **EAP** supportano la chiamata telefonica e la notifica dell'app per dispositivi mobili. |
| **USERNAME_CANONICALIZATION_ERROR** | Verificare che l'utente sia presente nell'istanza di Active Directory locale e che il servizio NPS disponga delle autorizzazioni per accedere alla directory. Se si usano i trust tra foreste, [contattare il supporto tecnico](#contact-microsoft-support) per maggiore assistenza. |

### <a name="alternate-login-id-errors"></a>Errori relativi all'ID di accesso alternativo

| Codice errore | Messaggio di errore | Passaggi per la risoluzione dei problemi |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Errore: la ricerca di userObjectSid non è riuscita | Verificare che l'utente esista nell'istanza di Active Directory locale. Se si usano i trust tra foreste, [contattare il supporto tecnico](#contact-microsoft-support) per maggiore assistenza. |
| **ALTERNATE_LOGIN_ID_ERROR** | Errore: la ricerca dell'ID di accesso alternativo non è riuscita | Verificare che LDAP_ALTERNATE_LOGINID_ATTRIBUTE sia impostato su un [attributo di Active Directory valido](/windows/win32/adschema/attributes-all). <br><br> Se LDAP_FORCE_GLOBAL_CATALOG è impostata su True o LDAP_LOOKUP_FORESTS è configurato con un valore non vuoto, verificare di avere configurato un catalogo globale e che vi sia stato aggiunto l'attributo AlternateLoginId. <br><br> Se LDAP_LOOKUP_FORESTS è configurato con un valore non vuoto, verificare che il valore sia corretto. Se è presente più di un nome di foresta, i nomi devono essere separati da punti e virgola, non da spazi. <br><br> Se questi passaggi non risolvono il problema, [contattare il supporto tecnico](#contact-microsoft-support) per maggiore assistenza. |
| **ALTERNATE_LOGIN_ID_ERROR** | Errore: il valore dell'ID di accesso alternativo è vuoto | Verificare che l'attributo AlternateLoginId sia configurato per l'utente. |

## <a name="errors-your-users-may-encounter"></a>Errori che possono verificarsi per gli utenti

| Codice errore | Messaggio di errore | Passaggi per la risoluzione dei problemi |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Caller tenant does not have access permissions to do authentication for the user (Il tenant chiamante non dispone delle autorizzazioni di accesso per eseguire l'autenticazione per l'utente) | Controllare che il dominio del tenant e il dominio del nome dell'entità utente (UPN) corrispondano. Ad esempio, assicurarsi che user@contoso.com stia tentando di eseguire l'autenticazione al tenant di Contoso. L'UPN rappresenta un utente valido per il tenant in Azure. |
| **AuthenticationMethodNotConfigured** | The specified authentication method was not configured for the user (Il metodo di autenticazione specificato non è stato configurato per l'utente) | Richiedere all'utente di aggiungere o verificare i metodi di verifica seguendo le istruzioni in [Gestire le impostazioni per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Specified authentication method is not supported. (Il metodo di autenticazione specificato non è supportato.) | Raccogliere tutti i log che includono questo errore e [contattare il supporto tecnico](#contact-microsoft-support). Quando si contatta il supporto tecnico, comunicare il nome utente e il metodo di verifica secondaria che ha generato l'errore. |
| **BecAccessDenied** | MSODS Bec call returned access denied, probably the username is not defined in the tenant (La chiamata MSODS Bec ha restituito un accesso negato, probabilmente il nome utente non è definito nel tenant) | L'utente è presente in Active Directory locale, ma non è sincronizzato in Azure AD da AD Connect. Oppure manca l'utente per il tenant. Aggiungere l'utente ad Azure AD e richiedergli di aggiungere i metodi di verifica seguendo le istruzioni in [Gestire le impostazioni per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** o **StrongAuthenticationServiceInvalidParameter** | The phone number is in an unrecognizable format (Il numero di telefono presenta un formato non riconoscibile) | Richiedere all'utente di correggere i numeri di telefono per la verifica. |
| **InvalidSession** | The specified session is invalid or may have expired (La sessione specificata non è valida o potrebbe essere scaduta) | Il completamente della sessione ha richiesto più di tre minuti. Verificare che l'utente inserisca il codice di verifica o risponda alla notifica app, entro tre minuti dall'avvio della richiesta di autenticazione. Se il problema persiste, verificare che non siano presenti latenze di rete tra client, server NAS, server dei criteri di rete e Azure AD endpoint di autenticazione a più fattori.  |
| **NoDefaultAuthenticationMethodIsConfigured** | No default authentication method was configured for the user (Non è stato configurato alcun metodo di autenticazione predefinito per l'utente) | Richiedere all'utente di aggiungere o verificare i metodi di verifica seguendo le istruzioni in [Gestire le impostazioni per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user-manage-settings.md). Verificare che l'utente abbia scelto un metodo di autenticazione predefinito e che questo sia stato configurato per il proprio account. |
| **OathCodePinIncorrect** | Wrong code and pin entered. (Codice e pin inseriti non corretti.) | Questo errore non è previsto nell'estensione NPS. Se l'utente rileva questo errore, [contattare il supporto tecnico](#contact-microsoft-support) per la risoluzione del problema. |
| **ProofDataNotFound** | Proof data was not configured for the specified authentication method. (I dati di prova non sono stati configurati per il metodo di autenticazione specificato.) | Richiedere all'utente provare un metodo di verifica diverso o di aggiungerne uno nuovo seguendo le istruzioni in [Gestire le impostazioni per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user-manage-settings.md). Se l'errore viene visualizzato ancora anche dopo aver confermato che il metodo di verifica è stato configurato correttamente, [contattare il supporto tecnico](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Wrong code and pin entered. (Codice e pin inseriti non corretti.) (OneWaySMS) | Questo errore non è previsto nell'estensione NPS. Se l'utente rileva questo errore, [contattare il supporto tecnico](#contact-microsoft-support) per la risoluzione del problema. |
| **TenantIsBlocked** | Tenant is blocked (Il tenant è bloccato) | [Contattare il supporto tecnico](#contact-microsoft-support) con l' *ID tenant* dalla pagina delle proprietà Azure ad nella portale di Azure. |
| **UserNotFound** | The specified user was not found (Impossibile trovare l'utente specificato) | Il tenant non è più visibile in Azure AD come attivo. Verificare che la sottoscrizione sia attiva e che si dispone delle app proprietarie necessarie. Assicurarsi anche che il tenant nel soggetto del certificato sia quello previsto e che il certificato sia ancora valido e registrato nell'entità servizio. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Messaggi che l'utente potrebbe visualizzare ma che non sono errori

In alcuni casi, gli utenti possono ricevere messaggi da Multi-Factor Authentication in quanto la richiesta di autenticazione ha esito negativo. Non si tratta di errori del prodotto di configurazione, ma sono avvisi intenzionali che spiegano il motivo per cui una richiesta di autenticazione è stata negata.

| Codice errore | Messaggio di errore | Procedure consigliate |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Wrong code entered\OATH Code Incorrect (Codice inserito non corretto\Codice OATH errato) | L'utente ha immesso il codice errato. Fare in modo che l'utente ripeta l'operazione richiedendo un nuovo codice o effettuando di nuovo l'accesso. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maximum allowed code retry reached (Numero massimo di tentativi di inserimento del codice raggiunto) | L'utente ha superato il numero di richieste di verifica consentito. A seconda delle impostazioni, potrebbe essere necessaria una procedura di sblocco da parte dell'amministratore.  |
| **SMSAuthFailedWrongCodeEntered** | Wrong code entered/Text Message OTP Incorrect (Codice inserito errato/OTP del messaggio di testo errato) | L'utente ha immesso il codice errato. Fare in modo che l'utente ripeta l'operazione richiedendo un nuovo codice o effettuando di nuovo l'accesso. |

## <a name="errors-that-require-support"></a>Errori per cui è necessario il supporto tecnico

Se si verifica uno di questi errori, è consigliabile [contattare il supporto tecnico](#contact-microsoft-support) per assistenza diagnostica. Non esistono procedure standard che consentono di risolvere questi errori. Quando si contatta il supporto tecnico, assicurarsi di includere più informazioni possibili sui passaggi che hanno causato l'errore e le informazioni del tenant.

| Codice errore | Messaggio di errore |
| ---------- | ------------- |
| **InvalidParameter** | Request must not be null (La richiesta non deve essere null) |
| **InvalidParameter** | ObjectId must not be null or empty for ReplicationScope:{0} (ObjectId non deve essere null o vuoto per ReplicationScope: {0}) |
| **InvalidParameter** | The length of CompanyName \{0}\ is longer than the maximum allowed length {1} (La lunghezza di CompanyName \{0}\ supera la lunghezza massima consentita { 1 }) |
| **InvalidParameter** | UserPrincipalName must not be null or empty (UserPrincipalName non deve essere null o vuoto) |
| **InvalidParameter** | The provided TenantId is not in correct format (Il TenantId inserito non è nel formato corretto) |
| **InvalidParameter** | SessionId must not be null or empty (SessionId non deve essere null o vuoto) |
| **InvalidParameter** | Could not resolve any ProofData from request or Msods. (Impossibile risolvere ProofData dalla richiesta o Msods.) The ProofData is unKnown (ProofData è sconosciuto) |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Passaggi successivi

### <a name="troubleshoot-user-accounts"></a>Risolvere i problemi relativi agli account utente

Se gli utenti hanno [Problemi con la verifica in due passaggi](../user-help/multi-factor-authentication-end-user-troubleshoot.md) è necessario aiutarli a diagnosticare autonomamente i problemi.

### <a name="health-check-script"></a>Script di controllo integrità

Lo [script di controllo integrità dell'estensione NPS di NPS di Azure ad](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) esegue un controllo di integrità di base per la risoluzione dei problemi relativi all'estensione NPS. Eseguire lo script e scegliere l'opzione 3.

### <a name="contact-microsoft-support"></a>Contattare il supporto tecnico Microsoft

Se è necessario avere un maggiore supporto, contattare un tecnico del supporto tramite il [supporto del server Multi-Factor Authentication di Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando si contatta Microsoft, è utile includere il maggior numero possibile di informazioni relative al problema. Le informazioni da dare includono la pagina in cui viene visualizzato l'errore, il codice dell'errore specifico, l'ID della sessione specifico, l'ID dell'utente che visualizza l'errore e i log di debug.

Per raccogliere i log di debug per la diagnostica di supporto, attenersi alla procedura seguente sul server NPS:

1. Aprire l'editor del Registro di sistema e passare a set HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa impostando **VERBOSE_LOG** su **TRUE**
2. Aprire un prompt dei comandi di amministratore ed eseguire questi comandi:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Riprodurre il problema

4. Arrestare la traccia con i comandi seguenti:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Aprire l'editor del Registro di sistema e passare a set HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa impostando **VERBOSE_LOG** su **FALSE**
6. Comprimere il contenuto della cartella C:\NPS e allegare il file compresso al caso di supporto.