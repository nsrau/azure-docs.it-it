---
title: 'Azure AD Connect: Autenticazione cloud tramite implementazione in fasi Documenti Microsoft'
description: In questo articolo viene illustrato come eseguire la migrazione dall'autenticazione federata all'autenticazione cloud usando un'implementazione in fasi.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915220"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Eseguire la migrazione all'autenticazione cloud usando l'implementazione in fasi (anteprima)Migrate to cloud authentication by using staged rollout (preview)

Utilizzando un approccio di implementazione in fasi, è possibile eseguire la migrazione dall'autenticazione federata all'autenticazione cloud. In questo articolo viene illustrato come effettuare il passaggio. Prima di iniziare l'implementazione in fasi, tuttavia, è necessario considerare le implicazioni se una o più delle seguenti condizioni sono vere:
    
-  Attualmente si utilizza un server Multi-Factor Authentication locale. 
-  Si utilizzano smart card per l'autenticazione. 
-  Il server corrente offre determinate funzionalità di sola federazione.

Prima di provare questa funzione, ti consigliamo di consultare la nostra guida sulla scelta del metodo di autenticazione corretto. Per altre informazioni, vedere la tabella "Confronto di metodi" in Scegliere il metodo di [autenticazione corretto per la soluzione](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)di identità ibrida di Azure Active Directory.For more information, see the "Comparing methods" table in Choose the right authentication method for your Azure Active Directory hybrid identity solution .

Per una panoramica della funzionalità, visualizzare questo "Azure Active Directory: che cos'è l'implementazione in fasi?" Dei video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Prerequisiti

-   Si dispone di un tenant di Azure Active Directory (Azure AD) con domini federati.

-   Si è deciso di passare a una delle due opzioni seguenti:
    - **Opzione Sincronizzazione** - *dell'hash delle password (sincronizzazione)* + *Single Sign-On (SSO) semplice*
    - **Opzione B** - *Autenticazione* + pass-through Senza soluzione di*continuità*
    
    Sebbene *SSO senza soluzione* di continuità sia facoltativo, è consigliabile abilitarlo per ottenere un'esperienza di accesso invisibile all'utente per gli utenti che eseguono computer aggiunti a un dominio dall'interno di una rete aziendale.

-   Sono stati configurati tutti i criteri di personalizzazione e accesso condizionale appropriati necessari per gli utenti di cui viene eseguita la migrazione all'autenticazione cloud.

-   Se si prevede di usare Azure Multi-Factor Authentication, è consigliabile usare la [registrazione convergente per la reimpostazione della password self-service (SSPR) e Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) per fare in modo che gli utenti registrino i metodi di autenticazione una sola volta.

-   Per usare la funzionalità di implementazione in fasi, è necessario essere un amministratore globale nel tenant.

-   Per abilitare *SSO senza problemi* in una foresta di Active Directory specifica, è necessario essere un amministratore di dominio.

## <a name="supported-scenarios"></a>Scenari supportati

Gli scenari seguenti sono supportati per l'implementazione in fasi. La funzione funziona solo per:

- Utenti di cui viene eseguito il provisioning in Azure AD tramite Azure AD Connect.Users who are provisioned to Azure AD by using Azure AD Connect. Non si applica agli utenti solo cloud.

- Traffico di accesso degli utenti sui browser e sui client di *autenticazione moderni.* Le applicazioni o i servizi cloud che usano l'autenticazione legacy eseguiranno il rollback ai flussi di autenticazione federata. Un esempio potrebbe essere Exchange online con autenticazione moderna disattivata o Outlook 2010, che non supporta l'autenticazione moderna.

## <a name="unsupported-scenarios"></a>Scenari non supportati

Gli scenari seguenti non sono supportati per l'implementazione in fasi:The following scenarios are not supported for staged rollout:

- Alcune applicazioni inviano il parametro di query "domain_hint" ad Azure AD durante l'autenticazione. Questi flussi continueranno e gli utenti abilitati per l'implementazione in fasi continueranno a utilizzare la federazione per l'autenticazione.

<!-- -->

- Gli amministratori possono implementare l'autenticazione cloud usando i gruppi di sicurezza. Per evitare la latenza di sincronizzazione quando si usano gruppi di sicurezza di Active Directory locali, è consigliabile usare i gruppi di sicurezza cloud. Si applicano le seguenti condizioni:

    - È possibile utilizzare un massimo di 10 gruppi per feature. Ovvero, è possibile utilizzare 10 gruppi ciascuno per *la sincronizzazione dell'hash delle password,* *l'autenticazione pass-through*e *SSO senza soluzione*di continuità .
    - I gruppi nidificati non sono *supportati.* Questo ambito si applica anche all'anteprima pubblica.
    - I gruppi dinamici non sono *supportati* per l'implementazione in fasi.
    - Gli oggetti contatto all'interno del gruppo impediranno l'aggiunta del gruppo.

- È comunque necessario eseguire l'autenticazione definitiva da federata a cloud usando Azure AD Connect o PowerShell.You still need to make the final cutover from federated to cloud authentication by using Azure AD Connect or PowerShell. L'implementazione in fasi non consente di passare da domini federati a gestiti.

- Quando si aggiunge per la prima volta un gruppo di sicurezza per l'implementazione in fasi, si è limitati a 200 utenti per evitare un timeout dell'esperienza utente. Dopo aver aggiunto il gruppo, è possibile aggiungervi altri utenti direttamente, in base alle esigenze.

## <a name="get-started-with-staged-rollout"></a>Introduzione all'implementazione graduale

Per testare l'accesso alla *sincronizzazione dell'hash* delle password tramite l'implementazione in fasi, seguire le istruzioni preliminari all'uso nella sezione successiva.

Per informazioni sui cmdlet di PowerShell da usare, vedere [Anteprima di Azure AD 2.0.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="pre-work-for-password-hash-sync"></a>Pre-lavoro per la sincronizzazione dell'hash delle password

1. Abilitare la *sincronizzazione dell'hash delle password* dalla pagina [Funzionalità facoltative](how-to-connect-install-custom.md#optional-features) in Azure AD Connect.Enable password hash sync from the Optional features page in Azure AD Connect. 

   ![Screenshot della pagina "Funzionalità facoltative" in Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Verificare che sia stato eseguito un ciclo di *sincronizzazione dell'hash della password* completo in modo che tutti gli hash delle password degli utenti siano stati sincronizzati con Azure AD. Per controllare lo stato della *sincronizzazione dell'hash*delle password, è possibile usare la diagnostica di PowerShell in Risolvere i problemi di [sincronizzazione dell'hash delle password con](tshoot-connect-password-hash-synchronization.md)la sincronizzazione di Azure AD Connect .

   ![Screenshot del registro di risoluzione dei problemi di AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Se si desidera testare l'accesso tramite *l'autenticazione pass-through* usando l'implementazione a fasi, abilitarla seguendo le istruzioni preliminari di lavoro nella sezione successiva.

## <a name="pre-work-for-pass-through-authentication"></a>Pre-lavoro per l'autenticazione pass-throughPre-work for pass-through authentication

1. Identificare un server che esegue Windows Server 2012 R2 o versione successiva in cui si desidera eseguire l'agente di *autenticazione pass-through.* 

   *Non* scegliere il server Azure AD Connect.Verificare che il server sia aggiunto a un dominio, che sia in grado di autenticare gli utenti selezionati con Active Directory e di comunicare con Azure AD su porte e URL in uscita. Per altre informazioni, vedere la sezione "Passaggio 1: Verificare i prerequisiti" di [Guida introduttiva: Accesso Single Sign-On semplice di Azure AD.](how-to-connect-sso-quick-start.md)

1. [Scaricare l'agente](https://aka.ms/getauthagent)di autenticazione Azure AD Connect e installarlo nel server. 

1. Per abilitare [la disponibilità elevata,](how-to-connect-sso-quick-start.md)installare agenti di autenticazione aggiuntivi in altri server.

1. Assicurati di aver configurato [le impostazioni](../authentication/howto-password-smart-lockout.md) di Blocco intelligente in modo appropriato. In questo modo si garantisce che gli account di Active Directory locali degli utenti non vengano bloccati da utenti malintenzionati.

È consigliabile abilitare *sSO senza interruzioni* indipendentemente dal metodo di accesso *(sincronizzazione dell'hash delle password* o *autenticazione pass-through)* selezionato per l'implementazione graduale. Per abilitare SSO senza soluzione di *continuità,* seguire le istruzioni di pre-lavoro nella sezione successiva.

## <a name="pre-work-for-seamless-sso"></a>Pre-lavoro per SSO senza soluzione di continuità

Abilitare *SSO senza soluzione* di continuità nelle foreste di Active Directory utilizzando PowerShell.Enable seamless SSO on the Active Directory forests by using PowerShell. Se si dispone di più foreste di Active Directory, abilitarla per ogni foresta singolarmente.  *L'accesso SSO* semplice viene attivato solo per gli utenti selezionati per l'implementazione in fasi. Non influisce sulla configurazione della federazione esistente.

Abilitare *SSO senza soluzione* di continuità eseguendo le operazioni seguenti:Enable seamless SSO by doing the following:

1. Accedere al server Azure AD Connect.Sign in to Azure AD Connect Server.

2. Passare alla cartella *\\%programfiles% Microsoft Azure Active Directory Connect.* 

3. Importare il modulo PowerShell *SSO trasparente* eseguendo il comando seguente: 

   `Import-Module .\AzureADSSO.psd1`

4. Eseguire PowerShell come amministratore. In PowerShell `New-AzureADSSOAuthenticationContext`chiamare . Questo comando apre un riquadro in cui è possibile immettere le credenziali di amministratore globale del tenant.

5. Chiamare `Get-AzureADSSOStatus | ConvertFrom-Json`. Questo comando visualizza un elenco di foreste di Active Directory (vedere l'elenco "Domini") in cui è stata attivata questa funzionalità. Per impostazione predefinita, è impostato su false a livello di tenant.

   ![Esempio di output di Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Chiamare `$creds = Get-Credential`. Quando richiesto, immettere le credenziali di amministratore di dominio per la foresta di Active Directory desiderata.

7. Chiamare `Enable-AzureADSSOForest -OnPremCredentials $creds`. Questo comando consente di creare l'account del computer COMEADSSOACC dal controller di dominio locale per la foresta di Active Directory necessaria per *SSO senza problemi.*

8. *Per SSO senza soluzione* di continuità è necessario che gli URL si appaiano nell'area Intranet. Per distribuire tali URL usando i criteri di gruppo, vedere [Guida introduttiva: Accesso Single Sign-On senza problemi](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)di Azure AD.

9. Per una procedura dettagliata completa, è anche possibile scaricare i [nostri piani](https://aka.ms/SeamlessSSODPDownload) di distribuzione per *SSO senza soluzione di continuità.*

## <a name="enable-staged-rollout"></a>Abilitare l'implementazione a fasiEnable staged rollout

Per distribuire una funzionalità specifica (*autenticazione pass-through,* *sincronizzazione dell'hash delle password*o *SSO semplice*) a un gruppo selezionato di utenti in un gruppo, seguire le istruzioni nelle sezioni successive.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Abilitare un'implementazione graduale di una funzionalità specifica nel tenantEnable a staged rollout of a specific feature on your tenant

È possibile implementare una di queste opzioni:You can out out one of these options:

- **Opzione Un** - *hash della password sincronizzare* + *senza soluzione di continuità SSO*
- **Opzione B** - *Autenticazione* + pass-through Senza soluzione di*continuità*
- **SSO** - di*seamless SSO* *sincronizzazione* +  + *dell'hash delle password*non supportata

Eseguire le operazioni seguenti:

1. Per accedere all'esperienza utente di anteprima, accedere al portale di [Azure AD.](https://aka.ms/stagedrolloutux)

2. Selezionare il collegamento **Abilita implementazione in fasi per l'accesso utente gestito (anteprima).**

   Ad esempio, se si desidera attivare *l'opzione A*, far scorrere i controlli **Sincronizzazione hash password** e Single **Sign-On senza problemi** su **On**, come illustrato nelle immagini seguenti.

   ![Pagina di Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![Pagina "Abilita feature di implementazione in fasi (anteprima)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Aggiungere i gruppi alla funzionalità per abilitare *l'autenticazione pass-through* e *SSO senza soluzione di continuità.* Per evitare un timeout dell'esperienza utente, assicurarsi che i gruppi di sicurezza contengano inizialmente non più di 200 membri.

   ![Pagina "Gestisci gruppi per sincronizzazione hash password (anteprima)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >I membri di un gruppo vengono abilitati automaticamente per l'implementazione in fasi. I gruppi nidificati e dinamici non sono supportati per l'implementazione in fasi.

## <a name="auditing"></a>Controllo

Sono stati abilitati gli eventi di controllo per le varie azioni eseguite per l'implementazione in fasi:We've enabled audit events for the various actions we perform for staged rollout:

- Evento di controllo quando si abilita un'implementazione graduale per la *sincronizzazione dell'hash*delle password, *l'autenticazione pass-through*o *SSO senza interruzioni.*

  >[!NOTE]
  >Un evento di controllo viene registrato quando *l'Accesso SSO semplice* viene attivato tramite l'implementazione in fasi.

  ![Riquadro "Crea criterio di implementazione per la funzionalità" - Scheda Attività](./media/how-to-connect-staged-rollout/sr7.png)

  ![Riquadro "Crea criteri di implementazione per la funzionalità" - Scheda Proprietà modificate](./media/how-to-connect-staged-rollout/sr8.png)

- Evento di controllo quando un gruppo viene aggiunto alla *sincronizzazione dell'hash delle password,* *all'autenticazione pass-through*o *all'SSO senza soluzione di continuità.*

  >[!NOTE]
  >Un evento di controllo viene registrato quando un gruppo viene aggiunto alla *sincronizzazione dell'hash delle password* per l'implementazione in fasi.

  ![Riquadro "Aggiungi un gruppo all'implementazione delle funzionalità" - Scheda Attività](./media/how-to-connect-staged-rollout/sr9.png)

  ![Riquadro "Aggiungi un gruppo all'implementazione delle funzionalità" - Scheda Proprietà modificate](./media/how-to-connect-staged-rollout/sr10.png)

- Evento di controllo quando un utente aggiunto al gruppo è abilitato per l'implementazione in fasi.

  ![Riquadro "Aggiungi utente all'implementazione delle funzionalità" - Scheda Attività](media/how-to-connect-staged-rollout/sr11.png)

  ![Riquadro "Aggiungi utente a implementazione delle funzionalità" - Scheda Destinazione/i](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Convalida

Per testare l'accesso con la *sincronizzazione dell'hash* delle password o *l'autenticazione pass-through* (accesso tramite nome utente e password), eseguire le operazioni seguenti:

1. Nella rete Extranet passare alla [pagina App](https://myapps.microsoft.com) in una sessione del browser privata e quindi immettere il NomePrincipaleUtente (UPN) dell'account utente selezionato per l'implementazione in fasi.

   Gli utenti che sono stati presi di mira per l'implementazione in fasi non vengono reindirizzati alla pagina di accesso federato. Al contrario, viene chiesto loro di accedere alla pagina di accesso con marchio tenant di Azure AD.

1. Verificare che l'accesso venga visualizzato correttamente nel report dell'attività di accesso di Azure AD filtrando con UserPrincipalName.Ensure that the sign-in successfully appears in the [Azure AD sign-in activity report](../reports-monitoring/concept-sign-ins.md) by filtering with the UserPrincipalName.

Per testare l'accesso con SSO senza soluzione di *continuità:*

1. Nella rete Intranet passare alla [pagina App](https://myapps.microsoft.com) in una sessione del browser privato e quindi immettere il NomePrincipaleUtente (UPN) dell'account utente selezionato per l'implementazione in fasi.

   Gli utenti che sono stati presi di mira per l'implementazione in fasi di *SSO senza soluzione* di continuità vengono presentati con un "Tentativo di accedere ..." messaggio prima di accedere in modo invisibile all'utente.

1. Verificare che l'accesso venga visualizzato correttamente nel report dell'attività di accesso di Azure AD filtrando con UserPrincipalName.Ensure that the sign-in successfully appears in the [Azure AD sign-in activity report](../reports-monitoring/concept-sign-ins.md) by filtering with the UserPrincipalName.

   Per tenere traccia degli account di accesso degli utenti che si verificano ancora in Active Directory Federation Services (ADFS) per gli utenti di implementazione in fasi selezionati, seguire le istruzioni in Risoluzione dei problemi di [ADFS: eventi e registrazione](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Controllare la documentazione del fornitore su come controllare questo su fornitori di federazione di terze parti.

## <a name="remove-a-user-from-staged-rollout"></a>Rimuovere un utente dall'implementazione in fasiRemove a user from staged rollout

La rimozione di un utente dal gruppo disabilita l'implementazione in fasi per tale utente. Per disattivare la funzionalità di implementazione graduale, riportare il controllo su **Off**.

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: È possibile utilizzare questa funzionalità nella produzione?**

R: Sì, è possibile usare questa funzionalità nel tenant di produzione, ma è consigliabile provarla prima nel tenant di test.

**D: Questa funzionalità può essere utilizzata per mantenere una "coesistenza" permanente, in cui alcuni utenti utilizzano l'autenticazione federata e altri utilizzano l'autenticazione cloud?**

R: No, questa funzionalità è progettata per la migrazione dall'autenticazione federata a quella cloud in fasi e quindi per l'interruzione dell'autenticazione cloud. Non è consigliabile usare uno stato misto permanente, perché questo approccio potrebbe causare flussi di autenticazione imprevisti.

**D: È possibile usare PowerShell per eseguire l'implementazione in fasi?**

A: Sì. Per informazioni su come usare PowerShell per eseguire l'implementazione in fasi, vedere [Azure AD Preview.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="next-steps"></a>Passaggi successivi
- [Anteprima di Azure AD 2.0Azure AD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
