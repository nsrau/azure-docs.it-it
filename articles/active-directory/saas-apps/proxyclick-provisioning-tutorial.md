---
title: 'Esercitazione: Configurare Proxyclick per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Proxyclick.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: f7d2a6f01e891a7fb1c14cde552d66679e474139
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359171"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Esercitazione: Configurare Proxyclick per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Proxyclick e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il de-provisioning di utenti e/o gruppi in Proxyclick.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Proxyclick](https://www.proxyclick.com/pricing)
* Un account utente in Proxyclick con autorizzazioni di amministratore.

## <a name="add-proxyclick-from-the-gallery"></a>Aggiungere Proxyclick dalla raccolta

Prima di configurare Proxyclick per il provisioning utenti automatico con Azure AD, è necessario aggiungere Proxyclick dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Proxyclick dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Proxyclick**, selezionare **Proxyclick** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Proxyclick nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Assegnazione di utenti a Proxyclick

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Proxyclick. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Proxyclick seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Suggerimenti importanti per l'assegnazione di utenti a Proxyclick

* È consigliabile assegnare un singolo utente di Azure AD a Proxyclick per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Proxyclick, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Configurazione del provisioning utenti automatico in Proxyclick 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Proxyclick in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Proxyclick, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Proxyclick](proxyclick-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Proxyclick in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Proxyclick**.

    ![Collegamento di Proxyclick nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Per recuperare l'**URL tenant** e il **Token segreto** dell'account Proxyclick, seguire la procedura dettagliata descritta nel passaggio 6.

6. Accedere alla [console di amministrazione di Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Passare a **Impostazioni** > **Integrazioni** > **Sfoglia il Marketplace**.

    ![Impostazioni di Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrazioni di Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Marketplace di Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selezionare **Azure AD**. Fare clic su **Installa ora**.

    ![Azure AD in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Installazione di Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selezionare **Provisioning utenti** e fare clic su **Avvia integrazione**. 

    ![Provisioning utenti in Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    L'interfaccia utente di configurazione delle impostazioni appropriata dovrebbe ora essere visualizzata in **Impostazioni**  > **Integrazioni**. Selezionare **Impostazioni** in **Azure AD (provisioning utenti)** .

    ![Creazione di Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Qui è possibile trovare l'**URL tenant** e il **Token segreto**.

    ![Creazione del token di Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Proxyclick. Se la connessione non riesce, verificare che l'account Proxyclick abbia autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Proxyclick** (Sincronizza utenti di Azure Active Directory in Proxyclick).

    ![Mapping utente di Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Proxyclick nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Proxyclick per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare il servizio di provisioning di Azure AD per Proxyclick, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

15. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Proxyclick scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Proxyclick.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Proxyclick richiede che **emails** e **userName** abbiano lo stesso valore di origine. Qualsiasi aggiornamento a uno dei due attributi modificherà l'altro valore.
* Proxyclick non supporta il provisioning per i gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

