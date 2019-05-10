---
title: 'Esercitazione: Configura Keeper Password Manager & Digital Vault per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Keeper Password Manager & Digital Vault.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: 445579eb780b49f536ef1a9e13e5ca43db6f98f6
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508505"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Esercitazione: Configura Keeper Password Manager & Digital Vault per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Keeper Password Manager & Digital Vault e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare il provisioning e deprovisioning di utenti e/o gruppi a Keeper Password automaticamente Manager & Digital Vault.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Keeper Password Manager & Digital Vault](https://keepersecurity.com/pricing.html?t=e)
* Un account utente in Keeper Password Manager & Digital Vault con le autorizzazioni di amministratore.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Aggiungere Keeper Password Manager & Digital Vault dalla raccolta

Prima configurazione di Keeper Password Manager & Digital Vault per il provisioning utenti automatico con Azure AD, è necessario aggiungere Keeper Password Manager & Digital Vault dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Keeper Password Manager & Digital Vault dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)**, nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Keeper Password Manager & Digital Vault**, selezionare **Keeper Password Manager & Digital Vault** nel pannello dei risultati e quindi fare clic su di **Add**pulsante per aggiungere l'applicazione.

    ![Keeper Password Manager & Digital Vault nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Assegnazione di utenti a Keeper Password Manager & Digital Vault

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a Keeper Password Manager & Digital Vault. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a Keeper Password Manager & Digital Vault seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Suggerimenti importanti per l'assegnazione di utenti a Keeper Password Manager & Digital Vault

* È consigliabile che un singolo utente di Azure AD viene assegnato a Keeper Password Manager & Digital Vault per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Keeper Password Manager & Digital Vault, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurazione del provisioning utenti automatico a Keeper Password Manager & Digital Vault 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in Keeper Password Manager & Digital Vault basato su utente e/o le assegnazioni dei gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare basato su SAML single sign-on per Keeper Password Manager & Digital Vault, seguendo le istruzioni disponibili nel [Keeper Password Manager & Digital Vault single sign-on di esercitazione](keeperpasswordmanager-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Keeper Password Manager & Digital Vault in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Keeper Password Manager & Digital Vault**.

    ![Collegamento di Keeper Password Manager & Digital Vault nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Sotto il **credenziali di amministratore** sezione di input il **URL Tenant** e **Token segreto** di Keeper Password Manager & account dell'insieme di credenziali digitali, come descritto nel passaggio 6.

6. Accedi per i [Console di amministrazione di Keeper](https://keepersecurity.com/console/#login). Fare clic su **Admin** e selezionare un nodo esistente o crearne uno nuovo. Passare il **Provisioning** scheda e selezionare **Aggiungi metodo**.

    ![Console di amministrazione di keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selezionare **SCIM (System for Cross-Domain Identity Management**.

    ![Aggiunta di keeper SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Fare clic su **creare un Token di Provisioning**.

    ![Keeper creare Endpoint](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copiare i valori per **URL** e **Token** e incollarli nel **URL del Tenant** e **Token segreto** in Azure AD. Fare clic su **salvare** per completare la configurazione del provisioning in Keeper.

    ![Creare Token keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Keeper Password Manager & Digital Vault. Se la connessione non riesce, verificare che l'account di Keeper Password Manager & Digital Vault abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e Token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Save**.

10. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users a Keeper Password Manager & Digital Vault**.

    ![Mapping utente keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Keeper Password Manager & Digital Vault nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà vengono usate per associare l'account utente in Keeper Password Manager & Digital Vault per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Sotto il **mapping** sezione, selezionare **sincronizzare Azure gruppi di Active Directory a Keeper Password Manager & Digital Vault**.

    ![Mapping dei gruppi di keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Keeper Password Manager & Digital Vault nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà usate devono rispettare i gruppi in Keeper Password Manager & Digital Vault per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo di keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il provisioning del servizio di Keeper Password Manager & Digital Vault AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Lo stato di provisioning sia attivato](common/provisioning-toggle-on.png)

16. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Keeper Password Manager & Digital Vault selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il provisioning di ambito](common/provisioning-scope.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio in Keeper Password Manager di provisioning di Azure AD provisioning & Digital Vault.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Richiede di keeper Password Manager & Digital Vault **messaggi di posta elettronica** e **userName** avere lo stesso valore di origine, perché tutti gli aggiornamenti di entrambi gli attributi modificherà l'altro valore.
* Keeper Password Manager & Digital Vault non supporta eliminazioni utente, disabilitare solo. Gli utenti disabilitati rimarrà bloccato nell'interfaccia utente Console di amministrazione di Keeper.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

