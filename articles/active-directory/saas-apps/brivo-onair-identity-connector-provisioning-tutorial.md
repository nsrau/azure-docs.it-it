---
title: 'Esercitazione: Configurare Brivo Onair Identity Connector per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Brivo Onair Identity Connector.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 4f230a70ae87c7c0ec9db347e7cf975c0252bb18
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998090"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Esercitazione: Configurare Brivo Onair Identity Connector per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Brivo Onair Identity Connector e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Brivo Onair Identity Connector.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di A Brivo Onair Identity Connector](https://www.brivo.com/lp/quote)
* Un account utente in Brivo Onair Identity Connector con autorizzazioni di amministratore senior.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Assegnazione di utenti a Brivo Onair Identity Connector

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Brivo Onair Identity Connector. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a Brivo Onair Identity Connector seguendo le istruzioni fornite qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Suggerimenti importanti per l'assegnazione di utenti a Brivo Onair Identity Connector

* È consigliabile assegnare un singolo utente di Azure AD a Brivo Onair Identity Connector per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Brivo Onair Identity Connector, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Configurare Brivo Onair Identity Connector per il provisioning

1. Accedere alla [console di amministrazione di Brivo Onair Identity Connector](https://acs.brivo.com/login/). Passare a **Account > Account Settings** (Impostazioni account).

   ![Console di amministrazione di Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. Fare clic sulla scheda **Azure AD**. Nella pagina dei dettagli **Azure AD** immettere di nuovo la password dell'account amministratore senior. Fare clic su **Invia**.

   ![Scheda Azure AD di Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. Fare clic sul pulsante **Copy Token** (Copia token) e salvare il valore di **Secret Token** (Token segreto). Questo valore dovrà essere immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Brivo Onair Identity Connector nel portale di Azure.

   ![Pulsante Copy Token in Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Aggiungere Brivo Onair Identity Connector dalla raccolta

Prima di configurare Brivo Onair Identity Connector per il provisioning utenti automatico con Azure AD, è necessario aggiungere Brivo Onair Identity Connector dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Brivo Onair Identity Connector dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Brivo Onair Identity Connector**, selezionare **Brivo Onair Identity Connector** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Brivo Onair Identity Connector nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Configurazione del provisioning utenti automatico in Brivo Onair Identity Connector 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Brivo Onair Identity Connector in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Brivo Onair Identity Connector in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Brivo Onair Identity Connector**.

    ![Collegamento a Brivo Onair Identity Connector nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://scim.brivo.com/ActiveDirectory/v2/` in **URL tenant**. Immettere il valore del **token di autenticazione SCIM** recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Brivo Onair Identity Connector. Se la connessione non riesce, verificare che l'account Brivo Onair Identity Connector abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Brivo Onair Identity Connector** (Sincronizza utenti di Azure Active Directory in Brivo Onair Identity Connector).

    ![Mapping utente di Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Esaminare gli attributi utente sincronizzati da Azure AD a Brivo Onair Identity Connector nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Brivo Onair Identity Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Brivo Onair Identity Connector** (Sincronizza gruppi di Azure Active Directory in Brivo Onair Identity Connector).

    ![Mapping dei gruppi di Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Brivo Onair Identity Connector nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account di gruppo in Brivo Onair Identity Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi di gruppo di Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Brivo Onair Identity Connector, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Brivo Onair Identity Connector selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Brivo Onair Identity Connector.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

