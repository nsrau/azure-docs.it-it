---
title: 'Esercitazione: Configurare Druva per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Druva.
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
ms.openlocfilehash: 5579a9d96828caa1453547e7c2e11b8f0d717d2a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359307"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Esercitazione: Configurare Druva per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Druva e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il de-provisioning di utenti e/o gruppi in Druva.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di Druva](https://www.druva.com/products/pricing-plans/).
* Account utente in Druva con autorizzazioni di amministratore.

## <a name="assigning-users-to-druva"></a>Assegnazione di utenti a Druva

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Druva. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Druva seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Suggerimenti importanti per l'assegnazione di utenti a Druva

* È consigliabile assegnare un singolo utente di Azure AD a Druva per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Druva, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-druva-for-provisioning"></a>Configurare Druva per il provisioning

Prima di configurare Druva per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in Druva.

1. Accedere alla [console di amministrazione di Druva](https://console.druva.com). Passare a **Druva > inSync**.

    ![Console di amministrazione di Druva](media/druva-provisioning-tutorial/menubar.png)

2. Passare a **Manage** > **Deployments** > **Users** (Gestisci > Distribuzioni > Utenti).

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Screenshot della console di amministrazione di Druva. L'opzione Manage (Gestisci) è evidenziata e il menu Manage è visibile. In tale menu l'opzione Users (Utenti) in Deployments (Distribuzioni) è evidenziata." border="false":::

3.  Passare a **Settings** (Impostazioni). Fare clic su **Generate Token** (Genera token).

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Screenshot di una pagina della console di amministrazione di Druva. L'opzione Settings (Impostazioni) è evidenziata e la scheda Settings è aperta. Il pulsante Generate token (Genera token) è evidenziato." border="false":::

4.  Copiare il valore indicato in **Auth token** (Token di autenticazione). Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Druva nel portale di Azure.
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Screenshot della pagina Create token nella console di amministrazione di Druva. Per copiare il valore di Auth token (Token di autenticazione), è disponibile un collegamento denominato Copy Token (Copia token)." border="false":::

## <a name="add-druva-from-the-gallery"></a>Aggiungere Druva dalla raccolta

Per configurare Druva per il provisioning utenti automatico con Azure AD, è necessario aggiungere Druva dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Druva dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Druva**, selezionare **Druva** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Druva nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Configurazione del provisioning utenti automatico in Druva 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Druva in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Druva, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Druva](druva-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Druva in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Druva**.

    ![Collegamento di Druva nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5.  Nella sezione Credenziali amministratore immettere `https://apis.druva.com/insync/scim` in **URL tenant**. Immettere il valore indicato in **Auth token** (Token di autenticazione) in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Druva. Se la connessione non riesce, verificare che l'account Druva abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Druva** (Sincronizza utenti di Azure Active Directory con Druva).

    ![Mapping utente di Druva](media/druva-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Druva nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Druva per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in Druva](media/druva-provisioning-tutorial/userattribute.png)


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Druva, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Druva selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Druva.

    Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Limitazioni dei connettori

* **email** è un attributo obbligatorio in Druva. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md).
