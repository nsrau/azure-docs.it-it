---
title: Registrare e annullare la registrazione di un server con Sincronizzazione file di Azure (anteprima) | Microsoft Docs
description: Informazioni su come registrare e annullare la registrazione di Windows Server con un servizio di sincronizzazione archiviazione di File di Azure.
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0acf183fbaea99e4316b668a3da28d79b20b7bef
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Registrare e annullare la registrazione di un server con Sincronizzazione file di Azure (anteprima)
Sincronizzazione file di Azure (anteprima) consente di replicare le condivisioni in locale o in Azure e di accedere alle stesse usando condivisioni SMB o NFS in Windows Server. Sincronizzazione file di Azure è utile per gli scenari in cui è necessario accedere e modificare i dati lontano da un data center di Azure, ad esempio in una succursale. I dati possono essere replicati tra più endpoint di Windows Server, ad esempio tra più succursali.

L'articolo seguente spiega come registrare e annullare la registrazione di un server con un servizio di sincronizzazione archiviazione. L'operazione può essere necessaria se un server viene ritirato o se si vuole usare un nuovo endpoint server in un gruppo di sincronizzazione. Vedere [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md) per informazioni sulla distribuzione di Sincronizzazione file di Azure end-to-end.

## <a name="prerequisites"></a>Prerequisiti
Per registrare Windows Server con un servizio di sincronizzazione archiviazione, è necessario prima preparare il server in modo che abbia i prerequisiti necessari:

* Verificare che sia stato distribuito un servizio di sincronizzazione archiviazione. Per altre informazioni sulla distribuzione di un servizio di sincronizzazione archiviazione, vedere [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md).
* Verificare che il server sia connesso a Internet e che Azure sia accessibile.
* Disabilitare la Configurazione sicurezza avanzata IE per gli amministratori con interfaccia utente di Server Manager.
    
    ![Interfaccia utente di Server Manager con Configurazione sicurezza avanzata IE evidenziata](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Verificare che il modulo AzureRM di PowerShell sia installato nel server. Se il server è un membro di un cluster di failover, ogni nodo del cluster richiederà il modulo AzureRM. Altre informazioni su come installare il modulo AzureRM sono contenute nell'articolo [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Installare e configurare Azure PowerShell).

    > [!Note]  
    > Si consiglia di usare la versione più recente del modulo AzureRM di PowerShell per registrare un server o annullare la registrazione. Se il pacchetto AzureRM è stato installato in precedenza in questo server e la versione di PowerShell nel server è 5.* o successiva, è possibile usare il cmdlet `Update-Module` per aggiornare il pacchetto. 

## <a name="register-a-server-with-storage-sync-service"></a>Registrare un server con il servizio di sincronizzazione archiviazione
Per poter essere usato come *endpoint server* in un *gruppo di sincronizzazione* di Sincronizzazione file di Azure, un server Windows Server deve essere registrato con un *servizio di sincronizzazione archiviazione*. È possibile registrare un server solo con un *servizio di sincronizzazione archiviazione* alla volta.

### <a name="install-the-azure-file-sync-agent"></a>Installare l'agente Sincronizzazione file di Azure
1. [Scaricare l'agente Sincronizzazione file di Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Avviare il programma di installazione dell'agente Sincronizzazione file di Azure.
    
    ![Il primo riquadro del programma di installazione dell'agente Sincronizzazione file di Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Assicurarsi di abilitare gli aggiornamenti dell'agente Sincronizzazione file di Azure usando Microsoft Update. È importante perché gli aggiornamenti critici della protezione e i miglioramenti apportati alle funzionalità per il pacchetto del server vengono messi a disposizione attraverso Microsoft Update.

    ![Verificare che Microsoft Update sia abilitato nel riquadro Microsoft Update del programma di installazione dell'agente Sincronizzazione file di Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se il server non è stato registrato in precedenza, l'interfaccia utente di Registrazione server appare immediatamente al termine dell'installazione.

> [!Important]  
> Se il server è un membro di un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster.

### <a name="register-the-server-using-the-server-registration-ui"></a>Registrare il server usando l'interfaccia utente di Registrazione server
1. Se l'interfaccia utente di Registrazione server non viene avviata subito dopo aver completato l'installazione dell'agente Sincronizzazione file di Azure, può essere avviata manualmente eseguendo `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Fare clic su *Accesso* per accedere alla sottoscrizione di Azure. 

    ![Apertura finestra di dialogo dell'interfaccia utente di Registrazione Server](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Nella finestra di dialogo selezionare la sottoscrizione corretta, un gruppo di risorse e il servizio di sincronizzazione archiviazione.

    ![Informazioni sul servizio di sincronizzazione archiviazione](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Nell'anteprima è necessario accedere una o più volte per completare il processo. 

    ![Finestra di dialogo di accesso](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se il server è membro di un cluster di failover, ogni server deve eseguire la registrazione del server. Quando si visualizza la finestra Server registrati nel portale di Azure, Sincronizzazione file di Azure riconosce automaticamente ogni nodo come membro dello stesso cluster di failover e raggruppa i nodi in modo appropriato.

## <a name="unregister-the-server-with-storage-sync-service"></a>Annullare la registrazione del server con il servizio di sincronizzazione archiviazione
Sono necessari alcuni passaggi per annullare la registrazione di un server con un servizio di sincronizzazione archiviazione. Di seguito viene illustrato come annullare correttamente la registrazione di un server.

### <a name="optional-recall-all-tiered-data"></a>(Facoltativo) Richiamare tutti i dati archiviati a livelli
Se è abilitata per un endpoint server, la suddivisione in livelli nel cloud *archivia i file a livelli* nelle condivisioni di File di Azure. Questo consente alle condivisioni di file locali di agire come una cache, anziché una copia completa del set di dati, in modo da ottimizzare l'uso dello spazio nel file server. Tuttavia, se un endpoint server è stato rimosso con i file archiviati a livelli ancora presenti in locale nel server, tali file diventeranno inaccessibili. Di conseguenza, per garantire la continuità dell'accesso ai file, è necessario richiamare tutti i file archiviati a livelli da File di Azure prima di procedere con l'annullamento della registrazione. 

Questa operazione può essere eseguita con il cmdlet di PowerShell come illustrato di seguito:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Se il volume locale che ospita il server non ha abbastanza spazio libero per richiamare tutti i dati archiviati a livelli, il cmdlet `Invoke-StorageSyncFileRecall` avrà esito negativo.  

### <a name="remove-the-server-from-all-sync-groups"></a>Rimuovere il server da tutti i gruppi di sincronizzazione
Prima di annullare la registrazione del server nel servizio di sincronizzazione archiviazione, è necessario rimuovere tutti gli endpoint server per quel server. Questa operazione può essere eseguita dal portale:

1. Passare al servizio di sincronizzazione archiviazione in cui è registrato il server.
2. Rimuovere tutti gli endpoint server per questo server in ogni gruppo di sincronizzazione nel servizio di sincronizzazione archiviazione. L'operazione può essere eseguita facendo clic con il pulsante destro del mouse sull'endpoint server nel riquadro del gruppo di sincronizzazione.

    ![Rimozione di un endpoint server da un gruppo di sincronizzazione](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Questa operazione può essere eseguita anche con un semplice script di PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>Annullare la registrazione del server
Ora che tutti i dati sono stati richiamati e il server è stato rimosso da tutti i gruppi di sincronizzazione, è possibile annullare la registrazione del server. 

1. Nel portale di Azure passare alla sezione "Server registrati" del servizio di sincronizzazione archiviazione.
2. Fare clic con il pulsante destro del mouse sul server per cui si vuole annullare la registrazione e fare clic su "Annulla registrazione server".

    ![Annullare la registrazione del server](media/storage-sync-files-server-registration/unregister-server-1.png)
