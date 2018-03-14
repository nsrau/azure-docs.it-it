---
title: Attivare sottoscrizioni e account di Azure | Microsoft Docs
description: Abilitare l'accesso con le API di Azure Resource Manager per gli account nuovi ed esistenti e risolvere i problemi comuni relativi agli account.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 03/01/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 66b2ef53e613a9c61eea17746a25357119cf6900
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="activate-azure-subscriptions-and-accounts-with-azure-cost-management"></a>Attivare sottoscrizioni e account di Azure con Gestione costi di Azure

L'aggiunta o l'aggiornamento delle credenziali di Azure Resource Manager consente a Gestione costi di Azure di individuare tutti gli account e le sottoscrizioni nel tenant di Azure. Se nelle macchine virtuali è abilitata anche l'estensione Diagnostica di Azure, Gestione costi di Azure può raccogliere le metriche estese, ad esempio CPU e memoria. Questo articolo descrive come abilitare l'accesso con le API di Azure Resource Manager per gli account nuovi ed esistenti. Spiega anche come risolvere i problemi comuni relativi agli account.

Gestione costi di Azure non può accedere alla maggior parte dei dati della sottoscrizione di Azure quando la sottoscrizione è _non attivata_. È necessario modificare gli account _non attivati_ in modo che Gestione costi di Azure possa accedervi.

## <a name="required-azure-permissions"></a>Autorizzazioni di Azure obbligatorie

Per completare le procedure illustrate in questo articolo, sono necessarie autorizzazioni specifiche. L'utente o l'amministratore del tenant deve avere entrambe le autorizzazioni seguenti:

- Autorizzazione a registrare l'applicazione CloudynCollector con il tenant di Azure AD.
- Possibilità di assegnare l'applicazione a un ruolo nelle sottoscrizioni di Azure.

Nelle sottoscrizione di Azure gli account devono avere l'accesso `Microsoft.Authorization/*/Write` per assegnare l'applicazione CloudynCollector. Questa azione è concessa tramite il ruolo [Proprietario](../active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore accessi utente](../active-directory/role-based-access-built-in-roles.md#user-access-administrator).

Se al proprio account è assegnato il ruolo **Collaboratore**, non è disponibile l'autorizzazione appropriata per assegnare l'applicazione. Quando si prova ad assegnare l'applicazione CloudynCollector alla sottoscrizione di Azure, viene visualizzato un errore.

### <a name="check-azure-active-directory-permissions"></a>Controllare le autorizzazioni di Azure Active Directory

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure selezionare **Azure Active Directory**.
3. In Azure Active Directory selezionare **Impostazioni utente**.
4. Controllare l'opzione **Registrazioni per l'app**.
    - Se è impostata su **Sì**, gli utenti non amministratori possono registrare le app AD. Questa impostazione indica che qualsiasi utente in Azure AD può registrare un'app. È possibile passare ad Autorizzazioni obbligatorie per la sottoscrizione di Azure.  
    ![Registrazioni per l'app](./media/activate-subs-accounts/app-register.png)
    - Se l'opzione **Registrazioni per l'app** è impostata su **No**, solo gli utenti amministratori del tenant possono registrare le app Azure Active Directory. L'amministratore del tenant deve registrare l'applicazione CloudynCollector.


## <a name="add-an-account-or-update-a-subscription"></a>Aggiungere un account o aggiornare una sottoscrizione

Quando si aggiunge una sottoscrizione a un aggiornamento dell'account, si concede a Gestione costi di Azure l'accesso ai dati di Azure.

### <a name="add-a-new-account-subscription"></a>Aggiungere un nuovo account (sottoscrizione)

1. Nel portale di Gestione costi di Azure fare clic sul simbolo a forma di ingranaggio in alto a destra e selezionare **Cloud Accounts** (Account cloud).
2. Fare clic su **Aggiungi un nuovo account** per visualizzare la finestra di dialogo **Aggiungi un nuovo account**. Immettere le informazioni necessarie.  
    ![Finestra di dialogo Aggiungi un nuovo account](./media/activate-subs-accounts//add-new-account.png)

### <a name="update-a-subscription"></a>Aggiornare una sottoscrizione

1. Per aggiornare una sottoscrizione _non attivata_ già esistente in Gestione costi di Azure, fare clic sul simbolo a forma di matita a destra del _GUID del tenant_ padre. Le sottoscrizioni vengono raggruppate in un tenant padre. Evitare pertanto di attivare le sottoscrizioni singolarmente.
    ![Individuare di nuovo le sottoscrizioni](./media/activate-subs-accounts/existing-sub.png)
2. Se necessario, immettere l'ID del tenant. Se non si conosce l'ID del tenant, seguire questa procedura per trovarlo:
    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Nel portale di Azure selezionare **Azure Active Directory**.
    3. Per ottenere l'ID tenant selezionare **Proprietà** per il tenanto di Azure AD.
    4. Copiare il GUID ID directory. Questo valore è l'ID tenant.
    Per altre informazioni, vedere [Ottenere l'ID tenant](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
3. Se necessario, selezionare l'ID della tariffa. Se non si conosce l'ID della tariffa, seguire questa procedura per trovarlo.
    1. In alto a destra nel portale di Azure fare clic sulle informazioni utente e quindi su **Visualizza fattura**.
    2. In **Account di fatturazione** fare clic su **Sottoscrizioni**.
    3. In **Sottoscrizioni personali** selezionare la sottoscrizione.
    4. L'ID della tariffa è indicato in **ID offerta**. Copiare l'ID offerta per la sottoscrizione.
4. Nella finestra di dialogo Aggiungi un nuovo account (o Modifica sottoscrizione) fare clic su **Salva** (o su **Avanti**). Si verrà reindirizzati al portale di Azure.
5. Accedere al portale. Fare clic su **Accetta** per autorizzare l'agente di raccolta Gestione costi di Azure ad accedere all'account Azure.

    Si verrà reindirizzati alla pagina di gestione degli account Gestione costi di Azure e la sottoscrizione verrà aggiornata con lo stato dell'account **attivo**. Nella colonna Resource Manager verrà visualizzato un simbolo di segno di spunta verde.

    Se non viene visualizzato un simbolo di segno di spunta verde per una o più sottoscrizioni, significa che non sono disponibili le autorizzazioni per creare l'applicazione lettore (CloudynCollector) per la sottoscrizione. Un utente con autorizzazioni più elevate per la sottoscrizione deve ripetere questo processo.

Guardare il video [Connecting to Azure Resource Manager with Azure Cost Management by Cloudyn](https://youtu.be/oCIwvfBB6kk) (Connessione ad Azure Resource Manager con Gestione costi di Azure di Cloudyn) che illustra il processo.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Risolvere i problemi di configurazione indiretta dell'organizzazione

Quando si usa per la prima volta il portale di Gestione costi di Azure, vengono visualizzati i messaggi seguenti se si ha un contratto Enterprise o Provider di soluzioni cloud (CSP):

- *The specified API key is not a top level enrollment key* (La chiave API specificata non è una chiave di registrazione principale), nella procedura guidata **Set Up Azure Cost Management** (Configurazione di Gestione costi di Azure).
- *Direct Enrollment – No* (Registrazione diretta - No), nel portale per i contratti Enterprise.
- *No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account* (Non sono stati trovati dati di utilizzo per gli ultimi 30 giorni. Contattare il distributore per verificare che per l'account Azure sia stato abilitato il markup), nel portale di Gestione costi di Azure.

I messaggi precedenti indicano che è stato acquistato un contratto Enterprise di Azure tramite un rivenditore o un CSP. Per poter visualizzare i dati in Gestione costi di Azure, è necessario che il rivenditore o il CSP abiliti il _markup_ per l'account Azure.

Ecco come risolvere i problemi:

1. Il rivenditore deve abilitare il _markup_ per l'account. Per le istruzioni, vedere la [guida introduttiva per i clienti indiretti](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. Generare la chiave del contratto Enterprise di Azure da usare con Gestione costi di Azure. Per istruzioni, vedere [Registrare un Azure Enterprise Agreement e visualizzare i dati sui costi](https://docs.microsoft.com/en-us/azure/cost-management/quick-register-ea).

Gestione costi può essere abilitato solo da un amministratore del servizio Azure. Le autorizzazioni di coamministratore non sono sufficienti.

Per poter generare la chiave API del contratto Enterprise di Azure per la configurazione di Gestione costi di Azure, abilitare prima di tutto l'API di fatturazione di Azure seguendo queste istruzioni:

- [Panoramica delle API di creazione di report per i clienti Enterprise](../billing/billing-enterprise-api.md)
- Sezione **Enabling data access to the API** (Abilitazione dell'accesso ai dati nell'API) in [Microsoft Azure Enterprise Portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (API di creazione report di Microsoft Azure Enterprise Portal)

Potrebbe anche essere necessario concedere ad amministratori di reparto, proprietari di account e amministratori dell'organizzazione le autorizzazioni per _visualizzare gli addebiti_ con l'API di fatturazione.

## <a name="next-steps"></a>Passaggi successivi

- Se non si è ancora completata la prima esercitazione per Gestione costi, leggere le informazioni riportate in [Esaminare l'utilizzo e i costi](tutorial-review-usage.md).
