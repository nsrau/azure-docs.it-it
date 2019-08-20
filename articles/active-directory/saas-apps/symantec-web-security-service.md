---
title: 'Esercitazione: Configurare Symantec Web Security Service (WSS) per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Symantec Web Security Service (WSS).
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
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: cfdf5487bcd4c33a21fca593fef829c58d2d099d
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576199"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Esercitazione: Configurare Symantec Web Security Service (WSS) per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Symantec Web Security Service (WSS) e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Symantec Web Security Servizio (WSS).

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di Symantec Web Security Service (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Un account utente in Symantec Web Security Service (WSS) con autorizzazioni di amministratore.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Assegnazione di utenti a Symantec Web Security Service (WSS)

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Symantec Web Security Service (WSS). Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a Symantec Web Security Service (WSS) seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Suggerimenti importanti per l'assegnazione di utenti a Symantec Web Security Service (WSS)

* È consigliabile assegnare un singolo Azure AD utente a Symantec Web Security Service (WSS) per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Symantec Web Security Service (WSS), è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Configurare Symantec Web Security Service (WSS) per il provisioning

Prima di configurare Symantec Web Security Service (WSS) per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in Symantec Web Security Service (WSS).

1. Accedere alla console di [amministrazione di Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Passare al**servizio** **soluzioni** > .

    ![Servizio di sicurezza Web Symantec (WSS)](media/symantec-web-security-service/service.png)

2. Passare a **account manutenzione** > **integrazioni** > **nuova integrazione**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selezionare **utenti di terze parti & sincronizzazione gruppi**. 

    ![Servizio di sicurezza Web Symantec](media/symantec-web-security-service/third-party-users.png)

4.  Copiare l' **URL** e il **token**di SCIM. Questi valori verranno immessi nel campo **URL tenant** e **token segreto** nella scheda provisioning dell'applicazione Symantec Web Security Service (WSS) nell'portale di Azure.

    ![Servizio di sicurezza Web Symantec](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Aggiungere Symantec Web Security Service (WSS) dalla raccolta

Per configurare Symantec Web Security Service (WSS) per il provisioning utenti automatico con Azure AD, è necessario aggiungere Symantec Web Security Service (WSS) dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Symantec Web Security Service (WSS) dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Symantec Web Security Service**, selezionare **Symantec Web Security Service** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Symantec Web Security Service (WSS) nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Configurazione del provisioning utenti automatico in Symantec Web Security Service (WSS)

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Symantec Web Security Service (WSS) in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per Symantec Web Security Service (WSS), seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di Symantec Web Security Service (WSS)](symantec-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Symantec Web Security Service (WSS) in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Symantec Web Security Service**.

    ![Collegamento di Symantec Web Security Service (WSS) nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione credenziali amministratore immettere l' **URL scim** e i valori dei **token** recuperati in precedenza rispettivamente in **URL tenant** e **token segreto** . Fare clic su **Test connessione** per verificare che Azure ad possibile connettersi a Symantec Web Security Service. Se la connessione non riesce, verificare che l'account Symantec Web Security Service (WSS) disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Symantec Web Security Service (WSS)** .

    ![Mapping utente di Symantec Web Security Service (WSS)](media/symantec-web-security-service/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Symantec Web Security Service (WSS) nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Symantec Web Security Service (WSS) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Mapping utente di Symantec Web Security Service (WSS)](media/symantec-web-security-service/userattribute.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Symantec Web Security Service**.

    ![Mapping utente di Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupmapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Symantec Web Security Service (WSS) nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Symantec Web Security Service (WSS) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Mapping utente di Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Symantec Web Security Service, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Symantec Web Security Service (WSS) scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning di utenti e/o gruppi, vedere [quanto tempo sarà necessario per il provisioning degli utenti](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in Symantec Web Security Service (WSS). Per altre informazioni, vedere [controllare lo stato del](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)provisioning dell'utente. Per leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
