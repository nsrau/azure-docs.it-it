---
title: 'Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in elemento di base OnDemand.
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

Questa esercitazione illustra i passaggi da eseguire in base a OnDemand e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi in elemento di base OnDemand.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS (software-as-a-Service) con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che siano disponibili:

* Un tenant di Azure AD.
* Tenant OnDemand fondamentale.
* Un account utente in elemento di base OnDemand con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning Azure AD si basa sul [servizio Web](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)di base OnDemand. Questo servizio è disponibile per i team di base OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Aggiungere l'elemento di base OnDemand da Azure Marketplace

Prima di configurare il componente OnDemand di base per il provisioning utenti automatico con Azure AD, aggiungere la funzione di base OnDemand dal Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere l'elemento di base OnDemand dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![Icona Azure Active Directory](common/select-azuread.png)

2. Passare ad **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere la **chiave fondamentale OnDemand** e selezionare l'elemento di base **OnDemand** nel pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Cornerstone OnDemand nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Assegna utenti a elemento di base OnDemand

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a elemento di base OnDemand. Per assegnare questi utenti o gruppi a elemento di base OnDemand, seguire le istruzioni riportate in [assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Suggerimenti importanti per l'assegnazione di utenti a Cornerstone OnDemand

* È consigliabile assegnare un singolo utente Azure AD a elemento fondamentale OnDemand per testare la configurazione del provisioning utenti automatico. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a elemento di base OnDemand, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di **accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurare il provisioning utenti automatico in elemento di base OnDemand

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. È possibile usarlo per creare, aggiornare e disabilitare gli utenti o i gruppi in un elemento di base OnDemand in base alle assegnazioni di utenti o gruppi in Azure AD.

Per configurare il provisioning utenti automatico per la funzione di base OnDemand in Azure AD, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni** > **aziendali tutte le applicazioni** > **elemento fondamentale OnDemand**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Cornerstone OnDemand**.

    ![Collegamento dell'elemento di base OnDemand nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **credenziali amministratore** immettere il nome utente amministratore, la password dell'amministratore e il dominio dell'account di OnDemand della chiave:

    * Nella casella **nome utente amministratore** compilare il dominio o il nome utente dell'account amministratore nel tenant di base OnDemand. Un esempio è contoso\admin.

    * Nella casella **password amministratore** immettere la password corrispondente al nome utente amministratore.

    * Nella casella **dominio** compilare l'URL del servizio Web del tenant di base OnDemand. Il servizio, ad esempio, si trova `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`in e per Contoso il dominio è `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Per ulteriori informazioni su come recuperare l'URL del servizio Web, vedere [questo file PDF](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Dopo aver compilato le caselle visualizzate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a elemento di base OnDemand. Se la connessione non riesce, verificare che l'account di base OnDemand abbia le autorizzazioni di amministratore e riprovare.

    ![Connessione test di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Posta elettronica di notifica di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selezionare **Salva**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Sincronizza utenti di Azure Active Directory in Cornerstone OnDemand).

    ![Sincronizzazione di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a elemento di base OnDemand nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Cornerstone OnDemand per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Mapping degli attributi di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'esercitazione relativa al [filtro di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning Azure AD per l'elemento di base OnDemand, nella sezione **Impostazioni** impostare **stato del provisioning** **su**attivato.

    ![Stato del provisioning di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in elemento di base OnDemand. Nella sezione **Impostazioni** selezionare i valori desiderati nell' **ambito**.

    ![Ambito OnDemand fondamentale](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salvataggio fondamentale OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nell' **ambito** nella sezione **Impostazioni** . La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti a condizione che venga eseguito il servizio di provisioning Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio Azure AD provisioning in base OnDemand.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

L'attributo **Position** di Cornerstone OnDemand prevede un valore che corrisponde ai ruoli nel portale di Cornerstone OnDemand. Per ottenere un elenco di valori di **posizione** validi, vedere **modificare il record utente > struttura dell'organizzazione > posizione** nel portale di OnDemand.

![Record utente per la modifica del provisioning dell'elemento di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posizione di provisioning dell'elemento di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Elenco delle posizioni di provisioning di base OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
