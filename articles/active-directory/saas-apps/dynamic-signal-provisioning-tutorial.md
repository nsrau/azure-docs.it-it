---
title: 'Esercitazione: Configurare il segnale dinamico per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Segnale dinamico.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2ec91d42dff8f3a1fc4b036aa1c3ec77faf6a0fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058043"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Esercitazione: Configurare il segnale dinamico per il provisioning automatico degli utentiTutorial: Configure Dynamic Signal for automatic user provisioning

L'obiettivo di questa esercitazione è dimostrare i passaggi da eseguire in Segnale dinamico e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning automatico di utenti e/o gruppi in Segnale dinamico.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di segnale dinamico](https://dynamicsignal.com/)
* Un account utente in Segnale dinamico con autorizzazioni di amministratore.

## <a name="add-dynamic-signal-from-the-gallery"></a>Aggiungi segnale dinamico dalla galleria

Prima di configurare Dynamic Signal per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Dynamic Signal dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere il segnale dinamico dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Dynamic Signal from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Segnale dinamico**, selezionare **Segnale dinamico** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Dynamic Signal nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Assegnazione di utenti a segnale dinamicoAssigning users to Dynamic Signal

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Dynamic Signal.Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Dynamic Signal. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a segnale dinamico seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Suggerimenti importanti per l'assegnazione di utenti a Segnale dinamico

* È consigliabile assegnare un singolo utente di Azure AD a Dynamic Signal per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a segnale dinamico, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Configurazione del provisioning automatico degli utenti in Segnale dinamico 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Segnale dinamico in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per il segnale dinamico, seguendo le istruzioni fornite nell'esercitazione single [sign-on del segnale dinamico](dynamicsignal-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per il segnale dinamico in Azure AD:To configure automatic user provisioning for Dynamic Signal in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Dynamic Signal**.

    ![Collegamento di Dynamic Signal nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL **tenant** e il **token segreto** dell'account del segnale dinamico, come descritto nel passaggio 6.

6. Nella console di amministrazione Dynamic Signal, accedi a **Admin > Advanced > API**.

    ![Provisioning dinamico del segnale](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Copiare **l'URL dell'API SCIM** nell'URL **tenant.** Fare clic su **Genera nuovo token** per generare un **gettone Bearer** e copiare il valore in **Secret Token**.

    ![Provisioning dinamico del segnale](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. Al momento del popolamento dei campi illustrati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Segnale dinamico. Se la connessione non riesce, verificare che l'account Segnale dinamico disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con segnale dinamico**.

    ![Mappature utente segnale dinamico](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a segnale dinamico nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Segnale dinamico per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente segnale dinamico](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per il segnale dinamico, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Dynamic Signal, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Segnale dinamico scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD su Segnale dinamico.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Il segnale dinamico non supporta le eliminazioni permanenti degli utenti da Azure AD. To delete a user permanently in Dynamic Signal, the operation has to be made through the Dynamic Signal admin console UI. 
* Il segnale dinamico non supporta attualmente i gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

