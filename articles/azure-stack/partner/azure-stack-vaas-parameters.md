---
title: Parametri comuni del flusso di lavoro nella convalida di Azure Stack come servizio | Microsoft Docs
description: Parametri comuni del flusso di lavoro per la convalida di Azure Stack come servizio
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 81a7be973739cfd6eb3f8fb8dc7a0723623c2b8e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235317"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Parametri comuni del flusso di lavoro per la convalida di Azure Stack come servizio

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Parametri comuni includono valori, ad esempio le variabili di ambiente e l'utente le credenziali richieste da tutti i test nella convalida come servizio (VaaS). Si definiscono questi valori a livello di flusso di lavoro. Salvare i valori quando si crea o si modifica un flusso di lavoro. In fase di pianificazione, il flusso di lavoro carica i valori per il test. 

## <a name="environment-parameters"></a>Parametri di ambiente

Parametri di ambiente descrivono l'ambiente dello Stack di Azure sottoposta a test. È necessario specificare questi valori mediante la generazione e il caricamento di file di configurazione dell'indicatore `&lt;link&gt;. [How to get the stamp info link].`

| Nome parametro | Obbligatorio | type | Description |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Compilazione di Azure Stack | Obbligatorio |  | Numero di build della distribuzione di Azure Stack (ad esempio, 1.0.170330.9) |
| Versione OEM | Sì |  | Numero di versione del pacchetto OEM usata durante la distribuzione di Azure Stack. |
| Firma OEM | Sì |  | Firma del pacchetto OEM usata durante la distribuzione di Azure Stack. |
| ID del tenant AAD | Obbligatorio |  | Tenant di Azure Active Directory GUID specificato durante la distribuzione di Azure Stack.|
| Region | Obbligatorio |  | Area di distribuzione di Azure Stack. |
| Endpoint di Resource Manager tenant | Obbligatorio |  | Endpoint per le operazioni di Azure Resource Manager tenant (ad esempio, https://management.<ExternalFqdn>) |
| Endpoint amministratore Resource Manager | Sì |  | Endpoint per le operazioni di Tenant di Azure Resource Manager (ad esempio, https://adminmanagement.<ExternalFqdn>) |
| Nome di dominio completo esterna | Sì |  | Nome di dominio usato come suffisso per gli endpoint esterno completo. (ad esempio, local.azurestack.external o redmond.contoso.com). |
| Numero di nodi | Sì |  | Numero di nodi nella distribuzione. |

## <a name="test-parameters"></a>Parametri di test

Parametri di test comuni includono informazioni riservate che non sono memorizzate nei file di configurazione e devono essere forniti manualmente.

| Nome parametro | Obbligatorio | type | Description |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome utente tenant | Obbligatorio |  | Azure amministratore del Tenant di Active Directory che sia stato effettuato il provisioning già oppure deve essere eseguito il provisioning per l'amministratore del servizio nella Directory di AAD. Per informazioni dettagliate sul provisioning degli account del tenant, vedere [iniziare a usare Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). Questo valore viene utilizzato dal test per eseguire operazioni a livello di tenant, ad esempio la distribuzione di modelli per eseguire il provisioning delle risorse (macchine virtuali, account di archiviazione e così via) ed eseguire i carichi di lavoro. Questo valore viene utilizzato dal test per eseguire operazioni a livello di tenant, ad esempio la distribuzione di modelli per eseguire il provisioning delle risorse (macchine virtuali, account di archiviazione e così via) ed eseguire i carichi di lavoro. |
| Password del tenant | Obbligatorio |  | Password per l'utente del tenant. |
| Nome utente amministratore del servizio | Richiesto: Convalida soluzione, la convalida del pacchetto<br>Non è necessario: superamento Test |  | Azure amministratore di Active Directory del Tenant di Directory AAD specificato durante la distribuzione di Azure Stack. |
| Password amministratore del servizio | Richiesto: Convalida soluzione, la convalida del pacchetto<br>Non è necessario: superamento Test |  | Password per l'utente amministratore del servizio. |
| Nome utente amministratore del cloud | Obbligatorio |  | Azure Stack account amministratore di dominio (ad esempio, contoso\cloudadmin). Ricerca per ruolo utente = "CloudAdmin" nel file di configurazione e selezionare il valore nel tag di nome utente nel file di configurazione. |
| Password amministratore del cloud | Obbligatorio |  | Password per l'utente amministratore del Cloud. |
| Stringa di connessione di diagnostica | Obbligatorio |  | Un URI SAS a un Account di archiviazione di Azure per la diagnostica quali log verranno copiati durante l'esecuzione di test. Le istruzioni per la generazione dell'URI SAS si trovano [configurare un account di archiviazione blob](azure-stack-vaas-set-up-account.md). |


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).
