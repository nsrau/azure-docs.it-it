---
title: Distribuire Sincronizzazione file di Azure (anteprima) | Microsoft Docs
description: Informazioni complete su come distribuire Sincronizzazione file di Azure.
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
ms.openlocfilehash: 7d6cb91f97020ad60bd2ea74b24df76511956f38
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-azure-file-sync-preview"></a>Distribuire Sincronizzazione file di Azure (anteprima)
È possibile usare Sincronizzazione file di Azure (anteprima) per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

È consigliabile leggere [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md) e [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) prima di completare i passaggi descritti in questo articolo.

## <a name="prerequisites"></a>Prerequisiti
* Un account di Archiviazione di Microsoft Azure e una condivisione file di Azure nella stessa area in cui si distribuisce Sincronizzazione file di Azure. Per altre informazioni, vedere:
    - [Aree di disponibilità](storage-sync-files-planning.md#region-availability) per Sincronizzazione file di Azure.
    - [Creare un account di archiviazione](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) per una descrizione dettagliata della procedura per la creazione di un account di archiviazione.
    - [Creare una condivisione file](storage-how-to-create-file-share.md) per una descrizione dettagliata della procedura per la creazione di una condivisione file.
* Almeno un'istanza supportata di Windows Server o di cluster Windows Server da sincronizzare con Sincronizzazione file di Azure. Per altre informazioni sulle versioni supportate di Windows Server, vedere [Interoperabilità di Sincronizzazione file di Azure](storage-sync-files-planning.md#azure-file-sync-interoperability).

## <a name="deploy-the-storage-sync-service"></a>Distribuire il servizio di sincronizzazione archiviazione 
Il servizio di sincronizzazione archiviazione è la principale risorsa di Azure per Sincronizzazione file di Azure. Per distribuire un servizio di sincronizzazione di archiviazione, passare al [portale di Azure](https://portal.azure.com/), fare clic su *New* e quindi eseguire la ricerca per la sincronizzazione di File di Azure. Nei risultati della ricerca selezionare **Sincronizzazione file di Azure (anteprima)** e quindi selezionare **Crea** per aprire la scheda **Distribuisci sincronizzazione archiviazione**.

Nel pannello che viene visualizzato immettere le informazioni seguenti:

- **Nome**: un nome univoco (per ogni sottoscrizione) per il servizio di sincronizzazione archiviazione.
- **Sottoscrizione**: la sottoscrizione in cui creare il servizio di sincronizzazione archiviazione. A seconda della strategia di configurazione dell'organizzazione, è possibile accedere a una o più sottoscrizioni. Una sottoscrizione di Azure è il contenitore di base per la fatturazione di ogni servizio cloud, ad esempio File di Azure.
- **Gruppo di risorse**: un gruppo di risorse è un gruppo logico di risorse di Azure, ad esempio un account di archiviazione o un servizio di sincronizzazione archiviazione. Per Sincronizzazione file di Azure è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo. È consigliabile usare i gruppi di risorse come contenitori per isolare le risorse in modo logico per l'organizzazione, ad esempio si possono raggruppare le risorse delle Risorse Umane o per un progetto specifico.
- **Percorso**: l'area in cui si vuole distribuire Sincronizzazione file di Azure. In questo elenco sono disponibili solo le aree supportate.

Al termine, selezionare **Crea** per distribuire il servizio di sincronizzazione di archiviazione.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparare Windows Server per l'uso con Sincronizzazione file di Azure
Per ogni server con cui si prevede di usare Sincronizzazione file di Azure, inclusi i nodi server in un cluster di failover, completare i passaggi seguenti:

1. Disabilitare **Configurazione sicurezza avanzata IE**. Questa operazione è necessaria solo per la registrazione iniziale del server e l'opzione può essere riabilitata dopo che il server è stato registrato.
    1. Aprire Server Manager.
    2. Fare clic su **Server locale**:  
        !["Server locale" sul lato sinistro dell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Ne riquadro secondario **Proprietà** selezionare il collegamento per **Configurazione sicurezza avanzata IE**.  
        ![Riquadro "Configurazione sicurezza avanzata IE" nell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Nella finestra di dialogo **Configurazione sicurezza avanzata IE** selezionare **Disattivato** per **Amministratori** e **Utenti**:  
        ![Finestra popup Configurazione sicurezza avanzata IE con opzione "Disattivato" selezionata](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Verificare che sia in esecuzione almeno PowerShell 5.1.\* (PowerShell 5.1 è l'impostazione predefinita per Windows Server 2016). Per verificare se si esegue PowerShell 5.1.\*, esaminare il valore della proprietà**PSVersion** dell'oggetto **$PSVersionTable**:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Se il valore di PSVersion è minore di 5.1.\*, come nella maggior parte delle installazioni di Windows Server 2012 R2, è possibile eseguire facilmente l'aggiornamento scaricando e installando [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Il pacchetto appropriato da scaricare e installare per Windows Server 2012 R2 è **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

3. [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Microsoft consiglia di usare la versione più recente dei moduli di Azure PowerShell.

## <a name="install-the-azure-file-sync-agent"></a>Installare l'agente Sincronizzazione file di Azure
L'agente Sincronizzazione file di Azure è un pacchetto scaricabile che consente di sincronizzare Windows Server con una condivisione file di Azure. È possibile scaricare l'agente dall'[Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Al termine del download, fare doppio clic sul pacchetto MSI per avviare l'installazione dell'agente Sincronizzazione file di Azure.

> [!Important]  
> Se si intende usare Sincronizzazione file di Azure con un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster.

Il pacchetto di installazione dell'agente Sincronizzazione file di Azure viene installato abbastanza rapidamente senza visualizzare troppi prompt aggiuntivi. È consigliabile eseguire queste operazioni:
- Lasciare il percorso di installazione predefinito (c:\Programmi\Microsoft Files\Azure\StorageSyncAgent), per semplificare la risoluzione dei problemi e la manutenzione del server.
- Abilitare Microsoft Update per mantenere sempre aggiornato Sincronizzazione file di Azure. Tutti gli aggiornamenti per l'agente Sincronizzazione file di Azure, inclusi gli aggiornamenti delle funzionalità e gli hotfix, vengono eseguiti tramite Microsoft Update. È consigliabile installare l'aggiornamento più recente di Sincronizzazione file di Azure. Per altre informazioni, vedere [Criteri di aggiornamento dell'agente Sincronizzazione file di Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Al termine dell'installazione dell'agente Sincronizzazione file di Azure, viene avviata automaticamente l'interfaccia utente di Registrazione server. Per informazioni su come registrare questo server con Sincronizzazione file di Azure, vedere la sezione che segue.

## <a name="register-windows-server-with-storage-sync-service"></a>Registrare Windows Server con il servizio di sincronizzazione archiviazione
La registrazione di Windows Server con un servizio di sincronizzazione archiviazione stabilisce una relazione di trust tra il server, o il cluster, in uso e il servizio di sincronizzazione archiviazione. L'interfaccia utente di Registrazione server viene visualizzata automaticamente al termine dell'installazione dell'agente Sincronizzazione file di Azure. In caso contrario, è possibile aprirla manualmente dal percorso: c:\Programmi\Microsoft Files\Azure\StorageSyncAgent\ServerRegistration.exe. Dopo avere aperto l'interfaccia utente di Registrazione server fare clic su **Accesso** per iniziare.

Eseguito l'accesso, viene richiesto di specificare le informazioni seguenti:

![Schermata dell'interfaccia utente di Registrazione Server](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Sottoscrizione di Azure**: la sottoscrizione che contiene il servizio di sincronizzazione archiviazione (vedere [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service)). 
- **Gruppo di risorse**: il gruppo di risorse che contiene il servizio di sincronizzazione archiviazione.
- **Servizio di sincronizzazione archiviazione**: il nome del servizio di sincronizzazione archiviazione con cui si effettua la registrazione.

Dopo avere selezionato le informazioni appropriate, fare clic su **Registra** per completare la registrazione del server. Come parte del processo di registrazione, viene richiesto un accesso aggiuntivo.

## <a name="create-a-sync-group"></a>Creare un gruppo di sincronizzazione
Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere almeno un endpoint cloud, che rappresenta una condivisione file di Azure, e un endpoint server, che rappresenta un percorso in Windows Server. Per creare un gruppo di sincronizzazione, accedere al [portale di Azure](https://portal.azure.com/), passare al servizio di sincronizzazione archiviazione e quindi selezionare **+ Gruppo di sincronizzazione**:

![Creare un nuovo gruppo di sincronizzazione nel portale di Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Nel riquadro che viene visualizzato immettere le informazioni seguenti per creare un gruppo di sincronizzazione con un endpoint cloud:

- **Nome gruppo di sincronizzazione**: il nome del gruppo di sincronizzazione da creare. Questo nome deve essere univoco all'interno del servizio di sincronizzazione archiviazione, ma può essere qualsiasi nome logico per l'utente.
- **Sottoscrizione**: la sottoscrizione in cui è stato distribuito il servizio di sincronizzazione archiviazione in [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service).
- **Account di archiviazione**: se si seleziona **Selezionare l'account di archiviazione**, viene visualizzato un altro riquadro in cui è possibile selezionare l'account di archiviazione che contiene la condivisione file di Azure con cui si vuole eseguire la sincronizzazione.
- **Condivisione file di Azure**: il nome della condivisione file di Azure con cui si vuole eseguire la sincronizzazione.

Per aggiungere un endpoint server, passare al gruppo di sincronizzazione appena creato e fare clic su **Aggiungi endpoint server**.

![Aggiungere un nuovo endpoint server nel riquadro del gruppo di sincronizzazione](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Nel riquadro **Aggiungi endpoint server** immettere le informazioni seguenti per creare un endpoint server:

- **Server registrato**: il nome del server o del cluster in cui viene creato l'endpoint server.
- **Percorso**: il percorso in Windows Server da sincronizzare come parte del gruppo di sincronizzazione.
- **Suddivisione in livelli cloud**: l'opzione che abilita o disabilita la suddivisione in livelli cloud, che consente di archiviare a livelli in File di Azure i file che si usano o a cui si accede raramente.
- **Spazio disponibile nel volume**: la quantità di spazio disponibile da riservare nel volume in cui si trova l'endpoint server. Se ad esempio lo spazio disponibile nel volume è impostato su 50% per un volume con un singolo endpoint server, circa la metà dei dati viene archiviata a livelli in File di Azure. A prescindere dall'abilitazione o meno della suddivisione in livelli nel cloud, per la condivisione file di Azure è sempre disponibile una copia completa dei dati nel gruppo di sincronizzazione.

Per aggiungere l'endpoint server, selezionare **Crea**. I file vengono ora mantenuti sincronizzati tra la condivisione file di Azure e Windows Server. 

> [!Important]  
> È possibile apportare modifiche a qualsiasi endpoint cloud o endpoint server nel gruppo di sincronizzazione e fare in modo che i file vengano sincronizzati con gli altri endpoint del gruppo di sincronizzazione. Se si apporta direttamente una modifica all'endpoint cloud (condivisione file di Azure), le modifiche apportate devono essere prima di tutto individuate da un processo di rilevamento delle modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una sola volta ogni 24 ore. Per altre informazioni, vedere [Domande frequenti su File di Azure](storage-files-faq.md#afs-change-detection).

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Eseguire la migrazione di una distribuzione di Replica DFS (DFS-R) in Sincronizzazione file di Azure
Per eseguire la migrazione di una distribuzione di DFS-R in Sincronizzazione file di Azure:

1. Creare un gruppo di sincronizzazione per rappresentare la topologia di DFS-R che si sta sostituendo.
2. Avviare il server contenente il set completo di dati in una topologia DFS-R di cui eseguire la migrazione. Installare Sincronizzazione file di Azure su tale server.
3. Registrare il server e creare un endpoint server per il primo server di cui eseguire la migrazione. Non abilitare la funzionalità Suddivisione in livelli cloud.
4. Consentire la sincronizzazione di tutti i dati in Condivisione file di Azure (endpoint cloud).
5. Installare e registrare l'agente Sincronizzazione file di Azure in ogni server DFS-R rimanente.
6. Disabilitare DFS-R. 
7. Creare un endpoint server in ogni server DFS-R. Non abilitare la funzionalità Suddivisione in livelli cloud.
8. Verificare che la sincronizzazione sia stata completata e testare la topologia in base alle esigenze.
9. Disattivare DFS-R.
10. A questo punto, è possibile abilitare la funzionalità Suddivisione in livelli cloud in qualsiasi endpoint server in base alle esigenze.

Per altre informazioni, vedere [Interoperabilità di Sincronizzazione file di Azure con il file system distribuito](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere e rimuovere un endpoint server di Sincronizzazione file di Azure](storage-sync-files-server-endpoint.md)
- [Registrare e annullare la registrazione di un server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md)
