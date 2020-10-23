---
title: 'Esercitazione: configurare la sincronizzazione del destinatario Cofense per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD alla sincronizzazione del destinatario Cofense.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 84fe20ef-0de0-4f7c-9b42-6385f3d834db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2020
ms.author: Zhchia
ms.openlocfilehash: fa187d9f7ee2b4b91c8559a185f55f0015f0b441
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455630"
---
# <a name="tutorial-configure-cofense-recipient-sync-for-automatic-user-provisioning"></a>Esercitazione: configurare la sincronizzazione del destinatario Cofense per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia nella sincronizzazione del destinatario Cofense che nella Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning degli utenti per la [sincronizzazione del destinatario Cofense](https://cofense.com/) tramite il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti nella sincronizzazione del destinatario Cofense
> * Rimuovere gli utenti nella sincronizzazione del destinatario Cofense quando non sono più necessari per l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e la sincronizzazione del destinatario Cofense

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../users-groups-roles/directory-assign-admin-roles.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un account operatore standard in Cofense PhishMe.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e la sincronizzazione del destinatario Cofense](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-cofense-recipient-sync-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare la sincronizzazione del destinatario Cofense per supportare il provisioning con Azure AD

1. Accedere a Cofense PhishMe. Passare a **destinatari > sincronizzazione del destinatario**. 
2. Accettare i termini e le condizioni e quindi **fare clic su inizia.**

    ![TNC sincronizzazione destinatario](media/cofense-provisioning-tutorial/recipient-sync-toc.png)

3. Copiare i valori dai campi **URL** e **token** .

    ![Sincronizzazione destinatario](media/cofense-provisioning-tutorial/recipient-sync-getting-started.png)


## <a name="step-3-add-cofense-recipient-sync-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere la sincronizzazione del destinatario Cofense dalla raccolta di applicazioni Azure AD

Aggiungere la sincronizzazione del destinatario Cofense dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in sincronizzazione destinatari di Cofense. Se in precedenza è stata impostata la sincronizzazione del destinatario Cofense per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi alla sincronizzazione del destinatario Cofense, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-cofense-recipient-sync"></a>Passaggio 5. Configurare il provisioning utenti automatico per la sincronizzazione del destinatario Cofense 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare gli utenti nella sincronizzazione del destinatario Cofense in base all'utente in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cofense-recipient-sync-in-azure-ad"></a>Per configurare il provisioning utenti automatico per la sincronizzazione del destinatario Cofense in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Cofense destinatario sincronizzazione**.

    ![Collegamento di Cofense nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning automatica](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL di **base scim 2,0 e** il valore del token di autenticazione scim recuperato in precedenza nel passaggio 2. Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi alla sincronizzazione del destinatario Cofense. Se la connessione non riesce, verificare che l'account di sincronizzazione del destinatario Cofense disponga delle autorizzazioni di amministratore e riprovare.

    ![Token URL tenant](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Cofense destinatario Sync**.

9. Esaminare gli attributi utente sincronizzati da Azure AD alla sincronizzazione del destinatario Cofense nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente nella sincronizzazione del destinatario Cofense per le operazioni di aggiornamento.  Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |externalId|string|
   |active|Boolean|
   |displayName|string|
   |name.formatted|string|
   |name.givenName|string|
   |name.familyName|string|
   |nome. honorificSuffix|string|
   |phoneNumbers [digitare EQ "Work"]. Value|string|
   |phoneNumbers [digitare EQ "Home"]. valore|string|
   |phoneNumbers [digitare EQ "other"]. Value|string|
   |phoneNumbers [digitare EQ "pager"]. valore|string|
   |phoneNumbers [digitare EQ "mobile"]. valore|string|
   |phoneNumbers [digitare EQ "fax"]. valore|string|
   |indirizzi [tipo EQ "other"]. formattato|string|
   |indirizzi [tipo EQ "lavoro"]. formattato|string|
   |indirizzi [digitare EQ "Work"]. streetAddress|string|
   |indirizzi [digitare EQ "Work"]. località|string|
   |indirizzi [digitare EQ "Work"]. Region|string|
   |indirizzi [digitare EQ "Work"]. postalCode|string|
   |indirizzi [digitare EQ "Work"]. Country|string|
   |title|string|
   |emails[type eq "work"].value|string|
   |messaggi di posta elettronica [digitare EQ "Home"]. valore|string|
   |messaggi di posta elettronica [digitare EQ "other"]. Value|string|
   |preferredLanguage|string|
   |nickName|string|
   |userType|string|
   |locale|string|
   |timezone|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per la sincronizzazione del destinatario Cofense, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning per la sincronizzazione del destinatario Cofense scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

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