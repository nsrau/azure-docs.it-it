---
title: 'Esercitazione: Configurazione di Samanage per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Samanage.
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 278ebf464fbe815568fbe332f80d5ea6b29e1811
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="tutorial-configuring-samanage-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Samanage per il provisioning utenti automatico


Questa esercitazione descrive le procedure da eseguire in Samanage e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Samanage. 

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory
*   Tenant di Samanage con il [piano Professional](https://www.samanage.com/pricing/) o superiore abilitato 
*   Account utente in Samanage con autorizzazioni di amministratore 

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API REST di Samanage](https://www.samanage.com/api/) disponibile per i team Samanage con piano Professional o superiore.

## <a name="assigning-users-to-samanage"></a>Assegnazione di utenti a Samanage

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Samanage. Dopo averlo stabilito, è possibile assegnare gli utenti all'app Samanage seguendo le istruzioni riportate in:

[Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Suggerimenti importanti per l'assegnazione di utenti a Samanage

*   È consigliabile assegnare un singolo utente di Azure AD a Samanage per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Samanage, è necessario selezionare il ruolo **Utente** o un altro ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Poiché il ruolo **Accesso predefinito** non è applicabile per il provisioning, i relativi utenti vengono ignorati.

> [!NOTE]
> Per praticità, il servizio di provisioning legge tutti i ruoli personalizzati definiti in Samanage e li importa in Azure AD dove è possibile selezionarli nella finestra di dialogo Seleziona ruolo. Questi ruoli saranno visibili nel portale di Azure dopo l'abilitazione del servizio di provisioning e l'esecuzione di un ciclo di sincronizzazione.

## <a name="configuring-user-provisioning-to-samanage"></a>Configurazione del provisioning utenti in Samanage 

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Samanage e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Samanage in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Samanage, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.


### <a name="configure-automatic-user-account-provisioning-to-samanage-in-azure-ad"></a>Configurare il provisioning automatico degli account utente in Samanage in Azure AD:


1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se Samanage è già stato configurato per l'accesso Single Sign-On, cercare l'istanza di Samanage usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Samanage** nella raccolta di applicazioni. Selezionare Samanage nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Samanage e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage1.png)

5. Nella sezione **Credenziali amministratore** immettere **nome utente e password dell'amministratore** dell'account Samanage. 

6. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Samanage. Se la connessione non riesce, verificare che l'account Samanage abbia autorizzazioni di amministratore e ripetere il passaggio 5.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

8. Fare clic su **Salva**. 

9. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Samanage** (Sincronizza utenti di Azure Active Directory in Samanage).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Samanage. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il servizio di provisioning di Azure AD per Samanage, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**

12. Fare clic su **Salva**. 

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Samanage nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning che descrivono tutte le azioni eseguite dal servizio di provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](active-directory-saas-provisioning-reporting.md)

