---
title: 'Esercitazione: configurare brivo OnAir Identity Connector per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in brivo OnAir Identity Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: c2db09d48f9efc22134f930b367ef16eda62ac6f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174512"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Esercitazione: configurare brivo OnAir Identity Connector per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in brivo OnAir Identity Connector e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in brivo OnAir Identity Connector.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di brivo OnAir Identity Connector](https://www.brivo.com/quote)
* Un account utente in brivo OnAir Identity Connector con autorizzazioni di amministratore senior.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Assegnazione di utenti a brivo OnAir Identity Connector

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a brivo OnAir Identity Connector. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a brivo OnAir Identity Connector seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Suggerimenti importanti per l'assegnazione di utenti a brivo OnAir Identity Connector

* È consigliabile assegnare un singolo utente Azure AD a brivo OnAir Identity Connector per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a brivo OnAir Identity Connector, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Configurare brivo OnAir Identity Connector per il provisioning

1.  Accedere alla console di [amministrazione di brivo OnAir Identity Connector](https://acs.brivo.com/login/). Passare a **account > impostazioni account**.

    ![Console di amministrazione di brivo OnAir Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  Fare clic sulla scheda **Azure ad** . Nella pagina Dettagli **Azure ad** immettere nuovamente la password dell'account amministratore senior. Fare clic su **Invia**.

    ![Connettore di identità brivo OnAir Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.  Fare clic sul pulsante **Copia token** e salvare il **token Secret**. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione brivo OnAir Identity Connector nel portale di Azure.

    ![Token del connettore di identità brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Aggiungere brivo OnAir Identity Connector dalla raccolta

Prima di configurare brivo OnAir Identity Connector per il provisioning utenti automatico con Azure AD, è necessario aggiungere brivo OnAir Identity Connector dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere brivo OnAir Identity Connector dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **brivo OnAir Identity Connector**, selezionare **brivo OnAir Identity Connector** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Connettore identità OnAir brivo nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Configurazione del provisioning utenti automatico in brivo OnAir Identity Connector 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in brivo OnAir Identity Connector in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Per configurare il provisioning utenti automatico per brivo OnAir Identity Connector in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **brivo OnAir Identity Connector**.

    ![Collegamento del connettore di identità brivo OnAir nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://scim.brivo.com/ActiveDirectory/v2/` in **URL tenant**. Immettere il valore del **token di autenticazione scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a brivo OnAir Identity Connector. Se la connessione non riesce, verificare che l'account connettore brivo OnAir Identity disponga di autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a brivo OnAir Identity Connector**.

    ![Mapping utente del connettore di identità brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Esaminare gli attributi utente sincronizzati da Azure AD a brivo OnAir Identity Connector nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in brivo OnAir Identity Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di brivo OnAir Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to brivo OnAir Identity Connector**.

    ![Mapping del gruppo di connettori di identità brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a brivo OnAir Identity Connector nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in brivo OnAir Identity Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppo connettore identità brivo OnAir](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per brivo OnAir Identity Connector, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in brivo OnAir Identity Connector scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad in brivo OnAir Identity Connector.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

