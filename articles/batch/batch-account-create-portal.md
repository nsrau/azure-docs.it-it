---
title: Creare un account Batch nel portale di Azure | Microsoft Docs
description: Informazioni su come creare un account Azure Batch nel portale di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/18/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89e41dc8e27cf39d9d0e6168dc7352267c321623
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460523"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creare un account Batch nel portale di Azure

Informazioni su come creare un account Batch di Azure nel [portale di Azure][azure_portal], scegliere le proprietà dell'account adatte allo scenario di calcolo. Informazioni su dove trovare le proprietà di account importanti come le chiavi di accesso e gli URL dell'account.

Per informazioni sugli account e gli scenari Batch, vedere la [panoramica della funzionalità](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Creare un account Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Accedere al [portale di Azure][azure_portal].

1. Selezionare **Crea una risorsa** > **Calcolo** > **Servizio Batch**.

    ![Batch in Marketplace][marketplace_portal]

1. Immettere le impostazioni di **Nuovo account Batch**. Vedere i dettagli seguenti.

    ![Creare un account Batch][account_portal]

    a. **Account name** (Nome dell'account): il nome scelto deve essere univoco all'interno dell'area di Azure in cui viene creato l'account. Vedere **Località** di seguito. Il nome dell'account può contenere solo caratteri minuscoli o numeri e deve avere una lunghezza di 3-24 caratteri.

    b. **Sottoscrizione** sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

    c. **Gruppo di risorse**: selezionare un gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.

    d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse vengono visualizzate come opzioni.

    e. **Account di archiviazione** (facoltativo): account di Archiviazione di Azure associato all'account Batch. Tale operazione è consigliata per la maggior parte degli account Batch. Per le opzioni dell'account di archiviazione in Batch, vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md#azure-storage-account). Nel portale selezionare un account di archiviazione esistente o crearne uno nuovo.

      ![Creare un account di archiviazione][storage_account]

    f. **Modalità di allocazione del pool**: per la maggior parte degli scenari accettare il valore predefinito **Servizio Batch**.

1. Selezionare **Crea** per creare l'account.



## <a name="view-batch-account-properties"></a>Visualizzare le proprietà dell'account Batch
Dopo aver creato l'account, selezionarlo per accedere alle impostazioni e proprietà associate. È possibile accedere a tutte le impostazioni e proprietà dell'account usando il menu a sinistra.

![Pagina Account Batch nel portale di Azure][account_blade]

* **Nome, URL e chiavi dell'account Batch**: quando si sviluppa un'applicazione con le [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), sono necessari un URL e una chiave dell'account per accedere alle risorse di Batch. Batch supporta anche l'autenticazione di Azure Active Directory.

    Per visualizzare le informazioni di accesso dell'account Batch, selezionare **Chiavi**.

    ![Chiavi dell'account Batch nel portale di Azure][account_keys]

* Per visualizzare il nome e le chiavi dell'account di archiviazione associato all'account Batch, selezionare **Account di archiviazione**.

* Per visualizzare le quote di risorse applicabili all'account Batch, selezionare **Quote**. Per dettagli, vedere [Quote e limiti del servizio Batch](batch-quota-limit.md).


## <a name="additional-configuration-for-user-subscription-mode"></a>Configurazione aggiuntiva per la modalità Sottoscrizione utente

Se si sceglie di creare un account Batch in modalità Sottoscrizione utente, prima di creare l'account eseguire questi passaggi aggiuntivi.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Consentire ad Azure Batch di accedere alla sottoscrizione (operazione occasionale)
Quando si crea il primo account Batch in modalità Sottoscrizione utente, è necessario registrare la sottoscrizione in Batch. Se questa operazione è stata fatta precedentemente, passare alla sezione successiva.

1. Accedere al [portale di Azure][azure_portal].

1. Selezionare **Altri servizi** > **Sottoscrizioni** e quindi fare clic sulla sottoscrizione che si intende usare per l'account Batch.

1. Nella pagina **Sottoscrizione** selezionare **Provider di risorse** e cercare **Microsoft.Batch**. Verificare che il provider di risorse **Microsoft.Batch** sia registrato nella sottoscrizione. Se non è registrato, selezionare il collegamento **Registra**.

    ![Registrare il provider Microsoft.Batch][register_provider]

1. Nella pagina **Sottoscrizione** selezionare**Controllo di accesso (IAM)** > **Assegnazioni di ruolo** > **Aggiungi assegnazione di ruolo**.

    ![Controllo di accesso alla sottoscrizione][subscription_access]

1. Nella pagina **Aggiungi assegnazione di ruolo** selezionare il ruolo **Collaboratore** e cercare l'API Batch. Cercare ognuna di queste stringhe fino a trovare l'API:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. I tenant di Azure AD più recenti potrebbero usare questo nome.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** è l'ID dell'API Batch. 

1. Dopo aver trovato l'API Batch, selezionarla e fare clic su **Salva**.

    ![Aggiungere le autorizzazioni di Batch][add_permission]

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
In modalità di sottoscrizione utente, è necessario un insieme di credenziali delle chiavi di Azure che appartiene allo stesso gruppo di risorse come l'account Batch da creare. Assicurarsi che il gruppo di risorse sia in un'area in cui Batch è [disponibile](https://azure.microsoft.com/regions/services/) e che supporta la sottoscrizione.

1. Nel [portale di Azure][azure_portal] selezionare **Nuovo** > **Sicurezza** > **Insieme di credenziali delle chiavi**.

1. Nella pagina **Crea insieme di credenziali delle chiavi** immettere un nome per l'insieme di credenziali delle chiavi e creare un gruppo di risorse nell'area desiderata per l'account Batch. Lasciare i valori predefiniti per le impostazioni rimanenti, quindi selezionare **Crea**.

Quando si crea l'account Batch in modalità di sottoscrizione utente, usare il gruppo di risorse per l'insieme di credenziali delle chiavi, specificare **Sottoscrizione utente** come modalità di allocazione del pool e selezionare l'insieme di credenziali delle chiavi.

## <a name="other-batch-account-management-options"></a>Altre opzioni di gestione dell'account Batch
Oltre a usare il portale di Azure, è possibile creare e gestire gli account Batch con alcuni strumenti, tra cui:

* [Cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md)
* [Interfaccia della riga di comando di Azure](batch-cli-get-started.md)
* [.NET per la gestione di Batch](batch-management-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md). L'articolo descrive le risorse primarie di Batch, tra cui pool, nodi di calcolo, processi e attività, e fornisce una panoramica delle funzionalità del servizio per carichi di lavoro di calcolo su larga scala.
* Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](quick-run-dotnet.md) o con [Python](quick-run-python.md). Queste guide introduttive illustrano un'applicazione di esempio che usa il servizio Batch per eseguire un carico di lavoro su più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png

