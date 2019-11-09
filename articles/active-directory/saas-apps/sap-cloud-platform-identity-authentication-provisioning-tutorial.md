---
title: 'Esercitazione: configurare SAP Cloud Platform Identity Authentication per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in SAP Cloud Platform Identity Authentication.
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
ms.openlocfilehash: 266e68b2378db7148649fd4067f1da6172932367
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833845"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Esercitazione: configurare SAP Cloud Platform Identity Authentication per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in SAP Cloud Platform Identity Authentication e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in SAP Cloud Platform Identity Authentication.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/pricing.html)
* Un account utente in SAP Cloud Platform Identity Authentication con autorizzazioni di amministratore.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Assegnazione di utenti a SAP Cloud Platform Identity Authentication

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a SAP Cloud Platform Identity Authentication. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a SAP Cloud Platform Identity Authentication seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Suggerimenti importanti per l'assegnazione di utenti a SAP Cloud Platform Identity Authentication

* È consigliabile assegnare un singolo Azure AD utente a SAP Cloud Platform Identity Authentication per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a SAP Cloud Platform Identity Authentication, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Configurare SAP Cloud Platform Identity Authentication per il provisioning

1. Accedere alla console di [amministrazione di SAP Cloud Platform Identity Authentication](https://sapmsftintegration.accounts.ondemand.com/admin). Passare a **utenti & autorizzazioni > Administrators**.

    ![Console di amministrazione di SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Creare un utente amministratore e selezionare l'utente.  

3.  In Configura autorizzazioni, attivare l'interruttore su **Gestisci utenti** e **Gestisci gruppi**.

    ![SAP Cloud Platform Identity Authentication aggiungere SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Si riceverà un messaggio di posta elettronica per l'attivazione dell'account e si imposterà una password per il **servizio SAP Cloud Platform Identity Authentication**.

4.  Copiare l' **ID utente** e la **password**. Questi valori verranno immessi rispettivamente nei campi nome utente amministratore e password amministratore nella scheda provisioning dell'applicazione SAP Cloud Platform Identity Authentication nel portale di Azure.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Aggiungere SAP Cloud Platform Identity Authentication dalla raccolta

Prima di configurare SAP Cloud Platform Identity Authentication per il provisioning utenti automatico con Azure AD, è necessario aggiungere SAP Cloud Platform Identity Authentication dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere SAP Cloud Platform Identity Authentication dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **SAP Cloud Platform Identity Authentication**, selezionare **SAP Cloud Platform Identity Authentication** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAP Cloud Platform Identity Authentication nell'elenco di risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Configurazione del provisioning utenti automatico in SAP Cloud Platform Identity Authentication 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in SAP Cloud Platform Identity Authentication in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per SAP Cloud Platform Identity Authentication, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Per configurare il provisioning utenti automatico per SAP Cloud Platform Identity Authentication in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SAP Cloud Platform Identity Authentication**.

    ![Collegamento di SAP HANA Cloud Platform Identity Authentication nell'elenco di applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://<tenantID>.accounts.ondemand.com/service/scim ` in **URL tenant**. Immettere rispettivamente i valori di **ID utente** e **password** recuperati in precedenza in **nome utente amministratore** e **password amministratore** . Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a SAP Cloud Platform Identity Authentication. Se la connessione non riesce, verificare che l'account di SAP Cloud Platform Identity Authentication disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a SAP Cloud Platform Identity Authentication**.

    ![Mapping utente di SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a SAP Cloud Platform Identity Authentication nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in SAP Cloud Platform Identity Authentication per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per SAP Cloud Platform Identity Authentication, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in SAP Cloud Platform Identity Authentication scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in SAP Cloud Platform Identity Authentication.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* L'endpoint SCIM di SAP Cloud Platform Identity Authentication richiede che determinati attributi siano di formato specifico. Per ulteriori informazioni su questi attributi e sul relativo formato specifico, vedere [qui](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

