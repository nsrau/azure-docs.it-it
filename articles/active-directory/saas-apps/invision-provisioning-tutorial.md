---
title: "Esercitazione: configurare l'Invisione per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a invisioning.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 72518dda-d485-45c8-849e-6b27ee09d9a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2020
ms.author: Zhchia
ms.openlocfilehash: c58a3d9f07b62e92d1e71096ae9dd3fc85515843
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459884"
---
# <a name="tutorial-configure-invision-for-automatic-user-provisioning"></a>Esercitazione: configurare l'Invisione per il provisioning utenti automatico

In questa esercitazione vengono descritti i passaggi da eseguire sia in Invision che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per l' [Invisione](https://www.invisionapp.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Invision
> * Rimuovere gli utenti in Invisione quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e Invision
> * [Single Sign-on](./invision-tutorial.md) per Invision (obbligatorio)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../users-groups-roles/directory-assign-admin-roles.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un [account Enterprise di Invisione](https://www.invisionapp.com/enterprise) con SSO abilitato.
* Un account utente in Invision con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e invisioning](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-invision-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare INVISIO per supportare il provisioning con Azure AD

1. Accedere all' [account aziendale di Invision](https://www.invisionapp.com/enterprise) come amministratore o proprietario. Aprire il cassetto **impostazioni team** in basso a sinistra e selezionare **Impostazioni**.

   ![Configurazione dell'installazione di SCIM](./media/invision-provisioning-tutorial/invision-scim-settings.png)

2. Selezionare **modifica** nell'impostazione **provisioning utenti con SCIM** .

   ![Impostazioni del provisioning di SCIM](./media/invision-provisioning-tutorial/invision-provisioning-settings.png)

3. Selezionare l'interruttore per abilitare il provisioning di SCIM. Si noti che è necessario che l'accesso SSO sia configurato per primo per poter abilitare SCIM:

   ![SCIM Abilita il provisioning](./media/invision-provisioning-tutorial/enable-scim-provisioning.png)

4. Copiare l' **URL dell'API scim** e accodarlo `/scim/v2` all'URL. Copiare il **token di autenticazione**. Salvare questi valori da usare in un secondo momento nei campi **URL tenant** e **token segreto** nella scheda provisioning dell'applicazione di Invisione nel portale di Azure.

   ![Token di accesso SCIM](./media/invision-provisioning-tutorial/invision-access-token.png)


## <a name="step-3-add-invision-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere invisioni dalla raccolta di applicazioni di Azure AD

Aggiungere invisioni dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Invision. Se in precedenza è stato impostato Invision per SSO, è possibile utilizzare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a Invision, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-invision"></a>Passaggio 5. Configurare il provisioning utenti automatico in Invision 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-invision-in-azure-ad"></a>Per configurare il provisioning utenti automatico per l'Invisione in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **InVision**.

    ![Collegamento di Invision nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere il valore dell'URL dell'API scim recuperato in precedenza in **URL tenant**. Immettere il valore del token di autenticazione recuperato in precedenza nel **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a Invision. Se la connessione ha esito negativo, verificare che l'account Invision disponga di autorizzazioni di amministratore e riprovare.

    ![Credenziali di amministratore](./media/inVision-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Provision Azure Active Directory Users** (Esegui provisioning utenti di Azure Active Directory).

9. Esaminare gli attributi utente sincronizzati da Azure AD a Invision nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Invision per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API di Invisione supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.familyName|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per Invision, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Invision selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

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