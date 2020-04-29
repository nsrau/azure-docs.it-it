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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78205104"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Esercitazione: configurare ServiceNow per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in ServiceNow che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [ServiceNow](https://www.servicenow.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in ServiceNow
> * Rimuovere gli utenti in ServiceNow quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e ServiceNow
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in ServiceNow
> * [Single Sign-on](servicenow-tutorial.md) per ServiceNow (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l' [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la configurazione del provisioning, ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale. 
* Un' [istanza ServiceNow](https://www.servicenow.com/) di Calgary o versione successiva
* [Istanza di ServiceNow Express](https://www.servicenow.com/) di Helsinki o superiore
* Un account utente in ServiceNow con il ruolo di amministratore

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul [funzionamento del servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare chi sarà nell' [ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare ServiceNow per supportare il provisioning con Azure AD

1. Identificare il nome dell'istanza di ServiceNow. È possibile trovare il nome dell'istanza nell'URL usato per accedere a ServiceNow. Nell'esempio seguente il nome dell'istanza è dev35214.

![Istanza di ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Ottenere le credenziali per un amministratore in ServiceNow. Passare al profilo utente in ServiceNow e verificare che l'utente disponga del ruolo di amministratore. 

![Ruolo di amministratore di ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere ServiceNow dalla raccolta di applicazioni Azure AD

Aggiungere ServiceNow dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in ServiceNow. Se in precedenza è stato configurato ServiceNow per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Altre informazioni sull'aggiunta di un'applicazione dalla raccolta sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi sarà nell'ambito per il provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi](../manage-apps/assign-user-or-group-access-portal.md) seguenti per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a ServiceNow, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Avviare Small. Eseguire il test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passaggio 5. Configurare il provisioning utenti automatico in ServiceNow 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Per configurare il provisioning utenti automatico per ServiceNow in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **ServiceNow**.

    ![Collegamento di ServiceNow nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere il nome utente e le credenziali di amministratore di ServiceNow. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a ServiceNow. Se la connessione non riesce, verificare che l'account ServiceNow disponga delle autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a ServiceNow**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a ServiceNow nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in ServiceNow per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API ServiceNow supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to ServiceNow**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a ServiceNow nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in ServiceNow per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per ServiceNow, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in ServiceNow selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti nell' **ambito** nella sezione **Impostazioni** . Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, a condizione che sia in esecuzione il servizio di provisioning Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo
2. Controllare l' [indicatore di stato per visualizzare](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) lo stato del ciclo di provisioning e il modo in cui terminarlo
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entra in quarantena. Altre informazioni sugli stati di quarantena sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
* **InvalidLookupReference:** Quando si esegue il provisioning di determinati attributi, ad esempio Department e location in ServiceNow, i valori devono essere già presenti in una tabella di riferimento in ServiceNow. Ad esempio, si possono avere due posizioni (Seattle, Los Angeles) e tre reparti (vendite, finanza, marketing) nella tabella **Inserisci nome tabella** in ServiceNow. Se si tenta di effettuare il provisioning di un utente in cui il suo reparto è "Sales" e location è "Seattle", ne verrà eseguito il provisioning. Se si tenta di effettuare il provisioning di un utente con reparto "vendite" e località "LA", non verrà eseguito il provisioning dell'utente. Il percorso LA deve essere aggiunto alla tabella di riferimento in ServiceNow o l'attributo utente in Azure AD deve essere aggiornato in modo che corrisponda al formato in ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Esaminare i [mapping degli attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) per identificare l'attributo corrispondente. Questo valore deve essere presente nell'utente o gruppo di cui si sta tentando di effettuare il provisioning. 
* Esaminare l' [API SOAP ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) per comprendere eventuali requisiti o limitazioni (ad esempio, formattare per specificare il codice paese per un utente)
* Per alcune distribuzioni di ServiceNow è necessario consentire gli intervalli IP per il servizio di provisioning Azure AD. Gli intervalli IP riservati per il servizio di provisioning Azure AD sono disponibili [qui](https://www.microsoft.com/download/details.aspx?id=56519) in "AzureActiveDirectoryDomainServices".
* Per impostazione predefinita, le richieste di provisioning vengono inviate a https://{nome-istanza}. servizio-ora. com/{nome-tabella}. Se è necessario un URL tenant personalizzato, è possibile specificare l'intero URL nel campo nome istanza.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
