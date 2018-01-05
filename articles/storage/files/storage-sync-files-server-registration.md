---
title: Gestire i server registrati con Sincronizzazione file di Azure (anteprima) | Microsoft Docs
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
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 10c8b708cad245f4ac0304489beb36dcf63cd4b1
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="manage-registered-servers-with-azure-file-sync-preview"></a>Gestire i server registrati con Sincronizzazione file di Azure (anteprima)
Sincronizzazione file di Azure (anteprima) consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Tutto questo avviene trasformando i sistemi Windows Server in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale (tra cui SMB, NFS e FTPS) ed è possibile scegliere tutte le cache necessarie in tutto il mondo.

L'articolo seguente illustra come registrare e gestire un server con un servizio di sincronizzazione archiviazione. Vedere [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md) per informazioni sulla distribuzione di Sincronizzazione file di Azure end-to-end.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Eseguire/annullare la registrazione di un server nel servizio di sincronizzazione archiviazione
La registrazione di un server in Sincronizzazione file di Azure stabilisce una relazione di trust tra Windows Server e Azure. Questa relazione può quindi essere usata per creare nel server *endpoint server* che rappresentano le cartelle specifiche che dovranno essere sincronizzate con una condivisione file di Azure (denominata anche *endpoint cloud*). 

### <a name="prerequisites"></a>Prerequisiti
Per registrare un server in un servizio di sincronizzazione archiviazione, è prima necessario preparare il server con i prerequisiti necessari:

* Il server deve eseguire una versione supportata di Windows Server. Per altre informazioni, vedere [Versioni di Windows Server supportate](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Verificare che sia stato distribuito un servizio di sincronizzazione archiviazione. Per altre informazioni sulla distribuzione di un servizio di sincronizzazione archiviazione, vedere [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md).
* Verificare che il server sia connesso a Internet e che Azure sia accessibile.
* Disabilitare la Configurazione sicurezza avanzata IE per gli amministratori con interfaccia utente di Server Manager.
    
    ![Interfaccia utente di Server Manager con Configurazione sicurezza avanzata IE evidenziata](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Verificare che il modulo AzureRM di PowerShell sia installato nel server. Se il server è un membro di un cluster di failover, ogni nodo del cluster richiederà il modulo AzureRM. Altre informazioni su come installare il modulo AzureRM sono contenute nell'articolo [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Installare e configurare Azure PowerShell).

    > [!Note]  
    > Si consiglia di usare la versione più recente del modulo AzureRM di PowerShell per registrare un server o annullare la registrazione. Se il pacchetto AzureRM è stato installato in precedenza in questo server e la versione di PowerShell nel server è 5.* o successiva, è possibile usare il cmdlet `Update-Module` per aggiornare il pacchetto. 

### <a name="register-a-server-with-storage-sync-service"></a>Registrare un server con il servizio di sincronizzazione archiviazione
Per poter essere usato come *endpoint server* in un *gruppo di sincronizzazione* di Sincronizzazione file di Azure, un server deve essere registrato in un *servizio di sincronizzazione archiviazione*. Un server può essere registrato in un solo servizio di sincronizzazione archiviazione alla volta.

#### <a name="install-the-azure-file-sync-agent"></a>Installare l'agente Sincronizzazione file di Azure
1. [Scaricare l'agente Sincronizzazione file di Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Avviare il programma di installazione dell'agente Sincronizzazione file di Azure.
    
    ![Il primo riquadro del programma di installazione dell'agente Sincronizzazione file di Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Assicurarsi di abilitare gli aggiornamenti dell'agente Sincronizzazione file di Azure usando Microsoft Update. È importante perché gli aggiornamenti critici della protezione e i miglioramenti apportati alle funzionalità per il pacchetto del server vengono messi a disposizione attraverso Microsoft Update.

    ![Verificare che Microsoft Update sia abilitato nel riquadro Microsoft Update del programma di installazione dell'agente Sincronizzazione file di Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se il server non è stato registrato in precedenza, l'interfaccia utente di Registrazione server appare immediatamente al termine dell'installazione.

> [!Important]  
> Se il server è un membro di un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrare il server usando l'interfaccia utente di Registrazione server
> [!Important]  
> Le sottoscrizioni Cloud Solution Provider (CSP) non possono usare l'interfaccia utente Registrazione server. Usare invece PowerShell (di seguito in questa sezione).

1. Se l'interfaccia utente di Registrazione server non viene avviata subito dopo aver completato l'installazione dell'agente Sincronizzazione file di Azure, può essere avviata manualmente eseguendo `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Fare clic su *Accesso* per accedere alla sottoscrizione di Azure. 

    ![Apertura della finestra di dialogo dell'interfaccia utente Registrazione server](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Nella finestra di dialogo selezionare la sottoscrizione corretta, un gruppo di risorse e il servizio di sincronizzazione archiviazione.

    ![Informazioni sul servizio di sincronizzazione archiviazione](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Nell'anteprima è necessario accedere una o più volte per completare il processo. 

    ![Finestra di dialogo di accesso](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se il server è membro di un cluster di failover, ogni server deve eseguire la registrazione del server. Quando si visualizzano i server registrati nel portale di Azure, Sincronizzazione file di Azure riconosce automaticamente ogni nodo come membro dello stesso cluster di failover e raggruppa i nodi in modo appropriato.

#### <a name="register-the-server-with-powershell"></a>Registrare il server con PowerShell
È anche possibile eseguire la registrazione del server tramite PowerShell. Questo è l'unico modo supportato per registrare server per le sottoscrizioni Cloud Solution Provider (CSP):

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Annullare la registrazione del server con il servizio di sincronizzazione archiviazione
Sono necessari alcuni passaggi per annullare la registrazione di un server con un servizio di sincronizzazione archiviazione. Di seguito viene illustrato come annullare correttamente la registrazione di un server.

#### <a name="optional-recall-all-tiered-data"></a>(Facoltativo) Richiamare tutti i dati archiviati a livelli
Se per un endpoint server è abilitata la suddivisione in livelli cloud, i file verranno *archiviati a livelli* nelle condivisioni file di Azure. Questo consente alle condivisioni di file locali di agire come una cache, anziché una copia completa del set di dati, in modo da ottimizzare l'uso dello spazio nel file server. In caso di rimozione di un endpoint server con file archiviati a livelli ancora presenti in locale nel server, tuttavia, tali file diventeranno inaccessibili. Di conseguenza, per garantire la continuità dell'accesso ai file, è necessario richiamare tutti i file archiviati a livelli da File di Azure prima di procedere con l'annullamento della registrazione. 

Questa operazione può essere eseguita con il cmdlet di PowerShell come illustrato di seguito:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Se lo spazio disponibile nel volume locale che ospita l'endpoint server non è sufficiente per richiamare tutti i dati archiviati a livelli, il cmdlet `Invoke-StorageSyncFileRecall` avrà esito negativo.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Rimuovere il server da tutti i gruppi di sincronizzazione
Prima di annullare la registrazione del server nel servizio di sincronizzazione archiviazione, è necessario rimuovere tutti gli endpoint server in tale server. Questa operazione può essere eseguita tramite il portale di Azure:

1. Passare al servizio di sincronizzazione archiviazione in cui è registrato il server.
2. Rimuovere tutti gli endpoint server per il server in ogni gruppo di sincronizzazione nel servizio di sincronizzazione archiviazione. Questa operazione può essere eseguita facendo clic con il pulsante destro del mouse sull'endpoint server pertinente nel riquadro del gruppo di sincronizzazione.

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

#### <a name="unregister-the-server"></a>Annullare la registrazione del server
Dopo che tutti i dati sono stati richiamati e il server è stato rimosso da tutti i gruppi di sincronizzazione, è possibile annullare la registrazione del server. 

1. Nel portale di Azure passare alla sezione *Server registrati* del servizio di sincronizzazione archiviazione.
2. Fare clic con il pulsante destro del mouse sul server per cui si vuole annullare la registrazione e fare clic su "Annulla registrazione server".

    ![Annullare la registrazione del server](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Corretta integrazione di Sincronizzazione file di Azure nel data center 
Dato che Sincronizzazione file di Azure raramente è l'unico servizio in esecuzione nel data center, potrebbe essere opportuno limitarne l'utilizzo della rete e dello spazio di archiviazione.

> [!Important]  
> L'impostazione di limiti troppo bassi influirà sulle prestazioni di sincronizzazione e richiamo di Sincronizzazione file di Azure.

### <a name="set-azure-file-sync-network-limits"></a>Impostare limiti di rete per Sincronizzazione file di Azure
È possibile limitare l'uso della rete di sincronizzazione di File di Azure tramite il `StorageSyncNetworkLimit` cmdlet. 

Ad esempio, è possibile creare un nuovo limite di velocità per garantire la sincronizzazione File di Azure non utilizzi più di 10 Mbps tra le 09: 00 e 17: 00 (17:00 h) durante la settimana lavorativa: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

È possibile visualizzare il limite con il cmdlet seguente:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Per rimuovere i limiti di rete, usare `Remove-StorageSyncNetworkLimit`. Il comando seguente, ad esempio, rimuove tutti i limiti di rete:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Usare QoS di archiviazione di Windows Server 
Quando il servizio Sincronizzazione file di Azure è ospitato in una macchina virtuale eseguita in un host di virtualizzazione Windows Server, è possibile usare QoS (Quality of Service) di archiviazione per regolamentare l'utilizzo di I/O di archiviazione. I criteri QoS di archiviazione possono essere impostati come massimo o limite (come il limite StorageSyncNetwork applicato sopra) oppure come minimo o prenotazione. Impostando un minimo anziché un massimo, Sincronizzazione file di Azure potrà potenziare l'uso della larghezza di banda di archiviazione disponibile se non viene usata da altri carichi di lavoro. Per altre informazioni, vedere [QoS di archiviazione](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Vedere anche 
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure (anteprima)](storage-sync-files-planning.md)
- [Distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md) 
- [Risolvere i problemi di Sincronizzazione file di Azure (anteprima)](storage-sync-files-troubleshoot.md)
