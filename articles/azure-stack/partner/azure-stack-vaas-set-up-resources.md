---
title: 'Esercitazione: configurare le risorse per la convalida come servizio | Microsoft Docs'
description: In questa esercitazione, informazioni su come configurare le risorse per la convalida come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 55c9120547472bb9a9a74533fe532d346844e89c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081764"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Esercitazione: Configurare le risorse per la convalida come servizio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

La convalida come servizio (VaaS) è un servizio di Azure che consente di convalidare e supportare le soluzioni di Azure Stack nel mercato. Seguire questo articolo prima di usare il servizio per accreditare la soluzione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Prepararsi a utilizzare VaaS impostando backup di Azure Active Directory (AD).
> * Creare un account di archiviazione.

## <a name="configure-an-azure-ad-tenant"></a>Configurare un tenant di Azure AD

Un tenant di Azure AD viene usato per registrare un'organizzazione e autenticare gli utenti con VaaS. Il partner utilizzerà le funzionalità di controllo degli accessi in base al ruolo di accesso del tenant per gestire quali utenti dell'organizzazione partner possono usare VaaS. Per altre informazioni, vedere [Informazioni su Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Creare un tenant

Creare un tenant che verranno utilizzati per accedere ai servizi VaaS nell'organizzazione. Usare un nome descrittivo, ad esempio `ContosoVaaS@onmicrosoft.com`.

1. Creare un tenant di Azure AD nel [portale di Azure](https://portal.azure.com), o usare un tenant esistente. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Aggiungere i membri dell'organizzazione nel tenant. Questi utenti sono responsabili per l'uso del servizio per visualizzare o pianificare i test. Dopo aver completato la registrazione, verranno definiti i livelli di accesso degli utenti.

    Autorizzare gli utenti nel tenant per eseguire azioni in VaaS assegnando i ruoli seguenti:

    | Nome ruolo | DESCRIZIONE |
    |---------------------|------------------------------------------|
    | Proprietario | Ha accesso completo a tutte le risorse. |
    | Reader | Può visualizzare tutte le risorse, ma non creare o gestire. |
    | Collaboratore di test | Creare e gestire le risorse di test. |

    Per assegnare i ruoli nel **servizio di Azure Stack convalida** applicazione:

   1. Accedere al [portale di Azure](https://portal.azure.com).
   2. Selezionare **tutti i servizi** > **Azure Active Directory** sotto il **identità** sezione.
   3. Selezionare **applicazioni aziendali** > **servizio di Azure Stack convalida** dell'applicazione.
   4. Selezionare **Utenti e gruppi**. Il **Azure Stack di servizio di convalida - utenti e i gruppi** pannello elenca gli utenti con autorizzazione a utilizzare l'applicazione.
   5. Selezionare **+ Add user** per aggiungere un utente dal tenant e assegnare un ruolo.

      Se si vuole isolare le risorse VaaS e azioni tra gruppi diversi all'interno di un'organizzazione, è possibile creare più directory di tenant di Azure AD.

### <a name="register-your-tenant"></a>Registrazione del tenant

Questo processo autorizza il tenant con il **servizio di Azure Stack convalida** applicazione Azure AD.

1. Inviare le informazioni seguenti relative al tenant a Microsoft all'indirizzo [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

    | Dati | DESCRIZIONE |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nome organizzazione | Il nome ufficiale dell'organizzazione. |
    | Nome della Directory Tenant di Azure AD | Il nome della Directory di Tenant di Azure AD in fase di registrazione. |
    | ID del Tenant di Azure Active Directory | Tenant di Azure AD Directory associata alla directory GUID. Per informazioni su come trovare l'ID di Directory del Tenant di Azure AD, vedere [ottenere l'ID tenant](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). |

2. Attendere la conferma dal team di Azure Stack convalida per verificare che il tenant può usare il portale VaaS.

### <a name="consent-to-the-vaas-application"></a>Fornire il consenso all'applicazione VaaS

Come amministratore di Azure AD, assegnare all'applicazione di Azure AD VaaS le autorizzazioni necessarie per conto del tenant:

1. Usare le credenziali di amministratore globale del tenant per accedere al [VaaS portale](https://azurestackvalidation.com/). 

2. Selezionare **il mio Account**.

3 accettare le condizioni per continuare quando viene richiesto di concedere le autorizzazioni VaaS elencati Azure AD.

## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Durante l'esecuzione dei test VaaS genera i log di diagnostica a un account di archiviazione di Azure. Oltre ai log di test, l'account di archiviazione può anche utilizzabile per i pacchetti di estensione caricamento OEM per il flusso di lavoro di convalida del pacchetto.

L'account di archiviazione di Azure è ospitata nel cloud pubblico di Azure, non nell'ambiente Azure Stack.

1. Nel portale di Azure, selezionare **tutti i servizi** > **archiviazione** > **gli account di archiviazione**. Nel **gli account di archiviazione** blade, selezionare **Add**.

2. Selezionare la sottoscrizione in cui creare l'account di archiviazione.

3. Sotto **gruppo di risorse**, selezionare **Crea nuovo**. Immettere un nome per il nuovo gruppo di risorse.

4. Rivedere le [convenzioni di denominazione](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions#storage) per gli account di archiviazione di Azure. Immettere un nome per l'account di archiviazione.

5. Selezionare il **Stati Uniti occidentali** area dell'account di archiviazione.

    Per garantire che per l'archiviazione dei log non vengono addebitati i costi rete, l'account di archiviazione di Azure può essere configurato per utilizzare solo il **Stati Uniti occidentali** area. La replica dei dati e la funzionalità di livello di archiviazione ad accesso frequente non sono necessari per questi dati. L'abilitazione di una funzionalità aumenta notevolmente i costi.

6. Lasciare le impostazioni per i valori predefiniti, ad eccezione di **tipologia Account**:

    - Il **modello di distribuzione** campo è impostato su **Resource Manager** per impostazione predefinita.
    - Per impostazione predefinita, il campo **Prestazioni** deve essere impostato su **Standard**.
    - Selezionare **tipologia Account** nel campo **archivio Blob**.
    - Il **campo replica** è impostata su **l'archiviazione con ridondanza locale (LRS)** per impostazione predefinita.
    - Per impostazione predefinita, il campo **Livello di accesso** deve essere impostato su **Frequente**.

7. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.

## <a name="next-steps"></a>Passaggi successivi

Se l'ambiente non consente connessioni in ingresso, seguire l'esercitazione sulla distribuzione l'agente locale per eseguire un test sull'hardware.

> [!div class="nextstepaction"]
> [Distribuire l'agente locale](azure-stack-vaas-local-agent.md)
