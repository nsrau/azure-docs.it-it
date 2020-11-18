---
title: 'Esercitazione: Provisioning utenti per Slack - Azure AD'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Slack.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 58fe99164c390d8a9435e5aa65e55fe7fca5d6db
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359477"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Esercitazione: Configurare Slack per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Slack e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Slack. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Slack
> * Rimuovere utenti da Slack quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e Slack
> * Effettuare il provisioning di gruppi e appartenenza a gruppi in Slack
> * [Accesso Single Sign-On](./slack-tutorial.md) a Slack (scelta consigliata)


## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md).
* Un account utente in Azure AD con l'[autorizzazione](../users-groups-roles/directory-assign-admin-roles.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* Un tenant di Slack con il [piano Plus](https://aadsyncfabric.slack.com/pricing) o superiore abilitato.
* Un account utente in Slack con autorizzazioni di amministratore di team.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati [mappare tra Azure AD e Slack](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Passaggio 2. Aggiungere Slack dalla raccolta di applicazioni di Azure AD

Aggiungere Slack dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Slack. Se Slack è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 3. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a Slack, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Passaggio 4. Configurare il provisioning automatico degli utenti per Slack 

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Slack e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Slack in base all'assegnazione di utenti e gruppi in Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Slack con Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Slack**.

    ![Collegamento di Slack nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Verrà aperta una finestra di dialogo di autorizzazione di Slack in una nuova finestra del browser.

    ![Screenshot che mostra il pulsante Autorizza per Credenziali amministratore.](media/slack-provisioning-tutorial/authorization.png)


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
   |addresses[type eq "untyped"].streetAddress|string|
   |addresses[type eq "untyped"].locality|string|
   |addresses[type eq "untyped"].region|string|
   |addresses[type eq "untyped"].postalCode|string|
   |addresses[type eq "untyped"].country|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "work"].value|string|
   |roles[primary eq "True"].value|string|
   |locale|string|
   |name.honorificPrefix|string|
   |photos[type eq "photo"].value|string|
   |profileUrl|string|
   |timezone|string|
   |userType|string|
   |urn:scim:schemas:extension:enterprise:1.0.department|string|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Informazioni di riferimento|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|string|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|string|
   |urn:scim:schemas:extension:enterprise:1.0.organization|string|
   |urn:scim:schemas:extension:enterprise:1.0.division|string|

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Slack** (Sincronizza gruppi di Azure Active Directory con Slack).

13. Nella sezione **Mapping degli attributi** esaminare gli attributi gruppo che verranno sincronizzati da Azure AD a Slack. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con i gruppi in Slack per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |Membri di|Informazioni di riferimento|

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Slack, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Slack scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-5-monitor-your-deployment"></a>Passaggio 5. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

* Durante la configurazione dell'attributo **displayName** di Slack, tenere presente i seguenti comportamenti:

  * I valori non sono completamente univoci (2 utenti, ad esempio, possono avere lo stesso nome visualizzato)

  * Supporta caratteri non inglesi, spazi, maiuscole/minuscole. 
  
  * I segni di punteggiature consentiti sono punti, caratteri di sottolineatura, trattini, apostrofi, parentesi (ad esempio **( [ { } ] )**) e separatori (ad esempio **,/;**).
  
  * La proprietà displayName non può includere il carattere '@'. Se è incluso il carattere '@', è possibile che un evento venga ignorato nei log di provisioning con la descrizione "AttributeValidationFailed".

  * Si aggiorna solo se queste due impostazioni sono configurate nell'area di lavoro/organizzazione di Slack: **è abilitata la sincronizzazione del profilo** e **gli utenti non possono modificare il nome visualizzato**.

* L'attributo **userName** di Slack deve essere inferiore a 21 caratteri e avere un valore univoco.

* Slack consente solo la corrispondenza con gli attributi **userName** e **email**.  
  
* I codici di errore comuni sono documentati nella documentazione ufficiale di Slack, https://api.slack.com/scim#errors

## <a name="change-log"></a>Registro delle modifiche

* 16/06/2020 - Modificato l'attributo DisplayName perché venga aggiornato solo durante la creazione di nuovi utenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)