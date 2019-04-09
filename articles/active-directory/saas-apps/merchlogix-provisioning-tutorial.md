---
title: 'Esercitazione: Configurazione di MerchLogix per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in MerchLogix.
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
ms.openlocfilehash: c8fecc5232b26c98c4027174454cf29b81b0ee41
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263598"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di MerchLogix per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in MerchLogix e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e deprovisioning di utenti e/o gruppi a MerchLogix.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* Un tenant di MerchLogix
* Contatto tecnico in MerchLogix in grado di fornire l'URL dell'endpoint SCIM e il token segreto richiesto per il provisioning utenti

## <a name="adding-merchlogix-from-the-gallery"></a>Aggiunta di MerchLogix dalla raccolta

Prima configurazione di MerchLogix per provisioning utenti automatico con Azure AD, è necessario aggiungere MerchLogix dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere MerchLogix dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]

3. Per aggiungere MerchLogix, scegliere il **nuova applicazione** pulsante nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **MerchLogix**.

5. Nel pannello dei risultati, selezionare **MerchLogix**, quindi fare clic sul **Aggiungi** pulsante per aggiungere MerchLogix al proprio elenco di applicazioni SaaS.

    ![Il Provisioning di MerchLogix][4]

## <a name="assigning-users-to-merchlogix"></a>Assegnazione di utenti a MerchLogix

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a MerchLogix. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a MerchLogix, seguire le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Suggerimenti importanti per l'assegnazione di utenti a MerchLogix

* È consigliabile che un singolo utente di Azure AD viene assegnato a MerchLogix per testare il configurazione del provisioning utenti automatico iniziale. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento, una volta completati i test.

* Quando si assegna un utente a MerchLogix, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configurazione del provisioning utenti automatico a MerchLogix 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in MerchLogix base alle assegnazioni utente e/o di gruppo in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare basato su SAML single sign-on di MerchLogix, seguire le istruzioni fornite nel [esercitazione MerchLogix single sign-on](merchlogix-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Per configurare il provisioning utenti automatico per MerchLogix in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare MerchLogix nell'elenco delle applicazioni SaaS.

3. Selezionare la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Il Provisioning di MerchLogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Sotto il **credenziali amministratore** sezione:

    * Nel **URL Tenant** immettere l'URL dell'endpoint SCIM fornito per il contatto tecnico MerchLogix.

    * Nel **Token segreto** immettere segreto token fornito dal contatto tecnico MerchLogix.

6. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a MerchLogix. Se la connessione non riesce, verificare che l'account di MerchLogix abbia autorizzazioni di amministratore e riprovare.

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

8. Fare clic su **Save**.

9. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users a MerchLogix**.

10. Esaminare gli attributi utente che vengono sincronizzati da Azure AD con MerchLogix nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in MerchLogix per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

11. Sotto il **mapping** sezione, selezionare **sincronizzare Azure gruppi di Active Directory a MerchLogix**.

12. Esaminare gli attributi gruppo sincronizzati da Azure AD a MerchLogix nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà usate devono rispettare i gruppi in MerchLogix per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

13. Per abilitare il provisioning del servizio per MerchLogix AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio in MerchLogix di provisioning di Azure AD provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione di provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
