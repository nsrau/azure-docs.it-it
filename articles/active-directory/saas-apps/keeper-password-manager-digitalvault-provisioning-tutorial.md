---
title: 'Esercitazione: Configurare Keeper Password Manager & Digital Vault per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Keeper Password Manager & Digital Vault.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358984"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Esercitazione: Configurare Keeper Password Manager & Digital Vault per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Keeper Password Manager & Digital Vault e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Keeper Password Manager & Digital Vault.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Keeper Password Manager & Digital Vault](https://keepersecurity.com/pricing.html?t=e)
* Un account di Keeper Password Manager & Digital Vault con autorizzazioni di amministratore.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Aggiungere Keeper Password Manager & Digital Vault dalla raccolta

Prima di configurare Keeper Password Manager & Digital Vault per il provisioning utenti automatico con Azure AD, è necessario aggiungere Keeper Password Manager & Digital Vault dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Keeper Password Manager & Digital Vault dalla raccolta di applicazioni di Azure AD seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Keeper Password Manager & Digital Vault**, selezionare **Keeper Password Manager & Digital Vault** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Keeper Password Manager & Digital Vault nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Assegnazione di utenti a Keeper Password Manager & Digital Vault

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Keeper Password Manager & Digital Vault. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Keeper Password Manager & Digital Vault seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Suggerimenti importanti per l'assegnazione di utenti a Keeper Password Manager & Digital Vault

* È consigliabile assegnare un singolo utente di Azure AD a Keeper Password Manager & Digital Vault per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Keeper Password Manager & Digital Vault, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurazione del provisioning utenti automatico in Keeper Password Manager & Digital Vault 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Keeper Password Manager & Digital Vault in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Keeper Password Manager & Digital Vault, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Keeper Password Manager & Digital Vault](keeperpasswordmanager-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Keeper Password Manager & Digital Vault in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Keeper Password Manager & Digital Vault**.

    ![Collegamento di Keeper Password Manager & Digital Vault nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot che mostra l'opzione puntini di sospensione per un indicatore KPI selezionata e l'opzione GESTISCI evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** specificare i campi **URL tenant** e **Token segreto** dell'account Keeper Password Manager & Digital Vault, come descritto nel passaggio 6.

6. Accedere alla [console di amministrazione di Keeper](https://keepersecurity.com/console/#login). Fare clic su **Admin** e selezionare un nodo esistente oppure crearne uno nuovo. Passare alla scheda **Provisioning** e selezionare **Aggiungi metodo**.

    ![Console di amministrazione di Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selezionare **SCIM (System for Cross-Domain Identity Management**.

    ![Aggiunta di SCIM in Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Fare clic su **Create Provisioning Token** (Crea token di provisioning).

    ![Creazione di endpoint in Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copiare i valori per **URL** e **Token** e incollarli in **Tenant URL** e **Secret Token** in Azure AD. Fare clic su **Salva** per completare la configurazione del provisioning in Keeper.

    ![Creazione del token in Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Keeper Password Manager & Digital Vault. Se la connessione non riesce, verificare che l'account Keeper Password Manager & Digital Vault abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Keeper Password Manager & Digital Vault** (Sincronizza utenti di Azure Active Directory con Keeper Password Manager & Digital Vault).

    ![Mapping degli utenti di Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Keeper Password Manager & Digital Vault nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Keeper Password Manager & Digital Vault per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Keeper Password Manager & Digital Vault** (Sincronizza gruppi di Azure Active Directory con Keeper Password Manager & Digital Vault).

    ![Mapping dei gruppi di Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Keeper Password Manager & Digital Vault nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Keeper Password Manager & Digital Vault per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppi in Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Keeper Password Manager & Digital Vault, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Keeper Password Manager & Digital Vault selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Keeper Password Manager & Digital Vault.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Keeper Password Manager & Digital Vault richiede che i valori di origine degli attributi **emails** e **userName** siano identici, perché qualsiasi aggiornamento di uno dei due attributi comporta la modifica dell'altro.
* Keeper Password Manager & Digital Vault supporta solo la disabilitazione, e non l'eliminazione degli utenti. Gli utenti disabilitati verranno visualizzati come bloccati nell'interfaccia utente della console di amministrazione di Keeper.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

