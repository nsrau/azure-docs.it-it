---
title: 'Esercitazione: Configurare Brivo Onair Identity Connector per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Brivo Onair Identity Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: f3c1e7337c0ce07b7fbebb9f954deeb75f0b9584
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246654"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Esercitazione: Configurare Brivo Onair Identity Connector per il provisioning automatico degli utentiTutorial: Configure Brivo Onair Identity Connector for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Brivo Onair Identity Connector e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Brivo Onair Identity Connector.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Brivo Onair Identity Connector](https://www.brivo.com/lp/quote)
* Un account utente in Brivo Onair Identity Connector con autorizzazioni di amministratore senior.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Assegnazione di utenti a Brivo Onair Identity Connector

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Brivo Onair Identity Connector. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Brivo Onair Identity Connector seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Suggerimenti importanti per l'assegnazione di utenti a Brivo Onair Identity Connector

* È consigliabile assegnare un singolo utente di Azure AD a Brivo Onair Identity Connector per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Brivo Onair Identity Connector, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Installazione di Brivo Onair Identity Connector per il provisioning

1.    Accedere alla [Console di amministrazione Brivo Onair Identity Connector](https://acs.brivo.com/login/). Passare a **Impostazioni account > account**.

    ![Console di amministrazione Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  Fare clic sulla scheda **Azure AD.** Nella pagina dei dettagli di **Azure AD** immettere nuovamente la password dell'account amministratore senior. Fare clic su **Invia**.

    ![Brivo Onair Identity Connector azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.    Fare clic sul pulsante **Copia token** e salvare il **token segreto**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Brivo Onair Identity Connector nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Brivo Onair Identity Connector application in the Azure portal.

    ![Token Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Aggiungere Brivo Onair Identity Connector dalla galleria

Prima di configurare Brivo Onair Identity Connector per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Brivo Onair Identity Connector dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Brivo Onair Identity Connector dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Brivo Onair Identity Connector from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Brivo Onair Identity Connector**, selezionare **Brivo Onair Identity Connector** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Brivo Onair Identity Connector nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Configurazione del provisioning automatico degli utenti su Brivo Onair Identity Connector 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Brivo Onair Identity Connector in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Brivo Onair Identity Connector in Azure AD:To configure automatic user provisioning for Brivo Onair Identity Connector in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Brivo Onair Identity Connector**.

    ![Il collegamento Brivo Onair Identity Connector nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://scim.brivo.com/ActiveDirectory/v2/` amministratore immettere **l'input**in URL tenant. Immettere il valore del token di **autenticazione SCIM** recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Brivo Onair Identity Connector.Click **Test Connection** to ensure Azure AD can connect to Brivo Onair Identity Connector. Se la connessione non riesce, verificare che l'account Brivo Onair Identity Connector disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Brivo Onair Identity Connector**.

    ![Mapping utente di Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Esaminare gli attributi utente sincronizzati da Azure AD a Brivo Onair Identity Connector nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Brivo Onair Identity Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Brivo Onair Identity Connector**.

    ![Mapping del gruppo Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Brivo Onair Identity Connector nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Brivo Onair Identity Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Brivo Onair Identity Connector, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Brivo Onair Identity Connector, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Brivo Onair Identity Connector scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Brivo Onair Identity Connector.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Brivo Onair Identity Connector.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

