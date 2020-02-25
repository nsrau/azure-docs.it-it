---
title: Apache Hadoop & archiviazione con trasferimento sicuro-Azure HDInsight
description: Informazioni su come creare cluster HDInsight con account di archiviazione di Azure con trasferimento sicuro abilitato.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560083"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop cluster con account di archiviazione con trasferimento sicuro in Azure HDInsight

La funzionalità [Trasferimento sicuro obbligatorio](../storage/common/storage-require-secure-transfer.md) aumenta la sicurezza dell'account di archiviazione di Azure perché consente l'invio di tutte le richieste all'account solo tramite connessioni sicure. Questa funzionalità e lo schema wasbs sono supportati solo dal cluster HDInsight versione 3.6 o successiva.

> [!IMPORTANT]
> L'abilitazione del trasferimento di archiviazione protetta dopo la creazione di un cluster può causare errori con l'account di archiviazione e non è consigliabile. È preferibile creare un nuovo cluster usando un account di archiviazione con trasferimento sicuro già abilitato.

## <a name="storage-accounts"></a>Account di archiviazione

### <a name="azure-portal"></a>Portale di Azure

Per impostazione predefinita, la proprietà trasferimento sicuro obbligatorio viene abilitata quando si crea un account di archiviazione in portale di Azure.

Per aggiornare un account di archiviazione esistente con portale di Azure, vedere [richiedere il trasferimento sicuro con portale di Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Per il cmdlet di PowerShell [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount), verificare che il parametro `-EnableHttpsTrafficOnly` sia impostato su `1`.

Per aggiornare un account di archiviazione esistente con PowerShell, vedere [richiedere il trasferimento sicuro con PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per il comando dell'interfaccia della riga di comando di Azure [AZ storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create), verificare che il parametro `--https-only` sia impostato su `true`.

Per aggiornare un account di archiviazione esistente con l'interfaccia della riga di comando di Azure, vedere [Require Secure transfer with Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Aggiungere altri account di archiviazione

Sono disponibili diverse opzioni per aggiungere altri account di archiviazione con trasferimento sicuro abilitato:

* Modificare il modello di Azure Resource Manager nell'ultima sezione.
* Creare un cluster usando il [portale di Azure](https://portal.azure.com) e specificare l'account di archiviazione collegato.
* Usare l'azione script per aggiungere altri account di archiviazione con trasferimento sicuro abilitato a un cluster HDInsight esistente. Per altre informazioni, vedere [Aggiungere altri account di archiviazione a HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passaggi successivi

* L'uso di archiviazione di Azure (WASB) anziché di [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) come archivio predefinito di dati
* Per informazioni sul modo in cui HDInsight usa Archiviazione di Azure, vedere [Usare Archiviazione di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Per informazioni su come caricare i dati in HDInsight, vedere [Caricare dati in HDInsight](hdinsight-upload-data.md).
