---
title: "Esercitazione: configurare BizAgi Studio per l'automazione dei processi digitali per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a BizAgi Studio per l'automazione dei processi digitali.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: d177931429642436ceccae9c9051106ba5880ada
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180311"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Esercitazione: configurare BizAgi Studio per l'automazione dei processi digitali per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in BizAgi Studio per l'automazione dei processi digitali e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [BizAgi Studio per l'automazione dei processi digitali](https://www.bizagi.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in BizAgi Studio per l'automazione dei processi digitali
> * Rimuovere gli utenti in BizAgi Studio per l'automazione dei processi digitali quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e BizAgi Studio per l'automazione dei processi digitali
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) per BizAgi Studio per l'automazione dei processi digitali (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* BizAgi Studio per l'automazione dei processi digitali versione 11.2.4.2 X o successiva.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e BizAgi Studio per l'automazione dei processi digitali](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-bizagi-studio-for-digital-process-automation-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare BizAgi Studio per l'automazione dei processi digitali per supportare il provisioning con Azure AD


1. Accedere al portale aziendale come utente con autorizzazioni di **amministratore**.

2. Passare a **amministratore-> sicurezza-> le applicazioni OAuth 2**.

   ![impostazioni](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Fare clic sul pulsante Aggiungi.
4. Scegliere **token di porta** in tipo di concessione. Scegliere **API** e **sincronizzazione utente** in ambito consentito e fare clic su Salva.

   ![api](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Copiare e salvare il **segreto client**. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione BizAgi Studio per l'automazione dei processi digitali nel portale di Azure.

   ![token](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="step-3-add-bizagi-studio-for-digital-process-automation-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere BizAgi Studio per l'automazione dei processi digitali dalla raccolta di applicazioni Azure AD

Aggiungere BizAgi Studio per l'automazione dei processi digitali dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in BizAgi Studio per l'automazione dei processi digitali. Se in precedenza è stato configurato BizAgi Studio per l'automazione dei processi digitali per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a BizAgi Studio per l'automazione dei processi digitali, è necessario selezionare un ruolo diverso da quello **predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-bizagi-studio-for-digital-process-automation"></a>Passaggio 5. Configurare il provisioning utenti automatico in BizAgi Studio per l'automazione dei processi digitali 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Per configurare il provisioning utenti automatico per BizAgi Studio per l'automazione dei processi digitali in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **BizAgi Studio per l'automazione dei processi digitali**.

    ![Collegamento di BizAgi Studio per l'automazione dei processi digitali nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant di BizAgi Studio per l'automazione dei processi digitali e il token segreto. 

      * **URL tenant:** Immettere l'endpoint SCIM di BizAgi con questa struttura: <Your_Bizagi_Project>esempio di/SCIM/V2/: `https://my-company.bizagi.com/scim/v2/`

      * **Token segreto:** Questo valore viene recuperato in precedenza nel passaggio 2.

      Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a BizAgi Studio per l'automazione dei processi digitali. Se la connessione non riesce, verificare che l'account di automazione dei processi digitali di BizAgi Studio disponga di autorizzazioni di amministratore e riprovare.

   ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a BizAgi Studio per l'automazione dei processi digitali**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a BizAgi Studio per l'automazione dei processi digitali nella sezione **attributo-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in BizAgi Studio per l'automazione dei processi digitali per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API BizAgi Studio per l'automazione dei processi digitali supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |phoneNumbers[type eq "mobile"].value|string|
   
10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per BizAgi Studio per l'automazione dei processi digitali, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in BizAgi Studio per l'automazione dei processi digitali scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

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
