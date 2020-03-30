---
title: 'Esercitazione: Configurare Meta Networks Connector per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Meta Networks Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061360"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Esercitazione: Configurare Meta Networks Connector per il provisioning automatico degli utentiTutorial: Configure Meta Networks Connector for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Meta Networks Connector e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Meta Networks Connector.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Meta Networks Connector](https://www.metanetworks.com/)
* Un account utente in Meta Networks Connector con autorizzazioni di amministratore.

## <a name="assigning-users-to-meta-networks-connector"></a>Assegnazione di utenti a Meta Networks Connector

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Meta Networks Connector. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Meta Networks Connector seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Suggerimenti importanti per l'assegnazione di utenti a Meta Networks Connector

* È consigliabile assegnare un singolo utente di Azure AD a Meta Networks Connector per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Meta Networks Connector, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Installazione di Meta Networks Connector per il provisioning

1. Accedere alla [Console di amministrazione Meta Networks Connector](https://login.metanetworks.com/login/) utilizzando il nome dell'organizzazione. Passare a **Chiavi API > di amministrazione**.

    ![Console di amministrazione di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Fare clic sul segno più sul lato superiore destro dello schermo per creare una nuova **chiave API**.

    ![Icona di Meta Networks Connector plus](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Impostare il nome della **chiave API** e la descrizione della **chiave API**.

    ![Token di creazione del connettore di meta reti](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Attivare i privilegi **di scrittura** per **gruppi** e **utenti**.

    ![Privilegi di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Fare clic su **Aggiungi**. Copiare il **SEGRETo** e salvarlo come questo sarà l'unico momento in cui è possibile visualizzarlo. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Meta Networks Connector nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Meta Networks Connector application in the Azure portal.

    ![Token di creazione del connettore di meta reti](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Aggiungere un IdP passando ad **Impostazioni > amministrazione > IdP > Crea nuovo**.

    ![Meta Networks Connector Aggiungi IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Nella pagina **Configurazione IdP** è possibile assegnare un **nome** alla configurazione IdP e scegliere **un'icona**.

    ![Nome IdP connettore reti meta-reti](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Icone IdP Connettore Meta Reti](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  In **Configura SCIM** selezionare il nome della chiave API creata nei passaggi precedenti. Fare clic su **Save**.

    ![Meta Networks Connector configura SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Passare a **Impostazioni > Amministrazione > scheda IdP**. Fare clic sul nome della configurazione IdP creata nei passaggi precedenti per visualizzare **l'ID IdP**. Questo ID viene aggiunto alla fine dell'URL tenant durante l'immissione del valore nel campo URL tenant nella scheda Provisioning dell'applicazione Meta Networks Connector nel portale di Azure.This **ID** is added to the end of **Tenant URL** while entering the value in Tenant **URL** field in the Provisioning tab of your Meta Networks Connector application in the Azure portal.

    ![ID IDP connettore reti meta-reti](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Aggiungere Meta Networks Connector dalla raccolta

Prima di configurare Meta Networks Connector per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Meta Networks Connector dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Meta Networks Connector dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Meta Networks Connector from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Meta Networks Connector**, selezionare Meta Networks **Connector** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Meta Networks Connector nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Configurazione del provisioning automatico degli utenti su Meta Networks Connector 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Meta Networks Connector in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Meta Networks Connector , seguendo le istruzioni fornite [nell'esercitazione Sul single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)di Meta Networks Connector . Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Meta Networks Connector in Azure AD:To configure automatic user provisioning for Meta Networks Connector in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Meta Networks Connector**.

    ![Collegamento a Meta Networks Connector nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://api.metanetworks.com/v1/scim/<IdP ID>` amministratore immettere **l'input**in URL tenant. Immettere il valore del token di **autenticazione SCIM** recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Meta Networks Connector.Click **Test Connection** to ensure Azure AD can connect to Meta Networks Connector. Se la connessione non riesce, verificare che l'account del connettore Meta Networks disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Meta Networks Connector**.

    ![Mapping utenti di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Meta Networks Connector nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Meta Networks Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente del connettore di reti di meta reti](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Meta Networks Connector**.

    ![Mapping dei gruppi di connettori di Meta Networks](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Meta Networks Connector nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Meta Networks Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Del connettore di meta reti](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Meta Networks Connector, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Meta Networks Connector, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Meta Networks Connector scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD nel connettore metareti.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

