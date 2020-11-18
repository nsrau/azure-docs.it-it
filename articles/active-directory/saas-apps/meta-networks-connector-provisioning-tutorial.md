---
title: 'Esercitazione: Configurare Meta Networks Connector per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Meta Networks Connector.
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
ms.openlocfilehash: ac41fb5ed6fd1e46719fcc39ccaf5b29375e7410
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359902"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Esercitazione: Configurare Meta Networks Connector per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Meta Networks Connector e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Meta Networks Connector.

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

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Meta Networks Connector. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a Meta Networks Connector seguendo le istruzioni fornite qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Suggerimenti importanti per l'assegnazione di utenti a Meta Networks Connector

* È consigliabile assegnare un singolo utente di Azure AD a Meta Networks Connector per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Meta Networks Connector, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Configurare Meta Networks Connector per il provisioning

1. Accedere alla [console di amministrazione di Meta Networks Connector](https://login.metanetworks.com/login/) usando il nome dell'organizzazione. Passare a **Administration > API Keys** (Amministrazione > Chiavi API).

    ![Console di amministrazione di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Fare clic sul segno più nell'angolo in alto a destra della schermata per creare una nuova **chiave API**.

    ![Icona più di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Specificare un nome di chiave API e la relativa descrizione rispettivamente in **API Key Name** e **API Key Description**.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="Screenshot della console di amministrazione di Meta Networks Connector in cui sono evidenziati i valori di API Key Name e API Key Description di Azure AD e della chiave API." border="false":::

4.  Attivare i privilegi **Write** (Scrittura) per **Groups** (Gruppi) e **Users** (Utenti).

    ![Privilegi di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Fare clic su **Aggiungi**. Copiare il valore di **SECRET** (Segreto) e salvarlo, in quanto questa è l'unica volta in cui è possibile visualizzarlo. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Meta Networks Connector nel portale di Azure.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="Screenshot di una finestra che informa gli utenti che è stata aggiunta la chiave API. È evidenziata la casella Secret, che contiene un valore indecifrabile." border="false":::

6.  Aggiungere un provider di identità (IdP) passando a **Administration > Settings > IdP > Create New** (Amministrazione > Impostazioni > IdP > Crea nuovo).

    ![Aggiunta di un IdP di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Nella pagina **IdP Configuration** (Configurazione IdP) è possibile assegnare un **nome** alla configurazione IdP e scegliere un'**icona**.

    ![Nome dell'IdP di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Icona dell'IdP di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  In **Configure SCIM** (Configura SCIM) selezionare il nome della chiave API creata nei passaggi precedenti. Fare clic su **Save**.

    ![Finestra Configure SCIM di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Passare a **Administration > Settings > IdP** (Amministrazione > Impostazioni > scheda IdP). Fare clic sul nome della configurazione IdP creata nei passaggi precedenti per visualizzare l'**ID IdP**. Questo **ID** viene aggiunto alla fine dell'**URL del tenant** quando si immette il valore nel campo **URL tenant** nella scheda Provisioning dell'applicazione Meta Networks Connector nel portale di Azure.

    ![ID dell'IdP di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Aggiungere Meta Networks Connector dalla raccolta

Prima di configurare Meta Networks Connector per il provisioning utenti automatico con Azure AD, è necessario aggiungere Meta Networks Connector dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Meta Networks Connector dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Meta Networks Connector**, selezionare **Meta Networks Connector** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Meta Networks Connector nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Configurazione del provisioning utenti automatico in Meta Networks Connector 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Meta Networks Connector in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Meta Networks Connector, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Meta Networks Connector](./metanetworksconnector-tutorial.md). Il Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Meta Networks Connector in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Meta Networks Connector**.

    ![Collegamento a Meta Networks Connector nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://api.metanetworks.com/v1/scim/<IdP ID>` in **URL tenant**. Immettere il valore del **token di autenticazione SCIM** recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Meta Networks Connector. Se la connessione non riesce, verificare che l'account Meta Networks Connector abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Meta Networks Connector** (Sincronizza utenti di Azure Active Directory in Meta Networks Connector).

    ![Mapping degli utenti di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Nella sezione **Mapping attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Meta Networks Connector. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Meta Networks Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Meta Networks Connector** (Sincronizza gruppi di Azure Active Directory in Meta Networks Connector).

    ![Mapping dei gruppi di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Nella sezione **Mapping attributi** esaminare gli attributi di gruppo che vengono sincronizzati da Azure AD a Meta Networks Connector. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con i gruppi in Meta Networks Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi di gruppo di Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Meta Networks Connector, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Meta Networks Connector selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Meta Networks Connector.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)