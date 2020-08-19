---
title: "Esercitazione: configurare Keeper Password Manager & l'insieme di credenziali digitale per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Keeper Password Manager & Digital Vault.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: a9ca77a059625ace13e6798e3fde84f11f3fe1db
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546791"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Esercitazione: configurare Keeper Password Manager & l'insieme di credenziali digitale per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Keeper Password Manager & insieme di credenziali digitali e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Keeper Password Manager & Digital Vault.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Gestore delle password di Keeper & tenant dell'insieme di credenziali digitale](https://keepersecurity.com/pricing.html?t=e)
* Un account utente in Keeper Password Manager & l'insieme di credenziali digitale con autorizzazioni di amministratore.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Aggiungere Keeper Password Manager & Digital Vault dalla raccolta

Prima di configurare Keeper Password Manager & l'insieme di credenziali digitale per il provisioning utenti automatico con Azure AD, è necessario aggiungere Keeper Password Manager & Digital Vault dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Keeper Password Manager & Digital Vault dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Keeper password manager & Digital Vault**, selezionare **keeper password manager & Digital Vault** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Keeper Password Manager & Digital Vault nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Assegnazione di utenti a Keeper Password Manager & insieme di credenziali digitale

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Keeper Password Manager & Digital Vault. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a Keeper Password Manager & insieme di credenziali digitale seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Suggerimenti importanti per l'assegnazione di utenti a Keeper Password Manager & insieme di credenziali digitale

* È consigliabile assegnare un singolo utente Azure AD a Keeper Password Manager & Digital Vault per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Keeper Password Manager & insieme di credenziali digitale, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurazione del provisioning utenti automatico in Keeper Password Manager & insieme di credenziali digitale 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Keeper Password Manager & l'insieme di credenziali digitale in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per Keeper Password Manager & Digital Vault, seguendo le istruzioni fornite nell' [esercitazione Keeper Password Manager & Digital vault Single Sign-on](keeperpasswordmanager-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Keeper Password Manager & insieme di credenziali digitale in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Keeper Password Manager & Digital Vault**.

    ![Collegamento di Keeper Password Manager & Digital Vault nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del **tenant** e il **token del segreto** di Keeper Password Manager & account dell'insieme di credenziali digitale, come descritto nel passaggio 6.

6. Accedere alla console di [amministrazione di Keeper](https://keepersecurity.com/console/#login). Fare clic su **amministratore** e selezionare un nodo esistente o crearne uno nuovo. Passare alla scheda **provisioning** e selezionare **Aggiungi metodo**.

    ![Console di amministrazione di Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selezionare **SCIM (sistema per la gestione delle identità tra domini**).

    ![Keeper Add SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Fare clic su **crea token di provisioning**.

    ![Endpoint crea endpoint](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copiare i valori per **URL** e **token** e incollarli in **URL Tenant** e **token segreto** in Azure ad. Fare clic su **Salva** per completare la configurazione del provisioning in Keeper.

    ![Token di creazione del custode](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Quando si popolano i campi indicati nel passaggio 5, fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a Keeper Password Manager & Digital Vault. Se la connessione non riesce, verificare che il gestore delle password di Keeper & account dell'insieme di credenziali digitale disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Keeper Password Manager & Digital Vault**.

    ![Mapping utente Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Keeper Password Manager & Digital Vault nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Keeper Password Manager & l'insieme di credenziali digitale per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Keeper Password Manager & Digital Vault**.

    ![Mapping del gruppo Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Keeper Password Manager & Digital Vault nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Keeper Password Manager & l'insieme di credenziali digitale per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning Azure AD per Keeper Password Manager & Digital Vault, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Keeper Password Manager & insieme di credenziali digitale selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in Keeper Password Manager & l'insieme di credenziali digitale.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Keeper Password Manager & l'insieme di credenziali digitale richiede che i **messaggi di posta elettronica** e il **nome utente** abbiano lo stesso valore di origine, poiché tutti gli aggiornamenti a entrambi gli attributi modificheranno l'altro valore.
* Keeper Password Manager & l'insieme di credenziali digitale non supporta le eliminazioni utente, ma solo Disabilita. Gli utenti disabilitati verranno visualizzati come bloccati nell'interfaccia utente della console di amministrazione di Keeper.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

