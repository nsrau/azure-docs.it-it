---
title: Configurare la convalida di Azure Stack come account del servizio | Microsoft Docs
description: Informazioni su come configurare la convalida come account del servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: b94d37d528739247cb4f94a17dadf3876c4db6bd
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158509"
---
# <a name="set-up-your-validation-as-a-service-account"></a>Configurare la convalida come account del servizio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

La convalida come servizio (VaaS) è un servizio di Azure che viene reso disponibile per i partner di Microsoft Azure Stack che dispongono di un contratto di CO-engineering con Microsoft per progettare, sviluppare, convalidare, vendere, distribuire e supportare soluzioni di Azure Stack nel mercato.

Informazioni su come prepararsi per la convalida come servizio di sistema. Configurare l'istanza di Azure Active Directory ed eseguire altre attività necessarie per la preparazione all'uso VaaS. 

Le attività includono:

- Creare un blob di archiviazione di Azure per archiviare i log
- Distribuire l'agente locale
- Scaricare le macchine virtuali di test immagine nell'istanza di Azure Stack da sottoporre a test

## <a name="create-an-azure-active-directory-tenant-id"></a>Creare un ID tenant di Azure Active Directory

1. Creare un tenant di Azure Active Directory nel [portale di Azure](https://portal.azure.com) o usare un tenant esistente.

    È consigliabile creare un tenant in modo specifico per l'uso con VaaS con un nome descrittivo, ad esempio, ContosoVaaS@onmicrosoft.com. Le funzionalità di controllo degli accessi in base al ruolo di accesso del tenant da utilizzare per il partner per gestire quali utenti dell'organizzazione partner possono usare VaaS.  
    
    Altre informazioni, vedere [gestire la directory di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

    > [!Note]  
    > Per altre informazioni sulla creazione di nuovi tenant di Azure Active Directory, vedere [iniziare a usare Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad).

2. Aggiungere i membri dell'organizzazione responsabile per l'utilizzo del servizio per il tenant. Assegnare a ogni utente nel tenant di uno dei seguenti ruoli per controllare il livello di accesso a VaaS:

    | Nome ruolo | DESCRIZIONE |
    |---------------------|------------------------------------------|
    | Proprietario | Ha accesso completo a tutte le risorse. |
    | Reader | Può visualizzare tutte le risorse, ma non modificare. |
    | Collaboratore di test | Può gestire le risorse di test. |
    | Collaboratore del catalogo | Può gestire le risorse di pubblicazione di una soluzione. |

## <a name="set-up-your-tenant"></a>Configurare il tenant

Configurare il tenant nel **servizio di Azure Stack convalida** dell'applicazione. 

1. Inviare le informazioni seguenti relative al tenant a Microsoft all'indirizzo vaashelp@microsoft.com.

    | Dati | DESCRIZIONE |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nome organizzazione | Nome ufficiale dell'organizzazione. |
    | Nome della Directory Tenant di Azure AD | Nome di Directory del Tenant AD Azure in corso la registrazione. |
    | Id del Tenant di Azure Active Directory | Azure AD Tenant Directory GUID associata alla directory.<br> Per informazioni su come trovare l'ID di Directory del Tenant di Azure AD sono disponibili, vedere "[ottenere l'ID tenant](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)." |

    

2. Il team di Azure Stack offre conferma che il tenant può usare il portale VaaS.

3. Usare le credenziali di amministratore globale del tenant per accedere al [VaaS portale](https://azurestackvalidation.com/
). Selezionare **il mio Account**.

    ![Accedere al portale VaaS](media/vaas_portalsignin.png)

3. Il sito verrà richiesto di concedere l'accesso per VaaS. Accettare le condizioni per continuare.

## <a name="assign-user-roles"></a>Assegnare i ruoli utente

Per assegnare un ruolo utente:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutti i servizi** > **Azure Active Directory** nel **identità** gruppo.
3. Selezionare **applicazioni aziendali** > **servizio di Azure Stack convalida** dell'applicazione.
4. Selezionare **Utenti e gruppi**. Il **Azure Stack di servizio di convalida - utenti e i gruppi** pannello elenca gli utenti con autorizzazione a utilizzare l'applicazione.
5. Selezionare **+ Add user** per aggiungere un'assegnazione.

## <a name="create-an-azure-storage-blob-to-store-logs"></a>Creare un blob di archiviazione di Azure per archiviare i log

VaaS crea i log di diagnostica quando si esegue il test di convalida. È necessario un percorso di un URL di firma di accesso condiviso per archiviare i log del servizio blob di Azure. L'account di archiviazione può anche utilizzabile per i pacchetti di personalizzazione di archivio OEM.

Questa procedura illustra come configurare e generare un URI di archiviazione-as-a-service (SAS) per un account di archiviazione di Azure e dove specificare l'account di archiviazione nel portale di VaaS all'avvio di test nel portale.

### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

1. Per creare un account di archiviazione, seguire le istruzioni [creare un account di archiviazione](../../storage/common/storage-quickstart-create-account.md).

2. Quando si seleziona il tipo di account di archiviazione, selezionare la **archiviazione Blob** tipo di account.

### <a name="generate-a-sas-url-for-the-storage-account"></a>Generare un URL di firma di accesso condiviso dell'account di archiviazione

1. Passare all'account di archiviazione creato in precedenza.

2. Nel pannello sotto **le impostazioni**, selezionare **firma di accesso condiviso**.

3. Solo controllo **Blob** dalla **opzioni di servizi consentiti** (deselezionare i rimanenti).

4. Controllare **assistenza**, * * contenitore, e **oggetto** dalla **tipi di risorse consentiti**.

5. Controllare **Read**, **scrivere**, **elenco**, **Aggiungi**, **crea** da **disponga delle autorizzazioni**  (deselezionare i rimanenti).

6. Impostare **ora di inizio** sull'ora corrente, e **ora di fine** per tre mesi dall'ora corrente.

7. Selezionare **stringa di connessione e genera firma di accesso condiviso** e salvare il **URL di firma di accesso condiviso del servizio Blob** stringa.

> [!Note]  
> L'URL di firma di accesso condiviso scade l'ora di fine impostato quando l'URL è stato generato. Assicurarsi che l'URL sia sufficientemente valido prima di condividerla con il team di prodotto per il debug oppure che l'URL sia valido per più di 30 giorni, durante la pianificazione dei test.

## <a name="next-steps"></a>Passaggi successivi

- Usare l'agente locale VaaS per controllare l'hardware. Per istruzioni, vedere [distribuire le macchine virtuali locali di test e agenti](azure-stack-vaas-test-vm.md).
- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).