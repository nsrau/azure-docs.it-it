---
title: 'Azure AD Connect: Autenticazione cloud tramite implementazione a fasi | Microsoft Docs'
description: Questo articolo illustra come eseguire la migrazione dall'autenticazione federata all'autenticazione cloud usando un'implementazione a fasi.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f800c11bb878ca1788c7258cde25266847e2a90
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89278582"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>Eseguire la migrazione all'autenticazione cloud tramite un'implementazione a fasi (anteprima)

L'implementazione di gestione temporanea consente di testare in modo selettivo gruppi di utenti con funzionalità di autenticazione cloud come Azure Multi-Factor Authentication (multi-factor authentication), l'accesso condizionale, la protezione delle identità per le credenziali perse, la governance delle identità e altre, prima di eseguire il passaggio dei domini.  Questo articolo illustra come eseguire il passaggio. Prima di iniziare l'implementazione a fasi, è tuttavia necessario considerare i possibili effetti in presenza di uno o più delle condizioni seguenti:
    
-  È attualmente in uso un server Multi-Factor Authentication locale. 
-  Si usano smart card per l'autenticazione. 
-  Il server corrente offre alcune funzionalità solo per la federazione.

Prima di provare questa funzionalità, è consigliabile rivedere la guida relativa alla scelta del metodo di autenticazione appropriato. Per altre informazioni, vedere la tabella "Confronto dei metodi" in [Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure Active Directory](./choose-ad-authn.md#comparing-methods).

Per una panoramica della funzionalità, vedere il video seguente sull'implementazione a fasi in Azure Active Directory.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Prerequisiti

-   Si ha un tenant Azure Active Directory (Azure AD) con domini federati.

-   Si è deciso di passare a una delle due opzioni seguenti:
    - **Opzione A**  -  *sincronizzazione dell'hash delle password (sincronizzazione)*  +  *Single Sign-on seamless (SSO)*.  Per altre informazioni, vedere [che cos'è la sincronizzazione degli hash delle password](whatis-phs.md) e [che cos'è seamless SSO](how-to-connect-sso.md)
    - **Opzione B**  -  autenticazione pass- *through*  +  accesso *SSO semplice*.  Per altre informazioni, vedere [che cos'è l'autenticazione pass-through](how-to-connect-pta.md)  
    
    Sebbene l'*accesso Single Sign-On facile* sia facoltativo, è consigliabile abilitarlo perché gli utenti che eseguono computer aggiunti a un dominio all'interno di una rete aziendale possano vivere un'esperienza di accesso automatica.

-   Sono stati configurati tutti i criteri appropriati di personalizzazione del tenant e di accesso condizionale necessari per gli utenti di cui viene eseguita la migrazione all'autenticazione cloud.

-   Se si prevede di usare Multi-Factor Authentication di Azure, è consigliabile usare la [registrazione combinata per la reimpostazione della password self-service (SSPR) e multi-factor authentication](../authentication/concept-registration-mfa-sspr-combined.md) per fare in modo che gli utenti registrino i propri metodi di autenticazione una volta.

-   Per usare la funzionalità di implementazione a fasi, è necessario essere un amministratore globale nel tenant.

-   Per abilitare l'*accesso Single Sign-On facile* in una foresta di Active Directory specifica, è necessario essere un amministratore di dominio.

-  Se si distribuisce Azure AD ibrido o Azure AD join, è necessario eseguire l'aggiornamento a Windows 10 1903 Update.


## <a name="supported-scenarios"></a>Scenari supportati

Per l'implementazione a fasi sono supportati gli scenari riportati di seguito. La funzionalità può essere usata solo nei casi seguenti:

- Utenti di cui è stato effettuato il provisioning per Azure AD tramite Azure AD Connect. Non si applica a utenti solo cloud.

- Traffico di accesso utente in browser e client  *con autenticazione moderna*. Le applicazioni o i servizi cloud che usano l'autenticazione legacy eseguiranno il fallback ai flussi di autenticazione federati, ad esempio Exchange Online con l'autenticazione moderna disattivata oppure Outlook 2010, che non supporta l'autenticazione moderna.
- Le dimensioni del gruppo sono attualmente limitate a 50.000 utenti.  Se i gruppi hanno più di 50.000 utenti, è consigliabile suddividerli in più gruppi per poter usare l'implementazione a fasi.

## <a name="unsupported-scenarios"></a>Scenari non supportati

Per l'implementazione a fasi non sono supportati gli scenari riportati di seguito:

- Alcune applicazioni inviano il parametro di query "domain_hint" ad Azure AD durante l'autenticazione. Questi flussi saranno ancora possibili e gli utenti abilitati per l'implementazione a fasi continueranno a usare la federazione per l'autenticazione.

<!-- -->

- Gli amministratori possono implementare l'autenticazione cloud usando i gruppi di sicurezza. Per evitare latenza di sincronizzazione durante l'uso dei gruppi di sicurezza Active Directory locali, è consigliabile usare gruppi di sicurezza cloud. Si applicano le condizioni seguenti:

    - È possibile usare un massimo di 10 gruppi per ogni funzionalità, vale a dire 10 gruppi per *sincronizzazione dell'hash delle password*, 10 per *autenticazione pass-through* e 10 per *accesso Single Sign-On facile*.
    - I gruppi annidati *non sono supportati*. Questo ambito è valido anche per l'anteprima pubblica.
    - I gruppi dinamici *non sono supportati* per l'implementazione a fasi.
    - Gli oggetti Contact all'interno del gruppo impediscono l'aggiunta del gruppo.

- È comunque necessario eseguire il passaggio finale dall'autenticazione federata all'autenticazione cloud usando Azure AD Connect o PowerShell. L'implementazione a fasi non cambia i domini da federati a gestiti.  Per ulteriori informazioni sul dominio cutover, vedere [eseguire la migrazione dalla Federazione alla sincronizzazione degli hash delle password](plan-migrate-adfs-password-hash-sync.md) e [migrare dalla Federazione all'autenticazione pass-through](plan-migrate-adfs-pass-through-authentication.md)



- Quando un gruppo di sicurezza viene aggiunto per l'implementazione a fasi per la prima volta, il limite di utenti è 200 per evitare che si verifichi un timeout nell'esperienza utente. Dopo aver aggiunto il gruppo, è possibile aggiungervi altri utenti direttamente, se necessario.

- Mentre gli utenti sono in fase di implementazione temporanea, quando EnforceCloudPasswordPolicyForPasswordSyncedUsers è abilitato, i criteri di scadenza delle password sono impostati su 90 giorni senza alcuna opzione per personalizzarli. 


## <a name="get-started-with-staged-rollout"></a>Introduzione all'implementazione a fasi

Per testare l'accesso tramite*sincronizzazione dell'hash delle password* usando l'implementazione a fasi, seguire le istruzioni preliminari illustrate nella sezione successiva.

Per informazioni sui cmdlet PowerShell da usare, vedere [Anteprima di Azure AD 2.0](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Istruzioni preliminari per la sincronizzazione dell'hash delle password

1. Abilitare la  *sincronizzazione dell'hash delle password*  dalla pagina  [Funzionalità facoltative](how-to-connect-install-custom.md#optional-features)  in Azure AD Connect. 

   ![Screenshot della pagina "Funzionalità facoltative" in Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Verificare che sia stato eseguito un ciclo completo di *sincronizzazione dell'hash delle password* in modo che tutti gli hash delle password degli utenti siano stati sincronizzati con Azure AD. Per controllare lo stato di *sincronizzazione dell'hash delle password*, è possibile usare la diagnostica di PowerShell descritta in [Risolvere i problemi di sincronizzazione degli hash delle password con la sincronizzazione Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

   ![Screenshot del log di risoluzione dei problemi di Azure AD Connect](./media/how-to-connect-staged-rollout/sr2.png)

Se si vuole testare l'accesso tramite *autenticazione pass-through* usando l'implementazione a fasi, abilitare la funzionalità seguendo le istruzioni preliminari illustrate nella sezione successiva.

## <a name="pre-work-for-pass-through-authentication"></a>Istruzioni preliminari per l'autenticazione pass-through

1. Identificare un server che esegue Windows Server 2012 R2 o versione successiva in cui si vuole eseguire l'autenticazione l'agente di *autenticazione pass-through*. 

   *Non* scegliere il server di Azure AD Connect. Verificare che il server sia aggiunto a un dominio, possa autenticare gli utenti selezionati con Active Directory e possa comunicare con Azure AD sulle porte e sugli URL in uscita. Per altre informazioni, vedere la sezione Passaggio 1: Verificare i prerequisiti in [Avvio rapido: Accesso Single Sign-On facile di Azure AD](how-to-connect-sso-quick-start.md).

1. [Scaricare l'agente di autenticazione di Azure AD Connect](https://aka.ms/getauthagent) e installarlo nel server. 

1. Per abilitare la  [disponibilità elevata](how-to-connect-sso-quick-start.md), installare agenti di autenticazione aggiuntivi in altri server.

1. Assicurarsi di aver configurato le [impostazione di blocco intelligente](../authentication/howto-password-smart-lockout.md) in modo appropriato. In questo modo è possibile assicurarsi che gli account Active Directory locali degli utenti non vengano bloccati da utenti malintenzionati.

È consigliabile abilitare l'*accesso Single Sign-On facile* indipendentemente dal metodo di accesso (*sincronizzazione dell'hash delle password* o *autenticazione pass-through*) selezionato per l'implementazione a fasi. Per abilitare l'*accesso Single Sign-On facile*, seguire le istruzioni preliminari illustrate nella sezione successiva.

## <a name="pre-work-for-seamless-sso"></a>Istruzioni preliminari per l'accesso Single Sign-On facile

Abilitare l' *accesso Single Sign-On facile*  nelle foreste di Active Directory usando PowerShell. Se si dispone di più foreste di Active Directory, abilitarlo singolarmente per ogni foresta. L' *accesso Single Sign-On facile* viene attivato solo per gli utenti selezionati per l'implementazione a fasi. Non condiziona la configurazione della federazione esistente.

Abilitare l'*accesso Single Sign-On facile* eseguendo le operazioni seguenti:

1. Accedere al server di Azure AD Connect.

2. Passare alla cartella  *%programfiles%\\Microsoft Azure Active Directory Connect* .

3. Importare il modulo PowerShell dell'*accesso Single Sign-On facile* eseguendo il comando seguente: 

   `Import-Module .\AzureADSSO.psd1`

4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando apre un riquadro in cui è possibile immettere le credenziali dell'amministratore globale del tenant.

5. Chiamare `Get-AzureADSSOStatus | ConvertFrom-Json`. Questo comando visualizza un elenco di foreste di Active Directory (vedere l'elenco "Domini") in cui è stata abilitata questa funzionalità. Il valore predefinito a livello di tenant è false.

   ![Esempio di output di Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Chiamare `$creds = Get-Credential`. Al prompt dei comandi, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

7. Chiamare `Enable-AzureADSSOForest -OnPremCredentials $creds`. Questo comando crea l'account computer AZUREADSSOACC dal controller di dominio locale per la foresta di Active Directory necessaria per l'*accesso Single Sign-On facile*.

8. Per l'*accesso Single Sign-On facile* è necessario che gli URL siano nell'area Intranet. Per distribuire tali URL usando i criteri di gruppo, vedere [Avvio rapido: Accesso Single Sign-On facile di Azure AD](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Per una procedura dettagliata completa, è anche possibile scaricare i [piani di distribuzione](https://aka.ms/SeamlessSSODPDownload) per l'*accesso Single Sign-On facile*.

## <a name="enable-staged-rollout"></a>Abilitare l'implementazione a fasi

Per implementare una funzionalità specifica (*autenticazione pass-through*, *sincronizzazione dell'hash delle password* o *accesso Single Sign-On facile*) per una serie di utenti in un gruppo, seguire le istruzioni illustrate nelle sezioni successive.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Abilitare un'implementazione a fasi di una funzionalità specifica nel tenant

È possibile implementare una delle opzioni seguenti:

- **Opzione A** - *sincronizzazione dell'hash delle password* + *accesso Single Sign-On facile*
- **Opzione B** - *autenticazione pass-through* + *accesso Single Sign-On facile (SSO)*
- **Non supportato** - *sincronizzazione dell'hash delle password* + *autenticazione pass-through* + *accesso Single Sign-On facile*

Eseguire le operazioni seguenti:

1. Per accedere all'esperienza utente di anteprima, accedere al [portale di Azure AD](https://aka.ms/stagedrolloutux).

2. Selezionare il collegamento **Abilita l'implementazione a fasi per l'accesso utente gestito (anteprima)** .

   Ad esempio, se si vuole abilitare l'*opzione A*, scorrere i controlli **Sincronizzazione hash password** e **Accesso Single Sign-On facile** su **On**, come illustrato nelle immagini seguenti.

   ![Pagina di Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![Pagina "Abilita le funzionalità dell'implementazione a fasi (anteprima)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Aggiungere i gruppi alla funzionalità per abilitare l'*autenticazione pass-through* e l'*accesso Single Sign-On facile*. Per evitare un timeout dell'esperienza utente, assicurarsi che i gruppi di sicurezza non contengano inizialmente più di 200 membri.

   ![Pagina "Gestione dei gruppi per Sincronizzazione hash password (anteprima)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >I membri di un gruppo vengono automaticamente abilitati per l'implementazione a fasi. I gruppi annidati e dinamici non sono supportati per l'implementazione a fasi.
   >Quando si aggiunge un nuovo gruppo, gli utenti del gruppo (fino a 200 utenti per un nuovo gruppo) verranno aggiornati per l'uso dell'autenticazione immidiatly gestita. Modifica di un gruppo (aggiunta o rimozione di utenti), possono essere necessarie fino a 24 ore prima che le modifiche abbiano effetto.

## <a name="auditing"></a>Controllo

Sono stati abilitati gli eventi di controllo per le varie azioni eseguite per l'implementazione a fasi:

- Evento di controllo durante l'abilitazione dell'implementazione a fasi per *sincronizzazione dell'hash delle password*, *autenticazione pass-through* o *accesso Single Sign-On facile*.

  >[!NOTE]
  >Un evento di controllo viene registrato quando si attiva l'*accesso Single Sign-On facile* tramite l'implementazione a fasi.

  ![Riquadro "Create rollout policy for feature" (Crea criteri di implementazione per la funzionalità) nella scheda Attività](./media/how-to-connect-staged-rollout/sr7.png)

  ![Riquadro "Create rollout policy for feature" (Crea criteri di implementazione per la funzionalità) nella scheda Proprietà modificate](./media/how-to-connect-staged-rollout/sr8.png)

- Evento di controllo durante l'aggiunta di un gruppo alla *sincronizzazione dell'hash delle password*, all'*autenticazione pass-through* o all'*accesso Single Sign-On facile*.

  >[!NOTE]
  >Un evento di controllo viene registrato quando un gruppo viene aggiunto alla *sincronizzazione dell'hash delle password* per l'implementazione a fasi.

  ![Riquadro "Add a group to feature rollout" (Aggiungi un gruppo all'implementazione della funzionalità) nella scheda Attività](./media/how-to-connect-staged-rollout/sr9.png)

  ![Riquadro "Add a group to feature rollout" (Aggiungi un gruppo all'implementazione della funzionalità) nella scheda Proprietà modificate](./media/how-to-connect-staged-rollout/sr10.png)

- Evento di controllo durante l'abilitazione a fasi per un utente che è stato aggiunto a un gruppo.

  ![Riquadro "Add user to feature rollout" (Aggiungi utente all'implementazione della funzionalità) nella scheda Attività](media/how-to-connect-staged-rollout/sr11.png)

  ![Riquadro "Add user to feature rollout" (Aggiungi utente all'implementazione della funzionalità) nella scheda Destinazione/i](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Convalida

Per testare l'accesso tramite *sincronizzazione dell'hash delle password* o *autenticazione pass-through* (accesso tramite nome utente e password), seguire questa procedura:

1. Nella Extranet andare alla [pagina App](https://myapps.microsoft.com) in una sessione privata del browser, quindi immettere il valore UserPrincipalName (UPN) dell'account utente selezionato per l'implementazione a fasi.

   Gli utenti selezionati per l'implementazione a fasi non vengono reindirizzati alla pagina di accesso federato. A loro viene invece chiesto di accedere alla pagina di accesso di personalizzazione del tenant di Azure AD.

1. Verificare che l'accesso venga visualizzato nel [report delle attività di accesso di Azure AD](../reports-monitoring/concept-sign-ins.md), usando UserPrincipalName come filtro.

Per verificare l'accesso tramite *accesso Single Sign-On facile*:

1. Nella Intranet andare alla [pagina App](https://myapps.microsoft.com) in una sessione privata del browser, quindi immettere il valore UserPrincipalName (UPN) dell'account utente selezionato per l'implementazione a fasi.

   Gli utenti selezionati per l'implementazione a fase dell'*accesso Single Sign-on facile* visualizzano il messaggio "Tentativo di accesso per conto dell'utente" prima di accedere automaticamente.

1. Verificare che l'accesso venga visualizzato nel [report delle attività di accesso di Azure AD](../reports-monitoring/concept-sign-ins.md), usando UserPrincipalName come filtro.

   Per tenere traccia degli accessi utente eseguiti ancora in Active Directory Federation Services (AD FS) per gli utenti selezionati per l'implementazione a fasi, seguire le istruzioni riportate in [Risoluzione dei problemi di AD FS: eventi e registrazione](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Vedere la documentazione del fornitore per informazioni su come eseguire il controllo in provider di federazione di terze parti.

## <a name="remove-a-user-from-staged-rollout"></a>Rimuovere un utente dall'implementazione a fasi

La rimozione di un utente dal gruppo disabilita l'implementazione a fasi per tale utente. Per disabilitare la funzionalità di implementazione a fasi, riportare il controllo su **Off**.

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: È possibile usare questa funzionalità in un tenant di produzione?**

A: Sì, è possibile usare questa funzionalità nel tenant di produzione, ma è consigliabile provare prima di tutto nel tenant di test.

**D: È possibile usare questa funzionalità per gestire una "coesistenza permanente", in cui alcuni utenti usano l'autenticazione federata e altri usano l'autenticazione cloud?**

A: No, questa funzionalità è progettata per eseguire la migrazione dall'autenticazione federata all'autenticazione cloud in fasi e passare infine all'autenticazione cloud. Non è consigliabile usare uno stato misto permanente, poiché questo approccio potrebbe generare flussi di autenticazione imprevisti.

**D: È possibile usare PowerShell per eseguire l'implementazione a fasi?**

A: Sì. Per informazioni su come usare PowerShell per eseguire l'implementazione a fasi, vedere [Anteprima di Azure AD](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Passaggi successivi
- [Anteprima di Azure AD 2.0](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )