---
title: 'Esercitazione: Configurare comeet Recruiting Software per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Comeet Recruiting Software.
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
ms.openlocfilehash: f427fb75cfaeda79b037c327992e4ad482a7e689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058335"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Esercitazione: Configurare comeet Recruiting Software per il provisioning automatico degli utenti

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Comeet Recruiting Software e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Comeet Recruiting Software.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Comeet Recruiting Software](https://www.comeet.co/)
* Un account utente in Comeet Recruiting Software con autorizzazioni di amministratore.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Aggiungi Comeet Recruiting Software dalla galleria

Prima di configurare Comeet Recruiting Software per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Comeet Recruiting Software dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Comeet Recruiting Software dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Comeet Recruiting Software from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Comeet Recruiting Software**, selezionare **Comeet Recruiting Software (Software** di selezione in arrivo) nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Comeet Recruiting Software nell'elenco dei risultati](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Assegnazione di utenti a Comeet Recruiting Software

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Comeet Recruiting Software. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Comeet Recruiting Software seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Suggerimenti importanti per l'assegnazione di utenti a Comeet Recruiting Software

* È consigliabile assegnare un singolo utente di Azure AD a Comeet Recruiting Software per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Comeet Recruiting Software, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Configurazione del provisioning automatico degli utenti per comeet Recruiting Software 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Comeet Recruiting Software in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Comeet Recruiting Software, seguendo le istruzioni fornite nell'esercitazione Single [Sign-On comeet Recruiting Software](comeetrecruitingsoftware-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Comeet Recruiting Software in Azure AD:To configure automatic user provisioning for Comeet Recruiting Software in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Comeet Recruiting Software**.

    ![Nell'elenco delle applicazioni selezionare il collegamento Comeet Recruiting Software](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL **tenant** e il **token segreto** dell'account del software comeet Recruiting, come descritto nel passaggio 6.

6. Nella console di [amministrazione Comeet Recruiting Software](https://app.comeet.co/)passare a **Comeet > Settings > Authentication > Microsoft Azure**e copiare il token **segreto per il** valore company nel campo Secret **Token** in Azure AD.

    ![Comeet Reclutamento Software Provisioning](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Quando si popolano i campi illustrati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Comeet Recruiting Software. Se la connessione non riesce, verificare che l'account Comeet Recruiting Software disponga delle autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-token.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare Sincronizza utenti di Azure Active Directory da **comeet**.

    ![Comeet Reclutamento Software Mappings](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Comeet Recruiting Software nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Comeet Recruiting Software per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Comeet Selezione degli attributi del gruppo software](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Comeet Recruiting Software, modificare lo stato di provisioning **su On** nella sezione Impostazioni.To enable the Azure AD provisioning service for Comeet Recruiting Software, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Comeet Recruiting Software scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD nel software comeet Recruiting.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Comeet Recruiting Software.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Comeet Recruiting Software attualmente non supporta i gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

