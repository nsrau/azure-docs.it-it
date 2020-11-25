---
title: 'Esercitazione: Configurare Smartsheet per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 79fa480e0cca590446a0251f43c45b2e04c97cd5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013157"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Esercitazione: Configurare Smartsheet per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Smartsheet e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il de-provisioning di utenti e/o gruppi in [Smartsheet](https://www.smartsheet.com/pricing). Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Smartsheet
> * Rimuovere utenti da Smartsheet quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e Smartsheet
> * Accesso Single Sign-On a Smartsheet (scelta consigliata)

> [!NOTE]
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md).
* Un account utente in Azure AD con l'[autorizzazione](../users-groups-roles/directory-assign-admin-roles.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* [Un tenant di Smartsheet](https://www.smartsheet.com/pricing).
* Un account utente in un piano Smartsheet Enterprise o Enterprise Premier con autorizzazioni di amministratore di sistema.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare i dati di cui [eseguire il mapping tra Azure AD e Smartsheet](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Smartsheet per supportare il provisioning con Azure AD

Prima di configurare Smartsheet per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in Smartsheet.

1. Accedere come **SysAdmin** nel **[portale di Smartsheet](https://app.smartsheet.com/b/home)** e passare ad **Account Admin** (Amministratore account).

    ![Opzione Account Admin di Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Passare a **Security Controls > User Auto Provisioning > Edit** (Controlli di sicurezza > Provisioning utenti automatico > Modifica).

    ![Opzione Security Controls di Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Aggiungere e convalidare i domini di posta elettronica per gli utenti di cui si intende effettuare il provisioning da Azure AD a Smartsheet. Scegliere **Not Enabled** (Non abilitato) per assicurarsi che tutte le azioni di provisioning vengano eseguite solo da Azure AD e che l'elenco di utenti Smartsheet sia sincronizzato con le assegnazioni di Azure AD.

    ![Provisioning utenti di Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Dopo la convalida, sarà necessario attivare il dominio. 

    ![Attivazione del dominio di Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generare il **token segreto** necessario per configurare il provisioning utenti automatico con Azure AD passando a **Apps & Integrations** (App e integrazioni).

    ![Screenshot della pagina Smartsheet Admin con l'avatar utente e l'opzione Apps & Integrations evidenziata.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Scegliere **API Access** (Accesso API). Fare clic su **Generate new access token** (Genera nuovo token di accesso).

    ![Screenshot della finestra di dialogo Personal Settings con le opzioni API Access e Generate new access token evidenziate.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definire il nome del token di accesso API. Fare clic su **OK**.

    ![Screenshot della finestra Step 1 of 2: Generate API Access Token con l'opzione OK evidenziata.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copiare il token di accesso API e salvarlo perché questa è l'unica volta in cui è possibile visualizzarlo. Tale valore è obbligatorio nel campo **Token segreto** in Azure AD.

    ![Token di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Smartsheet dalla raccolta di applicazioni di Azure AD

Aggiungere Smartsheet dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Smartsheet. Se Smartsheet è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a Smartsheet, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Per garantire la parità nelle assegnazioni di ruolo utente tra Smartsheet e Azure AD, è consigliabile usare le stesse assegnazioni di ruolo definite nell'elenco completo degli utenti di Smartsheet. Per recuperare l'elenco degli utenti da Smartsheet, passare a **Account Admin > User Management > More Actions > Download User List (csv)** (Amministrazione account > Gestione utenti > Altre azioni > Scarica elenco degli utenti - CSV).

* Per accedere a determinate funzionalità nell'app, Smartsheet richiede che a un utente siano assegnati più ruoli. Per altre informazioni sui tipi e le autorizzazioni utente in Smartsheet, vedere l'articolo su [tipi e autorizzazioni utente](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se a un utente sono assegnati più ruoli in Smartsheet, è **NECESSARIO** assicurarsi che tali assegnazioni di ruolo vengano replicate in Azure AD per evitare uno scenario in cui gli utenti potrebbero perdere l'accesso agli oggetti Smartsheet in modo permanente. Ogni ruolo univoco in Smartsheet **DEVE** essere assegnato a un gruppo diverso in Azure AD. L'utente **DEVE** quindi essere aggiunto a ognuno dei gruppi corrispondenti ai ruoli desiderati. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Passaggio 5. Configurare il provisioning automatico degli utenti per Smartsheet 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Smartsheet in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Smartsheet in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Smartsheet**.

    ![Collegamento di Smartsheet nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere i valori di **URL di base di SCIM 2.0 e token di accesso** recuperati in precedenza da Smartsheet rispettivamente in **URL tenant** e **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Smartsheet. Se la connessione non riesce, verificare che l'account Smartsheet abbia le autorizzazioni SysAdmin e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Smartsheet** (Sincronizza utenti di Azure Active Directory con Smartsheet).

9. Esaminare gli attributi utente sincronizzati da Azure AD a Smartsheet nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Smartsheet per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |active|Boolean|
   |title|string|
   |userName|string|
   |name.givenName|string|
   |name.familyName|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |externalId|string|
   |roles[primary eq "True"].display|string|
   |roles[primary eq "True"].type|string|
   |roles[primary eq "True"].value|string|
   |Ruoli|string|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|string|


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Smartsheet, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Smartsheet selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Smartsheet non supporta le eliminazioni temporanee. Quando l'attributo **active** dell'utente è impostato su False, Smartsheet elimina l'utente in modo permanente.

## <a name="change-log"></a>Registro delle modifiche

* 16/06/2020: è stato aggiunto il supporto per gli attributi "Cost Center", "Division", "Manager" e "Department" dell'estensione aziendale per gli utenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)