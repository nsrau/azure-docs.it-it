---
title: 'Esercitazione: Configurare MerchLogix per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in MerchLogix.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061300"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Esercitazione: Configurare MerchLogix per il provisioning automatico degli utentiTutorial: Configure MerchLogix for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in MerchLogix e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in MerchLogix.The objective of this tutorial is to demonstrate the steps to be performed in MerchLogix and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and/o provision users to MerchLogix.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* Un tenant MerchLogix
* Un contatto tecnico presso MerchLogix che può fornire l'URL dell'endpoint SCIM e il token segreto necessari per il provisioning degli utenti

## <a name="adding-merchlogix-from-the-gallery"></a>Aggiunta di MerchLogix dalla galleria

Prima di configurare MerchLogix per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere MerchLogix dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere MerchLogix dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add MerchLogix from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare a **Applicazioni** > aziendali**Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]

3. Per aggiungere MerchLogix, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **MerchLogix**.

5. Nel pannello dei risultati selezionare **MerchLogix**, quindi fare clic sul pulsante **Aggiungi** per aggiungere MerchLogix all'elenco delle applicazioni SaaS.

    ![MerchLogix Provisioning][4]

## <a name="assigning-users-to-merchlogix"></a>Assegnazione di utenti a MerchLogixAssigning users to MerchLogix

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a MerchLogix. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a MerchLogix seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Suggerimenti importanti per l'assegnazione di utenti a MerchLogix

* È consigliabile assegnare un singolo utente di Azure AD a MerchLogix per testare la configurazione iniziale del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento, una volta completati i test.

* Quando si assegna un utente a MerchLogix, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configurazione del provisioning automatico degli utenti in MerchLogix 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in MerchLogix in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per MerchLogix, seguendo le istruzioni fornite [nell'esercitazione sull'accesso Single Sign-On MerchLogix](merchlogix-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per MerchLogix in Azure AD:To configure automatic user provisioning for MerchLogix in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare MerchLogix dall'elenco delle applicazioni SaaS.

3. Selezionare la scheda **Provisioning.**

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![MerchLogix Provisioning](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Nella sezione **Credenziali amministratore:**

    * Nel campo **URL tenant** immettere l'URL dell'endpoint SCIM fornito dal contatto tecnico MerchLogix.

    * Nel campo **Token segreto** immettere il token segreto fornito dal contatto tecnico MerchLogix.

6. Al momento del popolamento dei campi illustrati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a MerchLogix. Se la connessione non riesce, verificare che l'account MerchLogix disponga delle autorizzazioni di amministratore e riprovare.

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

8. Fare clic su **Salva**.

9. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con MerchLogix.**

10. Esaminare gli attributi utente sincronizzati da Azure AD a MerchLogix nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in MerchLogix per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

11. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con MerchLogix.**

12. Esaminare gli attributi di gruppo sincronizzati da Azure AD a MerchLogix nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con i gruppi in MerchLogix per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

13. Per abilitare il servizio di provisioning di Azure AD per MerchLogix, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for MerchLogix, change the **Provisioning Status** to On in the **Settings** section.

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in MerchLogix.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on MerchLogix.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
