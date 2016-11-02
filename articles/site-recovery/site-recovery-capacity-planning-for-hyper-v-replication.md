<properties
	pageTitle="Eseguire lo strumento di pianificazione della capacità di Hyper-V per Site Recovery | Microsoft Azure"
	description="Questo articolo contiene le istruzioni per l'uso dello strumento di pianificazione della capacità di Hyper-V per Azure Site Recovery."
	services="site-recovery"
	documentationCenter="na"
	authors="rayne-wiselman"
	manager="jwhit"
	editor="" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="07/12/2016"
	ms.author="raynew" />

# Eseguire lo strumento di pianificazione della capacità di Hyper-V per Site Recovery

Come parte della distribuzione di Azure Site Recovery, è necessario determinare i requisiti di replica e di larghezza di banda. Lo strumento di pianificazione della capacità di Hyper-V per Site Recovery consente di determinare i requisiti di replica e di larghezza di banda per la replica delle macchine virtuali Hyper-V.


Questo articolo descrive come eseguire lo strumento di pianificazione della capacità di Hyper-V. Questo strumento deve essere usato con altri strumenti di pianificazione della capacità e in base alle informazioni presenti nell'articolo relativo alla [pianificazione della capacità per Site Recovery](site-recovery-capacity-planner.md).


## Prima di iniziare

Si esegue lo strumento su un server Hyper-V o su un nodo cluster nel sito primario. Per eseguire lo strumento, il server host Hyper-V richiede:

- Sistema operativo: Windows Server® 2012 o Windows Serve ® 2012 R2
- Memoria: 20 MB (minimo)
- CPU: sovraccarico del 5% (minimo)
- Spazio su disco: 5 MB (minimo)

Prima di eseguire lo strumento, sarà necessario preparare il sito primario. Se si sta eseguendo la replica tra due siti locali e si vuole controllare la larghezza di banda, sarà necessario preparare anche un server di replica.


## Passaggio 1: Preparare il sito primario
1. Nel sito primario crea un elenco di tutte le macchine virtuali Hyper-V da replicare e degli host/cluster Hyper-V in cui si trovano. Lo strumento può essere eseguito ogni volta per più host autonomi o per un singolo cluster, ma non per entrambi. Deve anche essere eseguito separatamente per ogni sistema operativo, quindi è consigliabile raccogliere e prendere nota dei server Hyper-V, come indicato di seguito:

  - Server autonomi di Windows Server® 2012
  - Cluster di Windows Server® 2012
  - Server autonomi di Windows Server ® 2012 R2
  - Cluster di Windows Server ® 2012 R2

3. Abilitare l'accesso remoto a WMI in tutti gli host e cluster Hyper-V. Eseguire questo comando in ogni server/cluster per verificare che le regole del firewall e le autorizzazioni utente siano impostate:

        netsh firewall set service RemoteAdmin enable

5. Abilitare il monitoraggio delle prestazioni su server e cluster, come indicato di seguito:

  - Aprire Windows Firewall con lo snap-in **Sicurezza avanzata** e quindi abilitare le regole in ingresso seguenti: **Accesso alla rete COM+ (DCOM-In)** e tutte le regole nel **gruppo Gestione remota registro eventi**.

## Passaggio 2: Preparare un server di replica (replica da locale a locale)

Questo passaggio non è necessario se si sta eseguendo la replica in Azure.

È consigliabile configurare un solo host Hyper-V come server di ripristino per potervi replicare una VM fittizia e controllare così la larghezza di banda. È possibile saltare questo passaggio, ma in tal caso non si potrà misurare la larghezza di banda.

1. Per usare un nodo cluster come replica, configurare il Gestore di replica Hyper-V:

	- In **Server Manager**, aprire **Gestione Cluster di Failover**.
	- Connettersi al cluster, evidenziare il nome del cluster e fare clic su **Azioni** > **Configura ruolo** per aprire la Configurazione guidata disponibilità elevata.
	- In **Selezione ruolo** fare clic su **Gestore di replica Hyper-V**. Nella procedura guidata specificare un **nome NetBIOS** e un **indirizzo IP** da usare come punto di connessione con il cluster (denominato punto di accesso client). Il **Gestore di replica Hyper-V** verrà configurato con il nome di un punto di accesso client di cui è meglio prendere nota.
	- Verificare che il ruolo di Gestore di replica Hyper-V sia stato connesso online correttamente e che possa eseguire il failover tra tutti i nodi del cluster. A tale scopo, fare clic con il pulsante del mouse sul ruolo, scegliere **Sposta**, quindi fare clic su **Seleziona nodo**. Selezionare un nodo > **OK**.
	- Se si usa l'autenticazione basata su certificato, verificare che ogni nodo del cluster e il punto di accesso client abbiano tutti il certificato installato.
2.  Abilitare un server di replica:

	- Per un cluster aprire Gestione cluster di failover, connettersi al cluster e fare clic su **Ruoli** > Selezione ruolo > **Impostazioni di replica** > **Abilita questo cluster come server di replica**. Si noti che, se si usa un cluster come replica, il ruolo Gestore di replica Hyper-V dovrà essere presente anche sul cluster nel sito primario.
	- Per un server autonomo, aprire la Console di gestione di Hyper-V. Nel riquadro **Azioni** fare clic su **Impostazioni Hyper-V** per il server da abilitare e in **Configurazione replica** fare clic **Abilita questo computer come server di replica**.
3. Configurare l'autenticazione:

	- In **Autenticazione e porte** scegliere come autenticare il server primario e le porte di autenticazione. Se si usa un certificato, fare clic su **Selezione certificato** per selezionarne uno. Usare Kerberos se gli host Hyper-V primario e di ripristino si trovano nello stesso dominio o in domini attendibili. Usare i certificati per la distribuzione in domini diversi o in un gruppo di lavoro.
	- Nella sezione **Autorizzazione e archiviazione** consentire a **qualsiasi** server (primario) autenticato l'invio di dati di replica al server di replica. Fare clic su **OK** o **Applica**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

	- Eseguire **netsh http show servicestate** per controllare che il listener sia in esecuzione per il protocollo o la porta specificata:
4. Configurare i firewall. Durante l'installazione di Hyper-V, vengono create regole del firewall per consentire il traffico sulle porte predefinite (HTTPS su 443, Kerberos su 80). Abilitare queste regole come indicato di seguito:

		- Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
		- Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
		- Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
		- Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## Passaggio 3: Eseguire lo strumento di pianificazione della capacità

Dopo avere preparato un sito primario e configurato un server di ripristino, è possibile eseguire lo strumento.

1. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39057) lo strumento dall'Area download Microsoft.
2. Eseguire lo strumento da uno dei server primari (o uno dei nodi del cluster primario). Il pulsante destro del file .exe e quindi scegliere **Esegui come amministratore**.
3. In **Before you begin** specificare per quanto tempo raccogliere i dati. È consigliabile eseguire lo strumento durante l'orario di produzione per essere certi che i dati siano rappresentativi. Se si sta cercando solo di convalidare la connettività di rete, è possibile raccoglierli solo per un minuto.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. In **Primary site details** specificare il nome del server o il nome FQDN per un host autonomo oppure, per un cluster, specificare il nome FQDN del punto di accettazione client, il nome del cluster o di qualsiasi nodo nel cluster e quindi fare clic su **Next**. Lo strumento rileva automaticamente il nome del server su cui è in esecuzione. Lo strumento seleziona le VM che possono essere monitorate per i server specificati.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. In **Replica Site Details**, se si sta eseguendo la replica in Azure o se si sta eseguendo la replica in un centro dati secondario e non si è configurato un server di replica, selezionare **Skip tests involving replica site**. Se si sta eseguendo la replica in un centro dati secondario e si è configurato un tipo di replica, digitare il nome FQDN del server autonomo o del punto di accettazione client per il cluster in **Server name (or) Hyper-V Replica Broker CAP**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. In **Extended Replica Details** abilitare **Skip the tests involving Extended Replica site**. Questi test non sono supportati da Site Recovery.
7. In **Choose VMs to Replicate** lo strumento di connette al server o al cluster e visualizza le VM e i dischi in esecuzione sul server primario, in base alle impostazioni specificate nella pagina **Primary Site Details**. Si noti che le VM già abilitate per la replica o non in esecuzione non verranno visualizzate. Selezionare le VM per cui raccogliere le metriche. Selezionando i VHD, i dati vengono automaticamente raccolti anche per le VM.
9. Se si è configurato un server o un cluster di replica, in **Network information** specificare la larghezza di banda WAN approssimativa che si ritiene verrà usata tra i siti primario e di replica e selezionare i certificati se si è configurata l'autenticazione del certificato.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. In **Summary** controllare le impostazioni e fare clic su **Next** per iniziare a raccogliere le metriche. L'avanzamento e lo stato dello strumento vengono visualizzati nella pagina **Calculate Capacity**. Quando è terminata l'esecuzione, fare clic su **View Report** per visualizzare l'output. Per impostazione predefinita, i report e i log vengono archiviati in **%systemdrive%\\Users\\Public\\Documents\\Capacity Planner**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## Passaggio 4: Interpretazione dei risultati
Ecco le metriche importanti: È possibile ignorare le metriche non elencate qui. Non sono rilevanti per Site Recovery.

### Replica da sito locale a sito locale
  - Impatto della replica nella capacità di elaborazione di memoria dell'host primari.
  - Impatto della replica sullo spazio su disco di archiviazione dell'host di ripristino primario, in IOPS
  - Larghezza di banda totale necessaria per la replica delta (Mbps)
  - Larghezza di banda di rete osservata tra l'host primario e l'host di ripristino (Mbps)
  - Suggerimenti per il numero ideale di trasferimenti paralleli attivi tra due host/cluster

### Replica da sito locale ad Azure
  - Impatto della replica nella capacità di elaborazione di memoria dell'host primari.
  - Impatto della replica sullo spazio su disco di archiviazione dell'host primario, in IOPS
  - Larghezza di banda totale necessaria per la replica delta (Mbps)

## Altre risorse

- Per informazioni dettagliate sullo strumento, leggere il documento che accompagna il download dello strumento.
- Guardare una procedura dettagliata dello strumento nel [blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx) di Keith Mayer.
- [Ottenere i risultati](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) dei test di prestazioni per la replica Hyper-V da sito locale a sito locale



## Passaggi successivi

Dopo aver completato la pianificazione della capacità, iniziare la distribuzione di Site Recovery:

- [Replicare le VM Hyper-V nei cloud VMM in Azure.](site-recovery-vmm-to-azure.md)
- [Eseguire la replica di VM Hyper-V (senza VMM) in Azure](site-recovery-hyper-v-site-to-azure.md)
- [Eseguire la replica di VM Hyper-V tra siti VMM](site-recovery-vmm-to-vmm.md)
- [Eseguire la replica di VM Hyper-V tra siti VMM con SAN](site-recovery-vmm-san.md)
- [Eseguire la replica di VM Hyper-V su server VMM singolo](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0720_2016-->