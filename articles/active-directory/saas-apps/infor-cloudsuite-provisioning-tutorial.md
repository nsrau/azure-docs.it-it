---
title: 'Esercitazione: Configurare Infor CloudSuite per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Infor CloudSuite.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057543"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Esercitazione: Configurare Infor CloudSuite per il provisioning automatico degli utentiTutorial: Configure Infor CloudSuite for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Infor CloudSuite and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Infor CloudSuite.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Infor CloudSuite](https://www.infor.com/products/infor-os)
* Un account utente in Infor CloudSuite con autorizzazioni di amministratore.

## <a name="assigning-users-to-infor-cloudsuite"></a>Assegnazione di utenti a Infor CloudSuiteAssigning users to Infor CloudSuite

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Infor CloudSuite. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Infor CloudSuite seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Suggerimenti importanti per l'assegnazione di utenti a Infor CloudSuite

* È consigliabile assegnare a Infor CloudSuite un singolo utente di Azure AD per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Infor CloudSuite, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurare Infor CloudSuite per il provisioning

1. Accedi alla Console [di amministrazione di Infor CloudSuite](https://www.infor.com/customer-center). Fare clic sull'icona dell'utente e quindi passare a **Gestione utenti**.

    ![Console di amministrazione di Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Fare clic sull'icona del menu nell'angolo in alto a sinistra dello schermo. Fare clic su **Gestisci**.

    ![Infor CloudSuite Aggiungi SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Passare a **Account SCIM**.

    ![Account SCIM Difor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Aggiungere un utente amministratore facendo clic sull'icona più. Specificare una **password SCIM** e digitare la stessa password in **Conferma password**. Fare clic sull'icona della cartella per salvare la password. Verrà quindi generato un **identificatore utente** per l'utente amministratore.

    ![Utente Amministratore di Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Password di Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Identificatore infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Per generare il token di connessione, copiare **l'identificatore utente** e la **password SCIM**. Incollarli nel blocco note, separati da due punti. Codificare il valore della stringa passando a **Plug-in > strumenti MIME > Codifica Basic64**. 

    ![Identificatore infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copiare il token di connessione. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Infor CloudSuite nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Infor CloudSuite application in the Azure portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Aggiungi Infor CloudSuite dalla galleria

Prima di configurare Infor CloudSuite per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Infor CloudSuite dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Infor CloudSuite dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Infor CloudSuite from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, inserisci **Infor CloudSuite**, seleziona **Infor CloudSuite** nel pannello dei risultati, quindi fai clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Infor CloudSuite nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configurazione del provisioning automatico degli utenti in Infor CloudSuite 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Infor CloudSuite in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Infor CloudSuite , seguendo le istruzioni fornite [nell'esercitazione Single Sign-On di Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per ulteriori informazioni sull'endpoint SCIM di Infor CloudSuite, fare riferimento a [questa opzione](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Infor CloudSuite in Azure AD:To configure automatic user provisioning for Infor CloudSuite in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Infor CloudSuite**.

    ![Collegamento di Infor CloudSuite nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` amministratore immettere **l'input**in URL tenant. Immettere il valore del token di connessione recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Infor CloudSuite.Click **Test Connection** to ensure Azure AD can connect to Infor CloudSuite. Se la connessione non riesce, assicurati che l'account Infor CloudSuite disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Infor CloudSuite.**

    ![Mapping utenti di Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Infor CloudSuite nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Infor CloudSuite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Infor CloudSuite User Attributes](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Infor CloudSuite**.

    ![Mapping dei gruppi di Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Infor CloudSuite nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Infor CloudSuite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Infor CloudSuite Group Attributes](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Infor CloudSuite, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Infor CloudSuite, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Infor CloudSuite scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Infor CloudSuite.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Infor CloudSuite.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)