---
title: 'Esercitazione: Configurare SmartFile per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in SmartFile.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to SmartFile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b113cc27195b2ce954d677ab0f1ec83e394946be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060230"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Esercitazione: Configurare SmartFile per il provisioning automatico degli utentiTutorial: Configure SmartFile for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in SmartFile and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to SmartFile.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant SmartFile](https://www.SmartFile.com/pricing/).
* Un account utente in SmartFile con autorizzazioni di amministratore.

## <a name="assigning-users-to-smartfile"></a>Assegnazione di utenti a SmartFile

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a SmartFile. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a SmartFile seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Suggerimenti importanti per l'assegnazione di utenti a SmartFile

* È consigliabile assegnare un singolo utente di Azure AD a SmartFile per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a SmartFile, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-smartfile-for-provisioning"></a>Configurazione di SmartFile per il provisioning

Prima di configurare SmartFile per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in SmartFile e raccogliere ulteriori dettagli necessari.

1. Accedi alla SmartFile Admin Console. Accedi all'angolo in alto a destra della SmartFile Admin Console. Selezionare **il codice Product Key**.

    ![Console di amministrazione SmartFile](media/smartfile-provisioning-tutorial/login.png)

2. Per generare un token di connessione, copiare il **codice Product Key** e la password del **prodotto**. Incollarli in un blocco note con i due punti tra di loro.
    
     ![SmartFile Aggiungi SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![SmartFile Aggiungi SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Aggiungere SmartFile dalla raccolta

Per configurare SmartFile per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere SmartFile dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere SmartFile dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add SmartFile from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **SmartFile**, selezionare **SmartFile** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SmartFile nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Configurazione del provisioning automatico degli utenti in SmartFile 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in SmartFile in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per SmartFile , seguendo le istruzioni fornite [nell'esercitazione sul single sign-on SmartFile](SmartFile-tutorial.md). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per SmartFile in Azure AD:To configure automatic user provisioning for SmartFile in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SmartFile**.

    ![Collegamento di SmartFile nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5.  Nella sezione **Credenziali di** `https://<SmartFile sitename>.smartfile.com/ftp/scim` amministratore immettere **l'input**in URL tenant. Un esempio sarebbe `https://demo1test.smartfile.com/ftp/scim`simile a . Immettere il valore del **token Bearer** (ProductKey:ProductPassword) recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a SmartFile.Click **Test Connection** to ensure Azure AD can connect to SmartFile. Se la connessione non riesce, assicurati che l'account SmartFile disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con SmartFile**.

    ![Mapping utente SmartFile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a SmartFile nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in SmartFile per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente SmartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con SmartFile**.

    ![Mapping dei gruppi SmartFile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a SmartFile nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in SmartFile per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo SmartFile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per SmartFile, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for SmartFile, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in SmartFile scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in SmartFile.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on SmartFile.

    Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utenteFor](../app-provisioning/check-status-user-account-provisioning.md) more information on how to read the Azure AD provisioning logs, see Reporting on automatic user account provisioning
    
## <a name="connector-limitations"></a>Limitazioni dei connettori

* SmartFile supporta solo eliminazioni rigide. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

 [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
