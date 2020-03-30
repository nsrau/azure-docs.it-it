---
title: 'Esercitazione: Configurare iPass SmartConnect per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in iPass SmartConnect.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057527"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Esercitazione: Configurare iPass SmartConnect per il provisioning automatico degli utenti

The objective of this tutorial is to demonstrate the steps to be performed in iPass SmartConnect and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to iPass SmartConnect.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant iPass SmartConnect](https://www.ipass.com/buy-ipass/).
* Un account utente in iPass SmartConnect con autorizzazioni di amministratore.

## <a name="assigning-users-to-ipass-smartconnect"></a>Assegnazione di utenti a iPass SmartConnect

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a iPass SmartConnect. Una volta deciso, puoi assegnare questi utenti e/o gruppi a iPass SmartConnect seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Suggerimenti importanti per l'assegnazione degli utenti a iPass SmartConnect

* È consigliabile assegnare un singolo utente di Azure AD a iPass SmartConnect per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a iPass SmartConnect, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Configurare iPass SmartConnect per il provisioning

Prima di configurare iPass SmartConnect per il provisioning automatico degli utenti con Azure AD, è necessario recuperare le informazioni di configurazione dalla console di amministrazione di iPass SmartConnect:

1. Per recuperare il token di connessione necessario per l'autenticazione rispetto all'endpoint SCIM iPass SmartConnect, fare riferimento alla prima volta che si configura iPass SmartConnect poiché questo valore viene fornito solo in questo caso. 
2. Se non hai il token di connessione, contatta il team di supporto di [iPass SmartConnect](mailto:help@ipass.com) per recuperarne uno nuovo.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Aggiungi iPass SmartConnect dalla galleria

Per configurare iPass SmartConnect per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere iPass SmartConnect dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere iPass SmartConnect dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add iPass SmartConnect from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, inserisci **iPass SmartConnect**, seleziona **iPass SmartConnect** nel pannello dei risultati, quindi fai clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![iPass SmartConnect nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Configurazione del provisioning automatico degli utenti su iPass SmartConnect 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in iPass SmartConnect in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
>  Puoi anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per BitaBI , seguendo le istruzioni fornite [nell'esercitazione sul single sign-on di iPass SmartConnect](ipasssmartconnect-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per iPass SmartConnect in Azure AD:To configure automatic user provisioning for iPass SmartConnect in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **iPass SmartConnect**.

    ![Collegamento iPass SmartConnect nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://openmobile.ipass.com/moservices/scim/v1` amministratore immettere **l'input**in URL tenant. Immettere il token di connessione recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a iPass SmartConnect.Click **Test Connection** to ensure Azure AD can connect to iPass SmartConnect. Se la connessione non riesce, assicurati che l'account iPass SmartConnect disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con iPass SmartConnect**.

    ![Mappature utente di iPass SmartConnect](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a iPass SmartConnect nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in iPass SmartConnect per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Mappature utente di iPass SmartConnect](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per iPass SmartConnect, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for iPass SmartConnect, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning su iPass SmartConnect scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in iPass SmartConnect.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on iPass SmartConnect.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* iPass SmartConnect accetta solo nomi utente i cui domini sono registrati nella console di amministrazione di iPass SmartConnect.  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
