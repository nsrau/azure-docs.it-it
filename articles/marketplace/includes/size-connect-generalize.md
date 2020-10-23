---
title: File di inclusione
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 24adbfe38a3d43a83307fb8726849f7c73def3f3
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284300"
---
## <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. Per ottenere questo risultato, il disco rigido virtuale del sistema operativo deve essere generalizzato. La generalizzazione è un'operazione che rimuove tutti gli identificatori specifici di istanze e tutti i driver software da una macchina virtuale.

### <a name="for-windows"></a>Per Windows

I dischi del sistema operativo Windows sono generalizzati con lo strumento [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Se successivamente si aggiorna o si riconfigura il sistema operativo, è necessario eseguire di nuovo Sysprep.

> [!WARNING]
> Dopo aver eseguito Sysprep, spegnere la macchina virtuale fino a quando non viene distribuita perché gli aggiornamenti possono essere eseguiti automaticamente. Questo arresto eviterà che gli aggiornamenti successivi possano apportare modifiche specifiche di istanza al sistema operativo o ai servizi installati. Per altre informazioni sull'esecuzione di sysprep, vedere [Procedura per generalizzare un disco rigido virtuale](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Per Linux

Il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata. Per informazioni dettagliate, vedere [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../virtual-machines/linux/capture-image.md). È possibile arrestare quando si raggiunge la sezione denominata "creare una macchina virtuale dall'immagine acquisita".

1. Rimuovere l'agente Linux di Azure.
    1. Connettersi alla VM Linux tramite un client SSH.
    2. Nella finestra SSH immettere il comando seguente: `sudo waagent –deprovision+user` .
    3. Digitare Y per continuare (è possibile aggiungere il parametro -force al comando precedente per evitare il passaggio di conferma).
    4. Al termine del comando, immettere **Exit** per chiudere il client SSH.
2. Arrestare la macchina virtuale.
    1. Nel portale di Azure selezionare il gruppo di risorse (RG) e deallocare la macchina virtuale.
    2. La VM è ora generalizzata ed è possibile creare una nuova macchina virtuale usando questo disco della macchina virtuale.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Eseguire uno snapshot del disco della macchina virtuale

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. A partire dall'angolo in alto a sinistra, selezionare **Crea una risorsa**, quindi cercare e selezionare **snapshot**.
3. Nel pannello snapshot selezionare  **Crea**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un gruppo di risorse esistente o immettere il nome per un nuovo gruppo.
6. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
7. Selezionare il **tipo di account** da usare per archiviare lo snapshot. Usare il tipo **Standard HDD** a meno che non sia necessario archiviare lo snapshot su un'unità SSD a prestazioni elevate.
8. Selezionare **Create** (Crea).

#### <a name="extract-the-vhd"></a>Estrarre il disco rigido virtuale

Usare lo script seguente per esportare lo snapshot in un disco rigido virtuale nell'account di archiviazione.

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $ snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per generare l'URI SAS per uno snapshot e copia il disco rigido virtuale sottostante in un account di archiviazione usando l'URI di firma di accesso condiviso. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
| --- | --- |
| az disk grant-access | Genera SAS di sola lettura usati per copiare il file del disco rigido virtuale sottostante in un account di archiviazione o scaricarlo in locale
| az storage blob copy start | Copia un BLOB in modo asincrono da un account di archiviazione a un altro. Usare `az storage blob show` per controllare lo stato del nuovo BLOB. |
|
