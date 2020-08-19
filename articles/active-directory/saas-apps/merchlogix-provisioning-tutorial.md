---
title: 'Esercitazione: configurare MerchLogix per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in MerchLogix.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 6fe256660dc73aa411cf06e2b56ce9ef26934e2f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548000"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Esercitazione: configurare MerchLogix per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in MerchLogix e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in MerchLogix.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* Tenant di MerchLogix
* Un contatto tecnico in MerchLogix che può fornire l'URL dell'endpoint SCIM e il token segreto necessari per il provisioning degli utenti

## <a name="adding-merchlogix-from-the-gallery"></a>Aggiunta di MerchLogix dalla raccolta

Prima di configurare MerchLogix per il provisioning utenti automatico con Azure AD, è necessario aggiungere MerchLogix dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere MerchLogix dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **applicazioni aziendali**  >  **tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]

3. Per aggiungere MerchLogix, fare clic sul pulsante **nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **MerchLogix**.

5. Nel pannello dei risultati selezionare **MerchLogix**e quindi fare clic sul pulsante **Aggiungi** per aggiungere MerchLogix al proprio elenco di applicazioni SaaS.

    ![Provisioning di MerchLogix][4]

## <a name="assigning-users-to-merchlogix"></a>Assegnazione di utenti a MerchLogix

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a MerchLogix. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a MerchLogix seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Suggerimenti importanti per l'assegnazione di utenti a MerchLogix

* È consigliabile assegnare un singolo Azure AD utente a MerchLogix per testare la configurazione iniziale del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento, una volta completati i test.

* Quando si assegna un utente a MerchLogix, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configurazione del provisioning utenti automatico in MerchLogix 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in MerchLogix in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per MerchLogix, seguendo le istruzioni fornite nell' [esercitazione su MerchLogix Single Sign-on](merchlogix-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Per configurare il provisioning utenti automatico per MerchLogix in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare MerchLogix dall'elenco di applicazioni SaaS.

3. Selezionare la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di MerchLogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Nella sezione **credenziali amministratore** :

    * Nel campo **URL tenant** immettere l'URL dell'endpoint scim fornito dal contatto tecnico di MerchLogix.

    * Nel campo **token segreto** immettere il token Secret fornito dal contatto tecnico di MerchLogix.

6. Quando si popolano i campi indicati nel passaggio 5, fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a MerchLogix. Se la connessione non riesce, verificare che l'account MerchLogix disponga delle autorizzazioni di amministratore e riprovare.

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

8. Fare clic su **Salva**.

9. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a MerchLogix**.

10. Esaminare gli attributi utente sincronizzati da Azure AD a MerchLogix nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in MerchLogix per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

11. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to MerchLogix**.

12. Esaminare gli attributi di gruppo sincronizzati da Azure AD a MerchLogix nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in MerchLogix per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

13. Per abilitare il servizio di provisioning Azure AD per MerchLogix, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su MerchLogix.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
