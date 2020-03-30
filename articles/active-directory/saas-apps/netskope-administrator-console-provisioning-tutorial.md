---
title: 'Esercitazione: Configurare Netskope Administrator Console per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente nella console di amministrazione netskope.
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
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061285"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Esercitazione: Configurare Netskope Administrator Console per il provisioning automatico degli utentiTutorial: Configure Netskope Administrator Console for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Netskope Administrator Console e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi nella console di amministrazione di Netskope.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant della console di amministrazione Netskope](https://www.netskope.com/)
* Un account utente nella Console di amministrazione Netskope con autorizzazioni di amministratore.

## <a name="assigning-users-to-netskope-administrator-console"></a>Assegnazione di utenti a Netskope Administrator Console

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere alla console di amministrazione di Netskope.Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Netskope Administrator Console. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Netskope Administrator Console seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Suggerimenti importanti per l'assegnazione di utenti a Netskope Administrator Console

* È consigliabile assegnare un singolo utente di Azure AD alla console di amministrazione di Netskope per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Netskope Administrator Console, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Configurare Netskope Administrator Console per il provisioning

1. Accedi alla [Console di amministrazione di Netskope Administrator Console](https://netskope.goskope.com/). Passare a **Impostazioni > Home**.

    ![Console di amministrazione Netskope Admin Console](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Passare a **Strumenti**. Nel menu **Strumenti** passare a **Strumenti directory > SCIM INTEGRATION**.

    ![Strumenti della console di amministrazione Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Administrator Console Aggiungi SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Scorrere verso il basso e fare clic sul pulsante **Aggiungi token.** Nella finestra di dialogo **Aggiungi nome client OAuth** specificare un **NOME CLIENT** e fare clic sul pulsante **Salva.**

    ![Netskope Administrator Console Aggiungi token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Administrator Console Nome CLient](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Copiare l'URL del **server SCIM** e il **file TOKEN**. Questi valori verranno immessi rispettivamente nei campi URL tenant e Token segreto rispettivamente nella scheda Provisioning dell'applicazione Netskope Administrator Console nel portale di Azure.These values will be entered in the Tenant URL and Secret Token fields respectively in the Provisioning tab of your Netskope Administrator Console application in the Azure portal.

    ![Netskope Administrator Console Crea token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Aggiungere Netskope Administrator Console dalla raccolta

Prima di configurare Netskope Administrator Console per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere la console di amministrazione di Netskope dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere la console di amministrazione Di Netskope dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Netskope Administrator Console from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, **immettere Netskope Administrator Console**, selezionare **Netskope Administrator Console** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Netskope Administrator Console nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Configurazione del provisioning automatico degli utenti nella console di amministrazione di Netskope 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi nella console di amministrazione Netskope in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Netskope Administrator Console seguendo le istruzioni fornite [nell'esercitazione Single Sign-On di Netskope Administrator Console](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

> [!NOTE]
> Per ulteriori informazioni sull'endpoint SCIM di Netskope Administrator Console, fare riferimento a [questa opzione](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Netskope Administrator Console in Azure AD:To configure automatic user provisioning for Netskope Administrator Console in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Netskope Administrator Console**.

    ![Il collegamento Netskope Administrator Console nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere il valore URL del server **SCIM** recuperato in precedenza in **URL tenant**. Immettere il valore **TOKEN** recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi alla console di amministrazione di Netskope.Click **Test Connection** to ensure Azure AD can connect to Netskope Administrator Console. Se la connessione non riesce, verificare che l'account Netskope Administrator Console disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di Azure Active Directory con **Netskope Administrator Console**.

    ![Mapping utenti della Console di amministrazione Netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Netskope Administrator Console nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente nella Console di amministrazione Netskope per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente della console di amministrazione Netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di Azure Active Directory con **Netskope Administrator Console**.

    ![Mapping dei gruppi di Netskope Administrator Console](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Netskope Administrator Console nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Netskope Administrator Console per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Netskope Administrator Console](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Netskope Administrator Console, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Netskope Administrator Console, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning nella console di amministrazione di Netskope scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD nella console di amministrazione di Netskope.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Netskope Administrator Console.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

