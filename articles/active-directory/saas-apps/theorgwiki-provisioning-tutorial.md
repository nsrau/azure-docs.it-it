---
title: 'Esercitazione: Configurare TheOrgWiki per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in TheOrgWiki.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to TheOrgWiki.
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
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063145"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Esercitazione: Configurare TheOrgWiki per il provisioning automatico degli utentiTutorial: Configure TheOrgWiki for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in TheOrgWiki e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in TheOrgWiki.The objective of this tutorial is to demonstrate the steps to be performed in TheOrgWiki and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and-provision users and/or groups to TheOrgWiki.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant OrgWiki](https://www.theorgwiki.com/welcome/).
* Un account utente in TheOrgWiki con autorizzazioni di amministratore.

## <a name="assign-users-to-theorgwiki"></a>Assegnare utenti a TheOrgWikiAssign users to TheOrgWiki

Azure Active Directory usa un concetto denominato assegnazioni per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a TheOrgWiki. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a TheOrgWiki seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Suggerimenti importanti per l'assegnazione di utenti a TheOrgWiki

* È consigliabile assegnare a TheOrgWiki un singolo utente di Azure AD per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a TheOrgWiki, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurare TheOrgWiki per il provisioning

Prima di configurare TheOrgWiki per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM su TheOrgWiki.

1. Accedi alla Console [di amministrazione TheOrgWiki](https://www.theorgwiki.com/login/). Fare clic su **Admin Console**.

    ![TheOrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. In Admin Console, fare clic sulla **scheda Impostazioni**. 

    ![TheOrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Passare a **Account di servizio**.

    ![TheOrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Fare clic su **Account di servizio**. In **Tipo di account di servizio**selezionare **Basato su token**. Fare clic su **Salva**.

    ![TheOrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copiare i **token attivi**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione TheOrgWiki nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your TheOrgWiki application in the Azure portal.
     
    ![TheOrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Aggiungere TheOrgWiki dalla galleria

Per configurare TheOrgWiki per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere TheOrgWiki dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, inserisci **TheOrgWiki**, seleziona **TheOrgWiki** nel pannello dei risultati. 

    ![TheOrgWiki nell'elenco dei risultati](common/search-new-app.png)

5. Seleziona il pulsante **Iscriviti a TheOrgWiki** che ti reindirizzerà alla pagina di accesso di TheOrgWiki. 

    ![TheOrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  Nell'angolo in alto a destra, seleziona **Login**.

    ![TheOrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Poiché TheOrgWiki è un'app OpenIDConnect, scegli di accedere a OrgWiki utilizzando il tuo account aziendale Microsoft.

    ![TheOrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Dopo una corretta autenticazione, l'applicazione verrà aggiunta automaticamente al tenant e si verrà reindirizzati all'account TheOrgWiki.

    ![OrgWiki Aggiungi SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configurare il provisioning automatico degli utenti in TheOrgWiki 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TheOrgWiki in base alle assegnazioni di utenti e/o gruppi in Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per TheOrgWiki in Azure AD:To configure automatic user provisioning for TheOrgWiki in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **TheOrgWiki**.

    ![Il collegamento OrgWiki nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` amministratore immettere **l'input**in URL tenant. 

    Esempio: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> Il valore del **sottodominio** può essere impostato solo durante il processo di iscrizione iniziale per TheOrgWiki.
 
6. Immettere il valore del token nel campo **Token segreto** recuperato in precedenza da TheOrgWiki. Fare clic su Test connessione per verificare che Azure AD possa connettersi a TheOrgWiki.Click **Test Connection** to ensure Azure AD can connect to TheOrgWiki. Se la connessione non riesce, verificare che l'account TheOrgWiki disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Salva**.

9. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con TheOrgWiki**.

    ![Mapping utente TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a TheOrgWiki nella sezione **Mapping degli** attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in TheOrgWiki per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per TheOrgWiki, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for TheOrgWiki, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in OrgWiki scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning degli utenti e/o dei gruppi, vedere [Quanto tempo è necessario per eseguire il provisioning degli utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione Stato corrente per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD su TheOrgWiki.You can use the **Current Status** section to monitor progress and follow links to your provisioning activity report, which describes all actions performed by the Azure AD provisioning service on TheOrgWiki. Per ulteriori informazioni, consultate [Controllare lo stato del provisioning degli utenti.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Per leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utente.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Scopri come esaminare i log e ottenere report sulle attività](../app-provisioning/check-status-user-account-provisioning.md)di provisioning.