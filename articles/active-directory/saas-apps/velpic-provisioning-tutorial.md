---
title: 'Esercitazione: configurazione di Velpic per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Velpic.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064122"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Esercitazione: configurazione di Velpic per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Velpic e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Velpic.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisites

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Tenant di Azure Active Directory
* Tenant di Velpic con il [piano Enterprise](https://www.velpic.com/pricing.html) o superiore abilitato
* Un account utente in Velpic con autorizzazioni di amministratore

## <a name="assigning-users-to-velpic"></a>Assegnazione di utenti a Velpic

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, verranno sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Velpic. Dopo aver stabilito questo, è possibile assegnare questi utenti all'app Velpic seguendo le istruzioni riportate qui:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Suggerimenti importanti per l'assegnazione di utenti a Velpic

* È consigliabile assegnare un singolo Azure AD utente a Velpic per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Velpic, è necessario selezionare il ruolo **utente** o un altro ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Si noti che il ruolo **accesso predefinito** non funziona per il provisioning e che questi utenti verranno ignorati.

## <a name="configuring-user-provisioning-to-velpic"></a>Configurazione del provisioning utenti in Velpic

Questa sezione illustra la connessione del Azure AD all'API di provisioning degli account utente di Velpic e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Velpic in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per Velpic, seguendo le istruzioni fornite in [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Velpic in Azure AD:

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se Velpic è già stato configurato per Single Sign-On, cercare l'istanza di Velpic usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Velpic** nella raccolta di applicazioni. Selezionare Velpic nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Velpic, quindi selezionare la scheda **provisioning** .

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del **tenant & token segreto** di Velpic. (È possibile trovare questi valori nell'account Velpic: **manage** > **Integration** > **plugin** > **scim**)

    ![Valori di autorizzazione](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Nella portale di Azure fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi all'app Velpic. Se la connessione non riesce, verificare che l'account Velpic disponga delle autorizzazioni di amministratore e ripetere il passaggio 5.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo seguente.

8. Fare clic su **Salva**.

9. Nella sezione Mapping selezionare **sincronizza Azure Active Directory utenti a Velpic**.

10. Nella sezione **mapping degli attributi** esaminare gli attributi utente che verranno sincronizzati da Azure ad a Velpic. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con gli account utente in Velpic per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il servizio di provisioning Azure AD per Velpic, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

12. Fare clic su **Salva**.

Verrà avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Velpic nella sezione utenti e gruppi. La sincronizzazione iniziale richiederà più tempo delle sincronizzazioni successive, che saranno eseguite ogni 40 minuti circa per tutto il tempo in cui il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning che descrivono tutte le azioni eseguite dal servizio di provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)