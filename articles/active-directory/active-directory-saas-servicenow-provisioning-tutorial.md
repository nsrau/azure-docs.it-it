---
title: 'Esercitazione: configurazione di ServiceNow per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: e338804a0c5fcb09dfdfd9bf18ae4d0466cd9a82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Esercitazione: configurazione di ServiceNow per il provisioning utenti automatico con Azure Active Directory

Questa esercitazione descrive le procedure da eseguire in ServiceNow e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a ServiceNow.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   È necessario disporre di un tenant valido per ServiceNow per le aziende o ServiceNow per l'istruzione. È possibile usare un account della versione di valutazione gratuita per entrambi i servizi.
*   Account utente in ServiceNow con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-servicenow"></a>Assegnazione di utenti a ServiceNow

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app ServiceNow. Dopo aver stabilito questo, è possibile assegnare questi utenti all'app ServiceNow seguendo le istruzioni riportate di seguito: [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   È consigliabile assegnare un singolo utente di Azure AD a ServiceNow per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.
>*   Quando si assegna un utente a ServiceNow, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatico

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di ServiceNow e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in ServiceNow in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
>Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per ServiceNow, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="configure-automatic-user-account-provisioning"></a>Configurare il provisioning automatico degli account utente

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Se ServiceNow è già stato configurato per l'accesso Single Sign-On, cercare l'istanza di ServiceNow usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **ServiceNow** nella raccolta di applicazioni. Selezionare ServiceNow nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di ServiceNow e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**. 

    ![provisioning](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. Nella sezione Credenziali amministratore, seguire questa procedura:
   
    a. Nella casella di testo **Nome istanza ServiceNow** digitare il nome dell'istanza di ServiceNow.

    b. Nella casella di testo **ServiceNow Admin User Name** (Nome utente amministratore ServiceNow) digitare il nome utente di un amministratore.

    c. Nella casella di testo **ServiceNow Admin Password** (Password amministratore ServiceNow) digitare la password dell'amministratore.

6. Nel portale di Azure fare clic su **Verifica connessione** per verificare che Azure AD possa connettersi all'app ServiceNow. Se la connessione non riesce, verificare che l'account ServiceNow abbia autorizzazioni di amministratore di team e ripetere il passaggio **"Credenziali amministratore"**.

7. Immettere l'indirizzo e-mail di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

8. Fare clic su **Salva**.

9. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to ServiceNow** (Sincronizza utenti di Azure Active Directory in ServiceNow).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD in ServiceNow. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in ServiceNow per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il servizio di provisioning di Azure AD per ServiceNow, impostare **Stato del provisioning** su **On** (Attivo) nella sezione Impostazioni

12. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a ServiceNow nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app ServiceNow.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-servicenow-tutorial.md)


