---
title: 'Esercitazione: Configurare G Suite per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a G Suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 6643ec72ec5126866b0ad6e924a92f02170bd278
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359664"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Esercitazione: Configurare G Suite per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in G Suite che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD effettua automaticamente il provisioning e il deprovisioning di utenti e gruppi per [G Suite](https://gsuite.google.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Il connettore per G Suite è stato aggiornato nel mese di ottobre 2019. Le modifiche apportate al connettore G Suite includono:
>
> * Aggiunta del supporto per altri attributi di gruppo e utente di G Suite.
> * Aggiornamento dei nomi degli attributi di destinazione di G Suite in base a quanto definito [qui](https://developers.google.com/admin-sdk/directory).
> * Aggiornamento dei mapping degli attributi predefiniti.

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in G Suite
> * Rimozione di utenti in G Suite quando l'accesso non è più necessario
> * Sincronizzazione degli attributi utente tra Azure AD e G Suite
> * Provisioning di gruppi e appartenenze a gruppi in G Suite
> * [Accesso Single Sign-On](./google-apps-tutorial.md) a G Suite (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../users-groups-roles/directory-assign-admin-roles.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* [Un tenant di G Suite](https://gsuite.google.com/pricing.html)
* Account utente in G Suite con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati [mappare tra Azure AD e G Suite](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare G Suite per supportare il provisioning con Azure AD

Prima di configurare G Suite per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in G Suite.

1. Accedere alla [console di amministrazione di G Suite](https://admin.google.com/) con l'account amministratore e selezionare **Security** (Sicurezza). Se il collegamento non è visibile, è possibile che sia nascosto sotto il menu **More Controls** (Altri controlli) nella parte inferiore della schermata.

    ![Sicurezza di G Suite](./media/google-apps-provisioning-tutorial/gapps-security.png)

2. Nella pagina **Security** (Sicurezza) fare clic su **API Reference** (Riferimento API).

    ![API G Suite](./media/google-apps-provisioning-tutorial/gapps-api.png)

3. Selezionare **Attiva accesso API**.

    ![API G Suite abilitata](./media/google-apps-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Per ogni utente per cui verrà effettuato il provisioning in G Suite, è **necessario** associare il relativo nome utente in Azure AD a un dominio personalizzato. Ad esempio, nomi utente simili a bob@contoso.onmicrosoft.com non vengono accettati da G Suite. mentre bob@contoso.com viene accettato. È possibile modificare il dominio di un utente esistente seguendo le istruzioni riportate [qui](../fundamentals/add-custom-domain.md).

4. Dopo aver aggiunto e verificato i domini personalizzati desiderati con Azure AD, è necessario verificarli di nuovo con G Suite. Per verificare i domini in G Suite, seguire questa procedura:

    a. Nella [console di amministrazione di G Suite](https://admin.google.com/) selezionare **Domains** (Domini).

    ![Domini di G Suite](./media/google-apps-provisioning-tutorial/gapps-domains.png)

    b. Selezionare **Add a domain or a domain alias** (Aggiungi un dominio o un alias di dominio).

    ![Aggiunta di un dominio di G Suite](./media/google-apps-provisioning-tutorial/gapps-add-domain.png)

    c. Selezionare **Add another domain** (Aggiungi un altro dominio) e quindi digitare il nome del dominio che si vuole aggiungere.

    ![Aggiunta di un altro dominio di G Suite](./media/google-apps-provisioning-tutorial/gapps-add-another.png)

    d. Selezionare **Continue and verify domain ownership** (Continua e verifica la proprietà del dominio). Seguire i passaggi per verificare che si è proprietari del nome di dominio. Per istruzioni dettagliate su come verificare il dominio con Google, vedere [Verificare la proprietà del sito](https://support.google.com/webmasters/answer/35179).

    e. Ripetere i passaggi precedenti per tutti i domini aggiuntivi che si intende aggiungere a G Suite.

5. Determinare quindi l'account amministratore da usare per gestire il provisioning utenti in G Suite. Passare **Ruoli di amministratore**.

    ![Amministratore di G Suite](./media/google-apps-provisioning-tutorial/gapps-admin.png)

6. Per il **ruolo di amministratore** dell'account, modificare i **privilegi** per quel ruolo. Assicurarsi di abilitare tutti i **privilegi dell'API di amministratore** in modo che l'account possa essere usato per il provisioning.

    ![Privilegi di amministratore di G Suite](./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere G Suite dalla raccolta di applicazioni di Azure AD

Aggiungere G Suite dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in G Suite. Se G Suite è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a G Suite, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Passaggio 5. Configurare il provisioning utenti automatico in G Suite 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!NOTE]
> Per altre informazioni sull'endpoint dell'API Directory di G Suite, vedere [API Directory](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Per configurare il provisioning utenti automatico per G Suite in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**. Gli utenti dovranno eseguire l'accesso a portal.azure.com e non potranno usare aad.portal.azure.com

    ![Pannello delle applicazioni aziendali](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![Pannello Tutte le applicazioni](./media/google-apps-provisioning-tutorial/all-applications.png)

2. Nell'elenco di applicazioni selezionare **G Suite**.

    ![Collegamento di G Suite nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**. Fare clic su **Inizia**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

      ![Pannello di avvio](./media/google-apps-provisioning-tutorial/get-started.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Si verrà reindirizzati a una finestra di dialogo di autorizzazione di Google in una nuova finestra del browser.

      ![Autorizzazione di G Suite](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. Confermare che si vuole concedere ad Azure AD le autorizzazioni per apportare modifiche al tenant di G Suite. Selezionare **Accetto**.

     ![Autorizzazione per il tenant di G Suite](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi a G Suite. Se la connessione non riesce, verificare che l'account G Suite disponga delle autorizzazioni di amministratore e riprovare. Ripetere quindi il passaggio per l'**autorizzazione**.

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Provision Azure Active Directory Users** (Esegui provisioning utenti di Azure Active Directory).

9. Esaminare gli attributi utente sincronizzati da Azure AD a G Suite nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in G Suite per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API G Suite supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |primaryEmail|string|
   |relations.[type eq "manager"].value|string|
   |name.familyName|string|
   |name.givenName|string|
   |suspended|string|
   |externalIds.[type eq "custom"].value|string|
   |externalIds.[type eq "organization"].value|string|
   |addresses.[type eq "work"].country|string|
   |addresses.[type eq "work"].streetAddress|string|
   |addresses.[type eq "work"].region|string|
   |addresses.[type eq "work"].locality|string|
   |addresses.[type eq "work"].postalCode|string|
   |emails.[type eq "work"].address|string|
   |organizations.[type eq "work"].department|string|
   |organizations.[type eq "work"].title|string|
   |phoneNumbers.[type eq "work"].value|string|
   |phoneNumbers.[type eq "mobile"].value|string|
   |phoneNumbers.[type eq "work_fax"].value|string|
   |emails.[type eq "work"].address|string|
   |organizations.[type eq "work"].department|string|
   |organizations.[type eq "work"].title|string|
   |phoneNumbers.[type eq "work"].value|string|
   |phoneNumbers.[type eq "mobile"].value|string|
   |phoneNumbers.[type eq "work_fax"].value|string|
   |addresses.[type eq "home"].country|string|
   |addresses.[type eq "home"].formatted|string|
   |addresses.[type eq "home"].locality|string|
   |addresses.[type eq "home"].postalCode|string|
   |addresses.[type eq "home"].region|string|
   |addresses.[type eq "home"].streetAddress|string|
   |addresses.[type eq "other"].country|string|
   |addresses.[type eq "other"].formatted|string|
   |addresses.[type eq "other"].locality|string|
   |addresses.[type eq "other"].postalCode|string|
   |addresses.[type eq "other"].region|string|
   |addresses.[type eq "other"].streetAddress|string|
   |addresses.[type eq "work"].formatted|string|
   |changePasswordAtNextLogin|string|
   |emails.[type eq "home"].address|string|
   |emails.[type eq "other"].address|string|
   |externalIds.[type eq "account"].value|string|
   |externalIds.[type eq "custom"].customType|string|
   |externalIds.[type eq "customer"].value|string|
   |externalIds.[type eq "login_id"].value|string|
   |externalIds.[type eq "network"].value|string|
   |gender.type|string|
   |GeneratedImmutableId|string|
   |Identificatore|string|
   |ims.[type eq "home"].protocol|string|
   |ims.[type eq "other"].protocol|string|
   |ims.[type eq "work"].protocol|string|
   |includeInGlobalAddressList|string|
   |ipWhitelisted|string|
   |organizations.[type eq "school"].costCenter|string|
   |organizations.[type eq "school"].department|string|
   |organizations.[type eq "school"].domain|string|
   |organizations.[type eq "school"].fullTimeEquivalent|string|
   |organizations.[type eq "school"].location|string|
   |organizations.[type eq "school"].name|string|
   |organizations.[type eq "school"].symbol|string|
   |organizations.[type eq "school"].title|string|
   |organizations.[type eq "work"].costCenter|string|
   |organizations.[type eq "work"].domain|string|
   |organizations.[type eq "work"].fullTimeEquivalent|string|
   |organizations.[type eq "work"].location|string|
   |organizations.[type eq "work"].name|string|
   |organizations.[type eq "work"].symbol|string|
   |OrgUnitPath|string|
   |phoneNumbers.[type eq "home"].value|string|
   |phoneNumbers.[type eq "other"].value|string|
   |websites.[type eq "home"].value|string|
   |websites.[type eq "other"].value|string|
   |websites.[type eq "work"].value|string|
   

10. Nella sezione **Mapping** selezionare **Provision Azure Active Directory Users** (Effettua il provisioning dei gruppi di Azure Active Directory).

11. Esaminare gli attributi del gruppo sincronizzati da Azure AD a G Suite nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in G Suite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |email|string|
      |Membri|string|
      |name|string|
      |description|string|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per G Suite, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in G Suite selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione.

> [!NOTE]
> Se gli utenti dispongono già di un account personale/consumer esistente che usa l'indirizzo di posta elettronica dell'utente Azure AD, potrebbe verificarsi un problema che è possibile risolvere usando lo strumento Google Transfer prima di eseguire la sincronizzazione della directory.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)