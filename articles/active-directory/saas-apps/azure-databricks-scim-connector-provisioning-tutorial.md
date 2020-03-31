---
title: 'Esercitazione: Configurare il connettore SCIM di Azure Databricks per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come eseguire automaticamente il provisioning e il provisioning degli account utente da Azure AD ad Azure Databricks SCIM Connector.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370529"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Esercitazione: Configurare il connettore SCIM di Azure Databricks per il provisioning automatico degli utentiTutorial: Configure Azure Databricks SCIM Connector for automatic user provisioning

Questa esercitazione descrive i passaggi da eseguire in Azure Databricks SCIM Connector e Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Quando si configura la configurazione, Azure AD esegue automaticamente il provisioning di utenti e gruppi in [Azure Databricks SCIM Connector](https://databricks.com/) usando il servizio Provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Azure Databricks SCIM ConnectorCreate users in Azure Databricks SCIM Connector
> * Rimuovere gli utenti in Azure Databricks SCIM Connector quando non richiedono più l'accessoRemove users in Azure Databricks SCIM Connector when they do not require access asat more
> * Mantenere sincronizzati gli attributi utente tra Azure AD e Azure Databricks SCIM Connector
> * Effettuare il provisioning di gruppi e appartenenze ai gruppi in Azure Databricks SCIM Connector

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale). 
* Un account Azure Databricks con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati [di cui eseguire il mapping tra Azure AD e Azure Databricks SCIM Connector](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare il connettore SCIM di Azure Databricks per supportare il provisioning con Azure ADConfigure Azure Databricks SCIM Connector to support provisioning with Azure ADConfigure Azure Databricks SCIM Connector to support

1. Per configurare il provisioning SCIM di Azure Databricks, aggiungerlo come risorsa nel tenant di Azure Active Directory e configurarlo usando le impostazioni seguenti.

    ![Configurazione di Azure DatabricksAzure Databricks setup](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Per generare un token di accesso personale in Azure Databricks fare riferimento a [questo](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management).

3. Copiare il **token**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Connettore SCIM di Azure Databricks nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Azure Databricks SCIM Connector application in the Azure portal.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere il connettore SCIM di Azure Databricks dalla raccolta di applicazioni di Azure ADAdd Azure Databricks SCIM Connector from the Azure AD application gallery

Aggiungere il connettore SCIM di Azure Databricks dalla raccolta di applicazioni di Azure AD per avviare la gestione del provisioning in Azure Databricks SCIM Connector.Add Azure Databricks SCIM Connector from the Azure AD application gallery to start managing provisioning to Azure Databricks SCIM Connector. Se in precedenza è stato configurato il connettore SCIM di Azure Databricks per SSO, è possibile usare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando si assegnano utenti e gruppi ad Azure Databricks SCIM Connector, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Passaggio 5. Configurare il provisioning automatico degli utenti nel connettore SCIM di Azure DatabricksConfigure automatic user provisioning to Azure Databricks SCIM Connector 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di Azure Databricks, fare riferimento a [questa](https://docs.databricks.com/dev-tools/api/latest/scim.html
)opzione .

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per il connettore SCIM di Azure Databricks in Azure AD:To configure automatic user provisioning for Azure Databricks SCIM Connector in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Connettore SCIM di Azure Databricks**.

    ![Collegamento Azure Databricks SCIM Connector nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere il valore dell'endpoint SCIM in **URL tenant.** L'URL del tenant `https://<region>.azuredatabricks.net/api/2.0/preview/scim` deve essere nel formato in cui è disponibile l'area nell'URL della home page di Azure Databricks.The tenant URL should be in the format where the **region** can be found in your Azure Databricks home page URL. Ad esempio, un endpoint SCIM per `https://westus.azuredatabricks.net/api/2.0/preview/scim`l'area **westus** sarà . Immettere il valore del token recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi ad Azure Databricks SCIM Connector.Click **Test Connection** to ensure Azure AD can connect to Azure Databricks SCIM Connector. Se la connessione non riesce, verificare che l'account del connettore SCIM di Azure Databricks disponga delle autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di Azure Active Directory con **il connettore SCIM di Azure Databricks.**

9. Esaminare gli attributi utente sincronizzati da Azure AD ad Azure Databricks SCIM Connector nella sezione **Attribute-Mapping.** Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare una corrispondenza con gli account utente in Azure Databricks SCIM Connector per le operazioni di aggiornamento. Se si sceglie di modificare l'attributo di [destinazione corrispondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)è necessario assicurarsi che l'API Del connettore SCIM di Azure Databricks supporti il filtro degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |displayName|string|
   |active|Boolean|

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di Azure Active Directory con **il connettore SCIM di Azure Databricks.**

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD ad Azure Databricks SCIM Connector nella sezione **Attribute-Mapping.** Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare una corrispondenza con i gruppi in Azure Databricks SCIM Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

     |Attributo|Type|
     |---|---|
     |displayName|string|
     |Membri di|Riferimento|

11. Nella sezione **Mapping** selezionare Sincronizza gruppi di Azure Active Directory con **il connettore SCIM di Azure Databricks.**

12. Per abilitare il servizio di provisioning di Azure AD per il connettore SCIM di Azure Databricks, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Azure Databricks SCIM Connector, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Azure Databricks SCIM Connector scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

* Utilizzare i registri di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno
* Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la sua vicinanza al completamento
* Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
* Databricks converte sempre i loro valori di nome utente in minuscolo quando si salva nella loro directory indipendentemente dalle maiuscole che inviamo loro tramite SCIM.
* Attualmente le richieste GET per l'API SCIM di Azure Databricks per gli utenti fanno distinzione tra maiuscole e minuscole, pertanto se si esegue una query per USER@contoso.com esso verranno visualizzati 0 risultati come vengono archiviati come user@contoso.com.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
