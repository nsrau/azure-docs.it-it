---
title: 'Esercitazione: Configurare ThousandEyes per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in ThousandEyes.
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
ms.openlocfilehash: 4b9d8fa17cf7d376d11ab692e41a65f0dc6b0fae
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Esercitazione: Configurare ThousandEyes per il provisioning utenti automatico


Questa esercitazione descrive le procedure da eseguire in ThousandEyes e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a ThousandEyes. 

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure Active Directory
*   Un tenant di ThousandEyes con il [piano Standard](https://www.thousandeyes.com/pricing) o superiore abilitato 
*   Un account utente in ThousandEyes con autorizzazioni di amministratore 

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API SCIM di ThousandEyes](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), disponibile per i team ThousandEyes con piano Standard o superiore.

## <a name="assigning-users-to-thousandeyes"></a>Assegnazione di utenti a ThousandEyes

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app ThousandEyes. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app ThousandEyes seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Suggerimenti importanti per l'assegnazione di utenti a ThousandEyes

*   È consigliabile assegnare un singolo utente di Azure AD a ThousandEyes per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a ThousandEyes, è necessario selezionare il ruolo **Utente** o un altro ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Poiché il ruolo **Accesso predefinito** non è applicabile per il provisioning, i relativi utenti vengono ignorati.


## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configurazione del provisioning utenti in ThousandEyes 

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di ThousandEyes e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in ThousandEyes in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per ThousandEyes, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configurare il provisioning automatico degli account utente in ThousandEyes con Azure AD


1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se si è già configurato ThousandEyes per l'accesso Single Sign-On, cercare l'istanza di ThousandEyes usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **ThousandEyes** nella raccolta di applicazioni. Selezionare ThousandEyes nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di ThousandEyes e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di ThousandEyes](./media/active-directory-saas-thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Nella sezione **Credenziali amministratore** immettere il **Token segreto** generato dall'account di ThousandEyes. È possibile trovare il token nell'account ThousandEyes: **Security & Authentication** (Sicurezza e autenticazione). 

    ![Provisioning di ThousandEyes](./media/active-directory-saas-thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app ThousandEyes. Se la connessione non riesce, verificare che l'account ThousandEyes abbia autorizzazioni di amministratore di team e ripetere il passaggio 5.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

8. Fare clic su **Save**. 

9. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to ThousandEyes** (Sincronizza utenti di Azure Active Directory in ThousandEyes).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a ThousandEyes. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in ThousandEyes per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il servizio di provisioning di Azure AD per ThousandEyes, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

12. Fare clic su **Save**. 

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a ThousandEyes nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning che descrivono tutte le azioni eseguite dal servizio di provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](active-directory-saas-provisioning-reporting.md)
