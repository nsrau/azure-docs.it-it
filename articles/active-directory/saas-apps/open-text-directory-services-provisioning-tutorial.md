---
title: 'Esercitazione: configurare i servizi directory di OpenText per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD ai servizi directory di OpenText.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: c0858c2e5b1ada866d252e45113f0f90b73caaf8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428885"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Esercitazione: configurare i servizi directory di OpenText per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire in OpenText Directory Services e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando è configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in servizi directory OpenText usando il servizio di provisioning Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti nei servizi directory di OpenText
> * Rimuovere gli utenti nei servizi directory di OpenText quando non sono più necessari per l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e i servizi di directory OpenText
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in OpenText Directory Services
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial) per OpenText Directory Services (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un'installazione di OTDS accessibile da Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e i servizi di directory OpenText](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare i servizi directory OpenText per supportare il provisioning con Azure AD

> [!NOTE]
> I passaggi seguenti si applicano a un'installazione di OpenText Directory Services. Non si applicano ai tenant OpenText CoreShare o OpenText OT2.

1. Creare un **client OAuth**riservato dedicato.
2. Impostare una durata per il **token di accesso**lungo.

      ![Durata del token di accesso](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Non specificare URL di reindirizzamento. Non sono necessarie. 
4. OTDS genererà e visualizzerà il **segreto client**. Salvare l' **ID client** e il **segreto client** in un luogo sicuro.

      ![Client Secret](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Creare una partizione per gli utenti e i gruppi da sincronizzare da Azure AD.

      ![Pagina partizione](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Concedere i diritti amministrativi al client OAuth creato nella partizione che verrà usata per la Azure AD utenti e gruppi sincronizzati.    
      * Azioni > di partizione-> modificare gli amministratori

      ![Pagina dell'amministratore](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. È necessario recuperare e configurare un token Secret in Azure AD. Per questa operazione è possibile utilizzare qualsiasi applicazione client HTTP. Di seguito sono riportati i passaggi da eseguire per recuperare usando l'applicazione dell'API spavalderia inclusa in OTDS.
      * In un Web browser passare a {OTDS URL}/otdsws/OAuth2
      * Passare a/token e fare clic sull'icona a blocchi in alto a destra. Immettere l'ID client OAuth e il segreto recuperati in precedenza rispettivamente come nome utente e password. Fare clic su Autorizza.

      ![Pulsante autorizzazione](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Selezionare **client_credentials** per il grant_type e fare clic su **Esegui**.

      ![Esegui pulsanti](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. Il token di accesso nella risposta deve essere usato nel campo **token segreto** in Azure ad.

      ![Token di accesso](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere servizi directory OpenText dalla raccolta di applicazioni Azure AD

Aggiungere servizi directory OpenText dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in servizi directory OpenText. Se in precedenza è stato configurato OpenText Directory Services for SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi ai servizi directory OpenText, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Passaggio 5. Configurare il provisioning utenti automatico per i servizi directory di OpenText 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Per configurare il provisioning utenti automatico per i servizi directory OpenText in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Dall'elenco delle applicazioni selezionare **OpenText Directory Services**.

    ![Collegamento dei servizi directory OpenText nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning automatica](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant dei servizi directory di OpenText
   * URL tenant non specifico: {OTDS URL}/scim/{partitionName}
   * URL tenant specifico: {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. Immettere il token Secret recuperato al passaggio 2. Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi ai servizi directory di OpenText. Se la connessione non riesce, verificare che l'account dei servizi directory OpenText disponga delle autorizzazioni di amministratore e riprovare.

      ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to OpenText Directory Services**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD ai servizi directory OpenText nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in OpenText Directory Services per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API dei servizi directory OpenText supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |active|Boolean|
   |displayName|string|
   |title|string|
   |emails[type eq "work"].value|string|
   |preferredLanguage|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |addresses[type eq "work"].formatted|string|
   |addresses[type eq "work"].streetAddress|string|
   |addresses[type eq "work"].locality|string|
   |addresses[type eq "work"].region|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[type eq "work"].country|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |externalId|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento| 

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to OpenText Directory Services**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD ai servizi directory OpenText nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in OpenText Directory Services per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per i servizi directory OpenText, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning nei servizi directory di OpenText scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

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
