---
title: 'Esercitazione: Configurare 4me per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in 4me.
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
ms.openlocfilehash: c0c428997cfba8871a29d9bfe0df0a6920a1d22f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998669"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Esercitazione: Configurare 4me per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in 4me e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in 4me.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di 4me](https://www.4me.com/trial/)
* Un account utente in 4me con autorizzazioni di amministratore.

## <a name="add-4me-from-the-gallery"></a>Aggiungere 4me dalla raccolta

Prima di configurare 4me per il provisioning utenti automatico con Azure AD, è necessario aggiungere 4me dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere 4me dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **4me**, selezionare **4me** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![4me nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Assegnazione di utenti a 4me

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a 4me. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a 4me seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Suggerimenti importanti per l'assegnazione di utenti a 4me

* È consigliabile assegnare un singolo utente di Azure AD a 4me per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a 4me, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Configurazione del provisioning utenti automatico per 4me 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in 4me in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per 4me, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per 4me](4me-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Per configurare il provisioning utenti automatico per 4me in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **4me**.

    ![Collegamento di 4me nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Per recuperare l'**URL tenant** e il **token segreto** dell'account 4me, seguire la procedura dettagliata descritta nel passaggio 6.

6. Accedere alla console di amministrazione di 4me. Passare a **Impostazioni**.

    ![Impostazioni di 4me](media/4me-provisioning-tutorial/4me01.png)

    Digitare **app** nella barra di ricerca.

    ![App 4me](media/4me-provisioning-tutorial/4me02.png)

    Aprire l'elenco a discesa **SCIM** per recuperare il token segreto e l'endpoint SCIM.

    ![SCIM in 4me](media/4me-provisioning-tutorial/4me03.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a 4me. Se la connessione non riesce, verificare che l'account 4me abbia autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to 4me** (Sincronizza utenti di Azure Active Directory in 4me).

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-mapping.png" alt-text="Screenshot della pagina Mapping. In Nome è evidenziata l'opzione Synchronize Azure Active Directory Users to 4me (Sincronizza utenti di Azure Active Directory in 4me)." border="false":::
    
11. Esaminare gli attributi utente sincronizzati da Azure AD a 4me nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in 4me per le operazioni di aggiornamento. Verificare che [4me supporti l'applicazione di filtri](https://developer.4me.com/v1/scim/users/) sull'attributo corrispondente scelto. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-attributes.png" alt-text="Screenshot della pagina Mapping di attributi. In una tabella sono elencati gli attributi di Azure Active Directory, gli attributi di 4me corrispondenti e lo stato dell'abbinamento." border="false":::
    
12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to 4me** (Sincronizza gruppi di Azure Active Directory in 4me).

    :::image type="content" source="media/4me-provisioning-tutorial/4me-group-mapping.png" alt-text="Screenshot della pagina Mapping. In Nome è evidenziata l'opzione Synchronize Azure Active Directory Groups to 4me (Sincronizza gruppi di Azure Active Directory in 4me)." border="false":::
    
13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a 4me nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con i gruppi in 4me per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Mapping dei gruppi di 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per 4me, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in 4me selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in 4me.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* 4me ha URL di endpoint SCIM diversi per gli ambienti di test e di produzione. Il primo termina con **.qa**, mentre il secondo termina con **.com**
* I token segreti generati da 4me hanno una data di scadenza pari a un mese dalla generazione.
* 4me non supporta le operazioni **DELETE**

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
