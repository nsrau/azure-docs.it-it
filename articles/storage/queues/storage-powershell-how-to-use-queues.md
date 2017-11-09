---
title: Eseguire operazioni nell'archivio code di Azure con PowerShell | Microsoft Docs
description: Come eseguire operazioni nell'archivio code di Azure con PowerShell
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 36eb6db83bb902b35efb8c9666ab06e0c618d602
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Eseguire operazioni nell'archivio code di Azure con Azure PowerShell

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. Per informazioni dettagliate, vedere [Introduzione alle code di Azure](storage-queues-introduction.md). Questa procedura illustra le operazioni più comuni nell'archivio code. Si apprenderà come:

> [!div class="checklist"]
> * Creare una coda
> * Recuperare una coda
> * Aggiungere un messaggio
> * Leggere un messaggio
> * Eliminare un messaggio 
> * Eliminare una coda

Questa procedura richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

Non sono disponibili cmdlet di PowerShell per il piano dati per le code. Per eseguire le operazioni del piano dati come aggiungere, leggere ed eliminare un messaggio, è necessario usare la libreria client di dell'archivio .NET come mostrato in PowerShell. Creare un oggetto messaggio e usare i comandi, ad esempio AddMessage per eseguire operazioni su tale messaggio. Questo articolo mostra come eseguire questa operazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperare un elenco di posizioni

Se non si sa quale posizione usare, è possibile elencare le posizioni disponibili. Nell'elenco visualizzato trovare la posizione da usare. In questa esercitazione viene usata la posizione **eastus**. Archiviare questo valore nella variabile **location** per uso futuro.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Archiviare il nome del gruppo di risorse in una variabile per uso futuro. In questo esempio viene creato un gruppo di risorse denominato *howtoqueuesrg* nell'area *eastus*.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Crea account di archiviazione

Creare un account di archiviazione standard per uso generico con archiviazione con ridondanza locale mediante [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Ottenere il contesto che definisce l'account di archiviazione da usare. Quando si usa un account di archiviazione, si può fare riferimento al contesto anziché fornire ripetutamente le credenziali.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Creare una coda

Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto dell'account di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso. Viene poi chiamato il cmdlet [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) per creare una coda denominata 'queuename'.

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Per informazioni sulle convenzioni di denominazione per il servizio di accodamento di Azure, vedere [Denominazione di code e metadati](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Recuperare una coda

È possibile eseguire query e recuperare una coda specifica o un elenco di tutte le code di un account di archiviazione. Gli esempi seguenti illustrano come recuperare tutte le code nell'account di archiviazione e una coda specifica. Entrambi i comandi usano il cmdlet [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue).

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda

Le operazioni che influiscono sui messaggi effettivi nella coda usano la libreria client di archiviazione .NET come illustrato in PowerShell. Per aggiungere un messaggio a una coda, creare una nuova istanza dell'oggetto messaggio della classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Quindi, chiamare il metodo [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . È possibile creare un oggetto CloudQueueMessage da una stringa in formato UTF-8 o da una matrice di byte.

L'esempio seguente mostra come aggiungere un messaggio alla coda.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Se si usa [Azure Storage Explorer](http://storageexplorer.com), è possibile connettersi al proprio account Azure, visualizzare le code nell'account di archiviazione ed eseguire il drill-down in una di esse per visualizzare i messaggi contenuti nella coda. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Leggere un messaggio dalla coda e quindi eliminarlo

I messaggi vengono letti nell'ordine first in, first out, ma senza alcuna garanzia. Quando il messaggio viene letto dalla coda, diventa invisibile a tutti gli altri processi che esaminano la coda. Ciò garantisce che, qualora il codice non riesca a elaborare il messaggio a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare.  

Questo **periodo di invisibilità** definisce per quanto tempo il messaggio rimane invisibile prima di diventare di nuovo disponibile per l'elaborazione. Il valore predefinito è 30 secondi. 

Il codice legge il messaggio dalla coda in due passaggi. Chiamando il metodo [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), si ottiene il messaggio successivo nella coda. Un messaggio restituito da **GetMessage** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per completare la rimozione del messaggio dalla coda, è necessario chiamare il metodo [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). 

Nell'esempio seguente vengono letti i tre messaggi in coda, seguiti da 10 secondi di attesa, il periodo di invisibilità. I tre messaggi vengono quindi letti di nuovo ed eliminati dopo la lettura chiamando **DeleteMessage**. Se si tenta di leggere la coda dopo l'eliminazione dei messaggi, $queueMessage viene restituito come NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Eliminare una coda
Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo Remove-AzureStorageQueue. L'esempio seguente mostra come eliminare la coda specifica usata in questo esercizio con il cmdlet Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare tutti gli asset creati in questo esercizio, rimuovere il gruppo di risorse. Così facendo vengono eliminate anche tutte le risorse in esso contenute. In questo caso, rimuove l'account di archiviazione creato e il gruppo di risorse stesso.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa procedura è stata illustrata la gestione di base dell'archivio delle code con PowerShell, tra cui come:

> [!div class="checklist"]
> * Creare una coda
> * Recuperare una coda
> * Aggiungere un messaggio
> * Leggere il messaggio successivo
> * Eliminare un messaggio 
> * Eliminare una coda

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlet di archiviazione per Microsoft Azure PowerShell
* [Cmdlet di PowerShell per l'archiviazione](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.