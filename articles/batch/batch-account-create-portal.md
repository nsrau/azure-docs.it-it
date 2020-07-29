---
title: Creare un account nel portale di Azure
description: Informazioni su come creare un account Azure Batch nel portale di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud
ms.topic: how-to
ms.date: 06/10/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1205de2b800588b735aeb20d388ba4b64bc6b078
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711341"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creare un account Batch nel portale di Azure

In questo argomento viene illustrato come creare un account Azure Batch nel [portale di Azure](https://portal.azure.com), scegliendo le proprietà dell'account adatte allo scenario di calcolo. Si apprenderà anche dove trovare importanti proprietà dell'account, come le chiavi di accesso e gli URL degli account.

Per informazioni sugli account e gli scenari Batch, vedere [Flusso di lavoro del servizio Batch e risorse](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Creare un account Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una risorsa**, quindi selezionare **calcolo** e **servizio batch**.

1. Immettere le impostazioni di **Nuovo account Batch**. Vedere i dettagli seguenti.

    ![Creare un account Batch][account_portal]

    a. **Sottoscrizione** sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

    b. **Gruppo di risorse**: selezionare un gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.

    c. **Account name** (Nome dell'account): il nome scelto deve essere univoco all'interno dell'area di Azure in cui viene creato l'account. Vedere **Località** di seguito. Il nome dell'account può contenere solo caratteri minuscoli o numeri e deve avere una lunghezza di 3-24 caratteri.

    d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse vengono visualizzate come opzioni.

    e. **Account di archiviazione**: un account di Archiviazione di Azure facoltativo da associare all'account Batch. Per prestazioni ottimali, è consigliabile configurare un account di archiviazione per utilizzo generico v2. Per tutte le opzioni dell'account di archiviazione in Batch, vedere [Panoramica delle funzionalità di Batch](accounts.md#azure-storage-accounts). Nel portale selezionare un account di archiviazione esistente o crearne uno nuovo.

      ![Creare un account di archiviazione][storage_account]

    f. **Modalità di allocazione del pool**: Nella scheda di impostazioni **Avanzate** è possibile specificare la modalità di allocazione del pool come **Servizio Batch** o come **Sottoscrizione utente**. per la maggior parte degli scenari accettare il valore predefinito **Servizio Batch**.

      ![Modalità di allocazione del pool][pool_allocation]

1. Selezionare **Crea** per creare l'account.

## <a name="view-batch-account-properties"></a>Visualizzare le proprietà dell'account Batch

Dopo aver creato l'account, selezionarlo per accedere alle impostazioni e proprietà associate. È possibile accedere a tutte le impostazioni e proprietà dell'account usando il menu a sinistra.

> [!NOTE]
> Il nome dell'account Batch è il relativo ID e non può essere modificato. Per modificare il nome di un account Batch, è necessario eliminare l'account e crearne uno nuovo con il nome desiderato.

![Pagina Account Batch nel portale di Azure][account_blade]

* **Nome, URL e chiavi dell'account Batch**: quando si sviluppa un'applicazione con le [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), sono necessari un URL e una chiave dell'account per accedere alle risorse di Batch. Batch supporta anche l'autenticazione di Azure Active Directory.

    Per visualizzare le informazioni di accesso dell'account Batch, selezionare **Chiavi**.

    ![Chiavi dell'account Batch nel portale di Azure][account_keys]

* Per visualizzare il nome e le chiavi dell'account di archiviazione associato all'account Batch, selezionare **Account di archiviazione**.

* Per visualizzare le quote di risorse applicabili all'account Batch, selezionare **Quote**. Per dettagli, vedere [Quote e limiti del servizio Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Configurazione aggiuntiva per la modalità Sottoscrizione utente

Se si sceglie di creare un account Batch in modalità Sottoscrizione utente, prima di creare l'account eseguire questi passaggi aggiuntivi.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Consentire ad Azure Batch di accedere alla sottoscrizione (operazione occasionale)

Quando si crea il primo account Batch in modalità Sottoscrizione utente, è necessario registrare la sottoscrizione in Batch. Se è già stato fatto, passare alla sezione successiva.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Altri servizi** > **Sottoscrizioni** e quindi fare clic sulla sottoscrizione che si intende usare per l'account Batch.

1. Nella pagina **Sottoscrizione** selezionare **Provider di risorse** e cercare **Microsoft.Batch**. Verificare che il provider di risorse **Microsoft.Batch** sia registrato nella sottoscrizione. Se non è registrato, selezionare il collegamento **Registra**.

    ![Registrare il provider Microsoft.Batch][register_provider]

1. Nella pagina **Sottoscrizione** selezionare**Controllo di accesso (IAM)**  > **Assegnazioni di ruolo** > **Aggiungi assegnazione di ruolo**.

    ![Controllo di accesso alla sottoscrizione][subscription_access]

1. Nella pagina **Aggiungi assegnazione ruolo** selezionare il ruolo **collaboratore** o **proprietario** , quindi cercare l'API batch. Cercare ognuna di queste stringhe fino a trovare l'API:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. I tenant di Azure AD più recenti potrebbero usare questo nome.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** è l'ID dell'API Batch.

1. Dopo aver trovato l'API Batch, selezionarla e fare clic su **Salva**.

    ![Aggiungere le autorizzazioni di Batch][add_permission]

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

In modalità di sottoscrizione utente è necessario un [Azure Key Vault](../key-vault/general/overview.md) . Il Key Vault deve trovarsi nella stessa sottoscrizione e nella stessa area dell'account batch da creare. 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Nuovo** > **Sicurezza** > **Key Vault**.

1. Nella pagina **crea Key Vault** immettere un nome per il Key Vault e creare un gruppo di risorse nell'area desiderata per l'account batch. Lasciare i valori predefiniti per le impostazioni rimanenti, quindi selezionare **Crea**.

Quando si crea l'account batch in modalità sottoscrizione utente, specificare **sottoscrizione utente** come modalità di allocazione pool, selezionare il Key Vault e selezionare la casella per concedere l'accesso Azure Batch al Key Vault.

Se si preferisce concedere l'accesso alla Key Vault manualmente, passare alla sezione **criteri di accesso** del Key Vault, selezionare **Aggiungi criteri di accesso** e cercare **Microsoft Azure batch**. Una volta selezionata, sarà necessario configurare le **autorizzazioni segrete** usando il menu a discesa. In Azure Batch deve essere specificato un numero minimo di autorizzazioni **Get**, **List**, **Set**e **Delete**.

![Autorizzazioni segrete per Azure Batch](./media/batch-account-create-portal/secret-permissions.png)

> [!NOTE]
> Assicurarsi che le caselle di controllo **Macchine virtuali di Azure per la distribuzione di** e **Azure Resource Manager per la distribuzione di modelli** siano selezionate in **Criteri di accesso** per la risorsa **Key Vault** collegata.
>
> ![Criteri di accesso Key Vault obbligatori](./media/batch-account-create-portal/key-vault-access-policy.png)

### <a name="configure-subscription-quotas"></a>Configurare le quote di sottoscrizione

Per gli account batch di sottoscrizione utente, le quote core devono essere impostate manualmente. Batch Standard le quote Core non si applicano agli account in modalità sottoscrizione utente.

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'account Batch in modalità sottoscrizione utente per visualizzare le relative impostazioni e proprietà.
1. Scegliere **Quote** dal menu a sinistra per visualizzare e configurare le quote di core associate all'account Batch.

Per altre informazioni sulle quote core della modalità sottoscrizione utente, vedere [quote e limiti del servizio batch](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Altre opzioni di gestione dell'account Batch

Oltre a usare il portale di Azure, è possibile creare e gestire gli account Batch con alcuni strumenti, tra cui:

* [Cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md)
* [Interfaccia della riga di comando di Azure](batch-cli-get-started.md)
* [.NET per la gestione di Batch](batch-management-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
* Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](quick-run-dotnet.md) o con [Python](quick-run-python.md). Queste guide introduttive illustrano un'applicazione di esempio che usa il servizio Batch per eseguire un carico di lavoro su più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.

[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
