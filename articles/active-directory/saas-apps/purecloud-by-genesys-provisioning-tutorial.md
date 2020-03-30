---
title: 'Esercitazione: Configurare PureCloud di Genesys per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a PureCloud di Genesys.Learn how to automatically provision and de-provision user accounts from Azure AD to PureCloud by Genesys.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370675"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Esercitazione: Configurare PureCloud di Genesys per il provisioning automatico degli utentiTutorial: Configure PureCloud by Genesys for automatic user provisioning

Questa esercitazione descrive i passaggi da eseguire sia in PureCloud by Genesys che in Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Quando si configura la configurazione, Azure AD esegue automaticamente il provisioning e il provisioning di utenti e gruppi in [PureCloud by Genesys](https://www.genesys.com) usando il servizio provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in PureCloud di Genesys
> * Rimuovere gli utenti in PureCloud da Genesys quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD e PureCloud da GenesysKeep user attributes synchronized between Azure AD and PureCloud by Genesys
> * Effettuare il provisioning di gruppi e appartenenze ai gruppi in PureCloud di Genesys
> * [Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) to PureCloud di Genesys (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale). 
* [Un'organizzazione](https://help.mypurecloud.com/?p=81984)PureCloud .
* Un utente con [le autorizzazioni](https://help.mypurecloud.com/?p=24360) per creare un client Oauth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati [di cui eseguire il mapping tra Azure AD e PureCloud di Genesys](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare PureCloud di Genesys per supportare il provisioning con Azure ADConfigure PureCloud by Genesys to support provisioning with Azure AD

1. Creare un [client Oauth](https://help.mypurecloud.com/?p=188023) configurato nell'organizzazione PureCloud.
2. Generare un token [con il client oauth.](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)
3. Se si vuole eseguire automaticamente il provisioning dell'appartenenza al gruppo in PureCloud, è necessario [creare gruppi](https://help.mypurecloud.com/?p=52397) in PureCloud con un nome identico al gruppo in Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere PureCloud di Genesys dalla raccolta di applicazioni di Azure ADAdd PureCloud by Genesys from the Azure AD application gallery

Aggiungere PureCloud di Genesys dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in PureCloud di Genesys.Add PureCloud by Genesys from the Azure AD application gallery to start managing provisioning to PureCloud by Genesys. Se in precedenza è stato configurato PureCloud da Genesys per SSO è possibile utilizzare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando assegnate utenti e gruppi a PureCloud da Genesys, dovete selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Passaggio 5. Configurare il provisioning automatico degli utenti su PureCloud di Genesys 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per PureCloud di Genesys in Azure AD:To configure automatic user provisioning for PureCloud by Genesys in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **PureCloud by Genesys**.

    ![Collegamento di PureCloud by Genesys nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL dell'API PureCloud by Genesys e il token Oauth rispettivamente nei campi **URL tenant** e **Token segreto.** L'URL dell'API `{{API Url}}/api/v2/scim/v2`sarà strutturato come , utilizzando l'URL dell'API per l'area PureCloud dal [Centro per sviluppatori PureCloud](https://developer.mypurecloud.com/api/rest/index.html). Fare clic su Test connessione per verificare che Azure AD possa connettersi a PureCloud tramite Genesys.Click **Test Connection** to ensure Azure AD can connect to PureCloud by Genesys. Se la connessione non riesce, assicurati che il tuo account PureCloud by Genesys disponga delle autorizzazioni di amministratore e riprova.

    ![provisioning](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di Azure Active Directory con **PureCloud di Genesys**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a PureCloud da Genesys nella sezione **Mapping degli** attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in PureCloud da Genesys per le operazioni di aggiornamento. Se si sceglie di modificare [l'attributo target corrispondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)è necessario assicurarsi che l'API PureCloud by Genesys supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

     |Attributo|Type|
     |---|---|
     |userName|string|
     |active|Boolean|
     |displayName|string|
     |emails[type eq "work"].value|string|
     |title|string|
     |phoneNumbers[type eq "mobile"].value|string|
     |phoneNumbers[type eq "work"].value|string|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Riferimento|

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di Azure Active Directory con **PureCloud di Genesys**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a PureCloud da Genesys nella sezione **Mapping degli** attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in PureCloud di Genesys per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche. PureCloud by Genesys non supporta la creazione o l'eliminazione di gruppi e supporta solo l'aggiornamento dei gruppi.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per PureCloud di Genesys, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for PureCloud by Genesys, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in PureCloud da Genesys scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

* Utilizzare i registri di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno
* Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la sua vicinanza al completamento
* Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
