---
title: 'Esercitazione: Configurare iProva per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in iProva.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to iProva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057502"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Esercitazione: Configurare iProva per il provisioning automatico degli utentiTutorial: Configure iProva for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in iProva e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in iProva.The objective of this tutorial is to demonstrate the steps to be performed in iProva and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and/o provision users to iProva.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant iProva](https://www.iProva.com/)
* Un account utente in iProva con autorizzazioni di amministratore.

## <a name="assigning-users-to-iprova"></a>Assegnazione di utenti a iProvaAssigning users to iProva

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a iProva. Una volta deciso, puoi assegnare questi utenti e/o gruppi a iProva seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Suggerimenti importanti per l'assegnazione di utenti a iProva

* È consigliabile assegnare un singolo utente di Azure AD a iProva per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a iProva, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-iprova-for-provisioning"></a>Configurare iProva per il provisioning

1. Accedi a [iProva Admin Console](https://www.iProva.com/). Passare **a Vai a > Gestione applicazioni**.

    ![Console di amministrazione iProva](media/iprova-provisioning-tutorial/admin.png)

2.  Fare clic su **Gestione utenti esterni**.

    ![iProva Aggiungi SCIM](media/iprova-provisioning-tutorial/external.png)

3. Per aggiungere un nuovo provider, fare clic sull'icona **più.** Nella nuova finestra di dialogo **Aggiungi provider** specificare **Un Titolo**. È possibile scegliere di aggiungere la restrizione di **accesso basata su IP**. Fare clic sul pulsante **OK.**

    ![iProva aggiungi nuovo](media/iprova-provisioning-tutorial/add.png)

    ![provider di aggiunta iProva](media/iprova-provisioning-tutorial/addprovider.png)

4.  Fare clic sul pulsante **Token permanente.** Copiare il **token permanente** e salvarlo come questo sarà l'unico momento in cui è possibile visualizzarlo. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione iProva nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your iProva application in the Azure portal.

    ![iProva Crea token](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Aggiungere iProva dalla raccolta

Prima di configurare iProva per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere iProva dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere iProva dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add iProva from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **iProva**, selezionare **iProva** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![iProva nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Configurazione del provisioning automatico degli utenti su iProvaConfiguring automatic user provisioning to iProva 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in iProva in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per iProva seguendo le istruzioni fornite [nell'esercitazione sul single sign-on iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per iProva in Azure AD:To configure automatic user provisioning for iProva in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **iProva**.

    ![Collegamento iProva nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://identitymanagement.services.iProva.nl/scim` amministratore immettere **l'input**in URL tenant. Immettere il valore del **token permanenti** recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a iProva.Click **Test Connection** to ensure Azure AD can connect to iProva. Se la connessione non riesce, assicurati che l'account iProva disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con iProva.**

    ![Mapping utente iProva](media/iprova-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a iProva nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in iProva per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente iProva](media/iprova-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con iProva.**

    ![Mapping dei gruppi iProvaiProva](media/iprova-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a iProva nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con i gruppi in iProva per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo iProvaiProva Group Attributes](media/iprova-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per iProva, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for iProva, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in iProva scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in iProva.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on iProva.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

