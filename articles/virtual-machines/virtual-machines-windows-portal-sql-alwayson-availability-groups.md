---
title: Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager
description: Creare un gruppo di disponibilità AlwaysOn con macchine virtuali di Azure in modalità Azure Resource Manager. Questa esercitazione usa principalmente l'interfaccia utente per creare automaticamente l'intera soluzione.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: MikeRayMSFT

---
# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager
> [!div class="op_single_selector"]
> * [Resource Manager: modello](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
> * [Resource Manager: manuale](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
> * [Classica: interfaccia utente](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
> * [Classica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)
> 
> 

<br/>

Questa esercitazione end-to-end mostra come creare un gruppo di disponibilità di SQL Server con macchine virtuali di Gestione risorse di Azure. L'esercitazione usa pannelli di Azure per configurare un modello. Nel corso dell'esercitazione l'utente rivedrà le impostazioni predefinite, digiterà i valori necessari e aggiornerà i pannelli nel portale.

Al termine dell'esercitazione, la soluzione per gruppi di continuità SQL Server in Azure sarà composta dagli elementi seguenti:

* Una rete virtuale contenente più subnet, tra cui una subnet front-end e una back-end
* Due controller di dominio con un dominio di Active Directory (AD)
* Due macchine virtuali di SQL Server distribuite nella subnet di back-end e aggiunte al dominio AD
* Un cluster WSFC a 3 nodi con il modello di quorum Maggioranza dei nodi
* Un gruppo di disponibilità con due repliche con commit sincrono di un database di disponibilità

La figura seguente è una rappresentazione grafica della soluzione.

![Architettura di laboratorio di test per gruppo di disponibilità in Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Tutte le risorse di questa soluzione appartengono a un solo gruppo.

Nell’esercitazione si presuppongono le condizioni seguenti:

* Si dispone già di un account Azure. In caso contrario, [iscriversi per ottenere un account di valutazione](http://azure.microsoft.com/pricing/free-trial/).
* Si conosce già la modalità di provisioning di una macchina virtuale SQL Server dalla raccolta di macchine virtuali tramite l'interfaccia utente grafica. Per altre informazioni, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md)
* Si ha già una conoscenza approfondita dei gruppi di disponibilità. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se si è interessati all'uso di gruppi di disponibilità con SharePoint, vedere anche [Configurare gruppi di disponibilità AlwaysOn di SQL Server 2012 per SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).
> 
> 

In questa esercitazione verrà usato il portale di Azure per i seguenti scopi:

* Selezionare il modello AlwaysOn dal portale
* Rivedere le impostazioni del modello e aggiornare alcune specifiche di configurazione per adattarle all'ambiente in uso
* Monitorare Azure durante la creazione dell'intero ambiente
* Connettersi a uno dei controller di dominio e quindi a uno dei server SQL

[!INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Effettuare il provisioning del cluster dalla raccolta
Azure offre un'immagine della raccolta per l'intera soluzione. Per individuare il modello:

1. Accedere al portale di Azure con il proprio account.
2. Nel portale di Azure fare clic su **+Nuovo**. Nel portale si aprirà il pannello Nuovo.
3. Nel pannello Nuovo cercare **AlwaysOn**.
   ![Individuare il modello AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Nei risultati della ricerca trovare **SQL Server AlwaysOn Cluster**.
   ![Modello AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. In **Selezionare un modello di distribuzione** scegliere **Resource Manager**.

### <a name="basics"></a>Nozioni di base
Fare clic su **Nozioni di base** e configurare quanto segue:

* **Nome utente amministratore** : si tratta di un account utente con autorizzazioni di amministratore di dominio ed è anche membro del ruolo predefinito del server sysadmin di SQL Server in entrambe le istanze di SQL Server. Per questa esercitazione, usare **DomainAdmin**.
* **Password** : la password dell'account di amministratore di dominio. Usare una password complessa. Confermare la password.
* **Sottoscrizione** : la sottoscrizione che verrà fatturata da Azure per l'esecuzione di tutte le risorse distribuite per il gruppo di disponibilità. Se l'account ha più sottoscrizioni, è possibile specificarne una diversa.
* **Gruppo di risorse** : nome del gruppo a cui appartengono tutte le risorse di Azure create da questa esercitazione. Per questa esercitazione usare **SQL-HA-RG**. Per altre informazioni, vedere (Panoramica di Gestione risorse di Azure)[resource-group-overview.md/#resource-groups].
* **Posizione** : l'area di Azure in cui verranno create le risorse per questa esercitazione. Selezionare un'area di Azure per ospitare l'infrastruttura.

Il pannello **Informazioni di base** avrà un aspetto simile al seguente:

![Nozioni di base](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

* Fare clic su **OK**.

### <a name="domain-and-network-settings"></a>Impostazioni di dominio e di rete
Questo modello di raccolta di Azure crea un nuovo dominio con nuovi controller di dominio. Crea inoltre una nuova rete e due subnet. Il modello non abilita la creazione di server in un dominio o in una rete virtuale esistente. Il passaggio successivo consiste nel configurare le impostazioni di dominio e di rete.

Nel pannello **Impostazioni di dominio e di rete** rivedere i valori predefiniti per le impostazioni di dominio e di rete:

* **Nome di dominio radice della foresta** : il nome di dominio che verrà usato per il dominio di Active Directory che ospiterà il cluster. Per questa esercitazione usare **contoso.com**.
* **Nome rete virtuale** : il nome della rete virtuale di Azure. Per questa esercitazione usare **autohaVNET**.
* **Nome subnet del controller di dominio** : il nome della parte della rete virtuale che ospita il controller di dominio. Per questa esercitazione usare **subnet-1**. Questa subnet userà il prefisso dell'indirizzo **10.0.0.0/24**.
* **Nome subnet di SQL Server** : il nome della parte della rete virtuale che ospita i server SQL Server e il controllo di condivisione file. Per questa esercitazione usare **subnet-2**. Questa subnet userà il prefisso dell'indirizzo **10.0.1.0/26**.

Per altre informazioni sulle reti virtuali in Azure, vedere [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md).  

Il pannello **Impostazioni di dominio e di rete** ha un aspetto simile al seguente:

![Impostazioni di dominio e di rete](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessario, questi valori possono essere modificati. Per questa esercitazione verranno usati i valori predefiniti.

* Rivedere le impostazioni e fare clic su **OK**.

### <a name="availability-group-settings"></a>Impostazioni gruppo di disponibilità
In **Impostazioni del gruppo di disponibilità** rivedere i valori predefiniti per il gruppo di disponibilità e il listener.

* **Nome del gruppo di disponibilità** : il nome della risorsa cluster per il gruppo di disponibilità. Per questa esercitazione usare **Contoso-ag**.
* **Nome del listener del gruppo di disponibilità** : usato dal cluster e dal servizio di bilanciamento del carico interno. I client che si connettono a SQL Server possono usare questo nome per connettersi alla replica corretta del database. Per questa esercitazione usare **Contoso-listener**.
* **Porta del listener del gruppo di disponibilità** : specifica la porta TCP che verrà usata dal listener di SQL Server. Per questa esercitazione usare la porta predefinita **1433**.

Se necessario, questi valori possono essere modificati. Per questa esercitazione usare i valori predefiniti.  

![Impostazioni gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

* Fare clic su **OK**.

### <a name="vm-size,-storage-settings"></a>Dimensione VM, impostazioni di archiviazione
In **Dimensioni macchina virtuale, impostazioni di archiviazione** scegliere la dimensione della macchina virtuale di SQL Server e rivedere le altre impostazioni.

* **Dimensioni macchina virtuale di SQL Server** : indica la dimensione della macchina virtuale di Azure per entrambi i server SQL. Scegliere la dimensione della macchina virtuale più adatta al proprio carico di lavoro. Se si compila l'ambiente per questa esercitazione, usare **DS2**. Per i carichi di lavoro di produzione, scegliere una dimensione della macchina virtuale in grado di supportare il carico. Molti carichi di lavoro di produzione richiedono dimensioni **DS4** o superiori. Il modello creerà due macchine virtuali di questa dimensione e installerà SQL Server su ciascuna di esse. Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md).

> [!NOTE]
> Azure installerà SQL Server Enterprise Edition. Il costo dipende dalla versione e dalla dimensione della macchina virtuale. Per informazioni dettagliate sui costi attuali, vedere [Prezzi di Macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
> 
> 

* **Dimensioni macchina virtuale del controller di dominio** : indica la dimensione della macchina virtuale per i controller di dominio. Per questa esercitazione usare **D2**.
* **Dimensioni macchina virtuale del controllo di condivisione file** : indica la dimensione della macchina virtuale per il controllo di condivisione file. Per questa esercitazione usare **A1**.
* **Account di archiviazione SQL** : indica il nome dell'account di archiviazione che contiene i dischi del sistema operativo e i dati di SQL Server. Per questa esercitazione usare **alwaysonsql01**.
* **Account di archiviazione del controller di dominio** : indica il nome dell'account di archiviazione per i controller di dominio. Per questa esercitazione usare **alwaysondc01**.
* **Dimensioni del disco dati di SQL Server** : indica la dimensione del disco dati di SQL Server espressa in TB. Specificare un numero compreso tra 1 e 4. Questa è la dimensione del disco dati che verrà associata a ciascun server SQL. Per questa esercitazione usare **1**.
* **Ottimizzazione dell'archiviazione** : definisce le impostazioni di configurazione dell'archiviazione specifiche per le macchine virtuali di SQL Server in base al tipo di carico di lavoro. Tutti i server SQL di questo scenario usano account di archiviazione Premium con la cache dell'host del disco dati di Azure impostata su Sola lettura. È inoltre possibile ottimizzare le impostazioni di SQL Server in base al carico di lavoro scegliendo una delle tre opzioni seguenti:
  
  * **Carico di lavoro generale** : non definisce impostazioni di configurazione specifiche.
  * **Elaborazione transazionale** : imposta i flag di traccia 1117 e 1118.
  * **Data warehousing** : imposta i flag di traccia 1117 e 610.

Per questa esercitazione usare **Carico di lavoro generale**.

![Impostazioni archiviazione dimensione VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

* Rivedere le impostazioni e fare clic su **OK**.

#### <a name="a-note-about-storage"></a>Nota sull'archiviazione
Altre scelte di ottimizzazione dipendono dalla dimensione dei dischi dati del server SQL. Per ciascun terabyte di disco dati, Azure aggiunge un 1 TB aggiuntivo di archiviazione Premium (SSD). Quando un server richiede 2 TB o più, il modello crea un pool di archiviazione in ogni server SQL. Un pool di archiviazione è una forma di virtualizzazione dell'archiviazione in cui sono configurati più dischi per fornire maggiore capacità, flessibilità e prestazioni.  Il modello crea uno spazio di archiviazione nel pool di archiviazione e lo presenta come dato unico al sistema operativo. Il modello definisce il disco come disco dati per SQL Server. Il modello ottimizza il pool di archiviazione per SQL Server mediante le impostazioni seguenti:

* Dimensione stripe: è l'impostazione di interfoliazione per il disco virtuale. Per i carichi di lavoro transazionali, il valore di questa impostazione è 64 KB. Per i carichi di lavoro di data warehousing, il valore di questa impostazione è 256 KB.
* Resilienza: nessuna resilienza.

> [!NOTE]
> Un account di archiviazione Premium di Azure è caratterizzato da ridondanza locale e mantiene tre copie di dati in una singola area. Non è quindi necessaria resilienza aggiuntiva nel pool di archiviazione.
> 
> 

* Totale colonne: indica il numero di dischi presenti nel pool di archiviazione.

Per altre informazioni sullo spazio di archiviazione e sui pool di archiviazione, vedere:

* [Panoramica di Spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx).
* [Windows Server Backup e pool di archiviazione](http://technet.microsoft.com/library/dn390929.aspx)

Per altre informazioni sulle procedure consigliate per la configurazione di SQL Server, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md)

### <a name="sql-server-settings"></a>Impostazioni di SQL Server
In **Impostazioni di SQL Server** rivedere e modificare il prefisso del nome della VM di SQL Server, la versione di SQL Server, l'account e la password del servizio SQL Server e il programma di manutenzione per l'applicazione automatica di patch di SQL Server.

* **Prefisso nome di SQL Server** : consente di creare un nome per ogni server SQL. Per questa esercitazione usare **Contoso-ag**. I nomi di SQL Server saranno *Contoso-ag-0* e *Contoso-ag-1*.
* **Versione di SQL Server** : è la versione di SQL Server. Per questa esercitazione usare **SQL Server 2014**. È anche possibile scegliere **SQL Server 2012** o **SQL Server 2016**.
* **Nome utente dell'account del servizio SQL Server** : indica il nome dell'account di dominio per il servizio SQL Server. Per questa esercitazione usare **sqlservice**.
* **Password** : la password dell'account del servizio SQL Server.  Usare una password complessa. Confermare la password.
* **Pianificazione manutenzione per l'applicazione automatica delle patch di SQL** : identifica il giorno della settimana in cui Azure applicherà automaticamente patch ai server SQL Server. Per questa esercitazione digitare **Domenica**.
* **Ora di inizio manutenzione per l'applicazione automatica delle patch di SQL** : in base all'area di Azure, indica l'ora in cui avrà inizio l'applicazione automatica delle patch.

> [!NOTE]
> La finestra di gestione delle patch per ogni VM è scaglionata in intervalli di un'ora. Per prevenire l'interruzione del servizio, le patch vengono applicate a una macchina virtuale alla volta.
> 
> 

![Impostazioni di SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Rivedere le impostazioni e fare clic su **OK**.

### <a name="summary"></a>Riepilogo
Convalidare le impostazioni nella pagina di riepilogo di Azure. È anche possibile scaricare il modello. Esaminare il riepilogo. Fare clic su **OK**.

### <a name="buy"></a>Acquistare
Il pannello finale contiene le **Condizioni per l'utilizzo** e l'**Informativa sulla privacy**. Esaminare le informazioni. Per consentire ad Azure di avviare la creazione delle macchine virtuali e di tutte le altre risorse necessarie per il gruppo di disponibilità, fare clic su **Crea**.

Il portale di Azure creerà il gruppo di risorse e tutte le altre risorse.

## <a name="monitor-deployment"></a>Monitorare la distribuzione
Monitorare l'avanzamento della distribuzione dal portale di Azure. Nel dashboard del portale di Azure viene aggiunta automaticamente un'icona che rappresenta lo stato di avanzamento.

![Dashboard di Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Connettersi a SQL Server
Le nuove istanze di SQL Server sono in esecuzione su macchine virtuali che non dispongono di connessioni a Internet. I controller di dominio, tuttavia, sono connessi direttamente a Internet. Per connettere i server SQL al desktop remoto, connettersi innanzitutto a uno dei controller di dominio mediante il protocollo RDP. Dal controller di dominio aprire una seconda connessione RDP al server SQL.

Per connettersi al controller di dominio primario mediante il protocollo RDP, seguire questa procedura:

1. Dal dashboard del portale di Azure verificare che la distribuzione abbia esito positivo.
2. Fare clic su **Risorse**.
3. Nel pannello **Risorse** fare clic su **ad-primary-dc**, ovvero il nome computer della macchina virtuale per il controller di dominio primario.
4. Nel pannello relativo ad **ad-primary-dc** click **Connetti**. Il browser chiederà se si desidera aprire o salvare l'oggetto connessione remota. Fare clic su **Apri**.
   ![Connessione al controller di dominio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
5. **Connessione desktop remoto** può informare l'utente che l'autore della connessione remota non può essere identificato. Fare clic su **Connect**.
6. Il sistema di sicurezza di Windows chiede all'utente di immettere le proprie credenziali per eseguire la connessione all'indirizzo IP del controller di dominio primario. Fare clic su **Usa un altro account**. In **Nome utente** digitare **contoso\DomainAdmin**. Si tratta dell'account scelto come nome utente dell'amministratore. Usare la password complessa scelta durante la configurazione del modello.
7. **Desktop remoto** potrebbe segnalare che il computer remoto non può essere autenticato a causa di problemi riguardanti il certificato di sicurezza. Verrà visualizzato il nome del certificato di sicurezza. Se sono stati eseguiti i passaggi dell'esercitazione, il nome sarà **ad-primary-dc.contoso.com**. Fare clic su **Sì**.

A questo punto si è connessi al controller di dominio primario. Per connettersi mediante RDP al server SQL, seguire questa procedura:

1. Nel controller di dominio aprire **Connessione desktop remoto**.
2. In **Computer**digitare il nome di uno dei server SQL Server. Per questa esercitazione digitare **sqlserver-0**.
3. Usare lo stesso account utente e la stessa password usati per connettersi al controller di dominio mediante RDP.

A questo punto si è connessi al server SQL mediante RDP. È possibile aprire SQL Server Management Studio, connettersi all'istanza predefinita di SQL Server e verificare che il gruppo di disponibilità sia configurato.

<!--HONumber=Oct16_HO2-->


