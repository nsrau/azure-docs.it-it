---
title: 'Esercitazione: Configurare 15Five per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente a 15Five.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to 15Five.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f1f66a7b69048180bc41c8f2fa432598f00f7f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059265"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Esercitazione: Configurare 15Five per il provisioning automatico degli utentiTutorial: Configure 15Five for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in 15Five e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi a 15Five.The objective of this tutorial is to demonstrate the steps to be performed in 15Five and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to 15Five.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un inquilino 15Five](https://www.15five.com/pricing/).
* Un account utente in 15Five con autorizzazioni di amministratore.

## <a name="assigning-users-to-15five"></a>Assegnazione di utenti a 15FiveAssigning users to 15Five

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a 15Five. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a 15Five seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Suggerimenti importanti per l'assegnazione di utenti a 15Five

* È consigliabile assegnare un singolo utente di Azure AD a 15Five per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a 15Five, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-15five-for-provisioning"></a>Configurazione di 15Five per il provisioning

Prima di configurare 15Five per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM su 15Five.

1. Accedi alla [tua Admin Console 15Five](https://my.15five.com/). Passare a **Funzionalità > integrazioni**.

    ![15Cinque Admin Console](media/15five-provisioning-tutorial/integration.png)

2.  Fare clic su **SCIM 2.0**.

    ![15Cinque Admin Console](media/15five-provisioning-tutorial/image00.png)

3.  Passare **all'integrazione SCIM > Genera token OAuth**.

    ![15Cinque Aggiungi SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Copiare i valori per **l'URL di base SCIM 2.0** e **il token di accesso**. Questo valore verrà immesso nel campo URL tenant e **token segreto** nella scheda Provisioning dell'applicazione 15Five nel portale di Azure.This value will be entered in the **Tenant URL** and Secret Token field in the Provisioning tab of your 15Five application in the Azure portal.
    
    ![15Cinque Aggiungi SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Aggiungi 15Five dalla galleria

Per configurare 15Five per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere 15Five dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere 15Five dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add 15Five from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **15Five**, selezionare **15Five** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![15Five nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Configurazione del provisioning automatico degli utenti su 15FiveConfiguring automatic user provisioning to 15Five 

In questa sezione vengono illustrati i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in 15Five in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per 15Five , seguendo le istruzioni fornite [nell'esercitazione 15Five Single Sign-On](15five-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per 15Five in Azure AD:To configure automatic user provisioning for 15Five in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **15Five**.

    ![Collegamento di 15Five nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5.  Nella sezione Credenziali amministratore immettere i valori dell'URL di base e del token di **accesso di SCIM 2.0** recuperati in precedenza rispettivamente in **URL tenant** e **token segreto.** Fare clic su Test connessione per verificare che Azure AD possa connettersi a 15Five.Click **Test Connection** to ensure Azure AD can connect to 15Five. Se la connessione non riesce, verificare che l'account 15Five disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con 15Five.**

    ![15Cinque mapping utenti](media/15five-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a 15Five nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in 15Five per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![15Cinque attributi utente](media/15five-provisioning-tutorial/userattribute.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con 15Five.**

    ![15Cinque mappature di gruppo](media/15five-provisioning-tutorial/groupmapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a 15Five nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in 15Five per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![15Cinque attributi di gruppo](media/15five-provisioning-tutorial/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per 15Five, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for 15Five, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in 15Five scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD su 15Five.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on 15Five.

    Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utenteFor](../app-provisioning/check-status-user-account-provisioning.md) more information on how to read the Azure AD provisioning logs, see Reporting on automatic user account provisioning
    
## <a name="connector-limitations"></a>Limitazioni dei connettori

* 15Cinque non supporta le eliminazioni forzate per gli utenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Scopri come esaminare i log e ottenere report sulle attività](../app-provisioning/check-status-user-account-provisioning.md)di provisioning.
