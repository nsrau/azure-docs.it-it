---
title: 'Esercitazione: Configurazione Velpic per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Velpic.
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
ms.openlocfilehash: 16c302fbe151d6cd8c2198240bc31a2bd69dbd7b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270914"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Esercitazione: Configurazione Velpic per il Provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Velpic e Azure AD per automaticamente il provisioning e deprovisioning degli account utente da Azure AD in Velpic.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Tenant di Azure Active Directory
* Un tenant di Velpic con il [piano Enterprise](https://www.velpic.com/pricing.html) o superiore abilitato
* Un account utente in Velpic con autorizzazioni di amministratore

## <a name="assigning-users-to-velpic"></a>Assegnazione di utenti a Velpic

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, verranno sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Velpic. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app di Velpic seguendo le istruzioni riportate qui:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Suggerimenti importanti per l'assegnazione di utenti a Velpic

* È consigliabile che un singolo utente di Azure AD da assegnare alle Velpic per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Velpic, è necessario selezionare la **utente** ruolo, o un'altra valida specifici dell'applicazione, se disponibile, nella finestra di dialogo di assegnazione. Si noti che il **accesso predefinito** ruolo non è applicabile per il provisioning e questi utenti verranno ignorati.

## <a name="configuring-user-provisioning-to-velpic"></a>Configurazione del provisioning utente in Velpic

Questa sezione descrive la connessione di Azure AD all'API di provisioning dell'account utente di Velpic e configurazione del servizio di provisioning per creare, aggiornare e disabilitare assegnati gli account utente in Velpic basata utenti e assegnazione dei gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare basato su SAML Single Sign-On per Velpic, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Per configurare il provisioning degli account utente automatico a Velpic in Azure AD:

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se si è già configurato Velpic per single sign-on, cercare l'istanza di Velpic usando il campo di ricerca. In caso contrario, selezionare **Add** e cercare **Velpic** nella raccolta di applicazioni. Selezionare Velpic dai risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Velpic, quindi selezionare il **Provisioning** scheda.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Il Provisioning di Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Sotto il **credenziali di amministratore** sezione di input il **URL del Tenant e segreto Token** di Velpic. ( È possibile trovare questi valori con il proprio account Velpic: **Gestire** > **Integration** > **Plugin** > **SCIM**)

    ![Valori di autorizzazione](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Nel portale di Azure, fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'App Velpic. Se la connessione non riesce, verificare che l'account di Velpic abbia autorizzazioni di amministratore e ripetere il passaggio 5.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo seguente.

8. Fare clic su **Save**.

9. Nella sezione mapping selezionare **Synchronize Azure Active Directory Users a Velpic**.

10. Nel **mapping di attributi** sezione, esaminare gli attributi utente che verranno sincronizzati da Azure AD a Velpic. Si noti che gli attributi selezionati come **corrispondenti** proprietà verranno utilizzate per corrispondere gli account utente in Velpic per operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il provisioning del servizio per Velpic AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione

12. Fare clic su **Save**.

Verrà avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Velpic nella sezione utenti e gruppi. La sincronizzazione iniziale richiederà più tempo delle sincronizzazioni successive, che saranno eseguite ogni 40 minuti circa per tutto il tempo in cui il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning che descrivono tutte le azioni eseguite dal servizio di provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)