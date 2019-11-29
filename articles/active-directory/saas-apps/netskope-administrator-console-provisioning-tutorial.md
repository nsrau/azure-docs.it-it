---
title: 'Esercitazione: configurare Console di amministrazione Netskope per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Netskope Console di amministrazione.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: f768fa141a0d3bee5a0bb6440768fa2778f6d3bd
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74565911"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Esercitazione: configurare Console di amministrazione Netskope per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Netskope Console di amministrazione e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Netskope Console di amministrazione.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di Netskope Console di amministrazione](https://www.netskope.com/)
* Un account utente in Netskope Console di amministrazione con autorizzazioni di amministratore.

## <a name="assigning-users-to-netskope-administrator-console"></a>Assegnazione di utenti a Netskope Console di amministrazione

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Netskope Console di amministrazione. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a Netskope Console di amministrazione seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Suggerimenti importanti per l'assegnazione di utenti a Netskope Console di amministrazione

* È consigliabile assegnare un singolo utente Azure AD a Netskope Console di amministrazione per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Netskope Console di amministrazione, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Configurare Console di amministrazione Netskope per il provisioning

1. Accedere alla console di [amministrazione di Netskope console di amministrazione](https://netskope.goskope.com/). Passare a **Home > impostazioni**.

    ![Console di amministrazione Console di amministrazione Netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Passare a **strumenti**. Nel menu **strumenti** passare a **strumenti di directory > integrazione scim**.

    ![Strumenti Console di amministrazione Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Console di amministrazione aggiungere SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Scorrere verso il basso e fare clic sul pulsante **Aggiungi token** . Nella finestra di dialogo **Aggiungi nome client OAuth** specificare un **nome client** e fare clic sul pulsante **Salva** .

    ![Netskope Console di amministrazione aggiungere un token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Console di amministrazione nome CLient](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Copiare l' **URL del server scim** e il **token**. Questi valori verranno immessi rispettivamente nei campi URL tenant e token Secret nella scheda provisioning dell'applicazione Netskope Console di amministrazione nel portale di Azure.

    ![Netskope Console di amministrazione creare un token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Aggiungere Netskope Console di amministrazione dalla raccolta

Prima di configurare Netskope Console di amministrazione per il provisioning utenti automatico con Azure AD, è necessario aggiungere Console di amministrazione Netskope dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Netskope Console di amministrazione dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Netskope console di amministrazione**, selezionare **Netskope console di amministrazione** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Console di amministrazione Netskope nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Configurazione del provisioning utenti automatico in Netskope Console di amministrazione 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Netskope Console di amministrazione in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per Netskope Console di amministrazione seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di Netskope console di amministrazione](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di Netskope Console di amministrazione, vedere [questo](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)articolo.

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Netskope Console di amministrazione in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Netskope Administrator Console**.

    ![Collegamento del Console di amministrazione di Netskope nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** , immettere il valore dell' **URL del server scim** recuperato in precedenza nell' **URL del tenant**. Immettere il valore del **token** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Netskope console di amministrazione. Se la connessione non riesce, verificare che l'account Netskope Console di amministrazione disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Netskope console di amministrazione**.

    ![Netskope Console di amministrazione mapping utente](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Netskope Console di amministrazione nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Netskope console di amministrazione per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Netskope Console di amministrazione attributi utente](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Netskope console di amministrazione**.

    ![Netskope mapping dei gruppi di Console di amministrazione](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Netskope Console di amministrazione nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Netskope console di amministrazione per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppo Console di amministrazione Netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Netskope Console di amministrazione, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Netskope Console di amministrazione scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad in Netskope console di amministrazione.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

