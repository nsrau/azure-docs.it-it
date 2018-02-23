---
title: 'Esercitazione: Configurare GitHub per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in GitHub.
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: e06d04234f33633275d85732a321a264c777fc59
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Esercitazione: Configurare GitHub per il provisioning utenti automatico


Questa esercitazione descrive le procedure da eseguire in GitHub e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a GitHub. 

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory
*   Tenant di Github con [piano Business](https://help.github.com/articles/organization-billing-plans/#business-plan) o superiore abilitato 
*   Account utente in GitHub con autorizzazioni di amministratore 

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API SCIM di GitHub](https://developer.github.com/v3/scim/), disponibile per team GitHub con piano Business o superiore.

## <a name="assigning-users-to-github"></a>Assegnazione di utenti a GitHub

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app GitHub. Dopo averlo stabilito, è possibile assegnare gli utenti all'app GitHub seguendo le istruzioni riportate in:

[Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Suggerimenti importanti per l'assegnazione di utenti a GitHub

*   È consigliabile assegnare un singolo utente di Azure AD a GitHub per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a GitHub, è necessario selezionare il ruolo **Utente** o un altro ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Poiché il ruolo **Accesso predefinito** non è applicabile per il provisioning, i relativi utenti vengono ignorati.


## <a name="configuring-user-provisioning-to-github"></a>Configurazione del provisioning utenti in GitHub 

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di GitHub e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in GitHub in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per GitHub, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configurare il provisioning automatico degli account utente in GitHub in Azure AD


1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se si è già configurato GitHub per l'accesso Single Sign-On, cercare l'istanza di GitHub usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **GitHub** nella raccolta di applicazioni. Selezionare GitHub nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di GitHub e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di GitHub](./media/active-directory-saas-github-provisioning-tutorial/GitHub1.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Viene aperta una finestra di dialogo di autorizzazione di GitHub in una nuova finestra del browser. 

6. Nella nuova finestra accedere a GitHub con l'account di amministratore. Nella finestra di dialogo di autorizzazione risultante selezionare il team GitHub per cui si vuole abilitare il provisioning e quindi **Authorize** (Autorizza). Al termine, tornare al portale di Azure per completare la configurazione del provisioning.

    ![Finestra di dialogo di autorizzazione](./media/active-directory-saas-github-provisioning-tutorial/GitHub2.png)

7. Nel portale di Azure immettere l'**URL tenant** e fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app GitHub. Se la connessione non riesce, verificare che l'account GitHub abbia le autorizzazioni di amministratore e che l'**URL tenant** sia stato immesso correttamente, quindi provare a eseguire di nuovo il passaggio per l'autorizzazione (è possibile calcolare l'**URL tenant** seguendo la regola: "https://api.github.com/scim/v2/organizations/ + <nome_organizzazioni>". È possibile individuare le organizzazioni nell'account GitHub in **Settings** (Impostazioni)  > **Organizations** (Organizzazioni)).

    ![Finestra di dialogo di autorizzazione](./media/active-directory-saas-github-provisioning-tutorial/GitHub3.png)

8. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

9. Fare clic su **Save**. 

10. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to GitHub** (Sincronizza utenti di Azure Active Directory in GitHub).

11. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a GitHub. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in GitHub per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

12. Per abilitare il servizio di provisioning di Azure AD per GitHub, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**

13. Fare clic su **Save**. 

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a GitHub nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning che descrivono tutte le azioni eseguite dal servizio di provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](active-directory-saas-provisioning-reporting.md)
