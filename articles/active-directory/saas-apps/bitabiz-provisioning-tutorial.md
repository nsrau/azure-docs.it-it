---
title: 'Esercitazione: Configurare BitaBIZ per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in BitaBIZ.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3d17d4dd88e29440304989b8c37eaa81125d1812
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357556"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Esercitazione: Configurare BitaBIZ per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in BitaBIZ e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in BitaBIZ.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di BitaBIZ](https://bitabiz.dk/en/price/).
* Un account utente in BitaBIZ con autorizzazioni di amministratore.

## <a name="assigning-users-to-bitabiz"></a>Assegnazione di utenti a BitaBIZ

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a BitaBIZ. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a BitaBIZ seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Suggerimenti importanti per l'assegnazione di utenti a BitaBIZ

* È consigliabile assegnare un singolo utente di Azure AD a BitaBIZ per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a BitaBIZ, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-bitabiz-for-provisioning"></a>Configurare BitaBIZ per il provisioning

Prima di configurare BitaBIZ per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in BitaBIZ.

1. Accedere alla [console di amministrazione di BitaBIZ](https://www.bitabiz.com/login?lang=en). Fare clic su **SETUP ADMIN** (CONFIGURA AMMINISTRATORE).

    :::image type="content" source="media/bitabiz-provisioning-tutorial/setup-admin.png" alt-text="Screenshot della console di amministrazione di BitaBIZ, con l'opzione Setup admin evidenziata." border="false":::

2.  Passare a **INTEGRATION** (Integrazione).

    :::image type="content" source="media/bitabiz-provisioning-tutorial/integration.png" alt-text="Screenshot della console di amministrazione di BitaBIZ, con l'opzione Integration evidenziata." border="false":::

2.  Passare a **Microsoft Azure AD Provisioning** (Provisioning di Microsoft Azure AD).  In Automatic user provisioning (Provisioning utente automatico) selezionare **Enabled** (Abilitato). Copiare i valori di **SCIM Provisioning endpoint URL** (URL dell'endpoint di provisioning SCIM) e **Bearer Token** (Token di connessione). Questi valori verranno immessi nei campi URL tenant e Token segreto nella scheda Provisioning dell'applicazione BitaBIZ nel portale di Azure.

    ![Aggiunta di SCIM in BitaBIZ](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Aggiungere BitaBIZ dalla raccolta

Per configurare BitaBIZ per il provisioning utenti automatico con Azure AD, è necessario aggiungere BitaBIZ dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere BitaBIZ dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **BitaBIZ**, selezionare **BitaBIZ** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![BitaBIZ nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Configurazione del provisioning utenti automatico in BitaBIZ 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in BitaBIZ in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per BitaBIZ, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per BitaBIZ](BitaBIZ-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Per configurare il provisioning utenti automatico per BitaBIZ in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **BitaBIZ**.

    ![Collegamento di BitaBIZ nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni di gestione con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore immettere i valori di **SCIM Provisioning endpoint URL** and **Bearer Token** recuperati in precedenza rispettivamente nei campi URL tenant e Token segreto. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a BitaBIZ. Se la connessione non riesce, verificare che l'account BitaBIZ abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to BitaBIZ** (Sincronizza utenti di Azure Active Directory con BitaBIZ).

    ![Mapping utente in BitaBIZ](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD e BitaBIZ nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in BitaBIZ per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in BitaBIZ](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per BitaBIZ, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in BitaBIZ scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in BitaBIZ.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Gli attributi **userName**, **email**, **firstName** e **lastName** sono obbligatori in BitaBIZ. 
* BitaBIZ non supporta attualmente le eliminazioni definitive.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md).
