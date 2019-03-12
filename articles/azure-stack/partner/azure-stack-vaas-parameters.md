---
title: Parametri comuni del flusso di lavoro in Azure Stack di convalida come servizio | Microsoft Docs
description: Parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f66f57799e1e6b6d0e27624e3dc08b4de5d09cac
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766200"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

I parametri comuni includono i valori come variabili di ambiente e l'utente le credenziali richieste da tutti i test nella convalida come servizio (VaaS). Questi valori sono definiti a livello di flusso di lavoro quando si crea o si modifica un flusso di lavoro. Durante la pianificazione dei test, questi valori vengono passati come parametri per ogni test con il flusso di lavoro.

> [!NOTE]
> Ogni test definisce il proprio set di parametri. In fase di pianificazione, un test potrebbe essere necessario immettere un valore indipendentemente dai parametri comuni, o può consentire è possibile sostituire il valore del parametro comune.

## <a name="environment-parameters"></a>Parametri di ambiente

Parametri di ambiente descrivono l'ambiente dello Stack di Azure sottoposta a test. Generazione e caricando un file di informazioni timbro Azure Stack per l'istanza specifica, che si esegue il test, è necessario specificare questi valori.

> [!NOTE]
> Nei flussi di lavoro ufficiale convalida dei parametri di ambiente non possono essere modificati dopo la creazione del flusso di lavoro.

### <a name="generate-the-stamp-information-file"></a>Generare il file di timestamp informazioni

1. Accedere a qualsiasi computer dotato di accesso all'ambiente Azure Stack o di DVM.
2. Eseguire i comandi seguenti in una finestra di PowerShell con privilegi elevata:

    ```PowerShell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Individuare i valori nel file di configurazione ECE

I valori di parametro di ambiente possono anche essere disponibili manualmente nel **file di configurazione ECE** che si trova in `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` di DVM.

## <a name="test-parameters"></a>Parametri di test

Parametri di test comuni includono informazioni riservate che non possono essere archiviate nei file di configurazione. Questi devono essere forniti manualmente.

Parametro    | DESCRIZIONE
-------------|-----------------
Utente amministratore tenant                            | Azure Active Directory amministratore Tenant è stato effettuato il provisioning dall'amministratore del servizio nella directory di AAD. L'utente esegue azioni a livello di tenant, ad esempio la distribuzione di modelli per configurare le risorse (macchine virtuali, gli account di archiviazione e così via) e l'esecuzione di carichi di lavoro. Per informazioni dettagliate sul provisioning account tenant, vedere [aggiunge un nuovo tenant di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad).
Utente amministratore del servizio             | Azure amministratore Active Directory del Tenant di Directory AAD specificato durante la distribuzione di Azure Stack. Cercare `AADTenant` nella configurazione ECE file e selezionare il valore di `UniqueName` elemento.
Utente amministratore del cloud               | Account amministratore di Azure Stack dominio (ad esempio, `contoso\cloudadmin`). Cercare `User Role="CloudAdmin"` nella configurazione ECE file e selezionare il valore di `UserName` elemento.
Stringa di connessione di diagnostica          | Un URL di firma di accesso condiviso a un Account di archiviazione di Azure per la diagnostica quali log verranno copiati durante l'esecuzione di test. Per istruzioni su come generare l'URL di firma di accesso condiviso, vedere [generi la stringa di connessione di diagnostica](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> Il **stringa di connessione di diagnostica** deve essere valido prima di procedere.

### <a name="generate-the-diagnostics-connection-string"></a>Generare la stringa di connessione di diagnostica

La stringa di connessione di diagnostica è necessaria per l'archiviazione dei log di diagnostica durante l'esecuzione di test. Usare l'Account di archiviazione di Azure creato durante l'installazione (vedere [configurare la convalida come le risorse di un servizio](azure-stack-vaas-set-up-resources.md)) per creare un URL di firma di accesso condiviso per concedere l'accesso VaaS per caricare i log all'account di archiviazione.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Selezionare **Blob** dalla **opzioni di servizi consentiti**. Deselezionare le altre opzioni.

1. Selezionare **assistenza**, **contenitore**, e **oggetto** da **tipi di risorse consentiti**.

1. Selezionare **Read**, **scrivere**, **elenco**, **aggiungere**, **creare** da **consentiti le autorizzazioni**. Deselezionare le altre opzioni.

1. Impostare **ora di inizio** sull'ora corrente, e **ora di fine** per tre mesi dall'ora corrente.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> L'URL di firma di accesso condiviso scade alla data di fine specificata quando è stato generato l'URL.  
Durante la pianificazione dei test, assicurarsi che l'URL è valido per almeno 30 giorni più il tempo necessario per l'esecuzione di test (tre mesi viene suggerito).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [convalida come un concetti chiave di servizio](azure-stack-vaas-key-concepts.md)