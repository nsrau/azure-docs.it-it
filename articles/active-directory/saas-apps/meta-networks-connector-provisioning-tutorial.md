---
title: 'Esercitazione: configurare il connettore meta Networks per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente nel connettore meta Networks.
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
ms.openlocfilehash: 40927597031205b5fb1bcc5869922bcc4f3f265c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518733"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Esercitazione: configurare il connettore meta Networks per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire nel connettore meta reti e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi nel connettore meta Networks.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant del connettore meta Networks](https://www.metanetworks.com/)
* Un account utente nel connettore meta Networks con autorizzazioni di amministratore.

## <a name="assigning-users-to-meta-networks-connector"></a>Assegnazione di utenti al connettore meta Networks

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere al connettore meta Networks. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi al connettore meta Network seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Suggerimenti importanti per l'assegnazione di utenti al connettore meta Networks

* È consigliabile assegnare un singolo utente Azure AD al connettore meta Networks per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a un connettore meta Networks, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Configurare il connettore meta Networks per il provisioning

1. Accedere alla console di [amministrazione del connettore meta Networks](https://login.metanetworks.com/login/) usando il nome dell'organizzazione. Passare ad **amministrazione > chiavi API**.

    ![Console di amministrazione del connettore meta Networks](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Fare clic sul segno più sul lato superiore destro della schermata per creare una nuova **chiave API**.

    ![Icona del connettore meta Networks Plus](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Impostare il **nome della chiave API** e la **Descrizione della chiave API**.

    ![Connettore di creazione del token per meta Networks](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Attivare i privilegi di **scrittura** per **gruppi** e **utenti**.

    ![Privilegi del connettore meta Networks](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Fare clic su **Aggiungi**. Copiare il **segreto** e salvarlo perché questo sarà l'unico momento in cui è possibile visualizzarlo. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione connettore meta reti nel portale di Azure.

    ![Connettore di creazione del token per meta Networks](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Per aggiungere un IdP, passare ad **amministrazione > impostazioni > IdP > Crea nuovo**.

    ![Connettore meta Networks Add IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Nella pagina **configurazione IDP** è possibile **assegnare un nome** alla configurazione IDP e scegliere un' **icona**.

    ![Nome IdP connettore meta reti](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Icona IdP connettore meta reti](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  In **Configure scim** selezionare il nome della chiave API creata nei passaggi precedenti. Fare clic su **Save**.

    ![Connettore meta Networks Configure SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Passare ad **amministrazione > impostazioni > scheda IDP**. Fare clic sul nome della configurazione IdP creata nei passaggi precedenti per visualizzare l' **ID IDP**. Questo **ID** viene aggiunto alla fine dell' **URL del tenant** immettendo il valore nel campo **URL tenant** nella scheda provisioning dell'applicazione del connettore meta reti nel portale di Azure.

    ![ID IdP connettore meta Networks](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Aggiungere il connettore meta Networks dalla raccolta

Prima di configurare il connettore meta Networks per il provisioning utenti automatico con Azure AD, è necessario aggiungere il connettore meta Networks dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere il connettore meta Networks dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **connettore meta Networks**, selezionare **connettore meta Networks** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Meta Networks Connector nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Configurazione del provisioning utenti automatico nel connettore meta reti 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi nel connettore di meta Networks in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per il connettore meta Networks seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on per il connettore meta Networks](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Per configurare il provisioning utenti automatico per il connettore meta Networks in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Meta Networks Connector**.

    ![Collegamento a Meta Networks Connector nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://api.metanetworks.com/v1/scim/<IdP ID>` in **URL tenant**. Immettere il valore del **token di autenticazione scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi al connettore meta Networks. Se la connessione non riesce, verificare che l'account connettore meta Networks disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a connettore meta Networks**.

    ![Mapping utente del connettore meta Networks](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente che vengono sincronizzati dal Azure AD al connettore meta reti nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente nel connettore meta Networks per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente del connettore meta Networks](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to meta Networks Connector**.

    ![Mapping del gruppo di connettori meta Networks](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati dal Azure AD al connettore meta reti nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi nel connettore meta reti per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo di connettori meta Networks](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per il connettore meta Networks, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning nel connettore meta reti scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning sul connettore meta Networks.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

