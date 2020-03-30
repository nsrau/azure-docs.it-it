---
title: 'Esercitazione: Configurare Proxyclick per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Proxyclick.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Proxyclick.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063357"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Esercitazione: Configurare Proxyclick per il provisioning automatico degli utentiTutorial: Configure Proxyclick for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Proxyclick e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Proxyclick.The objective of this tutorial is to demonstrate the steps to be performed in Proxyclick and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and/o provision users to Proxyclick.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Proxyclick](https://www.proxyclick.com/pricing)
* Un account utente in Proxyclick con autorizzazioni di amministratore.

## <a name="add-proxyclick-from-the-gallery"></a>Aggiungere Proxyclick dalla raccolta

Prima di configurare Proxyclick per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Proxyclick dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Proxyclick dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Proxyclick from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Proxyclick**, selezionare **Proxyclick** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Proxyclick nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Assegnazione di utenti a ProxyclickAssigning users to Proxyclick

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Proxyclick. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Proxyclick seguendo le istruzioni riportate di seguito:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Suggerimenti importanti per l'assegnazione di utenti a Proxyclick

* È consigliabile assegnare un singolo utente di Azure AD a Proxyclick per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Proxyclick, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Configurazione del provisioning automatico degli utenti su Proxyclick 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Proxyclick in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Proxyclick, seguendo le istruzioni fornite nell'esercitazione sul [single sign-on Proxyclick](proxyclick-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Proxyclick in Azure AD:To configure automatic user provisioning for Proxyclick in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Proxyclick**.

    ![Collegamento di Proxyclick nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Per recuperare **l'URL tenant** e il token **segreto** dell'account Proxyclick, seguire la procedura dettagliata descritta nel passaggio 6.

6. Accedere a [Proxyclick Admin Console](https://app.proxyclick.com/login//?destination=%2Fdefault). Passare a **Impostazioni** > **Integrazioni** > **Sfoglia Marketplace**.

    ![Impostazioni Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrazioni Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selezionare **Azure AD**. Fare clic su **Installa ora**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick Installa](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selezionare **Provisioning utente** e fare clic su **Avvia integrazione**. 

    ![Provisioning utente proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    L'interfaccia utente di configurazione delle impostazioni appropriata dovrebbe ora essere visualizzata in**Integrazioni**di **impostazioni** > . Selezionare **Impostazioni** in **Azure AD (provisioning utente)**.

    ![Proxyclick Crea](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    È possibile trovare **l'URL tenant** e il **token segreto** qui.

    ![Proxyclick Crea token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Dopo aver popolato i campi mostrati nel passaggio 5, fare clic su Test connessione per verificare che Azure AD possa connettersi a Proxyclick.Upon populating the fields shown in Step 5, click **Test Connection** to ensure Azure AD can connect to Proxyclick. Se la connessione non riesce, verificare che l'account Proxyclick disponga delle autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Proxyclick**.

    ![Proxyclick Mapping utente](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Proxyclick nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Proxyclick per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Proxyclick Attributi utente](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare il servizio di provisioning di Azure AD per Proxyclick, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for Proxyclick, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

15. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Proxyclick scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD su Proxyclick.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Proxyclick.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Proxyclick richiede che i messaggi di **posta elettronica** e **userName** abbiano lo stesso valore di origine. Qualsiasi aggiornamento a uno degli attributi modificherà l'altro valore.
* Proxyclick non supporta il provisioning per i gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

