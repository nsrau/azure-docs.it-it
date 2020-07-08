---
title: 'Esercitazione: Configurare G Suite per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a G Suite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 5ecf8ca6e6790b218216aed9a6ff82c8a7ac9dd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85250146"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Esercitazione: Configurare G Suite per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire in G Suite e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [G Suite](https://gsuite.google.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Il connettore G Suite è stato aggiornato di recente il 2019 ottobre. Le modifiche apportate al connettore G Suite includono:
>
> * Aggiunta del supporto per altri attributi di gruppo e utente di G Suite.
> * I nomi degli attributi di destinazione di G Suite sono stati aggiornati in base a quanto definito [qui](https://developers.google.com/admin-sdk/directory).
> * Mapping degli attributi predefiniti aggiornati.

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in G Suite
> * Rimuovere gli utenti in G Suite quando non sono più necessari per l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e G Suite
> * Eseguire il provisioning di gruppi e appartenenze a gruppi in G Suite
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial) per G Suite (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* [Tenant di G Suite](https://gsuite.google.com/pricing.html)
* Un account utente in una suite G con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e G Suite](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare G Suite per supportare il provisioning con Azure AD

Prima di configurare G Suite per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in G Suite.

1. Accedere alla console di [amministrazione di G Suite](https://admin.google.com/) con l'account amministratore e quindi selezionare **sicurezza**. Se il collegamento non è visibile, è possibile che sia nascosto sotto il menu **More Controls** (Altri controlli) nella parte inferiore della schermata.

    ![Sicurezza di G Suite](./media/google-apps-provisioning-tutorial/gapps-security.png)

2. Nella pagina **Security** (Sicurezza) fare clic su **API Reference** (Riferimento API).

    ![API G Suite](./media/google-apps-provisioning-tutorial/gapps-api.png)

3. Selezionare **Attiva accesso API**.

    ![API G Suite abilitata](./media/google-apps-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Per ogni utente di cui si intende eseguire il provisioning in G Suite, il nome utente in Azure AD **deve** essere associato a un dominio personalizzato. Ad esempio, nomi utente simili a bob@contoso.onmicrosoft.com non vengono accettati da G Suite. mentre bob@contoso.com viene accettato. È possibile modificare il dominio di un utente esistente seguendo le istruzioni riportate [qui](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

4. Dopo aver aggiunto e verificato i domini personalizzati desiderati con Azure AD, è necessario verificarli di nuovo con G Suite. Per verificare i domini in G Suite, fare riferimento alla procedura seguente:

    a. Nella [console di amministrazione di G Suite](https://admin.google.com/)selezionare **domini**.

    ![Domini G Suite](./media/google-apps-provisioning-tutorial/gapps-domains.png)

    b. Selezionare **Add a domain or a domain alias** (Aggiungi un dominio o un alias di dominio).

    ![Aggiungi dominio G Suite](./media/google-apps-provisioning-tutorial/gapps-add-domain.png)

    c. Selezionare **Add another domain** (Aggiungi un altro dominio) e quindi digitare il nome del dominio che si vuole aggiungere.

    ![G Suite Aggiungi un altro](./media/google-apps-provisioning-tutorial/gapps-add-another.png)

    d. Selezionare **Continue and verify domain ownership** (Continua e verifica la proprietà del dominio). Seguire i passaggi per verificare che si è proprietari del nome di dominio. Per istruzioni complete su come verificare il dominio con Google, vedere [verificare la proprietà del sito](https://support.google.com/webmasters/answer/35179).

    e. Ripetere i passaggi precedenti per tutti i domini aggiuntivi che si intende aggiungere a G Suite.

5. Determinare quindi l'account amministratore che si vuole usare per gestire il provisioning utenti in G Suite. Passare a **ruoli di amministratore**.

    ![Amministratore di G Suite](./media/google-apps-provisioning-tutorial/gapps-admin.png)

6. Per il **ruolo di amministratore** dell'account, modificare i **privilegi** per quel ruolo. Assicurarsi di abilitare tutti i **privilegi dell'API di amministratore** in modo che l'account possa essere usato per il provisioning.

    ![Privilegi di amministratore di G Suite](./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere G Suite dalla raccolta di applicazioni di Azure AD

Aggiungere G Suite dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in G Suite. Se in precedenza è stato configurato G Suite per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a G Suite, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Passaggio 5. Configurare il provisioning utenti automatico in G Suite 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!NOTE]
> Per altre informazioni sull'endpoint dell'API directory di G Suite, vedere [API directory](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Per configurare il provisioning utenti automatico per G Suite in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**. Gli utenti dovranno eseguire l'accesso a portal.azure.com e non potranno usare aad.portal.azure.com

    ![Pannello delle applicazioni aziendali](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![Pannello Tutte le applicazioni](./media/google-apps-provisioning-tutorial/all-applications.png)

2. Nell'elenco di applicazioni selezionare **G Suite**.

    ![Collegamento di G Suite nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**. Fare clic su **Inizia**.

    ![Scheda Provisioning](common/provisioning.png)

      ![Pannello di avvio](./media/google-apps-provisioning-tutorial/get-started.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** fare clic su **autorizza**. Si verrà reindirizzati a una finestra di dialogo di autorizzazione di Google in una nuova finestra del browser.

      ![Autorizzare G Suite](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. Confermare che si desidera concedere Azure AD autorizzazioni per apportare modifiche al tenant di G Suite. Selezionare **Accetto**.

     ![Autenticazione tenant di G Suite](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. Nella portale di Azure fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a G Suite. Se la connessione non riesce, verificare che l'account G Suite disponga delle autorizzazioni di amministratore e riprovare. Ripetere quindi il passaggio per l'**autorizzazione**.

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Provision Azure Active Directory Users** (Esegui provisioning utenti di Azure Active Directory).

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a G Suite nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in G Suite per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API G Suite supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |primaryEmail|string|
   |relazioni. [digitare EQ "Manager"]. valore|string|
   |name.familyName|string|
   |name.givenName|string|
   |suspended|string|
   |externalIds. [digitare EQ "Custom"]. Value|string|
   |externalIds. [digitare EQ "Organization"]. Value|string|
   |indirizzi. [digitare EQ "Work"]. Country|string|
   |indirizzi. [digitare EQ "Work"]. streetAddress|string|
   |indirizzi. [digitare EQ "Work"]. Region|string|
   |indirizzi. [digitare EQ "Work"]. località|string|
   |indirizzi. [digitare EQ "Work"]. postalCode|string|
   |e-mail. [digitare EQ "Work"]. Address|string|
   |organizzazioni. [digitare EQ "Work"]. Department|string|
   |organizzazioni. [digitare EQ "Work"]. title|string|
   |PhoneNumbers. [digitare EQ "Work"]. Value|string|
   |PhoneNumbers. [digitare EQ "mobile"]. valore|string|
   |PhoneNumbers. [digitare EQ "work_fax"]. valore|string|
   |e-mail. [digitare EQ "Work"]. Address|string|
   |organizzazioni. [digitare EQ "Work"]. Department|string|
   |organizzazioni. [digitare EQ "Work"]. title|string|
   |PhoneNumbers. [digitare EQ "Work"]. Value|string|
   |PhoneNumbers. [digitare EQ "mobile"]. valore|string|
   |PhoneNumbers. [digitare EQ "work_fax"]. valore|string|
   |indirizzi. [digitare EQ "Home"]. Country|string|
   |indirizzi. [digitare EQ "Home"]. formattato|string|
   |indirizzi. [digitare EQ "Home"]. località|string|
   |indirizzi. [digitare EQ "Home"]. postalCode|string|
   |indirizzi. [digitare EQ "Home"]. Region|string|
   |indirizzi. [digitare EQ "Home"]. streetAddress|string|
   |indirizzi. [digitare EQ "other"]. Country|string|
   |indirizzi. [digitare EQ "other"]. formattato|string|
   |indirizzi. [digitare EQ "other"]. località|string|
   |indirizzi. [digitare EQ "other"]. postalCode|string|
   |indirizzi. [digitare EQ "other"]. Region|string|
   |indirizzi. [digitare EQ "other"]. streetAddress|string|
   |indirizzi. [digitare EQ "Work"]. formattato|string|
   |changePasswordAtNextLogin|string|
   |e-mail. [digitare EQ "Home"]. Address|string|
   |e-mail. [digitare EQ "other"]. Address|string|
   |externalIds. [digitare EQ "account"]. valore|string|
   |externalIds. [digitare EQ "Custom"]. customType|string|
   |externalIds. [digitare EQ "Customer"]. valore|string|
   |externalIds. [digitare EQ "login_id"]. valore|string|
   |externalIds. [digitare EQ "Network"]. Value|string|
   |Gender. Type|string|
   |GeneratedImmutableId|string|
   |Identificatore|string|
   |IMS. [digitare EQ "Home"]. Protocol|string|
   |IMS. [digitare EQ "other"]. Protocol|string|
   |IMS. [digitare EQ "Work"]. Protocol|string|
   |includeInGlobalAddressList|string|
   |ipWhitelisted|string|
   |organizzazioni. [digitare EQ "School"]. costCenter|string|
   |organizzazioni. [digitare EQ "School"]. Department|string|
   |organizzazioni. [digitare EQ "School"]. Domain|string|
   |organizzazioni. [digitare EQ "School"]. fullTimeEquivalent|string|
   |organizzazioni. [digitare EQ "School"]. location|string|
   |organizzazioni. [digitare EQ "School"]. Name|string|
   |organizzazioni. [digitare EQ "School"]. Symbol|string|
   |organizzazioni. [digitare EQ "School"]. title|string|
   |organizzazioni. [digitare EQ "Work"]. costCenter|string|
   |organizzazioni. [digitare EQ "Work"]. Domain|string|
   |organizzazioni. [digitare EQ "Work"]. fullTimeEquivalent|string|
   |organizzazioni. [digitare EQ "Work"]. location|string|
   |organizzazioni. [digitare EQ "Work"]. Name|string|
   |organizzazioni. [digitare EQ "Work"]. Symbol|string|
   |OrgUnitPath|string|
   |PhoneNumbers. [digitare EQ "Home"]. valore|string|
   |PhoneNumbers. [tipo EQ "altro"]. valore|string|
   |siti Web. [digitare EQ "Home"]. valore|string|
   |siti Web. [tipo EQ "altro"]. valore|string|
   |siti Web. [digitare EQ "Work"]. Value|string|
   

10. Nella sezione **mapping** selezionare **provisioning Azure Active Directory groups**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a G Suite nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in G Suite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |email|string|
      |Membri|string|
      |name|string|
      |description|string|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per G Suite, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in G Suite scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione.

> [!NOTE]
> Se gli utenti dispongono già di un account personale/utente esistente che usa l'indirizzo di posta elettronica dell'utente Azure AD, potrebbe verificarsi un problema che potrebbe essere risolto usando lo strumento Google Transfer prima di eseguire la sincronizzazione della directory.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
