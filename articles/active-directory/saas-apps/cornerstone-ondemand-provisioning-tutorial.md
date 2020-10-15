---
title: 'Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Cornerstone OnDemand.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 4200bc9879aba5e18282123be03576cc617a5e14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549229"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Cornerstone OnDemand e Azure Active Directory (Azure AD) per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti o gruppi in Cornerstone OnDemand.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone i prerequisiti seguenti:

* Un tenant di Azure AD.
* Un tenant di Cornerstone OnDemand.
* Un account utente in Cornerstone OnDemand con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sul [servizio Web Cornerstone OnDemand](https://www.cornerstoneondemand.com/). Il servizio è disponibile per i team di Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Aggiungere Cornerstone OnDemand da Azure Marketplace

Prima di configurare Cornerstone OnDemand per il provisioning utenti automatico con Azure AD, è necessario aggiungere Cornerstone OnDemand dal Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Cornerstone OnDemand dal Marketplace seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Cornerstone OnDemand**, quindi selezionare **Cornerstone OnDemand** nel pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Cornerstone OnDemand nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Assegnare gli utenti a Cornerstone OnDemand

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti o gruppi in Azure AD devono poter accedere a Cornerstone OnDemand. Per assegnare gli utenti o i gruppi a Cornerstone OnDemand, seguire le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Suggerimenti importanti per l'assegnazione di utenti a Cornerstone OnDemand

* Inizialmente, è consigliabile assegnare un singolo utente di Azure AD a Cornerstone OnDemand per testare la configurazione del provisioning utenti automatico. Sarà possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a Cornerstone OnDemand, selezionare qualsiasi ruolo valido specifico dell'applicazione, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurare il provisioning utenti automatico per Cornerstone OnDemand

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD. In questo modo è possibile creare, aggiornare e disabilitare utenti e/o gruppi in Cornerstone OnDemand in base alle assegnazioni di utenti o gruppi in Azure AD.

Per configurare il provisioning utenti automatico per Cornerstone OnDemand in Azure AD, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Cornerstone OnDemand**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Cornerstone OnDemand**.

    ![Collegamento a Cornerstone OnDemand nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere il nome utente e la password di amministratore e il dominio dell'account di Cornerstone OnDemand:

    * Nella casella **Nome utente amministratore** inserire il dominio o il nome utente dell'account amministratore nel tenant di Cornerstone OnDemand. Esempio: contoso\admin.

    * Nella casella **Password amministratore** immettere la password corrispondente al nome utente amministratore.

    * Nella casella **Dominio** immettere l'URL del servizio Web del tenant di Cornerstone OnDemand. Ad esempio, il servizio si trova in `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx` e per Contoso il dominio è `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Per altre informazioni su come recuperare l'URL del servizio Web, vedere [questo file pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Dopo aver compilato le caselle mostrate nel passaggio 5, selezionare **Test connessione** per verificare che Azure AD possa connettersi a Cornerstone OnDemand. Se la connessione non riesce, assicurarsi che l'account di Cornerstone OnDemand disponga delle autorizzazioni di amministratore e riprovare.

    ![Test della connessione di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo e-mail di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selezionare **Salva**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Sincronizza utenti di Azure Active Directory in Cornerstone OnDemand).

    ![Sincronizzazione di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Cornerstone OnDemand. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Cornerstone OnDemand per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Mapping degli attributi di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per Cornerstone OnDemand, nella sezione **Impostazioni** impostare lo **Stato del provisioning** su **Sì**.

    ![Stato del provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Cornerstone OnDemand. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Ambito di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Operazione Salva in Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti nell'**Ambito** della sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Le sincronizzazioni successive vengono eseguite circa ogni 40 minuti durante l'esecuzione del servizio di provisioning di Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report dell'attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Cornerstone OnDemand.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

L'attributo **Position** di Cornerstone OnDemand prevede un valore che corrisponde ai ruoli nel portale di Cornerstone OnDemand. Per un elenco dei valori validi per **Position**, passare a **Edit User Record > Organization Structure > Position** (Modifica record utente > Struttura organizzativa > Posizione) nel portale di Cornerstone OnDemand.

![Record utente per la modifica del provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posizione del provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Elenco delle posizioni di provisioning di Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
