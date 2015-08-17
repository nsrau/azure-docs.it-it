<properties
	pageTitle="Pianificazione della capacità per la replica Hyper-V"
	description="In questo articolo sono contenute le istruzioni su come utilizzare lo strumento di pianificazione della capacità per Azure Site Recovery e include le risorse necessarie per la pianificazione della capacità per la replica Hyper-V"
	services="site-recovery"
	documentationCenter="na"
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn" />

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/01/2015"
	ms.author="lauraa" />


# Pianificazione della capacità per la replica Hyper-V

Azure Site Recovery utilizza la Replica Hyper-V per la effettuare la replica tra due siti VMM locali o tra un server VMM locale e una risorsa di archiviazione di Azure. In questo articolo vengono fornite istruzioni dettagliate sull'utilizzo di Capacity planner per Azure Site Recovery (ASR) - strumento di Replica Hyper-V. Capacity Planner per ASR: strumento di Replica Hyper-V che guida un amministratore IT nella progettazione di server, nella configurazione delle risorse di archiviazione e nella progettazione dell'infrastruttura di rete necessaria per distribuire correttamente la Replica Hyper-V e per convalidare la connettività di rete tra due siti.

## Requisiti di sistema
- Sistema operativo: Windows Server® 2012 o Windows Serve ® 2012 R2
- Memoria: 20 MB (minimo)
- CPU: sovraccarico del 5% (minimo)
- Spazio su disco: 5 MB (minimo)

## Passaggi dell'esercitazione
- Passaggio 1: Preparare il sito primario
- Passaggio 2: Preparare il sito di ripristino se il sito di ripristino è locale
- Passaggio 3: Eseguire lo strumento di pianificazione della capacità
- Passaggio 4: Interpretazione dei risultati

## Passaggio 1: Preparare il sito primario
1. Creare un elenco di tutte le macchine virtuali Hyper-V che dovrà essere abilitate alla replica e dei corrispondenti host/cluster Hyper V primari.
2. Gruppo primario di host Hyper-V e cluster in uno dei seguenti scenari:

  - Server autonomi di Windows Server® 2012
  - Cluster di Windows Server® 2012
  - Server autonomi di Windows Server ® 2012 R2
  - Cluster di Windows Server ® 2012 R2

3. Occorre eseguire lo strumento di pianificazione della capacità una volta per ogni gruppo di server autonomi e una volta per ogni cluster.
4. Abilitare l'accesso remoto a WMI in tutti gli host primari e cluster. Verificare che il gruppo di regole del firewall e le autorizzazioni utente siano impostati.

        netsh firewall set service RemoteAdmin enable

5. Abilitare il monitoraggio delle prestazioni negli host primari.

  - Aprire il **Windows Firewall** con lo snap-in **Sicurezza avanzata**, quindi abilitare le seguenti regole in ingresso:
    - Accesso alla rete COM+ (DCOM-In)
    - Tutte le regole nel gruppo di gestione remota registro eventi

## Passaggio 2: Preparare il sito di ripristino
Se si utilizza Azure come sito di ripristino o il sito di ripristino locale non è ancora pronto, è possibile ignorare questa sezione. Tuttavia, se ignorata, non sarà possibile misurare la larghezza di banda disponibile tra i due siti.

1. Identificare il metodo di autenticazione

	a. Kerberos: da utilizzare quando gli host Hyper-V primario e di ripristino si trovano nello stesso dominio o in domini attendibili.

	b. Certificato: da utilizzare quando gli host Hyper-V primario e di ripristino si trovano in domini diversi. È possibile creare i certificati utilizzando makecert. Per informazioni sui passaggi necessari per distribuire certificati tramite questa tecnica, vedere il post del blog relativo all'[autenticazione basata sul certificato di replica Hyper-V - makecert](http://blogs.technet.com/b/virtualization/archive/2013/04/13/hyper-v-replica-certificate-based-authentication-makecert.aspx).

2. Identificare un **singolo** host/cluster Hyper-V di ripristino dal sito di ripristino cluster .

	a. Questo cluster o host di ripristino verrà utilizzato per replicare una macchina virtuale fittizia e per stimare la larghezza di banda disponibile tra i siti primari e secondari.

	b. **Consigliato**: utilizzare un host Hyper-V di ripristino solo per l'esecuzione dei test.

### Preparare un singolo host Hyper-V come server di ripristino
1. Nella console di gestione di Hyper-V, nel riquadro **Azioni**, fare clic su **Impostazioni Hyper-V**
2. Nella finestra di dialogo **Impostazioni Hyper-V**, fare clic su **Configurazione replica**.
3. Nel riquadro dei dettagli selezionare **Abilita questo computer come server di Replica**.
4. Nella sezione **Autenticazione e porte** selezionare il metodo di autenticazione scelto in precedenza. Per entrambi i metodi autenticazione, specificare la porta da utilizzare (le porte predefinite sono 80 per Kerberos su HTTP e 443 per l'autenticazione basata su certificato su HTTPS).
5. Se si utilizza l'autenticazione basata su certificato, fare clic su **Seleziona certificato**, quindi fornire le relative informazioni.
6. Nella sezione **Autorizzazione e archiviazione** specificare un valore in modo da consentire a **qualsiasi** server (primario) autenticato l'invio di dati di replica al server di replica.
7. Fare clic su **OK** o **Applica**.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

8. Verificare che il listener https sia in esecuzione eseguendo il comando: netsh http show servicestate
9. Aprire le porte del firewall:

        Port 443 (certificae-based authentication):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"


        Port 80 (Kerberos):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"

### Preparare un singolo cluster Hyper-V come server di ripristino
Se è già stato preparato un host Hyper-V autonomo de un server di ripristino, è possibile ignorare questa sezione.

1. Configurare il Gestore di replica Hyper-V:

	a. In **Server Manager**, aprire **Gestione Cluster di Failover**.

	b. Nel riquadro sinistro, connettersi al cluster e, con il nome del cluster evidenziato, fare clic su **Configura ruolo** nel riquadro **Azioni**. Verrà visualizzata la **Configurazione guidata disponibilità elevata**.

	c. Nella schermata **Seleziona ruolo** selezionare **Gestore di replica Hyper-V**.

	d. Completare la procedura guidata, specificando un **nome NetBIOS** e un **indirizzo IP** da utilizzare come punto di connessione con il cluster (denominato punto di accesso client). Il **Gestore replica di Hyper-V** è configurato con il nome di un punto di accesso client. Prendere nota del nome del punto di accesso client, si utilizzerà per configurare la replica in un secondo momento.

	e. Verificare che il ruolo di Gestore di replica Hyper-V sia stato connesso online correttamente e che possa eseguire il failover tra tutti i nodi del cluster. A tale scopo, fare clic con il pulsante del mouse sul ruolo, scegliere **Sposta**, quindi fare clic su **Seleziona nodo**. Selezionare un nodo > **OK**.

	f. Se si utilizza l'autenticazione basata su certificato, assicurarsi che ogni nodo del cluster e il client del gestore di Replica Hyper-V accedere punto che tutto il certificato è installato.

2. Configurare le impostazioni di replica:

	a. In **Server Manager**, aprire **Gestione Cluster di Failover**.
	
	b. Nel riquadro sinistro, connettersi al cluster e, con il nome del cluster evidenziato, fare clic su **Ruoli** nella categoria di **spostamento** del riquadro **Azioni** .
	
	c. Fare clic con pulsante destro del mouse, quindi scegliere **Impostazioni di replica**.
	
	d. Nel riquadro **Dettagli** selezionare l'impostazione per **abilitare il computer come server di Replica**.

	e. Nella sezione **Autenticazioni e porte** selezionare il metodo di autenticazione scelto in precedenza. Per entrambi i metodi autenticazione, specificare la porta da utilizzare (le porte predefinite sono 80 per Kerberos su HTTP e 443 per l'autenticazione basata su certificato su HTTPS).

	f. Se si utilizza l'autenticazione basata su certificato, fare clic su **Seleziona certificato**, quindi fornire le relative informazioni.

	g. Nella sezione **Autorizzazione e archiviazione** specificare un valore in modo da consentire a **qualsiasi** server (primario) autenticato l'invio di dati di replica al server di replica. È possibile utilizzare caratteri jolly per limitare l'accettazione ai server da parte di un determinato dominio senza dover specificare singolarmente ciascuno di essi (ad esempio, *.contoso.com).

	h. Aprire le porte del firewall su tutti gli host Hyper-V di ripristino: porta 443 (autenticazione certificato): Get-ClusterNode | ForEach-Object {Invoke-command -computername \\$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}


          Port 80 (Kerberos auth):
              Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}


## Passaggio 3: Eseguire lo strumento di pianificazione della capacità
1. Scaricare lo strumento di pianificazione della capacità
2. Eseguire lo strumento da uno dei server primari (o uno dei nodi del cluster primario). Il pulsante destro del file .exe e quindi scegliere **Esegui come amministratore**.
3. In tal caso, accettare le **Condizioni di licenza**, quindi fare clic su **Avanti**.
4. Selezionare un **durata per la raccolta di metriche**. È consigliabile che lo strumento venga eseguito durante l'orario di produzione per garantire la raccolta dei dati più rappresentativi. La durata per la raccolta di metriche suggerita è 30 minuti. Se si sta cercando solo di convalidare la connettività di rete, è possibile scegliere un minuto come durata.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

5. Specificare i **Dettagli sito primario**, come illustrato, quindi fare clic su **Avanti**.

	Per un host autonomo, immettere il nome del server o il nome FQDN.

	Se l'host primario fa parte di un cluster, è possibile immettere il nome completo di uno o più delle seguenti operazioni:

	a. Punto di accesso Client gestore di Replica Hyper-V (CAP)

	b. Nome del cluster

	c. Qualsiasi nodo del cluster.

  ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)


6. Immettere i **Dettagli del sito di Replica** (replica solo da sito locale a sito locale)

  Se si desidera abilitare la replica in Azure o non è stato preparato un host Hyper-V o cluster come server di ripristino (come descritto nel passaggio 2), ignorare i test che includono il sito di replica.

  Specificare il **sito di Replica** dettagli e quindi fare clic su **Avanti**.

i. Per un host autonomo, immettere il nome del server o il nome FQDN.

ii. Se l'host primario fa parte di un cluster, è possibile immettere il nome completo di uno o più delle seguenti operazioni:

a. Punto di accesso Client gestore di Replica Hyper-V (CAP)

b. Nome del cluster

c. Qualsiasi nodo del cluster.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

7. Ignorare i test che coinvolgono il **sito di Replica estesa**. Non sono supportate dal ripristino automatico di sistema.
8. Selezionare le macchine virtuali Lo strumento si connette al cluster o ai server autonomi in **Dettagli sito primario** ed enumera le macchine virtuali in esecuzione. Selezionare le macchine virtuali e dischi virtuali per cui si desidera raccogliere le metriche.

Le macchine virtuali seguenti non verranno enumerate o illustrate:

- Macchine virtuali che sono già state abilitate per la replica
- Macchine virtuali che non sono in esecuzione

9. Immettere **Informazioni di rete** (ciò è applicabile solo al sito locale di replica, a siti locali e quando vengono forniti i dettali del sito di replica).

Specificare le informazioni di rete richieste, quindi fare clic su **Avanti**.

- Larghezza di banda WAN stimata
- Certificato da utilizzare per l'autenticazione (facoltativo): se si prevede di utilizzare l'autenticazione basata su certificato, è necessario fornire i certificati necessari in questa pagina.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Nella serie successiva di schermate, fare clic su **Avanti** per avviare lo strumento.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)  

11. Quando è terminata l'esecuzione, fare clic su **Visualizza report**.

    Percorso report predefinito:

    %systemdrive%\\Users\\Public\\Documents\\Capacity Planner

    Percorso dei log;

    %systemdrive%\\Users\\Public\\Documents\\CapacityPlanner

## Passaggio 4: Interpretazione dei risultati
Poiché non sono rilevanti per questo scenario, è possibile ignorare le metriche che non sono elencate in uno dei due scenari seguenti.

### Replica da sito locale a sito locale
  - Impatto della replica nella capacità di elaborazione di memoria dell'host primari.
  - Impatto della replica sullo spazio su disco di archiviazione dell'host di ripristino primario, in IOPS
  - Larghezza di banda totale necessaria per la replica delta (Mbps)
  - Larghezza di banda di rete osservata tra l'host primario e l'host di ripristino (Mbps)
  - Suggerimenti per il numero ideale di trasferimenti paralleli attivi tra due host/cluster

### Sito locale per la replica di Azure
  - Impatto della replica nella capacità di elaborazione di memoria dell'host primari.
  - Impatto della replica sullo spazio su disco di archiviazione dell'host primario, in IOPS
  - Larghezza di banda totale necessaria per la replica delta (Mbps)

Nella pagina di download sono disponibili informazioni più dettagliate su [Capacity Planner per Replica Hyper-V](http://go.microsoft.com/?linkid=9876170).

## Altre risorse:
Le risorse seguenti consentono di pianificarne capacità per la Replica Hyper-V:

- [Aggiornamento di Capacity Planner per Replica Hyper-V](http://go.microsoft.com/fwlink/?LinkId=510891): leggere questo post di blog per una panoramica su questo nuovo strumento.

- [Capacity Planner per Replica Hyper-V](http://go.microsoft.com/fwlink/?LinkId=510892): è possibile scaricare la versione più recente dello strumento.

- [Laboratorio pratico guidato](http://go.microsoft.com/fwlink/?LinkId=510893): eccezionale procedura dettagliata sulla pianificazione dellla capacità con lo strumento disponibile nel blog TechNet di Keith Mayer.

- In questo argomento viene illustrato il test dell'[impatto sulle prestazioni della replica utilizzando Azure Site Recovery in una distribuzione da locale a locale](https://msdn.microsoft.com/library/azure/dn760892.aspx).


## Passaggi successivi

Per avviare la distribuzione  

- [Configurare la protezione tra un sito VMM locale e Azure](site-recovery-vmm-to-azure.md)
- [Configurare la protezione tra un sito Hyper-V locale e Azure](site-recovery-hyper-v-site-to-azure)
- [Configurare la protezione tra due siti VMM locali](site-recovery-vmm-to-vmm)
- [Configurare la protezione tra due siti VMM locali con SAN](site-recovery-vmm-san)
- [Configurare la protezione con un singolo server VMM](site-recovery-single-vmm)
 

<!---HONumber=August15_HO6-->