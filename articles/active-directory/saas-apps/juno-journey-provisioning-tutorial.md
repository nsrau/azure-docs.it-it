---
title: 'Esercitazione: Configurare Juno Journey per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Juno Journey.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 08de07a52d1e43dea91e6684d33027d8bcad61fc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641849"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Esercitazione: Configurare Juno Journey per il provisioning automatico degli utentiTutorial: Configure Juno Journey for automatic user provisioning

Questa esercitazione descrive i passaggi da eseguire in Juno Journey e Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Quando si configura la configurazione, Azure AD esegue automaticamente il provisioning di utenti e gruppi in [Juno Journey](https://www.junojourney.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Juno Journey
> * Rimuovere gli utenti in Juno Journey quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD e Juno Journey
> * [Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) per Juno Journey (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale). 
*  Un [inquilino Juno Journey](https://www.junojourney.com/getstartedwithjuno).
*  Un account utente in Juno Journey con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati [di cui eseguire il mapping tra Azure AD e Juno Journey](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare Juno Journey per supportare il provisioning con Azure ADConfigure Juno Journey to support provisioning with Azure AD

1. Per **il token segreto** e **l'URL tenant,** contattare il team di supporto Juno Journey all'indirizzo support@the-juno.com. Questo valore verrà immesso rispettivamente nei campi **Token segreto** e URL tenant rispettivamente nella scheda Provisioning dell'applicazione Juno Journey nel portale di Azure.This value will be entered in the Secret Token and **Tenant URL** fields respectively in the Provisioning tab of your Juno Journey application in the Azure portal. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Juno Journey dalla raccolta di applicazioni di Azure ADAdd Juno Journey from the Azure AD application gallery

Aggiungere Juno Journey dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Juno Journey.Add Juno Journey from the Azure AD application gallery to start managing provisioning to Juno Journey. Se in precedenza è stato configurato Juno Journey per SSO è possibile utilizzare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando si assegnano utenti e gruppi a Juno Journey, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Passaggio 5. Configurare il provisioning automatico degli utenti in Juno Journey 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Juno Journey in Azure AD:To configure automatic user provisioning for Juno Journey in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Juno Journey**.

    ![Il collegamento Juno Journey nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere il valore dell'URL tenant recuperato in precedenza in **URL tenant.** Immettere il valore del token segreto recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Juno Journey.Click **Test Connection** to ensure Azure AD can connect to Juno Journey. Se la connessione non riesce, assicurati che l'account Juno Journey disponga delle autorizzazioni di amministratore e riprova.

    ![provisioning](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Juno Journey**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a Juno Journey nella sezione **Attribute-Mapping.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Juno Journey per le operazioni di aggiornamento. Se si sceglie di modificare l'attributo di [destinazione corrispondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)è necessario assicurarsi che l'API Juno Journey supporti il filtro degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Variabile|Type|
   |---|---|
   |userName|string|
   |externalId|string|
   |displayName|string|
   |title|string|
   |active|Boolean|
   |preferredLanguage|string|
   |emails[type eq "work"].value|string|
   |addresses[tipo eq "lavoro"].country|string|
   |addresses[digitare eq "lavoro"].regione|string|
   |addresses[tipo eq "lavoro"].località|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[tipo eq "lavoro"].formato|string|
   |addresses[type eq "work"].streetAddress|string|
   |name.givenName|string|
   |name.familyName|string|
   |nome.nomeme|string|
   |nome.formattato|string|
   |phoneNumbers[type eq "fax"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "work"].value|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|string|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Juno Journey, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Juno Journey, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Juno Journey scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

* Utilizzare i registri di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno
* Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la sua vicinanza al completamento
* Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
