---
title: "Esercitazione: Configurare l'autenticazione dell'identità della piattaforma cloud SAP per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft"
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in SAP Cloud Platform Identity Authentication.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060426"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Esercitazione: Configurare l'autenticazione dell'identità della piattaforma cloud SAP per il provisioning automatico degli utentiTutorial: Configure SAP Cloud Platform Identity Authentication for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in SAP Cloud Platform Identity Authentication e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in SAP Cloud Platform Identity Authentication.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di autenticazione dell'identità della piattaforma cloud SAP](https://cloudplatform.sap.com/pricing.html)
* Un account utente in SAP Cloud Platform Identity Authentication con autorizzazioni di amministratore.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Assegnazione di utenti a SAP Cloud Platform Identity Authentication

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a SAP Cloud Platform Identity Authentication. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a SAP Cloud Platform Identity Authentication seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Suggerimenti importanti per l'assegnazione di utenti a SAP Cloud Platform Identity Authentication

* È consigliabile assegnare un singolo utente di Azure AD a SAP Cloud Platform Identity Authentication per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a SAP Cloud Platform Identity Authentication, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Configurare l'autenticazione DELL'identità della piattaforma CLOUD SAP per il provisioning

1. Accedere alla [Console di amministrazione di SAP Cloud Platform Identity Authentication](https://sapmsftintegration.accounts.ondemand.com/admin). Passare a **Utenti & Autorizzazioni > Amministratori**.

    ![Console di amministrazione autenticazione IDentità basata su piattaforma SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Creare un utente amministratore e selezionare l'utente.  

3.  In Configura autorizzazioni, attivare il pulsante di attivazione/disattivazione su **Gestisci utenti** e **Gestisci gruppi.**

    ![L'autenticazione dell'identità della piattaforma SAP Aggiungi SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Riceverai un'e-mail per attivare il tuo account e impostare una password per **SAP Cloud Platform Identity Authentication Service**.

4.  Copiare **l'ID utente** e la **password**. Questi valori verranno immessi rispettivamente nei campi Nome utente amministratore e Password amministratore rispettivamente nella scheda Provisioning dell'applicazione SAP Cloud Platform Identity Authentication nel portale di Azure.These values will be entered in the Admin Username and Admin Password fields respectively in the Provisioning tab of your SAP Cloud Platform Identity Authentication application in the Azure portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Aggiungere SAP Cloud Platform Identity Authentication dalla raccolta

Prima di configurare SAP Cloud Platform Identity Authentication per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere SAP Cloud Platform Identity Authentication dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere SAP Cloud Platform Identity Authentication dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add SAP Cloud Platform Identity Authentication from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **SAP Cloud Platform Identity Authentication**, selezionare SAP Cloud Platform Identity **Authentication** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAP Cloud Platform Identity Authentication nell'elenco di risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Configurazione del provisioning automatico degli utenti per l'autenticazione dell'identità di SAP Cloud Platform 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in SAP Cloud Platform Identity Authentication in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per SAP Cloud Platform Identity Authentication , seguendo le istruzioni fornite [nell'esercitazione Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)di Autenticazione basata su SAP. Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per l'autenticazione dell'identità della piattaforma cloud SAP in Azure AD:To configure automatic user provisioning for SAP Cloud Platform Identity Authentication in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SAP Cloud Platform Identity Authentication**.

    ![Collegamento di SAP HANA Cloud Platform Identity Authentication nell'elenco di applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://<tenantID>.accounts.ondemand.com/service/scim ` amministratore immettere **l'input**in URL tenant. Immettere i valori **User ID** e **Password** recuperati in precedenza rispettivamente in **Admin Username** e **Admin Password.** Fare clic su Test connessione per verificare che Azure AD possa connettersi a SAP Cloud Platform Identity Authentication.Click **Test Connection** to ensure Azure AD can connect to SAP Cloud Platform Identity Authentication. Se la connessione non riesce, verificare che l'account SAP Cloud Platform Identity Authentication disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di Azure Active Directory con **L'autenticazione dell'identità della piattaforma cloud SAP.**

    ![Mapping utenti dell'autenticazione dell'identità della piattaforma SAP CLOUD](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a SAP Cloud Platform Identity Authentication nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in SAP Cloud Platform Identity Authentication per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente dell'autenticazione dell'identità della piattaforma SAP SAP](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per l'autenticazione dell'identità della piattaforma cloud SAP, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for SAP Cloud Platform Identity Authentication, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in SAP Cloud Platform Identity Authentication scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in SAP Cloud Platform Identity Authentication.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on SAP Cloud Platform Identity Authentication.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* L'endpoint SCIM di SAP Cloud Platform Identity Authentication richiede che determinati attributi siano di un formato specifico. Puoi saperne di più su questi attributi e sul loro formato specifico [qui](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

