---
title: 'Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Cornerstone OnDemand.
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
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127498"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Cornerstone OnDemand e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi a Cornerstone OnDemand.

> [!NOTE]
> Questa esercitazione descrive un connettore che si basa il servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio, come funziona e domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni software-as-a-service (SaaS) con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione si presuppone di avere:

* Un tenant di Azure AD.
* Un tenant di Cornerstone OnDemand.
* Un account utente in Cornerstone OnDemand con autorizzazioni di amministratore.

> [!NOTE]
> Integrazione del provisioning di Azure AD si basa sul [servizio web Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Questo servizio è disponibile per i team Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Aggiungere Cornerstone OnDemand da Azure Marketplace

Prima di configurare Cornerstone OnDemand per provisioning utenti automatico con Azure AD, aggiungere Cornerstone OnDemand da Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Cornerstone OnDemand dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![L'icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Cornerstone OnDemand** e selezionare **Cornerstone OnDemand** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Add**.

    ![Cornerstone OnDemand nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Assegnare gli utenti a Cornerstone OnDemand

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, stabilire quali utenti o gruppi in Azure AD devono poter accedere a Cornerstone OnDemand. Per assegnare tali utenti o gruppi a Cornerstone OnDemand, seguire le istruzioni in [assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Suggerimenti importanti per l'assegnazione di utenti a Cornerstone OnDemand

* È consigliabile assegnare un singolo utente di Azure AD a Cornerstone OnDemand per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Cornerstone OnDemand, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configura provisioning utenti automatico per Cornerstone OnDemand

Questa sezione descrive i passaggi per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in Cornerstone OnDemand mediante le assegnazioni di gruppo o utente di Azure AD.

Per configurare il provisioning utenti automatico per Cornerstone OnDemand in Azure AD, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali** > **tutte le applicazioni** > **Cornerstone OnDemand**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Cornerstone OnDemand**.

    ![Il collegamento di Cornerstone OnDemand nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di Provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Sotto il **credenziali amministratore** sezione, immettere il nome utente amministratore, amministratore password e dominio dell'account di Cornerstone OnDemand:

    * Nel **Admin Username** casella, immettere il dominio o il nome utente dell'account amministratore nel tenant di Cornerstone OnDemand. Un esempio è contoso\admin.

    * Nel **Password amministratore** casella, immettere la password che corrisponde al nome utente amministratore.

    * Nel **dominio** casella, immettere l'URL del servizio web del tenant di Cornerstone OnDemand. Ad esempio, il servizio è disponibile all'indirizzo `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, e per Contoso il dominio è `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Per altre informazioni su come recuperare l'URL del servizio web, vedere [questo pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Dopo aver inserito le caselle indicate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure AD possa connettersi a Cornerstone OnDemand. Se la connessione non riesce, assicurarsi che l'account di Cornerstone OnDemand abbia autorizzazioni di amministratore e riprovare.

    ![Cornerstone OnDemand Test connessione](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Nel **notifica tramite posta elettronica** casella, immettere l'indirizzo di posta elettronica della persona o il gruppo per la ricezione di notifiche di errore di provisioning. Selezionare il **invia una notifica di posta elettronica quando si verifica un errore** casella di controllo.

    ![Posta elettronica di notifica di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selezionare **Salva**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Sincronizza utenti di Azure Active Directory in Cornerstone OnDemand).

    ![Sincronizzazione di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a Cornerstone OnDemand nel **mapping di attributi** sezione. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Cornerstone OnDemand per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Mapping di Cornerstone OnDemand attributo](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Seguire le istruzioni per configurare i filtri di ambito, il [esercitazione filtro ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare Azure AD per Cornerstone OnDemand, servizio di provisioning nel **le impostazioni** sezione, modificare **stato del Provisioning** al **su**.

    ![Stato di Provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definire gli utenti o gruppi che si desidera eseguire il provisioning in Cornerstone OnDemand. Nel **le impostazioni** , selezionare i valori desiderati nelle **ambito**.

    ![Ambito di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando si è pronti per eseguire il provisioning, selezionare **salvare**.

    ![Salvataggio di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nella **ambito** nel **impostazioni** sezione. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano circa ogni 40 minuti fino a quando viene eseguito il servizio di provisioning di Azure AD. 

È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti per il rapporto attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio in Cornerstone OnDemand di provisioning di Azure AD.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

L'attributo **Position** di Cornerstone OnDemand prevede un valore che corrisponde ai ruoli nel portale di Cornerstone OnDemand. Per ottenere un elenco dei valori validi **posizione** valori, passare alla **modificare il Record utente > alla struttura dell'organizzazione > posizione** nel portale di Cornerstone OnDemand.

![Provisioning di Cornerstone OnDemand modificare Record utente](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posizione di Provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Elenco di Cornerstone OnDemand Provisioning posizione](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
