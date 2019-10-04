---
title: 'Esercitazione: Configurare TheOrgWiki per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f691b3e80bdde546074b9f243add82ace00c6301
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576381"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Esercitazione: Configurare TheOrgWiki per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in TheOrgWiki e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in TheOrgWiki.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di OrgWiki](https://www.theorgwiki.com/welcome/).
* Un account utente in TheOrgWiki con autorizzazioni di amministratore.

## <a name="assign-users-to-theorgwiki"></a>Assegnare gli utenti a TheOrgWiki

Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a TheOrgWiki. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a TheOrgWiki seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Suggerimenti importanti per l'assegnazione di utenti a TheOrgWiki

* È consigliabile assegnare un singolo Azure AD utente a TheOrgWiki per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a TheOrgWiki, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurare TheOrgWiki per il provisioning

Prima di configurare TheOrgWiki per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in TheOrgWiki.

1. Accedere a [TheOrgWiki Admin Console](https://www.theorgwiki.com/login/). Fare clic su **console di amministrazione**.

    ![TheOrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. Nella console di amministrazione fare clic sulla **scheda Impostazioni**. 

    ![TheOrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Passare ad **account del servizio**.

    ![TheOrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Fare clic su **+ account del servizio**. In **tipo di account del servizio**selezionare **basato su token**. Fare clic su **Save**.

    ![TheOrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copiare i **token attivi**. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione TheOrgWiki nel portale di Azure.
     
    ![TheOrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Aggiungere TheOrgWiki dalla raccolta

Per configurare TheOrgWiki per il provisioning utenti automatico con Azure AD, è necessario aggiungere TheOrgWiki dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **TheOrgWiki**, selezionare **TheOrgWiki** nel pannello dei risultati. 

    ![TheOrgWiki nell'elenco risultati](common/search-new-app.png)

5. Selezionare il pulsante **Iscriviti a TheOrgWiki** che consente di reindirizzare la pagina di accesso di TheOrgWiki. 

    ![TheOrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  Nell'angolo superiore destro selezionare **login**.

    ![TheOrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Poiché TheOrgWiki è un'app OpenIDConnect, scegliere di accedere a OrgWiki con l'account Microsoft Work.

    ![TheOrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Una volta completata l'autenticazione, l'applicazione verrà aggiunta automaticamente al tenant e si verrà reindirizzati all'account TheOrgWiki.

    ![OrgWiki aggiungere SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configurare il provisioning utenti automatico in TheOrgWiki 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TheOrgWiki in base alle assegnazioni di utenti e/o gruppi in Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Per configurare il provisioning utenti automatico per TheOrgWiki in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **TheOrgWiki**.

    ![Collegamento di OrgWiki nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` in **URL tenant**. 

    Esempio: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> Il **valore** del sottodominio può essere impostato solo durante il processo di iscrizione iniziale per TheOrgWiki.
 
6. Immettere il valore del token nel campo **token segreto** recuperato in precedenza da TheOrgWiki. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a TheOrgWiki. Se la connessione non riesce, verificare che l'account TheOrgWiki disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Save**.

9. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a TheOrgWiki**.

    ![Mapping utente TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a TheOrgWiki nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in TheOrgWiki per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning Azure AD per TheOrgWiki, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in OrgWiki selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning di utenti e/o gruppi, vedere [quanto tempo sarà necessario per il provisioning degli utenti](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su TheOrgWiki. Per altre informazioni, vedere [controllare lo stato del](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)provisioning dell'utente. Per leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md).