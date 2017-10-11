---
title: "Configurare la disponibilità elevata per macchine virtuali di Azure Resource Manager | Documentazione Microsoft"
description: "Questa esercitazione mostra come creare un gruppo di disponibilità AlwaysOn con macchine virtuali di Azure in modalità Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d430febee23081b26eee0a68d4beb43228549f52
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configurare manualmente un gruppo di disponibilità Always On nelle macchine virtuali di Azure tramite Resource Manager

Questa esercitazione illustra come creare un gruppo di disponibilità di SQL Server che usa macchine virtuali di Azure Resource Manager. L'esercitazione usa pannelli di Azure per configurare un modello. Nel corso dell'esercitazione l'utente rivedrà le impostazioni predefinite, digiterà i valori necessari e aggiornerà i pannelli nel portale.

L'esercitazione completa crea un gruppo di disponibilità di SQL Server in macchine virtuali di Azure, che includono gli elementi seguenti:

* Una rete virtuale con più subnet, inclusa una subnet front-end e una back-end
* Due controller di dominio che hanno un dominio di Active Directory
* Due macchine virtuali che eseguono SQL Server e che sono distribuite alla subnet back-end e aggiunte al dominio di Active Directory
* Un cluster di failover a tre nodi con il modello di quorum Maggioranza dei nodi
* Un gruppo di disponibilità che ha due repliche di commit sincrono di un database di disponibilità

La figura seguente rappresenta la soluzione completa.

![Architettura di lab di test per gruppi di disponibilità in Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Tutte le risorse di questa soluzione appartengono a un solo gruppo di risorse.

Prima di iniziare questa esercitazione, verificare quanto segue:

* Si dispone già di un account Azure. In caso contrario, [iscriversi per ottenere un account di valutazione](http://azure.microsoft.com/pricing/free-trial/).
* Si sa già come usare l'interfaccia utente grafica per effettuare il provisioning di una macchina virtuale di SQL Server dalla raccolta delle macchine virtuali. Per altre informazioni, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Si ha già una conoscenza approfondita dei gruppi di disponibilità. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se si è interessati all'uso di gruppi di disponibilità con SharePoint, vedere anche [Configurare gruppi di disponibilità AlwaysOn di SQL Server 2012 per SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).
>
>

In questa esercitazione verrà usato il portale di Azure per:

* Scegliere il modello Always On dal portale.
* Rivedere le impostazioni del modello e aggiornare alcune impostazioni di configurazione per adattarle all'ambiente in uso.
* Monitorare Azure durante la creazione dell'intero ambiente.
* Connettersi a un controller di dominio e quindi a un server che esegue SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Effettuare il provisioning del cluster dalla raccolta
Azure offre un'immagine della raccolta per l'intera soluzione. Per individuare il modello:

1. Accedere al Portale di Azure con il proprio account.
2. Nel Portale di Azure, fare clic su **+ Nuovo** per aprire il pannello **Nuovo**.
3. Nel pannello **Nuovo** cercare **AlwaysOn**.
   ![Individuare il modello AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Nei risultati della ricerca trovare **SQL Server AlwaysOn Cluster** (Cluster AlwaysOn di SQL Server).
   ![Modello AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. In **Selezionare un modello di distribuzione** scegliere **Resource Manager**.

### <a name="basics"></a>Nozioni di base
Fare clic su **Nozioni di base** e configurare le impostazioni seguenti:

* **Nome utente amministratore**: è un account utente con autorizzazioni di amministratore di dominio ed è anche membro del ruolo del server predefinito come amministratore di sistema di SQL Server in entrambe le istanze di SQL Server. Per questa esercitazione, usare **DomainAdmin**.
* **Password** : la password dell'account di amministratore di dominio. Usare una password complessa. Confermare la password.
* **Sottoscrizione**: è la sottoscrizione alla quale verrà emessa la fattura da Azure per l'esecuzione di tutte le risorse distribuite per il gruppo di disponibilità. Se l'account ha più sottoscrizioni, è possibile specificare una sottoscrizione diversa.
* **Gruppo di risorse**: è il nome del gruppo a cui appartengono tutte le risorse di Azure create con questo modello. Per questa esercitazione usare **SQL-HA-RG**. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Posizione**: l'area di Azure in cui verranno create le risorse per questa esercitazione. Scegliere un'area di Azure.

L'immagine è una schermata del pannello **Nozioni di base** completato:

![Nozioni di base](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Fare clic su **OK**.

### <a name="domain-and-network-settings"></a>Impostazioni di dominio e di rete
Questo modello di raccolta di Azure crea un dominio con controller di dominio. Crea inoltre una rete e due subnet. Il modello non può creare server in una rete virtuale o in un dominio esistente. Il passaggio successivo configura le impostazioni di dominio e di rete.

Nel pannello **delle impostazioni di dominio e di rete** rivedere i valori predefiniti per le impostazioni di dominio e di rete:

* **Nome di dominio radice della foresta**: è il nome di dominio per il dominio di Active Directory che ospita il cluster. Per questa esercitazione usare **contoso.com**.
* **Nome rete virtuale** : il nome della rete virtuale di Azure. Per questa esercitazione usare **autohaVNET**.
* **Nome subnet del controller di dominio** : il nome della parte della rete virtuale che ospita il controller di dominio. Usare **subnet-1**. Questa subnet usa il prefisso dell'indirizzo **10.0.0.0/24**.
* **Nome subnet di SQL Server**: è il nome della parte della rete virtuale che ospita i server che eseguono SQL Server e il controllo di condivisione file. Usare **subnet-2**. Questa subnet usa il prefisso dell'indirizzo **10.0.1.0/26**.

Per altre informazioni sulle reti virtuali in Azure, vedere [Panoramica di rete virtuale](../../../virtual-network/virtual-networks-overview.md).  

Il pannello delle **impostazioni di dominio e di rete** dovrebbe avere l'aspetto seguente:

![Impostazioni di dominio e di rete](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessario, questi valori possono essere modificati. Per questa esercitazione usare i valori predefiniti.

Rivedere le impostazioni e fare clic su **OK**.

### <a name="availability-group-settings"></a>Impostazioni gruppo di disponibilità
In **Impostazioni del gruppo di disponibilità** rivedere i valori predefiniti per il gruppo di disponibilità e il listener.

* **Nome del gruppo di disponibilità**: il nome della risorsa cluster per il gruppo di disponibilità. Per questa esercitazione usare **Contoso-ag**.
* **Nome del listener del gruppo di disponibilità**: viene usato dal cluster e dal servizio di bilanciamento del carico interno. I client che si connettono a SQL Server possono usare questo nome per connettersi alla replica corretta del database. Per questa esercitazione usare **Contoso-listener**.
* **Porta del listener del gruppo di disponibilità**: specifica la porta TCP del listener di SQL Server. Per questa esercitazione usare la porta predefinita **1433**.

Se necessario, questi valori possono essere modificati. Per questa esercitazione usare i valori predefiniti.  

![Impostazioni gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Fare clic su **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Impostazioni delle dimensioni della macchina virtuale e di archiviazione
Nelle **impostazioni delle dimensioni della macchina virtuale e di archiviazione** scegliere una dimensione per la macchina virtuale di SQL Server e rivedere le altre impostazioni.

* **Dimensioni macchina virtuale di SQL Server**: indica le dimensioni per entrambe le macchine virtuali che eseguono SQL Server. Scegliere le dimensioni più appropriate della macchina virtuale per il carico di lavoro. Se si crea questo ambiente per l'esercitazione, usare **DS2**. Per i carichi di lavoro di produzione, scegliere una dimensione della macchina virtuale in grado di supportare il carico. Molti carichi di lavoro di produzione richiedono dimensioni **DS4** o superiori. Il modello crea due macchine virtuali di questa dimensione e installa SQL Server su ciascuna di esse. Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure installa SQL Server Enterprise Edition. Il costo dipende dalla versione e dalla dimensione della macchina virtuale. Per informazioni dettagliate sui costi correnti, vedere [Prezzi di macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Dimensioni macchina virtuale del controller di dominio** : indica la dimensione della macchina virtuale per i controller di dominio. Per questa esercitazione usare **D2**.
* **Dimensioni macchina virtuale del controllo di condivisione file** : indica la dimensione della macchina virtuale per il controllo di condivisione file. Per questa esercitazione usare **A1**.
* **Account di archiviazione SQL**: è il nome dell'account di archiviazione che contiene i dischi del sistema operativo e i dati di SQL Server. Per questa esercitazione usare **alwaysonsql01**.
* **Account di archiviazione del controller di dominio** : indica il nome dell'account di archiviazione per i controller di dominio. Per questa esercitazione usare **alwaysondc01**.
* **Dimensioni del disco dati di SQL Server** : indica la dimensione del disco dati di SQL Server espressa in TB. Specificare un numero compreso tra 1 e 4. Per questa esercitazione usare **1**.
* **Ottimizzazione dell'archiviazione** : definisce le impostazioni di configurazione dell'archiviazione specifiche per le macchine virtuali di SQL Server in base al tipo di carico di lavoro. Tutti le macchine virtuali di SQL Server in questo scenario usano account di archiviazione Premium con la cache dell'host del disco dati di Azure impostata su sola lettura. È inoltre possibile ottimizzare le impostazioni di SQL Server in base al carico di lavoro scegliendo una delle tre opzioni seguenti:

  * **Carico di lavoro generale**: non definisce impostazioni di configurazione specifiche.
  * **Elaborazione transazionale**: imposta i flag di traccia 1117 e 1118.
  * **Data warehousing**: imposta i flag di traccia 1117 e 610.

Per questa esercitazione usare **Carico di lavoro generale**.

![Impostazioni archiviazione dimensione VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Rivedere le impostazioni e fare clic su **OK**.

#### <a name="a-note-about-storage"></a>Nota sull'archiviazione
Altre scelte di ottimizzazione dipendono dalla dimensione dei dischi dati del server SQL. Per ogni terabyte di disco dati, Azure aggiunge un extra 1 TB di archiviazione Premium. Quando un server richiede 2 TB o più, il modello crea un pool di archiviazione in ogni macchina di virtuale di SQL Server. Un pool di archiviazione è una forma di virtualizzazione dell'archiviazione in cui sono configurati più dischi per fornire maggiore capacità, flessibilità e prestazioni.  Il modello crea uno spazio di archiviazione nel pool di archiviazione e presenta un disco dati unico al sistema operativo. Il modello definisce il disco come disco dati per SQL Server. Il modello ottimizza il pool di archiviazione per SQL Server tramite le impostazioni seguenti:

* Dimensione stripe: è l'impostazione di interfoliazione per il disco virtuale. I carichi di lavoro transazionali usano 64 KB. I carichi di lavoro del warehouse di dati usano 256 KB.
* Resilienza: nessuna resilienza.

> [!NOTE]
> Un account di archiviazione Premium di Azure è caratterizzato da ridondanza locale e mantiene tre copie di dati in una singola area. Non è quindi necessaria resilienza aggiuntiva nel pool di archiviazione.
>
>

* Totale colonne: indica il numero di dischi presenti nel pool di archiviazione.

Per altre informazioni sullo spazio di archiviazione e sui pool di archiviazione, vedere:

* [Panoramica di spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server Backup e pool di archiviazione](http://technet.microsoft.com/library/dn390929.aspx)

Per altre informazioni sulle procedure consigliate per la configurazione di SQL Server, vedere [Procedure consigliate per le prestazioni per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Impostazioni di SQL Server
In **Impostazioni di SQL Server** rivedere e modificare il prefisso del nome della macchina virtuale di SQL Server, la versione di SQL Server, l'account e la password dell'account di servizio SQL Server e la pianificazione della manutenzione per l'applicazione automatica di patch di SQL Server.

* **Prefisso nome di SQL Server**: viene usato per creare un nome per ogni macchina virtuale di SQL Server. Per questa esercitazione usare **sqlserver**. I nomi dei modelli di macchine virtuali di SQL Server sono *sqlserver-0* e *sqlserver-1*.
* **Versione di SQL Server**: è la versione di SQL Server. Per questa esercitazione usare **SQL Server 2014**. È anche possibile scegliere **SQL Server 2012** o **SQL Server 2016**.
* **Nome utente dell'account del servizio SQL Server** : indica il nome dell'account di dominio per il servizio SQL Server. Per questa esercitazione usare **sqlservice**.
* **Password** : la password dell'account del servizio SQL Server.  Usare una password complessa. Confermare la password.
* **Pianificazione della manutenzione per l'applicazione automatica delle patch di SQL**: identifica il giorno della settimana in cui Azure applicherà automaticamente le patch ai server SQL Server. Per questa esercitazione digitare **Sunday** (domenica).
* **SQL Auto Patching maintenance start hour** (Ora di inizio manutenzione per l'applicazione automatica delle patch di SQL): in base all'area di Azure, indica l'ora in cui ha inizio l'applicazione automatica delle patch.

> [!NOTE]
> La finestra di gestione delle patch per ogni VM è sfalsata in intervalli di un'ora. Per prevenire l'interruzione del servizio, le patch vengono applicate a una macchina virtuale alla volta.
>
>

![Impostazioni di SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Rivedere le impostazioni e fare clic su **OK**.

### <a name="summary"></a>Riepilogo
Convalidare le impostazioni nella pagina di riepilogo di Azure. È anche possibile scaricare il modello. Esaminare il riepilogo. Fare clic su **OK**.

### <a name="buy"></a>Acquistare
Questo pannello finale contiene le **condizioni d'uso** e l'**informativa sulla privacy**. Esaminare le informazioni. Per consentire ad Azure di avviare la creazione delle macchine virtuali e di tutte le altre risorse necessarie per il gruppo di disponibilità, fare clic su **Crea**.

Il portale di Azure crea il gruppo di risorse e tutte le altre risorse.

## <a name="monitor-deployment"></a>Monitorare la distribuzione
Monitorare l'avanzamento della distribuzione dal portale di Azure. Un'icona che rappresenta la distribuzione viene automaticamente aggiunta al dashboard del Portale di Azure.

![Dashboard di Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Connettersi a SQL Server
Le nuove istanze di SQL Server sono in esecuzione su macchine virtuali che hanno indirizzi IP connessi a Internet. È possibile effettuare connessioni desktop remoto (RDP) direttamente a ogni macchina virtuale di SQL Server.

Per connettersi mediante RDP al server SQL, seguire questa procedura:

1. Dal dashboard del Portale di Azure verificare se la distribuzione è riuscita.
2. Fare clic su **Risorse**.
3. Nel pannello **Risorse** fare clic su **sqlserver-0**, ovvero il nome computer di una delle macchine virtuali che eseguono SQL Server.
4. Nel pannello di **sqlserver-0** fare clic su **Connetti**. Il browser chiederà se si desidera aprire o salvare l'oggetto di connessione remota. Fare clic su **Apri**.
5. La **connessione Desktop remoto** potrebbe visualizzare un avviso che l'entità di pubblicazione della connessione remota non può essere identificato. Fare clic su **Connect**.
6. Il sistema di sicurezza di Windows chiede all'utente di immettere le proprie credenziali per eseguire la connessione all'indirizzo IP del controller di dominio primario. Fare clic su **Usa un altro account**. In **Nome utente** digitare **contoso\DomainAdmin**. Questo account è stato configurato quando è stato impostato il nome utente dell'amministratore nel modello. Usare la password complessa scelta durante la configurazione del modello.
7. **Desktop remoto** potrebbe segnalare che il computer remoto non può essere autenticato a causa di problemi relativi al certificato di sicurezza. Mostra il nome del certificato di sicurezza. Se sono stati eseguiti i passaggi dell'esercitazione, il nome è **sqlserver-0.contoso.com**. Fare clic su **Sì**.

A questo punto si è connessi alla macchina virtuale di server SQL tramite Remote Desktop Protocol (RDP). È possibile aprire SQL Server Management Studio, connettersi all'istanza predefinita di SQL Server e verificare che il gruppo di disponibilità sia configurato.
