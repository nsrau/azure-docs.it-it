---
title: 'Esercitazione: Configurare BitaBI per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e l'annullamento del provisioning automatico degli account utente in BitaBI.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d0d38abe-c041-482a-9d3f-ca340678c226
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ad9176614c4a5235e5138444d4197286204a747f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059221"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Esercitazione: Configurare BitaBI per il provisioning automatico degli utentiTutorial: Configure BitaBI for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in BitaBI e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e l'annullamento del provisioning automatico di utenti e/o gruppi in BitaBI.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un inquilino BitaBI](https://bitabiz.dk/en/price/).
* Un account utente in BitaBI con autorizzazioni di amministratore.

## <a name="assigning-users-to-bitabiz"></a>Assegnazione di utenti a BitaBI

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a BitaBI. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a BitaBI seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Suggerimenti importanti per l'assegnazione degli utenti a BitaBI

* È consigliabile assegnare un singolo utente di Azure AD a BitaBI per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a BitaBI, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-bitabiz-for-provisioning"></a>Configurare BitaBI per il provisioning

Prima di configurare BitaBI per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in BitaBI.

1. Accedere alla [Console di amministrazione BitaBI](https://www.bitabiz.com/login?lang=en). Fare clic su **SETUP ADMIN** (CONFIGURA AMMINISTRATORE).

    ![Console di amministrazione BitaBI](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  Passare a **INTEGRATION**.

    ![Console di amministrazione BitaBI](media/bitabiz-provisioning-tutorial/integration.png)

2.  Passare a **Provisioning di Microsoft Azure AD**.  Selezionare **Abilitato** in Provisioning automatico degli utenti. Copiare i valori per **URL dell'endpoint di provisioning SCIM** e **Gettone bearer**. Questi valori verranno immessi nei campi URL tenant e Token segreto nella scheda Provisioning dell'applicazione BitaBI nel portale di Azure.

    ![Aggiunta di SCIM a BitaBI](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Aggiungere BitaBI dalla galleria

Per configurare BitaBI per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere BitaBI dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere BitaBI dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add BitaBI from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettete **BitaBI**, selezionate **BitaBI** nel pannello dei risultati, quindi fate clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![BitaBIZ nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Configurazione del provisioning automatico degli utenti su BitaBI 

In questa sezione vengono illustrati i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in BitaBI in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per BitaBI , seguendo le istruzioni fornite [nell'esercitazione sull'accesso single BitaBI](BitaBIZ-tutorial.md). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per BitaBI in Azure AD:To configure automatic user provisioning for BitaBI in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **BitaBIZ**.

    ![Collegamento di BitaBIZ nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore immettere i valori **URL dell'endpoint di provisioning SCIM** e **Gettario bearer** recuperati in precedenza rispettivamente in URL tenant e Token segreto. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a BitaBI. Se la connessione non riesce, verificare che l'account BitaBI abbia le autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con BitaBI**.

    ![Mapping degli utenti BitaBI](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a BitaBI nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in BitaBI per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente BitaBI](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per BitaBI, modificare **lo stato** di provisioning **su Attivato** nella sezione **Impostazioni.**

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in BitaBI scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in BitaBI.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* BitaBI richiede **userName**, **email**, **firstName** e **lastName** come attributi obbligatori. 
* Attualmente BitaBI non supporta le eliminazioni forzate.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Scopri come esaminare i log e ottenere report sulle attività](../app-provisioning/check-status-user-account-provisioning.md)di provisioning.
