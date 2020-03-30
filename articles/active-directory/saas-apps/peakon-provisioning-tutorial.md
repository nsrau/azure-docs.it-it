---
title: 'Esercitazione: Configurare il provisioning automatico degli utenti di Peakon con Azure Active Directory Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente a Peakon .
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 0a67dc8069ee71305a47bd5d2a724a61cec234a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063393"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Esercitazione: Configurare Peakon per il provisioning automatico degli utentiTutorial: Configure Peakon for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Peakon e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Peakon.The objective of this tutorial is to demonstrate the steps to be performed in Peakon and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and e-provision users and/or groups to Peakon.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che si disponga già dei prerequisiti seguenti

* Un tenant di Azure AD.
* [Un inquilino Peakon](https://peakon.com/us/pricing/).
* Un account utente in Peakon con autorizzazioni di amministratore.

## <a name="assigning-users-to-peakon"></a>Assegnazione di utenti a PeakonAssigning users to Peakon

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Peakon. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Peakon seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Suggerimenti importanti per l'assegnazione di utenti a Peakon 

* È consigliabile assegnare un singolo utente di Azure AD a Peakon per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Peakon , è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-peakon-for-provisioning"></a>Configurare Peakon per il provisioning

1.  Accedi a [Peakon Admin Console](https://app.Peakon.com/login). Fare clic su **Configurazione**. 

    ![Console di amministrazione Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selezionare **Integrazioni**.
    
    ![Fornitura di picchion-dipendenti](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Abilitare il **provisioning dei dipendenti**.

    ![Fornitura di picchion-dipendenti](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Copiare i valori per **URL SCIM 2.0** e **OAuth Bearer Token**. Questi valori verranno immessi nel campo URL tenant e **token segreto** nella scheda Provisioning dell'applicazione Peakon nel portale di Azure.These values will be entered in the **Tenant URL** and Secret Token field in the Provisioning tab of your Peakon application in the Azure portal.

    ![Token di creazione Peakon](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Aggiungere Peakon dalla galleria

Per configurare Peakon per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Peakon dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Peakon**, selezionare **Peakon** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Peakon nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Configurazione del provisioning automatico degli utenti su Peakon 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Peakon in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Peakon, seguendo le istruzioni fornite nell'esercitazione sul [single sign-on di Peakon](peakon-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Peakon in Azure AD:To configure automatic user provisioning for Peakon in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Peakon**.

    ![Il collegamento Peakon nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere i valori **URL SCIM 2.0** e **OAuth Bearer** recuperati in precedenza rispettivamente in **URL tenant** e **token segreto.** Fare clic su Test connessione per verificare che Azure AD possa connettersi a Peakon.Click **Test Connection** to ensure Azure AD can connect to Peakon. Se la connessione non riesce, verificare che l'account Peakon disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Salva**.

9. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Peakon**.

    ![Mapping utenti Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a Peakon nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Peakon per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni seguenti fornite [nell'esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)sui filtri di ambito .
    
    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Peakon.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Peakon.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Tutti gli attributi utente personalizzati in Peakon devono essere estesi dall'estensione utente SCIM personalizzata di Peakon di `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)