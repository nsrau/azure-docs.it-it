---
title: 'Azure AD Connect: autenticazione cloud-implementazione temporanea | Microsoft Docs'
description: Viene illustrato come eseguire la migrazione dall'autenticazione federata al cloud auth usando un'implementazione di gestione temporanea.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2596091324acde5c4fdc3f7c467849f90266fec9
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847238"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Autenticazione cloud: implementazione temporanea (anteprima pubblica)

Questa funzionalità consente di eseguire la migrazione dall'autenticazione federata all'autenticazione cloud usando un approccio di gestione temporanea.

L'allontanamento dall'autenticazione federata presenta implicazioni. Ad esempio, se si dispone di uno degli elementi seguenti:
    
-  un server di autenticazione a più fattori locale 
-  Usa Smart Card per l'autenticazione 
-  altre funzionalità solo Federazione

Queste funzionalità devono essere prese in considerazione prima di passare all'autenticazione cloud.  Prima di provare questa funzionalità, è consigliabile consultare la guida sulla scelta del metodo di autenticazione appropriato. Per ulteriori informazioni, vedere [questa tabella](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) .

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Prerequisiti

-   Si dispone di un tenant Azure AD con domini federati.

-   Si è deciso di passare alla sincronizzazione degli hash delle password e all'accesso SSO facile **(opzione a)** o all'autenticazione pass-through + seamless SSO **(opzione B).** Sebbene seamless SSO sia facoltativo, è consigliabile abilitare l'accesso SSO facile per ottenere un'esperienza di accesso invisibile agli utenti che usano computer aggiunti a un dominio dall'interno della rete aziendale.

-   Sono stati configurati tutti i criteri appropriati di personalizzazione del tenant e di accesso condizionale necessari per gli utenti di cui viene eseguita la migrazione all'autenticazione cloud.

-   Se si prevede di usare Multi-Factor Authentication di Azure, è consigliabile usare la [registrazione convergente per la reimpostazione della password self-service (SSPR) e](../authentication/concept-registration-mfa-sspr-combined.md) l'autenticazione a più fattori di Azure per consentire agli utenti di registrare i propri metodi di autenticazione una volta.

-   Per usare questa funzionalità, è necessario essere un amministratore globale nel tenant.

-   Per abilitare l'accesso SSO facile in una foresta di Active Directory specifica, è necessario essere amministratore di dominio.

## <a name="supported-scenarios"></a>Scenari supportati

Questi scenari sono supportati per l'implementazione di gestione temporanea:

- Questa funzionalità funziona solo per gli utenti di cui è stato effettuato il provisioning in Azure AD usando Azure AD Connect e non è applicabile per gli utenti solo cloud.

- Questa funzionalità funziona solo per il traffico di accesso utente nei browser e nei client di autenticazione moderna. Le applicazioni o i servizi cloud che usano l'autenticazione legacy eseguiranno il fallback ai flussi di autenticazione federati. (Esempio: Exchange Online con autenticazione moderna disattivata o Outlook 2010, che non supporta l'autenticazione moderna).

## <a name="unsupported-scenarios"></a>Scenari non supportati

Questi scenari non sono supportati per l'implementazione temporanea:

- Alcune applicazioni inviano il parametro di query "Domain\_hint" per Azure AD durante l'autenticazione. Questi flussi continueranno e gli utenti abilitati per l'implementazione temporanea continueranno a usare la Federazione per l'autenticazione.

<!-- -->

- L'amministratore può implementare l'autenticazione cloud usando i gruppi di sicurezza. I gruppi di sicurezza cloud sono consigliati per evitare la latenza di sincronizzazione quando si usano i gruppi di sicurezza di AD locali.

    - È possibile usare un **massimo di 10 gruppi per funzionalità**; ad esempio, per ciascuna sincronizzazione dell'hash delle password/autenticazione pass-through/SSO seamless.

    - I gruppi annidati **non sono supportati**. Questo è anche l'ambito per l'anteprima pubblica.

    - I gruppi dinamici **non sono supportati** per l'implementazione temporanea.

    - Contatti oggetti all'interno del gruppo bloccherà il modulo di gruppo da aggiungere.

- Il cutover finale dall'autenticazione federata a cloud deve ancora verificarsi usando Azure AD Connect o PowerShell. Questa funzionalità non cambia i domini dalla Federazione alla gestione.

- Quando si aggiunge per la prima volta un gruppo di sicurezza per l'implementazione temporanea, è limitato a 200 utenti per evitare il timeout dell'esperienza utente. Una volta aggiunto il gruppo nell'esperienza utente, è possibile aggiungere altri utenti direttamente al gruppo in modo obbligatorio.

## <a name="get-started-with-staged-rollout"></a>Introduzione all'implementazione di gestione temporanea

Se si vuole testare l'accesso con la sincronizzazione dell'hash delle password (pH) usando l'implementazione di gestione temporanea, completare il pre-lavoro seguente per abilitare l'implementazione temporanea della sincronizzazione dell'hash delle password.

Per altre informazioni sui cmdlet di PowerShell usati, vedere [AzureAD 2,0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="pre-work-for-password-hash-sync"></a>Pre-lavoro per la sincronizzazione degli hash delle password

1. Abilitare la sincronizzazione dell'hash delle password dalla pagina delle [funzionalità facoltative](how-to-connect-install-custom.md#optional-features) Azure ad Connect. 

   ![Screenshot della pagina delle funzionalità facoltative in Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Verificare che sia stato eseguito un ciclo completo di sincronizzazione dell'hash delle password in modo che tutti gli hash delle password degli utenti siano stati sincronizzati con Azure AD. Per controllare lo stato della sincronizzazione degli hash delle password, è possibile usare la [diagnostica di PowerShell](tshoot-connect-password-hash-synchronization.md) .

   ![Screenshot del log di risoluzione dei problemi di AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Se si vuole testare l'accesso pass-through-authentication (PTA) usando l'implementazione di gestione temporanea, completare il pre-lavoro seguente per abilitare PTA per l'implementazione temporanea.

## <a name="pre-work-for-pass-through-authentication"></a>Pre-lavoro per l'autenticazione pass-through

1. Identificare un server che esegue Windows Server 2012 R2 o versione successiva in cui si desidera che l'agente di autenticazione pass-through venga eseguito (**non scegliere il server di Azure ad Connect**). Verificare che il server sia aggiunto a un dominio, sia in grado di autenticare gli utenti selezionati con Active Directory e di comunicare con Azure AD su porte/URL in uscita (vedere [prerequisiti](how-to-connect-sso-quick-start.md)dettagliati).

1. [Scaricare](https://aka.ms/getauthagent) & installare il Microsoft Azure ad agente di autenticazione Connect nel server. 

1. Per abilitare la [disponibilità elevata](how-to-connect-sso-quick-start.md), installare gli agenti di autenticazione aggiuntivi in altri server.

1. Assicurarsi di aver configurato correttamente le [impostazioni di blocco Smart](../authentication/howto-password-smart-lockout.md) . Ciò consente di assicurarsi che gli account Active Directory locali degli utenti non vengano bloccati da attori malintenzionati.

Si consiglia di abilitare l'accesso SSO facile indipendentemente dal metodo di accesso (pH o PTA) selezionato per l'implementazione temporanea. Completare le operazioni preliminari seguenti per abilitare l'accesso SSO facile per l'implementazione temporanea.

## <a name="pre-work-for-seamless-sso"></a>Pre-lavora per seamless SSO

Abilitare l'accesso SSO facile nelle foreste di Active Directory tramite PowerShell. Se si dispone di più di una foresta di Active Directory, abilitare singolarmente lo stesso per ogni foresta. L'accesso SSO facile verrà attivato solo per gli utenti selezionati per l'implementazione temporanea e non influirà sulla configurazione della Federazione esistente.

**Riepilogo dei passaggi**

1. Per prima cosa, accedere a Azure AD Connect Server.

2. Passare alla cartella% ProgramFiles%\\Microsoft Azure Active Directory Connect.

3. Importare il modulo di PowerShell seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.

4. Eseguire PowerShell come amministratore. In PowerShell chiamare `New-AzureADSSOAuthenticationContext`. Questo comando dovrebbe fornire una finestra di dialogo in cui è possibile immettere le credenziali di amministratore globale del tenant.

5. Chiamare `Get-AzureADSSOStatus | ConvertFrom-Json`. Questo comando fornisce l'elenco delle foreste di Active Directory (esaminare l'elenco dei domini \"\") in cui è stata abilitata questa funzionalità. Per impostazione predefinita, è impostato su false a livello di tenant.

   > **Esempio:** 
   > ![esempio di output di Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Chiamare `$creds = Get-Credential`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

7. Eseguire la chiamata a `Enable-AzureADSSOForest -OnPremCredentials $creds`. Questo comando crea l'account del computer AZUREADSSOACC dal controller di dominio locale per questa foresta di Active Directory specifica, necessaria per la funzionalità seamless SSO.

8. L'accesso SSO facile richiede che gli URL si trovino nell'area Intranet. Per distribuire tali URL usando criteri di gruppo, fare riferimento alla [Guida introduttiva Single Sign-on](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) .

9.  Per una procedura dettagliata completa, è anche possibile scaricare i [piani di distribuzione](https://aka.ms/SeamlessSSODPDownload) per l'accesso SSO facile.

## <a name="enable-staged-rollout"></a>Abilita implementazione temporanea

Usare la procedura seguente per implementare una funzionalità specifica (autenticazione pass-through/sincronizzazione degli hash delle password/seamless SSO) a un set di utenti selezionato in un gruppo:

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Abilitare l'implementazione di gestione temporanea di una funzionalità specifica nel tenant

È possibile implementare una di queste opzioni

-   Sincronizzazione dell'hash delle password e seamless SSO **(opzione A)**

-   Autenticazione pass-through + seamless SSO **(opzione B)**

-   Sincronizzazione dell'hash delle password e autenticazione pass-through + accesso SSO facile **-\>** ***non supportati***

Completare i passaggi seguenti:

1. Accedere al portale di Azure AD usando l'URL seguente per accedere all'esperienza utente di anteprima.

   > <https://aka.ms/stagedrolloutux>

2. Fare clic su Abilita implementazione temporanea per l'accesso utente gestito (anteprima)

   *Ad esempio:* (**opzione B**) se si vuole abilitare la sincronizzazione dell'hash delle password e l'accesso SSO facile, far scorrere la sincronizzazione dell'hash delle password e le funzionalità Single Sign-on seamless a **"on"** , come illustrato di seguito.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Aggiungere i rispettivi gruppi alla funzionalità per abilitare l'autenticazione pass-through e Single Sign-On senza problemi. Assicurarsi che i gruppi di sicurezza non contengano inizialmente più di 200 membri per evitare il timeout di UX.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >I membri di un gruppo vengono automaticamente abilitati per l'implementazione di gestione temporanea. I gruppi annidati e dinamici non sono supportati per l'implementazione temporanea.

## <a name="auditing"></a>Controllo

Sono stati abilitati gli eventi di controllo per le diverse azioni eseguibili per l'implementazione di gestione temporanea.

- Evento di controllo quando si Abilita l'implementazione di gestione temporanea per la sincronizzazione dell'hash delle password o l'autenticazione pass-through/seamless SSO.

  >[!NOTE]
  >Evento di controllo registrato quando SeamlessSSO **viene attivato utilizzando** StagedRollout.

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Evento di controllo quando un gruppo viene aggiunto alla sincronizzazione dell'hash delle password/autenticazione pass-through/SSO facile.

  >[!NOTE]
  >Evento di controllo registrato quando viene aggiunto un gruppo alla sincronizzazione dell'hash delle password per l'implementazione temporanea

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Evento di controllo quando un utente che è stato aggiunto al gruppo è abilitato per l'implementazione temporanea

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Convalida

Per testare l'accesso con sincronizzazione dell'hash delle password o autenticazione pass-through (accesso con nome utente/password):

1. Passare a <https://myapps.microsoft.com> in una sessione del browser privata dalla rete Extranet e immettere il UserPrincipalName (UPN) dell'account utente selezionato per la distribuzione temporanea.

1. Se l'utente è stato indirizzato per l'implementazione di gestione temporanea, l'utente non verrà reindirizzato alla pagina di accesso federato e verrà richiesto di effettuare l'accesso nella pagina di accesso Azure AD con personalizzazione del tenant.

1. Verificare che l'accesso venga visualizzato correttamente nel report dell' [attività di accesso Azure ad](../reports-monitoring/concept-sign-ins.md) filtrando con userPrincipalName.

Per testare l'accesso con seamless SSO:

1. Passare a <https://myapps.microsoft.com>/in una sessione del browser privata da Intranet e immettere il UserPrincipalName (UPN) dell'account utente selezionato per la distribuzione temporanea.

1. Se l'utente è stato indirizzato all'implementazione temporanea di seamless SSO, l'utente visualizzerà un "tentativo di accesso in corso..." messaggio prima che venga eseguito l'accesso in modalità invisibile all'utente.

1. Assicurarsi che l'accesso venga visualizzato correttamente nel report dell' [attività di accesso Azure ad](../reports-monitoring/concept-sign-ins.md) filtrando con userPrincipalName.

Per controllare gli accessi utente ancora in corso sui provider di Federazione:

Ecco come tenere traccia degli accessi degli utenti ancora in AD FS per gli utenti di implementazione di gestione temporanea selezionati usando [queste istruzioni](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Consultare la documentazione del fornitore per informazioni su come controllarlo nei provider federativi di terze parti.

## <a name="roll-back"></a>Esegui rollback

### <a name="remove-a-user-from-staged-rollout"></a>Rimuovere un utente dall'implementazione di gestione temporanea

1.  La rimozione dell'utente dal gruppo Disabilita l'implementazione di gestione temporanea per l'utente.

2.  Se si vuole disabilitare la funzionalità di implementazione di gestione temporanea, riportare la funzionalità allo stato **"off"** per disattivare l'implementazione temporanea.


## <a name="frequently-asked-questions"></a>Domande frequenti

-   **D: un cliente può usare questa funzionalità in produzione?**

-   R: Sì, questa funzionalità può essere usata nel tenant di produzione, ma è consigliabile provare prima questa funzionalità nel tenant di test.

-   **D: è possibile usare questa funzionalità per mantenere una "co-esistenza" permanente in cui alcuni utenti usano l'autenticazione federata e altre autenticazioni cloud?**

-   R: No, questa funzionalità è progettata per la migrazione dall'autenticazione federata a quella cloud in fasi e quindi fino alla fine dell'autenticazione cloud. Non è consigliabile uno stato misto permanente perché potrebbe causare flussi di autenticazione imprevisti.

-   **D: è possibile usare PowerShell per eseguire l'implementazione temporanea?**

-   R: Sì, trovare la documentazione per usare PowerShell per eseguire l'implementazione [temporanea.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="next-steps"></a>Passaggi successivi
- [AzureAD 2,0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
