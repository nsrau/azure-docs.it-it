---
title: 'Esercitazione: Configurare ServiceNow per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205104"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Esercitazione: Configurare ServiceNow per il provisioning automatico degli utentiTutorial: Configure ServiceNow for automatic user provisioning

Questa esercitazione descrive i passaggi da eseguire sia in ServiceNow che in Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Quando si configura la configurazione, Azure AD esegue automaticamente il provisioning e il provisioning di utenti e gruppi in [ServiceNow](https://www.servicenow.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in ServiceNowCreate users in ServiceNow
> * Rimuovere gli utenti in ServiceNow quando non richiedono più l'accessoRemove users in ServiceNow when they do not require access atmore
> * Mantenere sincronizzati gli attributi utente tra Azure AD e ServiceNowKeep user attributes synchronized between Azure AD and ServiceNow
> * Effettuare il provisioning di gruppi e appartenenze ai gruppi in ServiceNowProvision groups and group memberships in ServiceNow
> * [Single Sign-On](servicenow-tutorial.md) a ServiceNow (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale). 
* [Un'istanza di ServiceNow](https://www.servicenow.com/) di Calgary o superiore
* [Un'istanza ServiceNow Express](https://www.servicenow.com/) di Helsinki o superiore
* Un account utente in ServiceNow con il ruolo di amministratore

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati [di cui eseguire il mapping tra Azure AD e ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare ServiceNow per supportare il provisioning con Azure ADConfigure ServiceNow to support provisioning with Azure AD

1. Identificare il nome dell'istanza ServiceNow.Identify your ServiceNow instance name. You can find the instance name in the URL that you use to access ServiceNow. Nell'esempio seguente, il nome dell'istanza è dev35214.In the example below, the instance name is dev35214.

![Istanza ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Ottenere le credenziali per un amministratore in ServiceNow.Obtain credentials for an admin in ServiceNow. Passare al profilo utente in ServiceNow e verificare che l'utente disponga del ruolo di amministratore. 

![Ruolo di amministratore ServiceNowServiceNow admin role](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere ServiceNow dalla raccolta di applicazioni di Azure ADAdd ServiceNow from the Azure AD application gallery

Aggiungere ServiceNow dalla raccolta di applicazioni di Azure AD per avviare la gestione del provisioning in ServiceNow.Add ServiceNow from the Azure AD application gallery to start managing provisioning to ServiceNow. Se in precedenza è stato configurato ServiceNow per SSO, è possibile utilizzare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando si assegnano utenti e gruppi a ServiceNow, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passaggio 5. Configurare il provisioning automatico degli utenti su ServiceNowConfigure automatic user provisioning to ServiceNow 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per ServiceNow in Azure AD:To configure automatic user provisioning for ServiceNow in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni, selezionare **ServiceNow**.

    ![Collegamento di ServiceNow nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere le credenziali di amministratore e il nome utente di ServiceNow. Fare clic su Test connessione per verificare che Azure AD possa connettersi a ServiceNow.Click **Test Connection** to ensure Azure AD can connect to ServiceNow. Se la connessione non riesce, verificare che l'account ServiceNow disponga delle autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con ServiceNow**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a ServiceNow nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in ServiceNow per le operazioni di aggiornamento. Se si sceglie di modificare l'attributo di [destinazione corrispondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)è necessario assicurarsi che l'API ServiceNow supporti il filtro degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con ServiceNow**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a ServiceNow nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con i gruppi in ServiceNow per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per ServiceNow, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for ServiceNow, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in ServiceNow scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

1. Utilizzare i registri di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno
2. Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la sua vicinanza al completamento
3. Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
* **InvalidLookupReference:** Quando si esegue il provisioning di determinati attributi, ad esempio Department e Location in ServiceNow, i valori devono essere già presenti in una tabella di riferimento in ServiceNow.When provisioning certain attributes such as Department and Location in ServiceNow, the values must already exist in a reference table in ServiceNow. Ad esempio, è possibile avere due sedi (Seattle, Los Angeles) e tre reparti (Sales, Finance, Marketing) nella tabella **Inserisci nome tabella** in ServiceNow. Se si tenta di eseguire il provisioning di un utente in cui il reparto è "Vendite" e la posizione è "Seattle" verrà eseguito il provisioning con successo. Se si tenta di eseguire il provisioning di un utente con il reparto "Vendite" e la posizione "LA" non verrà eseguito il provisioning dell'utente. The location LA must either be added to the reference table in ServiceNow or the user attribute in Azure AD must be updated to match the format in ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Esaminare [i mapping degli attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) per identificare l'attributo corrispondente. Questo valore deve essere presente nell'utente o nel gruppo di cui si sta tentando di eseguire il provisioning. 
* Esaminare [l'API SOAP ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) per comprendere eventuali requisiti o limitazioni (ad esempio, formato per specificare il codice paese per un utente)
* Alcune distribuzioni di ServiceNow richiedono la autorizzazione degli intervalli IP per il servizio di provisioning di Azure AD. Gli intervalli IP riservati per il servizio di provisioning di Azure AD sono disponibili [qui](https://www.microsoft.com/download/details.aspx?id=56519) in "AzureActiveDirectoryDomainServices".
* Le richieste di provisioning vengono inviate per impostazione predefinita a https://<a0>nome-istanza-utente/.service-now.com/'nome-tabella'. Se è necessario un URL tenant personalizzato, è possibile fornire l'intero URL nel campo del nome dell'istanza.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
