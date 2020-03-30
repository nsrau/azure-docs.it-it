---
title: 'Esercitazione: Configurare il programma di installazione per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a .Apier.Learn how to automatically provision and de-provision user accounts from Azure AD to .apier.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 66e224f9-2311-4564-bb84-99fce59a398f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: Zhchia
ms.openlocfilehash: 4091e4fd544dbc6450bc14bd0e0731c4d3024592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76992157"
---
# <a name="tutorial-configure-zapier-for-automatic-user-provisioning"></a>Esercitazione: Configurare la configurazione per il provisioning automatico degli utentiTutorial: Configure apier for automatic user provisioning

Questa esercitazione descrive i passaggi che è necessario eseguire sia in Azure Active Directory (Azure AD) per configurare il provisioning automatico degli utenti. Una volta configurato, Azure AD esegue automaticamente il provisioning e il provisioning di utenti e gruppi a [,apier](https://zapier.com/pricing) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in .apier
> * Rimuovere gli utenti in .apier quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD e .apier
> * Effettuare il provisioning di gruppi e appartenenze ai gruppi in .apier
> * Single Sign-On per la funzionalità di accesso a a zpier (consigliato)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning (ad esempio Amministratore applicazioni, Amministratore applicazione cloud, Proprietario applicazione o Amministratore globale). 
* Un account utente in .apier con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul funzionamento del servizio di [provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinare chi riverrà [nell'ambito del provisioning.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determinare i dati di cui eseguire [il mapping tra Azure AD e .](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-zapier-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare il supporto del provisioning con Azure ADConfigure apier to support provisioning with Azure AD

1. Accedere alla [Console di amministrazione di ](https://zapier.com/app/login/). Passare a **Impostazioni** sotto l'ID tenant.

    ![Console di amministrazione di .apier](media/zapier-provisioning-tutorial/admin.png)

2. In **IMPOSTAZIONI COMPANY**, selezionare Provisioning **utenti**.

    ![Aggiungere SCIM](media/zapier-provisioning-tutorial/user.png)

3. Copiare **l'URL di base SCIM** e il token di **bearer SCIM**. Questi valori verranno immessi rispettivamente nei campi URL tenant e Token segreto rispettivamente nella scheda Provisioning dell'applicazione .apier nel portale di Azure.

    ![Gettone Per la creazione di zapier](media/zapier-provisioning-tutorial/token.png)

## <a name="step-3-add-zapier-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere a .apier dalla raccolta di applicazioni di Azure ADAdd apier from the Azure AD application gallery

Per iniziare a gestire il provisioning in .apier, aggiungere .apier dalla raccolta di applicazioni di Azure AD. Se in precedenza è stato configurato il programma di installazione di SSO per SSO, è possibile utilizzare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata durante il test iniziale dell'integrazione. Ulteriori informazioni sull'aggiunta di un'applicazione dalla raccolta [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi riverrà nell'ambito del provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito degli utenti in base all'assegnazione all'applicazione e o in base agli attributi dell'utente/gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning nell'app in base all'assegnazione, è possibile usare la [procedura](../manage-apps/assign-user-or-group-access-portal.md) seguente per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning in base esclusivamente agli attributi dell'utente o del gruppo, è possibile utilizzare un filtro di ambito come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* Quando si assegnano utenti e gruppi a ,apier, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non effettivamente autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Inizia in piccolo. Esegui test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-zapier"></a>Passaggio 5. Configurare il provisioning automatico degli utenti su .apier 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zapier-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per la funzionalità di Azure AD:To configure automatic user provisioning for apier in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

   ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **.**

   ![Il collegamento .apier nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

   ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

   ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di amministratore,** immettere l'URL e il **token segreto**del **tenant** di zpier . Fare clic su **Test connessione** per verificare che Azure AD sia in grado di connettersi a .apier. Se la connessione non riesce, assicurati che il tuo account zopier disponga delle autorizzazioni di amministratore e riprova.

   ![provisioning](./media/zapier-provisioning-tutorial/provisioning.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

   ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza gli utenti di Azure Active Directory con il nome **di Azure .**

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a appier nella sezione **mapping degli** attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in .apier per le operazioni di aggiornamento. Se si sceglie di modificare l'attributo di [destinazione corrispondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)è necessario assicurarsi che l'API di protezione supporti il filtro degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Variabile|Type|
   |---|---|
   |userName|string|
   |active|Boolean|
   |externalId|string|
   |name.givenName|string|
   |name.familyName|string|
   |emails[type eq "work"].value|string|

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di Azure Active Directory con **.apier.**

11. Esaminare gli attributi di gruppo che vengono sincronizzati da Azure AD a appier nella sezione **mapping degli** attributi. Gli attributi selezionati come Proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con i gruppi in apier per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    |Variabile|Type|
    |---|---|
    |displayName|string|
    |Membri di|Riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per la barra, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for apier, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning su .apier scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione del ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:Once you've configured provisioning, use the following resources to monitor your deployment:

- Utilizzare i registri di [provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare quali utenti sono stati sottoposti a provisioning con successo o meno
- Controllare la barra di [avanzamento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e la sua vicinanza al completamento
- Se la configurazione di provisioning sembra essere in uno stato non integro, l'applicazione andrà in quarantena. Ulteriori informazioni sugli stati di quarantena [sono disponibili qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
