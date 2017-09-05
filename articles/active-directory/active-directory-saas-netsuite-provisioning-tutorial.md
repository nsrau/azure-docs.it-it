---
title: 'Esercitazione: Integrazione di Azure Active Directory con NetSuite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Netsuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 277c393536615fc8bfe8af0bc6d487115f04776c
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017


---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Netsuite per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Netsuite e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Netsuite.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Sottoscrizione di Netsuite abilitata per l'accesso Single Sign-On.
*   Account utente in Netsuite con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-netsuite"></a>Assegnazione di utenti a Netsuite

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Netsuite. Dopo averlo stabilito, è possibile assegnare gli utenti all'app Netsuite seguendo le istruzioni riportate in:

[Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite"></a>Suggerimenti importanti per l'assegnazione di utenti a Netsuite

*   È consigliabile assegnare un singolo utente di Azure AD a Netsuite per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Netsuite, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-user-provisioning"></a>Abilitare il provisioning utenti

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Netsuite e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Netsuite in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP] 
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Netsuite, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-user-account-provisioning"></a>Per configurare il provisioning degli account utente:

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in Netsuite.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se si è già configurato Netsuite per l'accesso Single Sign-On, cercare l'istanza di Netsuite usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Netsuite** nella raccolta di applicazioni. Selezionare Netsuite nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Netsuite e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**. 

    ![provisioning](./media/active-directory-saas-netsuite-provisioning-tutorial/provisioning.png)

5. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **Nome utente amministratore** digitare un nome di account Netsuite che abbia il profilo **Amministratore di sistema** assegnato in Netsuite.com.
   
    b. Nella casella di testo **Password amministratore** digitare la password per questo account.
      
6. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Netsuite.

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo.

8. Fare clic su **Salva**.

9. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Netsuite** (Sincronizza utenti di Azure Active Directory in Netsuite).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Netsuite. Si noti che gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Netsuite per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il servizio di provisioning di Azure AD per Netsuite, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni

12. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Netsuite nella sezione Utenti e gruppi. Notare che la sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Netsuite.

È ora possibile creare un account di test. Attendere 20 minuti per verificare che l'account sia stato sincronizzato con Netsuite.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-netsuite-tutorial.md)
