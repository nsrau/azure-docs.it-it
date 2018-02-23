---
title: 'Esercitazione: Configurare Dropbox per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ff85b5c20d74bde476416bdd70d5a345f4a6fefb
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Esercitazione: Configurare Dropbox for Business per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Dropbox for Business e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Dropbox for Business.

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Sottoscrizione di Dropbox for Business abilitata per l'accesso Single Sign-On.
*   Account utente in Dropbox for Business con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-dropbox-for-business"></a>Assegnazione di utenti in Dropbox for Business

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Dropbox for Business. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Dropbox for Business seguendo le istruzioni riportate qui:

[Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Suggerimenti importanti per l'assegnazione di utenti a Dropbox for Business

*   È consigliabile assegnare un singolo utente di Azure AD a Dropbox for Business per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Dropbox for Business, è necessario selezionare un ruolo utente valido. Il ruolo "Accesso predefinito" non è applicabile per il provisioning.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatizzato

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Dropbox for Business e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Dropbox for Business in base all'assegnazione di utenti e gruppi in Azure AD.

>[!Tip]
>Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Dropbox for Business, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning"></a>Per configurare il provisioning automatico degli account utente:

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se è già stato configurato Dropbox for Business per l'accesso Single Sign-On, cercare l'istanza di Dropbox for Business usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Dropbox for Business** nella raccolta di applicazioni. Selezionare Dropbox for Business nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Dropbox for Business e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**. 

    ![provisioning](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Viene aperta una finestra di dialogo di accesso di Dropbox for Business in una nuova finestra del browser.

6. Nella finestra di dialogo di **Sign in to Dropbox to link with Azure AD** (Accedi a Dropbox per il collegamento ad Azure AD) accedere al tenant di Dropbox for Business.

     ![Provisioning degli utenti](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769518.png "Provisioning degli utenti")

7. Confermare che si desidera concedere l'autorizzazione Azure Active Directory per apportare modifiche al tenant di Dropbox for Business. Fare clic su **Consenti**.
    
      ![Provisioning degli utenti](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769519.png "Provisioning degli utenti")

8. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Dropbox for Business. Se la connessione non riesce, verificare che l'account di Dropbox for Business abbia autorizzazioni di amministratore di team e ripetere il passaggio **Autorizza**.

9. Immettere l'indirizzo e-mail di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

10. Fare clic su **Salva**.

11. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Dropbox for Business** (Sincronizza utenti di Azure Active Directory in Dropbox for Business).

12. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Dropbox for Business. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Dropbox for Business per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

13. Per abilitare il servizio di provisioning di Azure AD per Dropbox for Business, impostare **Stato del provisioning** su **Attivato** nella sezione Impostazioni

14. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Dropbox for Business nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning per l'app Dropbox for Business.

È ora possibile creare un account di test. Attendere 20 minuti per verificare che l'account sia stato sincronizzato con Dropbox for Business.

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato.

![Assegnare utenti](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/IC769523.png "Assegnare utenti")


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-dropboxforbusiness-tutorial.md)