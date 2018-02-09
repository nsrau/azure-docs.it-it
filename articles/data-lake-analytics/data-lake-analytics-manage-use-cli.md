---
title: "Gestire Azure Data Lake Analytics mediante l’interfaccia della riga di comando (CLI) di Azure | Documentazione Microsoft"
description: "Informazioni su come gestire gli account, le origini dati, i processi e gli utenti di Data Lake Analytics tramite l’interfaccia della riga di comando di Azure"
services: data-lake-analytics
documentationcenter: 
author: SnehaGunda
manager: Kfile
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: sngun
ms.openlocfilehash: edaedaa517a672cd4bad5dc35527f4595ab4a85f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Gestire Azure Data Lake Analytics mediante l’interfaccia della riga di comando (CLI) di Azure

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, gli utenti e i processi di Azure Data Lake Analytics usando l'interfaccia della riga di comando di Azure. Per visualizzare gli argomenti relativi alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.


**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario avere a disposizione le risorse seguenti:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* Interfaccia della riga di comando di Azure. Vedere [Installare e configurare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Scaricare e installare gli **Strumenti di Azure CLI** [pre-release](https://github.com/MicrosoftBigData/AzureDataLake/releases) per completare questa demo.

* Eseguire l'autenticazione usando il comando `az login` e selezionare la sottoscrizione che si vuole usare. Per altre informazioni sull'autenticazione con un account aziendale o dell'istituto di istruzione, vedere [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   È ora possibile accedere ai comandi di Data Lake Analytics e Data Lake Store. Eseguire il comando seguente per elencare i comandi di Data Lake Analytics e Data Lake Store:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Gestisci account

Prima di eseguire qualsiasi processo di Analisi Data Lake, è necessario disporre di un account di Analisi Data Lake. A differenza di Azure HDInsight, un account di Analisi non è soggetto ad alcun pagamento fino a quando il processo non è in esecuzione. Il pagamento, infatti, viene richiesto solo per la durata di esecuzione di un processo.  Per altre informazioni, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Creare account

Eseguire il comando seguente per creare un account di Data Lake: 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aggiornare account

Il seguente comando aggiorna le proprietà di un account Data Lake Analytics esistente

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Elencare gli account

Elencare gli account di Data Lake Analytics all'interno di un gruppo di risorse specifico

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Ottenere i dettagli di un account

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Eliminare un account

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Gestire le origini dati

Data Lake Analytics supporta attualmente le due origini dati seguenti:

* [Archivio Data Lake di Azure](../data-lake-store/data-lake-store-overview.md)
* [Archiviazione di Azure](../storage/common/storage-introduction.md)

Quando si crea un account di Analytics, è necessario impostare un account di archiviazione di Azure Data Lake come account di archiviazione predefinito. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. Dopo aver creato un account di Analytics, è possibile aggiungere altri account di archiviazione di Data Lake e/o account di archiviazione di Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Trovare l'account di Data Lake Store predefinito

È possibile visualizzare l'account di Data Lake Store predefinito usato eseguendo il comando `az dla account show`. Il nome dell'account predefinito è indicato per la proprietà defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Aggiungere altri account di archiviazione BLOB

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Sono supportati solo nomi brevi di archiviazione BLOB. Non utilizzare FQDN, ad esempio "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Aggiungere altri account di Data Lake Store

Il comando seguente aggiorna l'account di Data Lake Analytics specificato con un altro account di Data Lake Store:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Aggiornare l'origine dati esistente

Per aggiornare una chiave di account di archiviazione BLOB esistente:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Elencare le origini dati:

Per elencare gli account di Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Per elencare l'account di archiviazione BLOB:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Origine dati dell'elenco Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Eliminare origini dati:
Per eliminare un account Archivio Data Lake:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Per eliminare un account di archiviazione BLOB:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Gestire i processi
È necessario disporre di un account di Data Lake Analytics prima di poter creare un processo.  Per altre informazioni, vedere [Gestire gli account di Analisi Data Lake](#manage-accounts).

### <a name="list-jobs"></a>Elencare i processi

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Origine dati dell'elenco Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Ottenere i dettagli dei processi

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Inviare i processi

> [!NOTE]
> La priorità predefinita di un processo è 1000 e il livello predefinito di parallelismo per un processo è 1.
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>Annullare i processi
Utilizzare il comando list per cercare l'id del processo e quindi utilizzare cancel per annullare il processo.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="use-azure-resource-manager-groups"></a>Usare i gruppi Gestione risorse di Azure
Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Azure Resource Manager consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione in un'unica operazione coordinata. Per la distribuzione viene usato un modello; questo modello può essere usato per diversi ambienti, ad esempio di testing, staging e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md). 

Un servizio di Analisi Data Lake può includere i componenti seguenti:

* Account di Azure Data Lake Analytics
* Account di archiviazione predefinito obbligatorio di Azure Data Lake
* Account di archiviazione aggiuntivi di Azure Data Lake
* Account di archiviazione aggiuntivi di Azure

È possibile creare tutti questi componenti in un unico gruppo di Resource Manager per semplificarne la gestione.

![Account e archiviazione di Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un account di Analisi Data Lake e gli account di archiviazione dipendenti devono trovarsi nello stesso data center di Azure,
mentre il gruppo di Resource Manager può trovarsi anche in un data center diverso.  

## <a name="see-also"></a>Vedere anche 
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Esercitazione: Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

