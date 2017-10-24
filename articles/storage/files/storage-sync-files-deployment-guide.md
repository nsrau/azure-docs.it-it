---
title: Come distribuire Sincronizzazione file di Azure (anteprima) | Microsoft Docs
description: Informazioni sulla distribuzione di Sincronizzazione file di Azure dall'inizio alla fine.
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
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 9f9ff0674fd4e3f9b0598a982d81681eaa6d1997
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>Come distribuire Sincronizzazione file di Azure (anteprima)
Sincronizzazione file di Azure (anteprima) consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale, attraverso la trasformazione dei server Windows in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale (tra cui SMB, NFS e FTPS) e sono disponibili tutte le cache di cui si ha bisogno in tutto il mondo.

Si consiglia vivamente di leggere gli articoli [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md) e [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) prima di seguire i passaggi di questa guida.

## <a name="prerequisites"></a>Prerequisiti
* Un account di archiviazione e una condivisione di file di Azure nella stessa area in cui si distribuisce Sincronizzazione file di Azure. Per altre informazioni, vedere:
    - [Aree di disponibilità](storage-sync-files-planning.md#region-availability) di Sincronizzazione file di Azure,
    - [Creare un account di archiviazione](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) per istruzioni dettagliate su come creare un account di archiviazione e
    - [Creare una condivisione file](storage-how-to-create-file-share.md) per istruzioni dettagliate su come creare una condivisione file.
* Almeno un server o un cluster Windows Server supportato per la sincronizzazione con Sincronizzazione file di Azure. Vedere la sezione relativa all'[interoperabilità con Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability) per altre informazioni sulle versioni di Windows Server supportate.

## <a name="deploy-the-storage-sync-service"></a>Distribuire il servizio di sincronizzazione archiviazione 
Il servizio di sincronizzazione archiviazione è la risorsa principale di Azure che rappresenta Sincronizzazione file di Azure. Per distribuire un servizio di sincronizzazione archiviazione, accedere al [portale di Azure](https://portal.azure.com/) e cercare Sincronizzazione file di Azure. Dopo aver selezionato "Sincronizzazione file di Azure (anteprima)" dai risultati della ricerca, selezionare "Crea" per aprire la scheda "Distribuisci sincronizzazione archiviazione".

Il pannello visualizzato richiede le informazioni seguenti:

- **Nome**: un nome univoco (per ogni sottoscrizione) per il servizio di sincronizzazione archiviazione.
- **Sottoscrizione**: la sottoscrizione in cui creare il servizio di sincronizzazione archiviazione. In base alla strategia di configurazione dell'organizzazione, è possibile accedere a una o più sottoscrizioni. Una sottoscrizione di Azure è il contenitore di base per la fatturazione per ogni servizio cloud, ad esempio File di Azure.
- **Gruppo di risorse**: un gruppo di risorse è un gruppo logico di risorse di Azure, ad esempio un account di archiviazione o un servizio di sincronizzazione archiviazione. È possibile creare un nuovo gruppo di risorse o usare un gruppo già esistente per Sincronizzazione file di Azure. Si consiglia di usare i gruppi di risorse come contenitori che consentono di isolare le risorse in modo logico per l'organizzazione, raggruppando ad esempio le risorse del reparto Risorse Umane o le risorse di un particolare progetto.
- **Percorso**: l'area in cui si vuole distribuire Sincronizzazione file di Azure. In questo elenco sono disponibili solo le aree supportate.

Dopo aver completato il modulo "Distribuisci sincronizzazione archiviazione", fare clic su "Crea" per distribuire il servizio di sincronizzazione archiviazione.

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Preparare Windows Server per l'uso con Sincronizzazione file di Azure
Per ogni server con cui si prevede di usare Sincronizzazione file di Azure, inclusi i nodi server in un cluster di failover, completare i passaggi seguenti:

Per ogni server, inclusi i nodi server di un cluster di failover, che si prevede di usare con Sincronizzazione file di Azure completare i passaggi seguenti:

1. Disabilitare Configurazione sicurezza avanzata IE. Questa operazione è necessaria solo per la registrazione iniziale del server e l'opzione può essere riabilitata dopo che il server è stato registrato.
    1. Aprire Server Manager.
    2. Fare clic su **Server locale**:  
        !["Server locale" sul lato sinistro dell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Selezionare il collegamento per **Configurazione sicurezza avanzata IE** nel riquadro secondario delle proprietà:  
        !["Configurazione sicurezza avanzata IE" nell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Selezionare **Disattivato** per Amministratori e Utenti nella finestra popup Configurazione sicurezza avanzata IE:  
        ![Finestra popup Configurazione sicurezza avanzata IE con opzione "Disattivato" selezionata](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Verificare che sia in esecuzione almeno PowerShell 5.1.\* (PowerShell 5.1 è l'impostazione predefinita per Windows Server 2016). Per verificare se si esegue PowerShell 5.1.\*, esaminare il valore della proprietà PSVersion dell'oggetto $PSVersionTable:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - Se PSVersion è minore di 5.1.\*, come nella maggior parte delle installazioni di Windows Server 2012 R2, è possibile eseguire facilmente l'aggiornamento scaricando e installando [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Il pacchetto appropriato da scaricare e installare per Windows Server 2012 R2 è **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

3. [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Microsoft consiglia sempre di usare la versione più recente dei moduli di Azure PowerShell.

## <a name="install-the-azure-file-sync-agent"></a>Installare l'agente Sincronizzazione file di Azure
L'agente Sincronizzazione file di Azure è un pacchetto scaricabile che consente di sincronizzare un server Windows Server con una condivisione file di Azure. L'agente può essere scaricato dall'[Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Eseguito il download, fare doppio clic sul pacchetto MSI per avviare l'installazione dell'agente Sincronizzazione file di Azure.

> [!Important]  
> Se si prevede di usare Sincronizzazione file di Azure con un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster.

Il pacchetto di installazione dell'agente Sincronizzazione file di Azure si deve installare abbastanza rapidamente senza visualizzare troppi prompt aggiuntivi. È consigliabile eseguire queste operazioni:
- Lasciare il percorso di installazione predefinito (`C:\Program Files\Azure\StorageSyncAgent`) per semplificare la risoluzione dei problemi e la manutenzione del server.
- Abilitare di Microsoft Update per mantenere aggiornato Sincronizzazione file di Azure. Tutti gli aggiornamenti, inclusi aggiornamenti delle funzionalità e hotfix, per l'agente Sincronizzazione file di Azure verranno eseguiti da Microsoft Update. È consigliabile usare sempre l'aggiornamento più recente per Sincronizzazione file di Azure. Per altre informazioni, vedere la sezione relativa ai [criteri di aggiornamento di Sincronizzazione file di Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Al termine dell'installazione dell'agente Sincronizzazione file di Azure, viene avviata automaticamente l'interfaccia utente di Registrazione server. Vedere la sezione successiva per la modalità di registrazione del server con Sincronizzazione file di Azure.

## <a name="register-windows-server-with-storage-sync-service"></a>Registrare Windows Server con il servizio di sincronizzazione archiviazione
La registrazione di Windows Server con un servizio di sincronizzazione archiviazione consente di stabilire una relazione di trust tra il server, o il cluster, in uso e il servizio di sincronizzazione archiviazione. L'interfaccia utente di Registrazione Server deve avviarsi automaticamente dopo l'installazione dell'agente Sincronizzazione file di Azure, ma se non si avvia, si può aprire manualmente dalla posizione: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`. Dopo aver aperto l'interfaccia utente di Registrazione server fare clic su **Accesso** per iniziare.

Eseguito l'accesso, viene richiesto di specificare le informazioni seguenti:

![Schermata dell'interfaccia utente di Registrazione Server](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Sottoscrizione di Azure**: la sottoscrizione che contiene il servizio di sincronizzazione archiviazione (vedere la sezione [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service)). 
- **Gruppo di risorse**: il gruppo di risorse che contiene il servizio di sincronizzazione archiviazione.
- **Servizio di sincronizzazione archiviazione**: il nome del servizio di sincronizzazione archiviazione con cui si effettua la registrazione.

Dopo aver selezionato le informazioni appropriate dai menu a discesa, fare clic su **Registra** per completare la registrazione del server. Come parte del processo di registrazione, viene richiesto un accesso aggiuntivo.

## <a name="create-a-sync-group"></a>Creare un gruppo di sincronizzazione
Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere almeno un endpoint cloud, che rappresenta una condivisione file di Azure, e un endpoint server, che rappresenta un percorso in Windows Server. Per creare un gruppo di sincronizzazione, accedere al servizio di sincronizzazione archiviazione nel [portale di Azure](https://portal.azure.com/), fare clic su **+ Gruppo di sincronizzazione**:

![Creare un nuovo gruppo di sincronizzazione nel portale di Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Il riquadro visualizzato richiede le informazioni seguenti per creare un gruppo di sincronizzazione con un endpoint cloud:

- **Nome gruppo di sincronizzazione**: il nome del gruppo di sincronizzazione da creare. Questo nome deve essere univoco all'interno del servizio di sincronizzazione archiviazione, ma può essere qualsiasi nome logico per l'utente.
- **Sottoscrizione**: la sottoscrizione del servizio di sincronizzazione archiviazione distribuito nella sezione [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service).
- **Account di archiviazione**: facendo clic su "Selezionare l'account di archiviazione" si aprirà un riquadro aggiuntivo in cui è possibile selezionare l'account di archiviazione che contiene la condivisione file di Azure con cui si vuole eseguire la sincronizzazione.
- **Condivisione file di Azure**: il nome della condivisione file di Azure con cui si vuole eseguire la sincronizzazione.

Per aggiungere un endpoint server, passare al gruppo di sincronizzazione creato e fare clic su "Aggiungi endpoint server".

![Aggiungere un nuovo endpoint server nel riquadro del gruppo di sincronizzazione](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Il riquadro "Aggiungi endpoint server" visualizzato richiede le informazioni seguenti per creare un endpoint server:

- **Server registrato**: il nome del server o del cluster in cui viene creato l'endpoint server.
- **Percorso**: il percorso in Windows Server da sincronizzare come parte del gruppo di sincronizzazione.
- **Suddivisione in livelli cloud**: opzione che abilita o disabilita la suddivisione in livelli nel cloud, che consente di archiviare a livelli in File di Azure i file che si usano o a cui si accede raramente.
- **Spazio disponibile nel volume**: la quantità di spazio disponibile da riservare nel volume in cui si trova l'endpoint server. Ad esempio, se lo spazio disponibile nel volume è impostato su 50% per un volume con un singolo endpoint server, circa la metà dei dati verranno archiviati a livelli in File di Azure. Si noti che, a prescindere dall'abilitazione della suddivisione in livelli nel cloud, per la condivisione file di Azure è sempre disponibile una copia completa dei dati nel gruppo di sincronizzazione.

Fare clic su "Crea" per aggiungere l'endpoint server. I file ora verranno mantenuti sincronizzati tra la condivisione file di Azure e Windows Server. 

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere e rimuovere un endpoint server di Sincronizzazione file di Azure](storage-sync-files-server-endpoint.md)
- [Registrare e annullare la registrazione di un server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md)