---
title: 'Esercitazione: Configurare ServiceNow per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: bddb33210f3b1d76a89ce18ddf6884898905f388
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91286404"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Esercitazione: Configurare ServiceNow per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in ServiceNow che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [ServiceNow](https://www.servicenow.com/) tramite il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in ServiceNow
> * Rimozione di utenti in ServiceNow quando l'accesso non è più necessario
> * Gestione della sincronizzazione degli attributi utente tra Azure AD e ServiceNow
> * Provisioning di gruppi e appartenenze a gruppi in ServiceNow
> * [Accesso Single Sign-On](servicenow-tutorial.md) a ServiceNow (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un'[istanza di ServiceNow](https://www.servicenow.com/) Calgary o versione successiva
* Un'[istanza di ServiceNow Express](https://www.servicenow.com/) Helsinki o versione successiva
* Un account utente in ServiceNow con il ruolo amministratore

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare i dati da [mappare tra Azure AD e ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare ServiceNow per supportare il provisioning con Azure AD

1. Identificare il nome dell'istanza di ServiceNow. Il nome dell'istanza è disponibile nell'URL usato per accedere a ServiceNow. Nell'esempio seguente il nome dell'istanza è dev35214.

   ![Istanza di ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Ottenere le credenziali per un amministratore in ServiceNow. Passare al profilo utente in ServiceNow e verificare che l'utente disponga del ruolo di amministratore. 

   ![Ruolo amministratore ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Assicurarsi che le impostazioni seguenti siano **disabilitate** in ServiceNow:

   1. Selezionare **sicurezza di sistema**  >  **impostazioni di sicurezza elevata**  >  **richiedere l'autenticazione di base per le richieste di schema in ingresso**.
   2. Selezione **proprietà di sistema**  >  i**servizi Web**  >  **richiedono l'autorizzazione di base per le richieste SOAP in ingresso**.
     
   > [!IMPORTANT]
   > Se queste impostazioni sono *abilitate*, il motore di provisioning non riuscirà a comunicare con ServiceNow.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere ServiceNow dalla raccolta di applicazioni di Azure AD

Aggiungere ServiceNow dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in ServiceNow. Se ServiceNow è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a ServiceNow, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passaggio 5. Configurare il provisioning utenti automatico per ServiceNow 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Per configurare il provisioning utenti automatico per ServiceNow in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni, selezionare **ServiceNow**.

    ![Collegamento di ServiceNow nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni Gestisci con l'opzione di provisioning denominata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere le credenziali dell'amministratore di ServiceNow e il nome utente. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app ServiceNow. Se la connessione non riesce, verificare che l'account ServiceNow disponga delle autorizzazioni di amministratore e riprovare.

    ![Screenshot mostra la pagina di provisioning del servizio, in cui è possibile immettere le credenziali di amministratore.](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to ServiceNow** (Sincronizza utenti di Azure Active Directory in ServiceNow).

9. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD in ServiceNow. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in ServiceNow per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API ServiceNow supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to ServiceNow** (Sincronizza gruppi di Azure Active Directory con ServiceNow).

11. Nella sezione **Mapping degli attributi** esaminare gli attributi del gruppo che vengono sincronizzati da Azure AD in ServiceNow. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in ServiceNow per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per ServiceNow, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in ServiceNow selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
* **InvalidLookupReference:** Quando si effettua il provisioning di determinati attributi, ad esempio Department (Reparto) e Location (Sede) in ServiceNow, i valori devono essere già presenti in una tabella di riferimento in ServiceNow. Ad esempio, potrebbero essere presenti due sedi (Seattle, Los Angeles) e tre reparti (Vendite, Finanza, Marketing) nella tabella **nome tabella** in ServiceNow. Se si tenta di effettuare il provisioning di un utente il cui reparto è "Vendite" e la sede è "Seattle", il provisioning avrà esito positivo. Se si tenta di effettuare il provisioning di un utente con reparto "Vendite" e sede "LA", il provisioning avrà esito negativo. È necessario aggiungere la sede LA alla tabella di riferimento in ServiceNow o aggiornare l'attributo utente in Azure AD in modo che corrisponda al formato in ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Esaminare i [mapping degli attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) per identificare l'attributo corrispondente. Questo valore deve essere presente nell'utente o nel gruppo di cui si sta tentando di effettuare il provisioning. 
* Esaminare l'[API SOAP di ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) per informazioni su eventuali requisiti o limitazioni, ad esempio, il formato per specificare il codice paese per un utente
* Per impostazione predefinita, le richieste di provisioning vengono inviate a https://{nome-istanza}. servizio-ora. com/{nome-tabella}. Se è necessario un URL tenant personalizzato, è possibile specificare l'intero URL nel campo del nome dell'istanza.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Questo errore indica un problema di comunicazione con l'istanza di ServiceNow. Controllare per assicurarsi che le impostazioni seguenti siano *disabilitate* in ServiceNow:
   
   1. Selezionare **sicurezza di sistema**  >  **impostazioni di sicurezza elevata**  >  **richiedere l'autenticazione di base per le richieste di schema in ingresso**.
   2. Selezione **proprietà di sistema**  >  i**servizi Web**  >  **richiedono l'autorizzazione di base per le richieste SOAP in ingresso**.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
