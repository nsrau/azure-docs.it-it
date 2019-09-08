---
title: 'Esercitazione: Configurare il cibo per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in un alimento.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: e444d6254b575ead4b5d436a4b92f960c61b1d1c
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802875"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Esercitazione: Configurare il foodee per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in foodee e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in un alimento.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant del cibo](https://Foodee.com/pricing/)
* Un account utente in foodee con autorizzazioni di amministratore.

## <a name="assigning-users-to-foodee"></a>Assegnazione di utenti al cibo 

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere al cibo. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi al cibo seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-foodee"></a>Suggerimenti importanti per l'assegnazione di utenti a un alimento 

* Per testare la configurazione del provisioning utenti automatico, è consigliabile assegnare un singolo Azure AD utente al cibo. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a un alimento, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-foodee-for-provisioning"></a>Configurare un alimento per il provisioning

Prima di configurare foodee per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning di SCIM nel cibo.

1. Accedere a [Foode](https://www.food.ee/login/). Fare clic sull' **ID tenant**

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

2. In Enterprise Portal > selezionare **Single Sign-on**.

    ![Foodee](media/Foodee-provisioning-tutorial/scim.png)

3. Copiare il **token dell'API**. Questi valori verranno immessi nel campo **token segreto** nella scheda provisioning dell'applicazione Foode nella portale di Azure.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)


## <a name="add-foodee-from-the-gallery"></a>Aggiungere un alimento dalla raccolta

Per configurare gli utenti per il provisioning utenti automatico con Azure AD, è necessario aggiungere un alimento dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere un alimento dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Foode**, selezionare **Foode** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Foode nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-foodee"></a>Configurazione del provisioning utenti automatico in foodee  

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in alimenti in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per foodee, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on](Foodee-tutorial.md)per i buongustai. Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-foodee-in-azure-ad"></a>Per configurare il provisioning utenti automatico per i buongustai in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Foodee**.

    ![Collegamento del cibo nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione credenziali amministratore immettere rispettivamente i valori ` https://concierge.food.ee/scim/v2` del **token API** e recuperati in precedenza in **URL tenant** e **token segreto** . Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi al cibo. Se la connessione ha esito negativo, verificare che l'account del cibo abbia le autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti al cibo**.

    ![Mapping utenti del cibo](media/Foodee-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a foodee nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in foodee per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente del cibo](media/Foodee-provisioning-tutorial/userattribute.png)

10. Nella sezione **mapping** selezionare * * Sincronizza i gruppi di Azure Active Directory al **cibo**

    ![Attributi utente del cibo](media/Foodee-provisioning-tutorial/groupmapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a foodee nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account di gruppo in foodee per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente del cibo](media/Foodee-provisioning-tutorial/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Foode, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in foodee scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning di utenti e/o gruppi, vedere [quanto tempo sarà necessario per il provisioning degli utenti](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning sul cibo. Per altre informazioni, vedere [controllare lo stato del provisioning dell'utente](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
