---
title: 'Esercitazione: Configurare DocuSign per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 3a0a69784be3b03b030ef599037b57c2c20ea2c6
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454678"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Esercitazione: Configurare DocuSign per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in DocuSign e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a DocuSign.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Una sottoscrizione di DocuSign abilitata per l'accesso Single Sign-On.
*   Un account utente in DocuSign con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-docusign"></a>Assegnazione di utenti a DocuSign

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app DocuSign. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app DocuSign seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Suggerimenti importanti per l'assegnazione di utenti a DocuSign

*   È consigliabile assegnare un singolo utente di Azure AD a DocuSign per testare la configurazione del provisioning. È possibile assegnare utenti aggiuntivi in un secondo momento.

*   Quando si assegna un utente a DocuSign, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

> [!NOTE]
> Azure Active Directory non supporta il provisioning di gruppi con l'applicazione Docusign. È possibile eseguire il provisioning solo degli utenti.

## <a name="enable-user-provisioning"></a>Abilitare il provisioning utenti

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di DocuSign e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in DocuSign in base all'assegnazione di utenti e gruppi in Azure AD.

> [!Tip]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per DocuSign, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-user-account-provisioning"></a>Per configurare il provisioning degli account utente:

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in DocuSign.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

1. Se si è già configurato DocuSign per l'accesso Single Sign-On, cercare l'istanza di DocuSign usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **DocuSign** nella raccolta di applicazioni. Selezionare DocuSign nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

1. Selezionare l'istanza di DocuSign e quindi la scheda **Provisioning**.

1. Impostare **Modalità di provisioning** su **Automatico**. 

    ![Screenshot della scheda provisioning per DocuSign in portale di Azure. La modalità di provisioning è impostata su automatico e il nome utente amministratore, la password e la connessione di test sono evidenziati.](./media/docusign-provisioning-tutorial/provisioning.png)

1. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **Nome utente amministratore** digitare un nome di account DocuSign che abbia il profilo **Amministratore di sistema** assegnato in DocuSign.com.
   
    b. Nella casella di testo **Password amministratore** digitare la password per questo account.

> [!NOTE]
> Se sia il provisioning SSO che l'utente sono impostati, le credenziali di autorizzazione usate per il provisioning devono essere configurate per l'uso sia di SSO che di nome utente/password.

1. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app DocuSign.

1. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo.

1. Fare clic su **Salva**.

1. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to DocuSign** (Sincronizza utenti di Azure Active Directory in DocuSign).

1. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a DocuSign. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in DocuSign per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

1. Per abilitare il servizio di provisioning di Azure AD per DocuSign, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni.

1. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti assegnati a DocuSign nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app DocuSign.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
* Il provisioning di un ruolo o di un profilo di autorizzazione per un utente in DocuSign può essere eseguito usando un'espressione nei mapping degli attributi usando le funzioni [Switch](https://docs.microsoft.com/azure/active-directory/app-provisioning/functions-for-customizing-application-data#switch) e [singleAppRoleAssignment](https://docs.microsoft.com/azure/active-directory/app-provisioning/functions-for-customizing-application-data#singleapproleassignment) . Nell'espressione seguente, ad esempio, viene eseguito il provisioning dell'ID "8032066" quando a un utente è assegnato il ruolo di amministratore DS in Azure AD. Non verrà effettuato il provisioning di alcun profilo di autorizzazione se all'utente non è assegnato un ruolo sul lato Azure AD. L'ID può essere recuperato dal [portale](https://support.docusign.com/articles/Default-settings-for-out-of-the-box-DocuSign-Permission-Profiles)di DocuSign.

Switch (SingleAppRoleAssignment ([appRoleAssignments]), "", "8032066", "admin DS")


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura accesso Single Sign-on](docusign-tutorial.md)