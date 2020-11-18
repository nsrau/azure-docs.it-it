---
title: 'Esercitazione: Configurare Templafy per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Templafy.
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
ms.openlocfilehash: 754e23a7d3f4bdf27f273927a5bc3b3658940d90
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359235"
---
# <a name="tutorial-configure-templafy-for-automatic-user-provisioning"></a>Esercitazione: Configurare Templafy per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Templafy e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il de-provisioning di utenti e/o gruppi in Templafy.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di Templafy](https://www.templafy.com/pricing/).
* Un account utente in Templafy con autorizzazioni di amministratore.

## <a name="assigning-users-to-templafy"></a>Assegnazione di utenti a Templafy

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Templafy. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Templafy seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy"></a>Suggerimenti importanti per l'assegnazione di utenti a Templafy

* È consigliabile assegnare un singolo utente di Azure AD a Templafy per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Templafy, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-templafy-for-provisioning"></a>Configurare Templafy per il provisioning

Prima di configurare Templafy per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in Templafy.

1. Accedere alla console di amministrazione di Templafy. Fare clic su **Administration** (Amministrazione).

    ![Console di amministrazione di Templafy](media/templafy-provisioning-tutorial/image00.png)

2. Fare clic su **Authentication Method** (Metodo di autenticazione).

    ![Screenshot della sezione di amministrazione di Templafy con l'opzione Authentication Method (Metodo di autenticazione) evidenziata.](media/templafy-provisioning-tutorial/image01.png)

3. Copiare il valore **SCIM Api Key** (Chiave API SCIM). Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Templafy nel portale di Azure.

    ![Screenshot della chiave API SCIM.](media/templafy-provisioning-tutorial/image02.png)

## <a name="add-templafy-from-the-gallery"></a>Aggiungere Templafy dalla raccolta

Per configurare Templafy per il provisioning utenti automatico con Azure AD, è necessario aggiungere Templafy dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere Templafy dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Templafy**, selezionare **Templafy** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Templafy nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-templafy"></a>Configurazione del provisioning utenti automatico in Templafy 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Templafy in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Templafy, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Templafy](templafy-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-templafy-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Templafy in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Templafy**.

    ![Collegamento di Templafy nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://scim.templafy.com/scim` in **URL tenant**. Immettere il valore **SCIM API Key** (Chiave API SCIM) recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Templafy. Se la connessione non riesce, verificare che l'account Templafy abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Templafy** (Sincronizza utenti di Azure Active Directory in Templafy).

    ![Mapping utente in Templafy](media/templafy-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Templafy nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Templafy per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in Templafy](media/templafy-provisioning-tutorial/userattribute.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Templafy** (Sincronizza gruppi di Azure Active Directory con Templafy).

    ![Mapping dei gruppi di Templafy](media/templafy-provisioning-tutorial/groupmapping.png)

11. Esaminare gli attributi gruppo sincronizzati da Azure AD a Templafy nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Templafy per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppo di Templafy](media/templafy-provisioning-tutorial/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Templafy, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Templafy selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Templafy.

    Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
