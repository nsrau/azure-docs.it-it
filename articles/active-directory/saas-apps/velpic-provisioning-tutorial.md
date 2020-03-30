---
title: 'Esercitazione: Configurazione di Velpic per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Velpic.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064122"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Velpic per il provisioning automatico degli utentiTutorial: Configuring Velpic for Automatic User Provisioning

The objective of this tutorial is to show you the steps you need to perform in Velpic and Azure AD to automatically provision and de-provision user accounts from Azure AD to Velpic.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Tenant di Azure Active Directory
* Un tenant Velpic con il [piano Enterprise](https://www.velpic.com/pricing.html) o abilitato meglio
* Un account utente in Velpic con autorizzazioni di amministratore

## <a name="assigning-users-to-velpic"></a>Assegnazione di utenti a Velpic

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, verranno sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario decidere quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Velpic. Una volta deciso, puoi assegnare questi utenti alla tua app Velpic seguendo le istruzioni qui:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Suggerimenti importanti per l'assegnazione di utenti a Velpic

* È consigliabile assegnare un singolo utente di Azure AD a Velpic per testare la configurazione di provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Velpic, è necessario selezionare il ruolo **Utente** o un altro ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Si noti che il ruolo **Accesso predefinito** non funziona per il provisioning e questi utenti verranno ignorati.

## <a name="configuring-user-provisioning-to-velpic"></a>Configurazione del provisioning degli utenti su Velpic

Questa sezione illustra come connettere Azure AD all'API di provisioning degli account utente di Velpic e configurare il servizio di provisioning per creare, aggiornare e disabilitare gli account utente assegnati in Velpic in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare Single Sign-On basato su SAML per Velpic, seguendo le istruzioni fornite nel portale di [Azure.](https://portal.azure.com) L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>To configure automatic user account provisioning to Velpic in Azure AD:

1. Nel [portale di Azure](https://portal.azure.com)passare alla sezione **Azure Active Directory > Enterprise Apps > Tutte le applicazioni.**

2. Se Velpic è già stato configurato per l'accesso Single Sign-On, cercare l'istanza di Velpic utilizzando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Velpic** nella raccolta di applicazioni. Selezionare Velpic dai risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Velpic, quindi selezionare la scheda **Provisioning.**

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Velpic Provisioning](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL del tenant&**token segreto** di Velpic. (Puoi trovare questi valori sotto il tuo account Velpic: **Gestisci** > **Plug-in** > **Plugin** > di integrazione**SCIM**)

    ![Valori di autorizzazione](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Nel portale di Azure fare clic su Test connessione per verificare che Azure AD possa connettersi all'app Velpic.In the Azure portal, click **Test Connection** to ensure Azure AD can connect to your Velpic app. Se la connessione non riesce, verificare che l'account Velpic disponga delle autorizzazioni di amministratore e riprovare a eseguire il passaggio 5.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo seguente.

8. Fare clic su **Salva**.

9. Nella sezione Mapping selezionare **Sincronizza utenti di Azure Active Directory con Velpic**.

10. Nella sezione Mapping attributi esaminare gli attributi utente che verranno sincronizzati da Azure AD a Velpic.In the **Attribute Mappings** section, review the user attributes that will be synchronized from Azure AD to Velpic. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno utilizzati per abbinare gli account utente in Velpic per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il servizio di provisioning di Azure AD per Velpic, modificare lo stato di provisioning **su Attivato** nella sezione **ImpostazioniTo** enable the Azure AD provisioning service for Velpic, change the Provisioning Status to **On** in the Settings section

12. Fare clic su **Salva**.

Verrà avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Velpic nella sezione Utenti e gruppi. La sincronizzazione iniziale richiederà più tempo delle sincronizzazioni successive, che saranno eseguite ogni 40 minuti circa per tutto il tempo in cui il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning che descrivono tutte le azioni eseguite dal servizio di provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)