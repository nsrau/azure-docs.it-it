---
title: 'Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Cornerstone OnDemand.Learn how to configure Azure Active Directory to automatically provision and deprovision user accounts to Cornerstone OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058425"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Cornerstone OnDemand e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti o gruppi in Cornerstone OnDemand.This tutorial demonstrates the steps to perform to perform in Cornerstone OnDemand and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and deprovision users or groups to Cornerstone OnDemand.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning degli utenti di Azure AD. Per informazioni sulle operazioni eseguite da questo servizio, sul suo funzionamento e sulle domande frequenti, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS (Software-as-A-Service) con Azure Active Directory.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che si dispone di:The scenario outlined in this tutorial assumes that you have:

* Un tenant di Azure AD.
* Un tenant OnDemand Cornerstone.
* Un account utente in Cornerstone OnDemand con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sul [servizio Web Cornerstone OnDemand.](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf) Questo servizio è disponibile per i team di Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Aggiungere Cornerstone OnDemand da Azure MarketplaceAdd Cornerstone OnDemand from the Azure Marketplace

Prima di configurare Cornerstone OnDemand per il provisioning automatico degli utenti con Azure AD, aggiungere Cornerstone OnDemand dal Marketplace all'elenco delle applicazioni SaaS gestite.

Per aggiungere Cornerstone OnDemand dal Marketplace, attenersi alla seguente procedura.

1. Nel [portale di Azure,](https://portal.azure.com)nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory.**

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Cornerstone OnDemand** e selezionare **Cornerstone OnDemand** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Cornerstone OnDemand nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Assegnare utenti a Cornerstone OnDemandAssign users to Cornerstone OnDemand

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, decidere quali utenti o gruppi in Azure AD devono accedere a Cornerstone OnDemand.Before you configure and enable automatic user provisioning, decide which users or groups in Azure AD need access to Cornerstone OnDemand. Per assegnare questi utenti o gruppi a Cornerstone OnDemand, seguire le istruzioni in [Assegnare un utente o un gruppo a un'app aziendale.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Suggerimenti importanti per l'assegnazione di utenti a Cornerstone OnDemand

* È consigliabile assegnare un singolo utente di Azure AD a Cornerstone OnDemand per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a Cornerstone OnDemand, selezionare un ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurare il provisioning automatico degli utenti su Cornerstone OnDemand

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. Utilizzarlo per creare, aggiornare e disabilitare utenti o gruppi in Cornerstone OnDemand in base alle assegnazioni di utenti o gruppi in Azure AD.

Per configurare il provisioning automatico degli utenti per Cornerstone OnDemand in Azure AD, eseguire la procedura seguente.

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni** > aziendali**Tutte le applicazioni** > **Cornerstone OnDemand**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Cornerstone OnDemand**.

    ![Collegamento Cornerstone OnDemand nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning OnDemand di Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere il nome utente amministratore, la password di amministratore e il dominio dell'account di Cornerstone OnDemand:

    * Nella casella **Nome utente amministratore** immettere il dominio o il nome utente dell'account amministratore nel tenant Cornerstone OnDemand. Un esempio è contoso.admin.

    * Nella casella **Password amministratore** immettere la password corrispondente al nome utente dell'amministratore.

    * Nella casella **Dominio** immettere l'URL del servizio Web del tenant Cornerstone OnDemand. Ad esempio, il servizio `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`si trova in `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`, e per Contoso il dominio è . Per ulteriori informazioni su come recuperare l'URL del servizio Web, vedere [questo pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Dopo aver compilato le caselle illustrate nel passaggio 5, selezionare Test connessione per assicurarsi che Azure AD possa connettersi a Cornerstone OnDemand.After you fill in the boxes shown in Step 5, select **Test Connection** to make sure that Azure AD can connect to Cornerstone OnDemand. Se la connessione non riesce, assicurarsi che l'account Cornerstone OnDemand disponga delle autorizzazioni di amministratore e riprovare.

    ![Cornerstone OnDemand Test Connection](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Nella casella **Email di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche di errore di provisioning. Selezionare la casella di controllo **Invia una notifica tramite posta elettronica quando si verifica un errore.**

    ![E-mail di notifica Dissatcione di Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selezionare **Salva**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Sincronizza utenti di Azure Active Directory in Cornerstone OnDemand).

    ![Sincronizzazione OnDemand di Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a Cornerstone OnDemand nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Cornerstone OnDemand per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Mapping degli attributi Distone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Per configurare i filtri di ambito, seguire le istruzioni nell'esercitazione sui [filtri di ambito.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

12. Per abilitare il servizio di provisioning di Azure AD per Cornerstone OnDemand, nella sezione **Impostazioni** modificare **Stato provisioning** su **Attivato**.

    ![Stato provisioning OnDemand di Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definire gli utenti o i gruppi di cui si desidera eseguire il provisioning in Cornerstone OnDemand. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Ambito Di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salva OnDemand di Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti, purché venga eseguito il servizio di provisioning di Azure AD. 

È possibile utilizzare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning. The report describes all the actions performed by the Azure AD provisioning service on Cornerstone OnDemand.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

L'attributo **Position** di Cornerstone OnDemand prevede un valore che corrisponde ai ruoli nel portale di Cornerstone OnDemand. Per ottenere un elenco di valori **Position** validi, passare a **Modifica record utente > struttura organizzativa > posizione** nel portale Cornerstone OnDemand.

![Cornerstone OnDemand Provisioning Modifica record utente](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posizione di provisioning OnDemand Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand Elenco posizioni provisioning](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
