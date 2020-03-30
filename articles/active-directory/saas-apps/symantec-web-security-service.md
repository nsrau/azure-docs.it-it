---
title: 'Esercitazione: Configurare il servizio WSS (Symantec Web Security Service) per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Symantec Web Security Service (WSS).
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
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063119"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Esercitazione: Configurare Symantec Web Security Service (WSS) per il provisioning automatico degli utenti

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Symantec Web Security Service (WSS) e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi nel servizio WsS (Symantec Web Security Service).

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant del servizio di sicurezza Web Symantec (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Un account utente in Symantec Web Security Service (WSS) con autorizzazioni di amministratore.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Assegnazione di utenti a Symantec Web Security Service (WSS)

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Symantec Web Security Service (WSS). Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Symantec Web Security Service (WSS) seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Suggerimenti importanti per l'assegnazione di utenti a Symantec Web Security Service (WSS)

* È consigliabile assegnare un singolo utente di Azure AD a Symantec Web Security Service (WSS) per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Symantec Web Security Service (WSS), è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Configurare Symantec Web Security Service (WSS) per il provisioning

Prima di configurare Symantec Web Security Service (WSS) per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in Symantec Web Security Service (WSS).

1. Accedere alla console di amministrazione del [servizio Symantec Web Security](https://portal.threatpulse.com/login.jsp). Passare a**Servizio** **soluzioni** > .

    ![Servizio di protezione Web Symantec (WSS)](media/symantec-web-security-service/service.png)

2. Passare a**Integrazioni** > di **manutenzione** > account**Nuova integrazione**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selezionare **Utenti di terze parti & sincronizzazione dei gruppi**. 

    ![Symantec Web Security Service](media/symantec-web-security-service/third-party-users.png)

4.  Copiare **l'URL SCIM** e il **token**. Questi valori verranno immessi nel campo **URL tenant** e **token segreto** nella scheda Provisioning dell'applicazione Symantec Web Security Service (WSS) nel portale di Azure.

    ![Symantec Web Security Service](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Aggiungere il servizio WsS (Web Security Service) Symantec dalla raccolta

Per configurare Symantec Web Security Service (WSS) per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Symantec Web Security Service (WSS) dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere il servizio WsS (Web Security Service) Symantec dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Servizio protezione Web Symantec**, selezionare **Servizio di sicurezza Web Symantec** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Symantec Web Security Service (WSS) nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Configurazione del provisioning automatico degli utenti per Symantec Web Security Service (WSS)

In questa sezione vengono illustrati i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Symantec Web Security Service (WSS) in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Symantec Web Security Service (WSS), seguendo le istruzioni fornite [nell'esercitazione sull'accesso Single Sign-On di Symantec Web Security Service (WSS).](symantec-tutorial.md) Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Symantec Web Security Service (WSS) in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Servizio di sicurezza Web Symantec**.

    ![Collegamento di Symantec Web Security Service (WSS) nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore immettere i valori URL E **Token** e Token **SCIM** recuperati in precedenza rispettivamente in **URL tenant** e Token **segreto.** Fare clic su Test connessione per verificare che Azure AD possa connettersi al servizio Di sicurezza Web Symantec.Click **Test Connection** to ensure Azure AD can connect to Symantec Web Security Service. Se la connessione non riesce, verificare che l'account Symantec Web Security Service (WSS) disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Symantec Web Security Service (WSS).**

    ![Mapping utenti wsS (Web Security Service) di Symantec](media/symantec-web-security-service/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Symantec Web Security Service (WSS) nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con gli account utente in Symantec Web Security Service (WSS) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Mapping utenti wsS (Web Security Service) di Symantec](media/symantec-web-security-service/userattribute.png)

10. Nella sezione **Mapping** selezionare **Sincronizza gruppi di Azure Active Directory con Symantec Web Security Service**.

    ![Mapping utenti wsS (Web Security Service) di Symantec](media/symantec-web-security-service/groupmapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Symantec Web Security Service (WSS) nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con i gruppi in Symantec Web Security Service (WSS) per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Mapping utenti wsS (Web Security Service) di Symantec](media/symantec-web-security-service/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per il servizio di sicurezza Web Symantec, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Symantec Web Security Service, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Symantec Web Security Service (WSS) scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning degli utenti e/o dei gruppi, vedere [Quanto tempo è necessario per eseguire il provisioning degli utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **Stato corrente** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD nel servizio WSS (Web Security Service). Per ulteriori informazioni, consultate [Controllare lo stato del provisioning degli utenti.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Per leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utente.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
