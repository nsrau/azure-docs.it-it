---
title: 'Esercitazione: Configurare MediusFlow per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: 71dc05e3949c2675be6255723530693304e1aa3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850082"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Esercitazione: Configurare MediusFlow per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in MediusFlow che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [MediusFlow](https://www.mediusflow.com/) tramite il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in MediusFlow
> * Rimozione di utenti in MediusFlow quando l'accesso non è più necessario
> * Mantenimento della sincronizzazione degli attributi utente tra Azure AD e MediusFlow
> * Provisioning di gruppi e appartenenze a gruppi in MediusFlow
> * Accesso Single Sign-On a MediusFlow (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Una sottoscrizione MediusFlow attiva con un controllo di qualità o un tenant di produzione.
* Un account utente in MediusFlow con diritti di accesso di amministratore per poter eseguire la configurazione in MediusFlow.
* Aziende aggiunte al tenant di MediusFlow in cui è necessario effettuare il provisioning degli utenti.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati [mappare tra Azure AD e MediusFlow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare MediusFlow per supportare il provisioning con Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Attivare l'app Microsoft 365 in MediusFlow
Per iniziare, abilitare l'accesso all'account di accesso di Azure AD e la funzionalità di configurazione Azure AD in MediusFlow attenendosi alla procedura seguente:

#### <a name="user-login"></a>Accesso utente
Per abilitare il flusso di accesso Microsoft 365/Azure AD fare riferimento a [this] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) articolo.

#### <a name="user-transfer-configuration"></a>Configurazione di trasferimento utente
Per abilitare il portale di configurazione degli utenti per il provisioning da Azure AD fare riferimento a [questo](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) articolo.

#### <a name="configure-user-provisioning"></a>Configura provisioning utenti

1.  Accedere alla [console di amministrazione di MediusFlow](https://office365.cloudapp.mediusflow.com/) fornendo l'ID tenant.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

2. Verificare la connessione con MediusFlow.

    ![Verifica](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Immettere l'ID del tenant di Azure AD.

    ![Immettere l'ID tenant](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Per altre informazioni, vedere le [domande frequenti](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) su come trovarlo.

4. Salvare la configurazione.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

5. Selezionare il provisioning utenti e fare clic su **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

6. Fare clic su **Generate Secret Key** (Genera chiave privata). Copiare e salvare il valore che verrà immesso nel campo **Token segreto** nella scheda **Provisioning** dell'applicazione MediusFLow nel portale di Azure.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

7. Fare clic su **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

8. Per fare in modo che gli utenti importati con un set predefinito di ruoli, aziende e altre configurazioni generali in MediusFlow, è necessario configurarlo per primo. Per iniziare, aggiungere la configurazione facendo clic su **Add new configuration** (Aggiungi nuova configurazione).

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

9. Usare le impostazioni seguenti per gli utenti. In questa visualizzazione è possibile impostare l'attributo predefinito. Se le impostazioni standard sono corrette, è sufficiente immettere un nome aziendale valido. Poiché queste impostazioni di configurazione vengono recuperate da Mediusflow, devono essere configurate per prime. Per altre informazioni, vedere la sezione **Prerequisiti** di questo articolo.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

10. Fare clic sul pulsante **Salva** per salvare la configurazione utente.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

11. Per ottenere il collegamento del provisioning utenti, fare clic su **Copy SCIM Link** (Copia collegamento SCIM). Copiare e salvare questo valore che verrà immesso nel campo **URL tenant** nella scheda **Provisioning** dell'applicazione MediusFLow nel portale di Azure.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Screenshot della console di amministrazione di MediusFlow. La casella nome tenant MediusFlow e il pulsante Authenticate sono evidenziati nel primo passaggio di integrazione." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere MediusFlow dalla raccolta di applicazioni di Azure AD

Aggiungere MediusFlow dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in MediusFlow. Se MediusFlow è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a MediusFlow, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Passaggio 5. Configurare il provisioning utenti automatico per MediusFlow 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Per configurare il provisioning utenti automatico per MediusFlow in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **MediusFlow**.

    ![Collegamento di MediusFlow nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni Gestisci con l'opzione di provisioning denominata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere il valore di URL tenant recuperato in precedenza in **URL tenant**. Immettere il valore del token segreto recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a MediusFlow. Se la connessione non riesce, verificare che l'account MediusFlow disponga delle autorizzazioni di amministratore e riprovare.

      ![Screenshot mostra la finestra di dialogo credenziali amministratore, in cui è possibile immettere il tenant U R L e il token segreto.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to MediusFlow** (Sincronizza utenti di Azure Active Directory in MediusFlow).

9. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD in MediusFlow. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in MediusFlow per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API MediusFlow supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |emails[type eq "work"].value|string|
   |name.displayName|string|
   |active|Boolean|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |externalID|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|


10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to MediusFlow** (Sincronizza gruppi di Azure Active Directory con MediusFlow).

11. Nella sezione **Mapping degli attributi** esaminare gli attributi gruppo che vengono sincronizzati da Azure AD in MediusFlow. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in MediusFlow per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalID|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per MediusFlow, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in MediusFlow selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
