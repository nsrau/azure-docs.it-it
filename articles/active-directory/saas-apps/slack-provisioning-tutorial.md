---
title: 'Esercitazione: provisioning utenti per Slack-Azure AD'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Slack.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b7fa5aea835329be8f65a3bb1775ba5b0d97d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389862"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Esercitazione: Configurare Slack per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Slack e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Slack. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Slack
> * Rimuovere gli utenti in slack quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e slack
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in Slack
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) a Slack (scelta consigliata)


## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* [Tenant Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* Un tenant Slack con [piano Plus](https://aadsyncfabric.slack.com/pricing) o superiore abilitato.
* Un account utente in slack con autorizzazioni di amministratore di team.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e slack](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Passaggio 2: Aggiungere Slack dalla raccolta di applicazioni Azure AD

Aggiungere Slack dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in slack. Se in precedenza è stato configurato slack per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 3. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a slack, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Passaggio 4. Configurare il provisioning utenti automatico in Slack 

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Slack e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Slack in base all'assegnazione di utenti e gruppi in Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Slack con Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Slack**.

    ![Collegamento di Slack nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Verrà aperta una finestra di dialogo di autorizzazione di Slack in una nuova finestra del browser.

    ![Autorizzazione](media/slack-provisioning-tutorial/authorization.png)


6. Nella nuova finestra accedere a Slack con l'account di amministratore di team. Nella finestra di dialogo di autorizzazione risultante selezionare il team Slack per cui si vuole abilitare il provisioning e quindi **Authorize** (Autorizza). Al termine, tornare al portale di Azure per completare la configurazione del provisioning.

    ![Finestra di dialogo di autorizzazione](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Slack. Se la connessione non riesce, verificare che l'account Slack abbia autorizzazioni di amministratore di team e ripetere il passaggio "Authorize" (Autorizza).

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Selezionare **Salva**.

10. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Slack** (Sincronizza utenti di Azure Active Directory in Slack).

11. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che verranno sincronizzati da Azure AD a Slack. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con gli account utente in Slack per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |active|Boolean|
   |externalId|string|
   |displayName|string|
   |name.familyName|string|
   |name.givenName|string|
   |title|string|
   |emails[type eq "work"].value|string|
   |userName|string|
   |nickName|string|
   |indirizzi [tipo EQ "non tipizzato"]. streetAddress|string|
   |indirizzi [tipo EQ "non tipizzato"]. località|string|
   |indirizzi [tipo EQ "non tipizzato"]. Region|string|
   |indirizzi [tipo EQ "non tipizzato"]. postalCode|string|
   |indirizzi [tipo EQ "non tipizzato"]. Country|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "work"].value|string|
   |ruoli [Primary EQ "true"]. Value|string|
   |locale|string|
   |nome. honorificPrefix|string|
   |Foto [digitare EQ "Photo"]. valore|string|
   |profileUrl|string|
   |timezone|string|
   |userType|string|
   |urn: SCIM: schemas: Extension: Enterprise: 1.0. Department|string|
   |urn: SCIM: schemas: Extension: Enterprise: 1.0. Manager|Informazioni di riferimento|
   |urn: SCIM: schemas: Extension: Enterprise: 1.0. employeeNumber|string|
   |urn: SCIM: schemas: Extension: Enterprise: 1.0. costCenter|string|
   |urn: SCIM: schemas: estensione: Enterprise: 1.0. Organization|string|
   |urn: SCIM: schemas: Extension: Enterprise: 1.0. Division|string|

12. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Slack**.

13. Nella sezione **Mapping degli attributi** esaminare gli attributi gruppo che verranno sincronizzati da Azure AD a Slack. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con i gruppi in Slack per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |Membri di|Informazioni di riferimento|

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Slack, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in slack selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-5-monitor-your-deployment"></a>Passaggio 5. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Durante la configurazione dell'attributo **displayName** di Slack, tenere presente i seguenti comportamenti:

  * I valori non sono completamente univoci (2 utenti, ad esempio, possono avere lo stesso nome visualizzato)

  * Supporta caratteri non inglesi, spazi, maiuscole/minuscole. 
  
  * I segni di punteggiature consentiti sono punti, caratteri di sottolineatura, trattini, apostrofi, parentesi (ad esempio **( [ { } ] )**) e separatori (ad esempio **,/;**).
  
  * Si aggiorna solo se queste due impostazioni sono configurate nell'area di lavoro/organizzazione di Slack: **è abilitata la sincronizzazione del profilo** e **gli utenti non possono modificare il nome visualizzato**.
  
* L'attributo **userName** di Slack deve essere inferiore a 21 caratteri e avere un valore univoco.

* Slack consente solo la corrispondenza con gli attributi **username** e **email**.  

## <a name="change-log"></a>Registro delle modifiche

* 06/16/2020-attributo DisplayName modificato da aggiornare solo durante la creazione di nuovi utenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)