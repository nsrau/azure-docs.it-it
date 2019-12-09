---
title: 'Azure AD Connect: autenticazione cloud tramite implementazione di gestione temporanea | Microsoft Docs'
description: Questo articolo illustra come eseguire la migrazione dall'autenticazione federata all'autenticazione cloud usando un'implementazione di gestione temporanea.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915220"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Eseguire la migrazione all'autenticazione cloud usando l'implementazione di gestione temporanea (anteprima)

Usando un approccio di implementazione di gestione temporanea, è possibile eseguire la migrazione dall'autenticazione federata all'autenticazione cloud. Questo articolo illustra come eseguire l'opzione. Prima di iniziare l'implementazione di gestione temporanea, è tuttavia necessario prendere in considerazione le implicazioni se si verificano una o più delle condizioni seguenti:
    
-  Attualmente si sta usando un server di Multi-Factor Authentication locale. 
-  Si stanno usando le smart card per l'autenticazione. 
-  Il server corrente offre alcune funzionalità di solo Federazione.

Prima di provare questa funzionalità, è consigliabile consultare la guida relativa alla scelta del metodo di autenticazione appropriato. Per altre informazioni, vedere la tabella "confronto dei metodi" in [scegliere il metodo di autenticazione appropriato per la soluzione di identità ibrida Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods).

Per una panoramica della funzionalità, vedere la sezione "Azure Active Directory: che cos'è l'implementazione temporanea?" video

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Prerequisiti

-   Si dispone di un tenant di Azure Active Directory (Azure AD) con domini federati.

-   Si è deciso di passare a una delle due opzioni seguenti:
    - **Opzione A** - *sincronizzazione dell'hash delle password (sincronizzazione)*  + *Single Sign-on seamless (SSO)*
    - **Opzione B** - *autenticazione pass-through* + accesso *SSO* facile
    
    Sebbene *seamless SSO* sia facoltativo, è consigliabile abilitarlo per ottenere un'esperienza di accesso invisibile agli utenti che eseguono computer aggiunti a un dominio all'interno di una rete aziendale.

-   Sono stati configurati tutti i criteri appropriati di personalizzazione del tenant e di accesso condizionale necessari per gli utenti di cui viene eseguita la migrazione all'autenticazione cloud.

-   Se si prevede di usare Multi-Factor Authentication di Azure, è consigliabile usare la [registrazione convergente per la reimpostazione della password self-service (SSPR) e multi-factor authentication](../authentication/concept-registration-mfa-sspr-combined.md) per fare in modo che gli utenti registrino i propri metodi di autenticazione una volta.

-   Per usare la funzionalità di implementazione di gestione temporanea, è necessario essere un amministratore globale nel tenant.

-   Per abilitare l'accesso *SSO* facile in una foresta Active Directory specifica, è necessario essere un amministratore di dominio.

## <a name="supported-scenarios"></a>Scenari supportati

Gli scenari seguenti sono supportati per l'implementazione di gestione temporanea. La funzionalità funziona solo per:

- Utenti di cui è stato effettuato il provisioning per Azure AD tramite Azure AD Connect. Non si applica a utenti solo cloud.

- Traffico di accesso utente nei browser e nei client di *autenticazione moderna* . Le applicazioni o i servizi cloud che usano l'autenticazione legacy eseguiranno il fallback ai flussi di autenticazione federati. Un esempio potrebbe essere Exchange Online con l'autenticazione moderna disattivata o Outlook 2010, che non supporta l'autenticazione moderna.

## <a name="unsupported-scenarios"></a>Scenari non supportati

Gli scenari seguenti non sono supportati per l'implementazione di gestione temporanea:

- Alcune applicazioni inviano il parametro di query "domain_hint" per Azure AD durante l'autenticazione. Questi flussi continueranno e gli utenti abilitati per l'implementazione temporanea continueranno a usare la Federazione per l'autenticazione.

<!-- -->

- Gli amministratori possono implementare l'autenticazione cloud usando i gruppi di sicurezza. Per evitare la latenza di sincronizzazione quando si usano i gruppi di sicurezza Active Directory locali, è consigliabile usare i gruppi di sicurezza cloud. Si applicano le condizioni seguenti:

    - È possibile usare un massimo di 10 gruppi per ogni funzionalità. Ovvero, è possibile utilizzare 10 gruppi ciascuno per la *sincronizzazione dell'hash delle password*, *l'autenticazione pass-through e l'* accesso *SSO*facile.
    - I gruppi annidati *non sono supportati*. Questo ambito è valido anche per l'anteprima pubblica.
    - I gruppi dinamici *non sono supportati* per l'implementazione temporanea.
    - Contatti oggetti all'interno del gruppo bloccherà l'aggiunta del gruppo.

- È comunque necessario rendere il cutover finale da federato ad autenticazione cloud usando Azure AD Connect o PowerShell. L'implementazione temporanea non cambia i domini da federati a gestiti.

- Quando si aggiunge per la prima volta un gruppo di sicurezza per l'implementazione temporanea, si è limitati a 200 utenti per evitare un timeout di UX. Una volta aggiunto il gruppo, è possibile aggiungervi altri utenti direttamente, come richiesto.

## <a name="get-started-with-staged-rollout"></a>Introduzione all'implementazione di gestione temporanea

Per testare l'accesso con *sincronizzazione dell'hash delle password* usando l'implementazione di gestione temporanea, seguire le istruzioni di pre-lavoro nella sezione successiva.

Per informazioni sui cmdlet di PowerShell da usare, vedere [Azure AD 2,0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Pre-lavoro per la sincronizzazione degli hash delle password

1. Abilitare la *sincronizzazione dell'hash delle password* dalla pagina delle [funzionalità facoltative](how-to-connect-install-custom.md#optional-features) Azure ad Connect. 

   ![Screenshot della pagina "funzionalità facoltative" in Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Verificare che sia stato eseguito un ciclo completo di *sincronizzazione dell'hash delle password* in modo che tutti gli hash delle password degli utenti siano stati sincronizzati con Azure ad. Per controllare lo stato della *sincronizzazione degli hash delle password*, è possibile usare la diagnostica di PowerShell in [risolvere i problemi di sincronizzazione dell'hash delle password con Azure ad Connect sincronizzazione](tshoot-connect-password-hash-synchronization.md).

   ![Screenshot del log di risoluzione dei problemi di AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Se si vuole testare l'accesso dell' *autenticazione pass-through* usando l'implementazione di gestione temporanea, abilitarla seguendo le istruzioni di pre-lavoro nella sezione successiva.

## <a name="pre-work-for-pass-through-authentication"></a>Pre-lavoro per l'autenticazione pass-through

1. Identificare un server che esegue Windows Server 2012 R2 o versione successiva in cui si vuole eseguire l'agente di *autenticazione pass-through* . 

   *Non scegliere il* server Azure ad Connect. Verificare che il server sia aggiunto a un dominio, sia in grado di autenticare gli utenti selezionati con Active Directory e di comunicare con Azure AD sulle porte e sugli URL in uscita. Per ulteriori informazioni, vedere la sezione "passaggio 1: verificare i prerequisiti" della [Guida introduttiva: Azure AD Single Sign-on senza](how-to-connect-sso-quick-start.md)problemi.

1. [Scaricare l'agente di autenticazione Azure ad Connect](https://aka.ms/getauthagent)e installarlo nel server. 

1. Per abilitare la [disponibilità elevata](how-to-connect-sso-quick-start.md), installare gli agenti di autenticazione aggiuntivi in altri server.

1. Verificare di aver configurato correttamente le [impostazioni di blocco Smart](../authentication/howto-password-smart-lockout.md) . In questo modo è possibile garantire che gli account Active Directory locali degli utenti non vengano bloccati da attori malintenzionati.

Si consiglia di abilitare l'accesso *SSO* facile indipendentemente dal metodo di accesso (*sincronizzazione dell'hash delle password* o *autenticazione pass-through*) selezionata per l'implementazione temporanea. Per abilitare l'accesso *SSO*facile, seguire le istruzioni di pre-lavoro nella sezione successiva.

## <a name="pre-work-for-seamless-sso"></a>Pre-lavora per seamless SSO

Abilitare l'accesso *SSO* facile sulle foreste Active Directory usando PowerShell. Se si dispone di più di una foresta Active Directory, abilitarla singolarmente per ogni foresta.  *Seamless SSO* viene attivato solo per gli utenti selezionati per l'implementazione temporanea. Non influisce sulla configurazione della Federazione esistente.

Abilitare l'accesso *SSO* facile eseguendo le operazioni seguenti:

1. Accedere a Azure AD Connect Server.

2. Passare alla cartella *% ProgramFiles%\\Microsoft Azure Active Directory Connect* .

3. Importare il modulo di PowerShell *seamless SSO* eseguendo il comando seguente: 

   `Import-Module .\AzureADSSO.psd1`

4. Eseguire PowerShell come amministratore. In PowerShell chiamare `New-AzureADSSOAuthenticationContext`. Questo comando apre un riquadro in cui è possibile immettere le credenziali di amministratore globale del tenant.

5. Chiamare `Get-AzureADSSOStatus | ConvertFrom-Json`. Questo comando Visualizza un elenco di foreste Active Directory (vedere l'elenco "domini") in cui è stata abilitata questa funzionalità. Per impostazione predefinita, è impostato su false a livello di tenant.

   ![Esempio di output di Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Chiamare `$creds = Get-Credential`. Al prompt di, immettere le credenziali di amministratore di dominio per la foresta Active Directory desiderata.

7. Eseguire la chiamata a `Enable-AzureADSSOForest -OnPremCredentials $creds`. Questo comando crea l'account computer AZUREADSSOACC dal controller di dominio locale per la foresta Active Directory necessaria per la funzionalità *seamless SSO*.

8. L'accesso *SSO* facile richiede che gli URL si trovino nell'area Intranet. Per distribuire tali URL usando criteri di gruppo, vedere [Guida introduttiva: Azure AD Single Sign-on seamless](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Per una procedura dettagliata completa, è anche possibile scaricare i [piani di distribuzione](https://aka.ms/SeamlessSSODPDownload) per *seamless SSO*.

## <a name="enable-staged-rollout"></a>Abilita implementazione temporanea

Per implementare una funzionalità specifica, ovvero l'*autenticazione pass-through*, la *sincronizzazione dell'hash delle password*o l'accesso *SSO*facile, a un set di utenti selezionato in un gruppo, seguire le istruzioni riportate nelle sezioni successive.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Abilitare un'implementazione di gestione temporanea di una funzionalità specifica nel tenant

È possibile implementare una di queste opzioni:

- **Opzione A** - *sincronizzazione degli hash delle password + accesso* *SSO* facile
- **Opzione B** - *autenticazione pass-through* + accesso *SSO* facile
- **Non supportato** - la *sincronizzazione dell'hash delle password* + *l'autenticazione pass-through* + *seamless SSO*

Eseguire le operazioni seguenti:

1. Per accedere all'esperienza utente di anteprima, accedere al [portale di Azure ad](https://aka.ms/stagedrolloutux).

2. Selezionare il collegamento **Abilita implementazione temporanea per l'accesso utente gestito (anteprima)** .

   Ad esempio, se si vuole abilitare l' *opzione a*, far scorrere la **sincronizzazione dell'hash delle password** e i controlli **Single Sign-on trasparente** **su on**, come illustrato nelle immagini seguenti.

   ![Pagina Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![Pagina "Abilita funzionalità di implementazione di gestione temporanea (anteprima)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Aggiungere i gruppi alla funzionalità per abilitare l' *autenticazione pass-through* e l'accesso *SSO semplice*. Per evitare un timeout di UX, assicurarsi che i gruppi di sicurezza non contengano inizialmente più di 200 membri.

   ![Pagina "Gestisci gruppi per sincronizzazione hash password (anteprima)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >I membri di un gruppo vengono automaticamente abilitati per l'implementazione di gestione temporanea. I gruppi annidati e dinamici non sono supportati per l'implementazione temporanea.

## <a name="auditing"></a>Controllo

Sono stati abilitati gli eventi di controllo per le varie azioni eseguite per l'implementazione di gestione temporanea:

- Evento di controllo quando si Abilita un'implementazione di gestione temporanea per la *sincronizzazione degli hash delle password*, *l'autenticazione pass-through o l'* accesso *SSO semplice*.

  >[!NOTE]
  >Un evento di controllo viene registrato quando l'accesso *SSO* facile viene attivato usando l'implementazione di gestione temporanea.

  ![Riquadro "Crea criterio di implementazione per funzionalità"-scheda attività](./media/how-to-connect-staged-rollout/sr7.png)

  ![Scheda proprietà modificata del riquadro "Crea criterio di implementazione per funzionalità"](./media/how-to-connect-staged-rollout/sr8.png)

- Evento di controllo quando un gruppo viene aggiunto alla *sincronizzazione dell'hash delle password*, all' *autenticazione pass-through*o alla funzionalità *seamless SSO*.

  >[!NOTE]
  >Un evento di controllo viene registrato quando un gruppo viene aggiunto alla *sincronizzazione dell'hash delle password* per l'implementazione di gestione temporanea.

  ![Riquadro "Aggiungi un gruppo alla funzionalità di implementazione"-scheda attività](./media/how-to-connect-staged-rollout/sr9.png)

  ![Riquadro "Aggiungi un gruppo alla funzionalità di implementazione"-scheda proprietà modificata](./media/how-to-connect-staged-rollout/sr10.png)

- Evento di controllo quando un utente che è stato aggiunto al gruppo è abilitato per l'implementazione temporanea.

  ![Riquadro "Aggiungi utente alla funzionalità di implementazione"-scheda attività](media/how-to-connect-staged-rollout/sr11.png)

  ![Scheda "Aggiungi utente alla funzionalità di implementazione" del riquadro-destinazioni](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Convalida

Per testare l'accesso con sincronizzazione dell' *hash delle password* o *autenticazione pass-through* (accesso con nome utente e password), seguire questa procedura:

1. Nella rete Extranet andare alla [pagina app](https://myapps.microsoft.com) in una sessione del browser privata, quindi immettere il USERPRINCIPALNAME (UPN) dell'account utente selezionato per l'implementazione temporanea.

   Gli utenti che sono stati assegnati per l'implementazione temporanea non vengono reindirizzati alla pagina di accesso federata. Al contrario, viene richiesto di accedere alla pagina di accesso Azure AD tenant.

1. Assicurarsi che l'accesso venga visualizzato correttamente nel report dell' [attività di accesso Azure ad](../reports-monitoring/concept-sign-ins.md) filtrando con userPrincipalName.

Per testare l'accesso con *seamless SSO*:

1. Nella Intranet andare alla [pagina app](https://myapps.microsoft.com) in una sessione del browser privata, quindi immettere il USERPRINCIPALNAME (UPN) dell'account utente selezionato per l'implementazione temporanea.

   Gli utenti a cui è destinata l'implementazione temporanea di *seamless SSO* vengono presentati con un "tentativo di accesso in corso..." messaggio prima che venga eseguito l'accesso in modalità invisibile all'utente.

1. Assicurarsi che l'accesso venga visualizzato correttamente nel report dell' [attività di accesso Azure ad](../reports-monitoring/concept-sign-ins.md) filtrando con userPrincipalName.

   Per tenere traccia degli accessi degli utenti ancora presenti nel Active Directory Federation Services (AD FS) per gli utenti di implementazione di gestione temporanea selezionati, seguire le istruzioni in [ad FS risoluzione dei problemi: eventi e registrazione](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Consultare la documentazione del fornitore per informazioni su come controllarlo nei provider federativi di terze parti.

## <a name="remove-a-user-from-staged-rollout"></a>Rimuovere un utente dall'implementazione di gestione temporanea

La rimozione di un utente dal gruppo Disabilita l'implementazione di gestione temporanea per tale utente. Per disabilitare la funzionalità di implementazione temporanea, spostare il controllo di nuovo su **disattivato**.

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: è possibile usare questa funzionalità in produzione?**

R: Sì, è possibile usare questa funzionalità nel tenant di produzione, ma è consigliabile provare prima di tutto nel tenant di test.

**D: è possibile usare questa funzionalità per mantenere una "co-esistenza permanente", in cui alcuni utenti usano l'autenticazione federata e altri usano l'autenticazione cloud?**

R: No, questa funzionalità è progettata per la migrazione dall'autenticazione federata a quella cloud in fasi e quindi fino alla fine dell'autenticazione cloud. Non è consigliabile usare uno stato misto permanente, perché questo approccio potrebbe causare flussi di autenticazione imprevisti.

**D: è possibile usare PowerShell per eseguire l'implementazione temporanea?**

A: Sì. Per informazioni su come usare PowerShell per eseguire l'implementazione temporanea, vedere [Azure ad Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Passaggi successivi
- [Anteprima di Azure AD 2,0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
