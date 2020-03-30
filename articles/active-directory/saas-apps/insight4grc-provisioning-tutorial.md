---
title: 'Esercitazione: Configurare Insight4GRC per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Insight4GRC.Learn how to automatically provision and de-provision user accounts from Azure AD to Insight4GRC.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0eab8a0-571b-4609-96b1-bdbc761a25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2020
ms.author: Zhchia
ms.openlocfilehash: 1404854e054c8fc4967ba863486969b8a87db526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621520"
---
# <a name="tutorial-configure-insight4grc-for-automatic-user-provisioning"></a>Esercitazione: Configurare Insight4GRC per il provisioning automatico degli utentiTutorial: Configure Insight4GRC for automatic user provisioning

Questa esercitazione descrive i passaggi da eseguire in Insight4GRC e Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Quando si configura la configurazione, Azure AD esegue automaticamente il provisioning e il provisioning di utenti e gruppi in [Insight4GRC](https://www.rsmuk.com/) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Insight4GRC
> * Rimuovere gli utenti in Insight4GRC quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD e Insight4GRC
> * Effettuare il provisioning di gruppi e appartenenze ai gruppi in Insight4GRC
> * [Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial) a Insight4GRC (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale). 
* Un account utente in Insight4GRC con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati [di cui eseguire il mapping tra Azure AD e Insight4GRC](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-insight4grc-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare Insight4GRC per supportare il provisioning con Azure ADConfigure Insight4GRC to support provisioning with Azure AD

Prima di configurare Insight4GRC per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in Insight4GRC.

1. Per ottenere il token di connessione, il cliente finale deve contattare il team di [supporto.](mailto:support.ss@rsmuk.com)
2. Per ottenere l'URL dell'endpoint SCIM, è necessario che il nome di dominio Insight4GRC sia pronto in quanto verrà utilizzato per creare l'URL dell'endpoint SCIM. È possibile recuperare il nome di dominio Insight4GRC come parte dell'acquisto iniziale del software con Insight4GRC.

## <a name="step-3-add-insight4grc-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Insight4GRC dalla raccolta di applicazioni di Azure ADAdd Insight4GRC from the Azure AD application gallery

Aggiungere Insight4GRC dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Insight4GRC.Add Insight4GRC from the Azure AD application gallery to start managing provisioning to Insight4GRC. Se in precedenza è stato configurato Insight4GRC per SSO, è possibile utilizzare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando si assegnano utenti e gruppi a Insight4GRC, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-insight4grc"></a>Passaggio 5. Configurare il provisioning automatico degli utenti in Insight4GRC 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Insight4GRC in Azure AD:To configure automatic user provisioning for Insight4GRC in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Insight4GRC**.

    ![Collegamento di Insight4GRC nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL dell'endpoint SCIM in **URL tenant.** L'URL dell'enpoint `https://<Insight4GRC Domain Name>.insight4grc.com/public/api/scim/v2 ` deve essere nel formato in cui **Nome dominio Insight4GRC** è il valore recuperato nei passaggi precedenti. Immettere il valore del token di connessione recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Insight4GRC.Click **Test Connection** to ensure Azure AD can connect to Insight4GRC. Se la connessione non riesce, verificare che l'account Insight4GRC disponga delle autorizzazioni di amministratore e riprovare.

    ![provisioning](./media/insight4grc-provisioning-tutorial/provisioning.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di Azure Active Directory con **Insight4GRC.**

9. Esaminare gli attributi utente sincronizzati da Azure AD a Insight4GRC nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con gli account utente in Insight4GRC per le operazioni di aggiornamento. Se si sceglie di modificare l'attributo di [destinazione corrispondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)è necessario assicurarsi che l'API Insight4GRC supporti il filtro degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |externalId|string|
   |active|Boolean|
   |title|string|
   |name.givenName|string|
   |name.familyName|string|
   |emails[type eq "work"].value|string|
   |phoneNumbers[type eq "work"].value|string|

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Insight4GRC.**

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Insight4GRC nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con i gruppi in Insight4GRC per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Riferimento|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Insight4GRC, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Insight4GRC, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Insight4GRC scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

* Utilizzare i log di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno.
* Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la scadenza al completamento.
* Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Scopri come esaminare i log e ottenere report sulle attività](../app-provisioning/check-status-user-account-provisioning.md)di provisioning.
