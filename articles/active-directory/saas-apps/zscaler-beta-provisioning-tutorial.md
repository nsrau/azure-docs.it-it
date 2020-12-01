---
title: 'Esercitazione: Configurare Zscaler Beta per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zscaler Beta.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 59784b6e618e875a6eedc947ce75afdf3bd1df74
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327407"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zscaler Beta per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Zscaler Beta e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Zscaler Beta.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD
* Un tenant di Zscaler Beta
* Un account utente in Zscaler Beta con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'API SCIM Zscaler Beta, disponibile per gli sviluppatori di Zscaler Beta per gli account con il pacchetto Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Aggiunta di Zscaler Beta dalla raccolta

Prima di configurare Zscaler Beta per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zscaler Beta dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere Zscaler Beta dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zscaler Beta**, selezionare **Zscaler Beta** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler Beta nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Assegnazione di utenti a Zscaler Beta

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Zscaler Beta. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Zscaler Beta seguendo le istruzioni riportate di seguito:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler Beta

* È consigliabile assegnare un singolo utente di Azure AD a Zscaler Beta per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Zscaler Beta, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configurazione del provisioning utenti automatico in Zscaler Beta

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Zscaler Beta in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Zscaler Beta, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Zscaler Beta](zscaler-beta-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Zscaler Beta in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com), selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Zscaler Beta**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler Beta**.

    ![Collegamento di Zscaler Beta nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![È visualizzato un elenco di schede disposte in categorie, denominate ZScaler Beta - Provisioning/Enteprise Application. La scheda Provisioning della categoria Gestisci è selezionata.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Nell'elenco a discesa Modalità di provisioning è selezionata la modalità automatica. Sono presenti i campi per le credenziali dell'amministratore, usati per connettersi all'API di Zscaler Beta, oltre a un pulsante Test connessione.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **Credenziali amministratore** immettere i valori nei campi **URL tenant** e **Token segreto** dell'account Zscaler Beta, come descritto nel passaggio 6.

6. Per ottenere i valori di **URL tenant** e **Token segreto**, passare a **Administration > Authentication Settings** (Amministrazione > Impostazioni di autenticazione) nell'interfaccia utente del portale Zscaler Beta, quindi fare clic su **SAML** in **Authentication Type** (Tipo di autenticazione).

    ![Nella scheda Authentication Profile della finestra Authentication Settings l'opzione Directory Type è impostata su Hosted DB e Authentication Type su SAML.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Fare clic su **Configure SAML** (Configura SAML) per aprire le opzioni per la **configurazione di SAML**.

    ![In Configure SAML sono selezionate le opzioni Enable SAML Auto-Provisioning e Enable SCIM-Based Provisioning. Le caselle di testo Base URL e Bearer Token sono evidenziate.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Selezionare **Enable SCIM-Based Provisioning** (Abilita provisioning basato su SCIM), copiare il valore di **Base URL** (URL di base) e **Bearer Token** (Token di connessione), quindi salvare le impostazioni. Copiare il valore di **Base URL** in **URL tenant** e il valore di **Bearer Token** in **Token segreto** nel portale di Azure.

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Zscaler Beta. Se la connessione non riesce, verificare che l'account Zscaler Beta abbia autorizzazioni di amministratore e riprovare.

    ![Nella finestra Credenziali amministratore i campi URL tenant e Token segreto includono valori e il pulsante Test connessione è evidenziato.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![La casella di testo Messaggio di posta elettronica di notifica è vuota e la casella di controllo Invia una notifica di posta elettronica in caso di errore è deselezionata.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zscaler Beta** (Sincronizza utenti di Azure Active Directory con Zscaler Beta).

    ![L'opzione Synchronize Azure Active Directory Users to Zscaler Beta è selezionata e abilitata.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati tra Azure AD e Zscaler Beta nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zscaler Beta per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Nella sezione Mapping degli attributi per gli attributi utente vengono visualizzati gli attributi di Active Directory accanto a quelli di Zscalar Beta con cui sono sincronizzati. Una coppia di attributi è mostrata come corrispondente.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Zscaler Beta** (Sincronizza gruppi di Azure Active Directory con Zscaler Beta).

    ![L'opzione Synchronize Azure Active Directory Groups to Zscaler Beta è selezionata e abilitata.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi gruppo sincronizzati tra Azure AD e Zscaler Beta nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zscaler Beta per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Nella sezione Mapping degli attributi per gli attributi gruppo vengono visualizzati gli attributi di Active Directory accanto a quelli di Zscalar Beta con cui sono sincronizzati. Una coppia di attributi è mostrata come corrispondente.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Zscaler Beta, impostare **Stato del provisioning** su **Attivato** nella sezione **Impostazioni**.

    ![Lo stato del provisioning è visualizzato e impostato su Attivato.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Zscaler Beta scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![È visualizzato l'elenco a discesa Ambito, con l'opzione Sincronizza solo utenti e gruppi assegnati selezionata. L'altro valore disponibile è Sincronizza tutti gli utenti e i gruppi.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Il pulsante Salva nella finestra Zscaler Beta - Provisioning è evidenziato. È presente anche un pulsante Rimuovi.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Zscaler Beta.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png