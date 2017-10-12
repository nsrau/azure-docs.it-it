---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix GoToMeeting | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Citrix GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 1ddfcd991431a11e5c3e306bd5905003d094ac18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-citrix-gotomeeting-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Citrix GoToMeeting per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Citrix GoToMeeting e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Citrix GoToMeeting.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure Active Directory.
*   Una sottoscrizione di Citrix GoToMeeting abilitata per l'accesso Single Sign-On.
*   Un account utente in Citrix GoToMeeting con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-citrix-gotomeeting"></a>Assegnazione di utenti a Citrix GoToMeeting

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Citrix GoToMeeting. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Citrix GoToMeeting seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-citrix-gotomeeting"></a>Suggerimenti importanti per l'assegnazione di utenti a Citrix GoToMeeting

*   È consigliabile assegnare un singolo utente di Azure AD a Citrix GoToMeeting per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Citrix GoToMeeting, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatizzato

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Citrix GoToMeeting e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Citrix GoToMeeting in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Citrix GoToMeeting, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning"></a>Per configurare il provisioning automatico degli account utente:

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se si è già configurato Citrix GoToMeeting per l'accesso Single Sign-On, cercare l'istanza di Citrix GoToMeeting usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Citrix GoToMeeting** nella raccolta di applicazioni. Selezionare Citrix GoToMeeting nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Citrix GoToMeeting e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**. 

    ![provisioning](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. Nella sezione Credenziali amministratore, seguire questa procedura:
   
    a. Nella casella di testo **Nome utente amministratore Citrix GoToMeeting** digitare il nome utente di un amministratore.

    b. Nella casella di testo **Password amministratore Citrix GoToMeeting** digitare la password dell'amministratore.

6. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Citrix GoToMeeting. Se la connessione non riesce, verificare che l'account Citrix GoToMeeting abbia autorizzazioni di amministratore di team e ripetere il passaggio **Credenziali amministratore**.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

8. Fare clic su **Salva**.

9. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Citrix GoToMeeting** (Sincronizza utenti di Azure Active Directory in Citrix GoToMeeting).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Citrix GoToMeeting. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Citrix GoToMeeting per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il servizio di provisioning di Azure AD per Citrix GoToMeeting, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni.

12. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Citrix GoToMeeting nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Citrix GoToMeeting.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-citrix-gotomeeting-tutorial.md)


