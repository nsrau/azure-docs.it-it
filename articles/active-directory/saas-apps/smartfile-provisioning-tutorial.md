---
title: 'Esercitazione: Configurare SmartFile per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in SmartFile.
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
ms.openlocfilehash: fca18a58ccb8d4e2f10b5db606ad01a97c2d5bac
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359930"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Esercitazione: Configurare SmartFile per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in SmartFile e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in SmartFile.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di SmartFile](https://www.SmartFile.com/pricing/).
* Un account utente in SmartFile con autorizzazioni di amministratore.

## <a name="assigning-users-to-smartfile"></a>Assegnazione di utenti a SmartFile

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a SmartFile. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a SmartFile seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Suggerimenti importanti per l'assegnazione di utenti a SmartFile

* È consigliabile assegnare un singolo utente di Azure AD a SmartFile per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a SmartFile, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-smartfile-for-provisioning"></a>Configurare SmartFile per il provisioning

Prima di configurare SmartFile per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in SmartFile.

1. Accedere alla console di amministrazione di SmartFile. Passare all'angolo in alto a destra della console di amministrazione di SmartFile. Selezionare **Product Key** (Codice Product Key).

    ![Console di amministrazione di SmartFile](media/smartfile-provisioning-tutorial/login.png)

2. Per generare un token di connessione, copiare i valori di **Product Key** (Codice Product Key) e **Product Password** (Password del prodotto). Incollarli nel Blocco note separandoli con due punti.
    
     ![Screenshot della sezione codice Product Key con le caselle di testo codice Product Key e Product Password evidenziate.](media/smartfile-provisioning-tutorial/auth.png)

    ![Screenshot del testo non crittografato che mostra il codice Product Key e la password del prodotto separati da due punti.](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Aggiungere SmartFile dalla raccolta

Per configurare SmartFile per il provisioning utenti automatico con Azure AD, è necessario aggiungere SmartFile dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere SmartFile dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **SmartFile**, selezionare **SmartFile** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SmartFile nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Configurazione del provisioning utenti automatico per SmartFile 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in SmartFile in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per SmartFile, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per SmartFile](SmartFile-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Per configurare il provisioning utenti automatico per SmartFile in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SmartFile**.

    ![Collegamento di SmartFile nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni di gestione con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5.  Nella sezione **Credenziali amministratore** immettere `https://<SmartFile sitename>.smartfile.com/ftp/scim` in **URL tenant**. Ad esempio, sarà simile a `https://demo1test.smartfile.com/ftp/scim`. Immettere il valore del **token di connessione** (ProductKey:ProductPassword) recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a SmartFile. Se la connessione non riesce, verificare che l'account SmartFile abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to SmartFile** (Sincronizza utenti di Azure Active Directory con SmartFile).

    ![Mapping utente in SmartFile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD a SmartFile nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in SmartFile per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in SmartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to SmartFile** (Sincronizza gruppi di Azure Active Directory con SmartFile).

    ![Mapping dei gruppi in SmartFile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Esaminare gli attributi gruppo sincronizzati tra Azure AD a SmartFile nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in SmartFile per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppo in SmartFile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per SmartFile, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in SmartFile selezionando i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in SmartFile.

    Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Limitazioni dei connettori

* SmartFile supporta solo le eliminazioni definitive. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

 [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
