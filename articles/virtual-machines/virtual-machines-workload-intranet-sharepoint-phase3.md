<properties
	pageTitle="Farm di SharePoint Server 2013 fase 3 | Microsoft Azure"
	description="Creare i computer e il cluster SQL Server e abilitare i gruppi di disponibilità nella fase 3 della farm di SharePoint Server 2013 in Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Carico di lavoro di Farm Intranet di SharePoint Fase3: configurare l'infrastruttura di SQL Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione delle risorse con il modello di distribuzione classica.

In questa fase della distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità SQL Server AlwaysOn in servizi di infrastruttura di Azure, vengono creati e configurati i due computer SQL Server e il computer del nodo di maggioranza cluster, quindi vengono combinati in un cluster Windows Server.

È necessario completare questa fase prima di passare alla [Fase 4](virtual-machines-workload-intranet-sharepoint-phase4.md). Per conoscere tutte le fasi, vedere [Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

> [AZURE.NOTE]In queste istruzioni viene usata un'immagine di SQL Server della raccolta immagini di Azure e vengono addebitati i costi per l'utilizzo della licenza di SQL Server. È inoltre possibile creare macchine virtuali in Azure e installare licenze di SQL Server, ma è necessario disporre di Software Assurance e della mobilità delle licenze per utilizzare la licenza di SQL Server in una macchina virtuale, compresa una macchina virtuale di Azure. Per ulteriori informazioni sull'installazione di SQL Server in una macchina virtuale, vedere [Installazione per SQL Server](https://msdn.microsoft.com/library/bb500469.aspx).

## Creare le macchine virtuali del cluster SQL Server in Azure

Sono presenti due macchine virtuali SQL server. Un server SQL server contiene la replica di database primario di un gruppo di disponibilità. Il secondo server SQL contiene la replica di backup secondaria. Il backup viene fornito per garantire disponibilità elevata. Un'altra macchina virtuale è per la maggioranza dei nodi del cluster.

Usare il seguente blocco di comandi di PowerShell per creare le macchine virtuali per i tre server. Specificare i valori per le variabili, rimuovendo i caratteri < and >. Si noti che in questo set di comandi di PowerShell vengono utilizzati i valori delle tabelle seguenti:

- Tabella M, per le macchine virtuali.
- Tabella V, per le impostazioni della rete virtuale.
- Tabella S, per la subnet.
- Tabella A, per i set di disponibilità.
- Tabella C, per i servizi cloud.

È importante ricordare che la tabella M è stata definita nella [Fase 2: configurare i controller di dominio](virtual-machines-workload-intranet-sharepoint-phase2.md) mentre le tabelle V, S, A e C nella [Fase 1: configurare Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Una volta specificati tutti i valori appropriati, eseguire il blocco risultante al prompt dei comandi di Azure PowerShell.

	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 2 – Availability set name column>"

	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Enterprise on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server computer."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 2 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SQL server
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server computer."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the cluster majority node server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Configurare i computer SQL Server

Per ogni SQL server, eseguire le operazioni seguenti:

1. Utilizzare la [procedura di accesso a una macchina virtuale con una procedura di connessione Desktop remoto](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) per accedere utilizzando le credenziali dell'account dell'amministratore locale.

2. Per aggiungere il disco dati aggiuntivo utilizzare la [procedura per inizializzare un disco vuoto](virtual-machines-workload-intranet-sharepoint-phase2.md#datadisk) per due volte (una volta per ogni computer SQL server).

3. Eseguire i comandi seguenti da un prompt dei comandi di Windows PowerShell.

		md f:\Data
		md f:\Log
		md f:\Backup

4. Utilizzare la [procedura per testare la connettività](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) per verificare la connettività in percorsi di rete dell'organizzazione. Questa procedura garantisce il corretto funzionamento della risoluzione dei nomi DNS (ovvero, garantisce che la macchina virtuale sia configurata correttamente con i server DNS nella rete virtuale) e che i pacchetti possano essere inviati da e verso la rete virtuale cross-premise.

Per configurare SQL server per l'utilizzo dell'unità F: per i nuovi database, gli account e le autorizzazioni, utilizzare la seguente procedura per due volte (una volta per ogni computer SQL server).


1.	Nella schermata Start digitare **SQL Studio**, quindi fare clic su **SQL Server 2014 Management Studio**.
2.	In **Connetti al Server**, fare clic su **Connetti**.
3.	Nel riquadro sinistro, fare clic con il pulsante destro del mouse sul nodo principale (l'istanza predefinita denominata dopo il computer), quindi fare clic su **Proprietà**.
4.	In **Proprietà Server** fare clic su **Impostazioni database**.
5.	In **Percorsi predefiniti database**, impostare i valori seguenti:
- Per **Dati**, impostare il percorso **F:\\Data**.
- Per **Log**, impostare il percorso **F:\\Log**.
- Per **Backup**, impostare il percorso **F:\\Backup**.
- Solo i nuovi database useranno questi percorsi.
6.	Fare clic su **OK** per chiudere la finestra.
7.	Nel riquadro sinistro, espandere la **cartella Sicurezza**.
8.	Fare clic con il pulsante destro del mouse su **Account di accesso**, quindi scegliere **Nuovo account di accesso**.
9.	In **Nome account di accesso**, digitare *dominio*\\sp\_farm\_db in (in cui *dominio* è il nome del dominio in cui è stato creato l'account sp\_farm\_db).
10.	In **Selezione pagina**, fare clic su **Ruoli server**, quindi fare clic **sysadmin** e infine su **OK**.
11.	Chiudere SQL Server 2014 Management Studio.

Utilizzare la seguente procedura due volte (una volta per ogni computer SQL server) per consentire le connessioni Desktop remoto tramite l'account sp\_farm\_db.

1.	Nella schermata Start, fare doppio clic su **Computer**, quindi fare clic su **Proprietà**.
2.	Nella finestra **Sistem** fare clic su **Impostazioni di connessione remota**.
3.	Nella sezione **Desktop remoto** fare clic su **Selezione utenti**, quindi su **Aggiungi**.
4.	In **Immettere i nomi di oggetto da selezionare**, digitare [domain]**\\sp\_farm\_db**, quindi fare clic su **OK** per tre volte.

SQL Server richiede una porta utilizzata dai client per accedere al server di database. È necessario inoltre che le porte per la connessione con SQL Server Management Studio e la gestione del gruppo di disponibilità elevata. Successivamente, eseguire il comando seguente a un prompt di Windows PowerShell a livello di amministratore per due volte (una volta per ogni SQL server) per aggiungere una regola firewall che consenta il traffico in ingresso a SQL server.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 4234, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Per ognuna delle macchine virtuali SQL server, disconnettersi come amministratore locale.

Per informazioni sull'ottimizzazione delle prestazioni di SQL Server in Azure, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-performance-best-practices.md). È inoltre possibile disabilitare l'archiviazione con ridondanza geografica per l'account di archiviazione farm di SharePoint e utilizzare gli spazi di archiviazione per ottimizzare gli IOPS.

## Configurare il server di un nodo di maggioranza cluster

Utilizzare procedura di [Accesso a una macchina virtuale con una connessione Desktop remoto](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) per accedere utilizzando le credenziali dell'account di dominio.

Sun nodo di maggioranza cluster utilizzare la [procedura per testare la connettività](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) per verificare la connettività in percorsi di rete dell'organizzazione.

## Creare il cluster di Windows Server.

I gruppi di disponibilità AlwaysOn di SQL Server si basano sulla funzionalità di clustering di failover di Windows Server. La funzionalità consente a più macchine di partecipare come gruppo in un cluster. Quando un computer presenta un guasto, una seconda macchina è pronta a sostituirla. Pertanto la prima attività consiste nell'abilitare la funzionalità di clustering di failover in tutti i computer partecipanti, che includono:

- Il server SQL principale
- Il server SQL secondario
- Server di un nodo di maggioranza cluster

Il cluster di failover richiede almeno tre macchine virtuali. Due macchine ospitano SQL Server. La seconda macchina virtuale con SQL Server è una replica secondaria asincrona, che garantisce di evitare completamente la perdita di dati se la macchina principale presenta un guasto. La terza macchina non richiede di ospitare SQL Server. Il nodo di maggioranza cluster funziona come un quorum di controllo nella funzionalità clustering di failover di Windows Server. Poiché il cluster con la funzionalità cluster di failover di Windows Server si basa su un quorum per monitorare l'integrità, deve sempre essere una maggioranza per assicurarsi che il tale cluster sia online. Se solo due macchine si trovano in un cluster e uno ha esito negativo, potrebbe non esserci maggioranza quando solo uno di due ha esito negativo. Per ulteriori informazioni, vedere [Modalità Quorum della funzionalità clustering di Windows Server e configurazione del voto (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Per entrambi i computer SQL server e per il nodo di maggioranza cluster, eseguire il comando seguente in un prompt di Windows PowerShell a livello di amministratore.

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

A causa di un comportamento corrente non conforme a RFC da parte del DHCP in Azure, la creazione di un cluster di failover di Windows Server può avere esito negativo. Per altre informazioni, cercare "Comportamento del cluster di failover di Windows Server nella rete di Azure" nella disponibilità elevata e nel ripristino di emergenza per SQL Server in macchine virtuali di Azure. Tuttavia, esiste una soluzione alternativa. Utilizzare i passaggi seguenti per testare il cluster:

1.	Accedere alla macchina virtuale SQL Server primaria con l'account **sp\_install**.
2.	Dalla schermata Start digitare **Failover**, quindi fare clic su **Gestione cluster di failover**.
3.	Nel riquadro sinistro, fare clic con il pulsante destro del mouse su **Gestione cluster di failover**, quindi fare clic su **Crea cluster**.
4.	Nella pagina Prima di iniziare, fare clic su **Avanti**.
5.	Nella pagina Selezione server digitare il nome del computer SQL Server primario, fare clic su **Aggiungi**, quindi scegliere **Avanti**.
6.	Nella pagina di avviso di convalida, fare clic su **No. Non è necessario il supporto di Microsoft per il cluster e pertanto non desidero eseguire i test di convalida. Facendo clic su Avanti verrà proseguita la creazione del cluster **, quindi fare clic su **Avanti**.
7.	Nella casella di testo **Nome cluster** della pagina Punto di accesso per l'amministrazione del cluster, digitare il nome del cluster, quindi fare clic su **Avanti**.
8.	Nella pagina di conferma, fare clic su **Avanti** per iniziare la creazione del cluster.
9.	Nella pagina Riepilogo fare clic su **Fine**.
10.	Nel riquadro sinistro fare clic sul nuovo cluster. Nella sezione **Risorse principali del cluster** del riquadro del contenuto, aprire il nome del cluster del server. La risorsa **Indirizzo IP** verrà visualizzata nello stato **Non riuscito**. Non è possibile connettere la risorsa indirizzo IP perché al cluster è assegnato lo stesso indirizzo IP della macchina. Il risultato è un indirizzo duplicato.
11.	Fare clic con il pulsante destro del mouse sulla risorsa **Indirizzo IP** non riuscita, quindi fare clic su **Proprietà**.
12.	Nella finestra di dialogo **Proprietà dell'indirizzo IP** fare clic su **Indirizzo IP statico**.
13.	Digitare un indirizzo IP inutilizzato nell'intervallo di indirizzi corrispondente alla subnet in cui si trova il server SQL, quindi fare clic su **OK**.
14.	Fare clic con il pulsante destro del mouse sulla risorsa Indirizzo IP non riuscita, quindi fare clic su **Connetti**. Attendere finché entrambe le risorse non siano online. Quando la risorsa del nome cluster torna online, il controller di dominio viene aggiornato con un nuovo account del computer di Active Directory. L'account di Active Directory viene successivamente usato per eseguire il servizio cluster del gruppo di disponibilità.
15.	Una volta creato l'account di Active Directory, disconnettere il nome del cluster. Fare clic con il pulsante del mouse sul nome del cluster in **Risorse principali del cluster**, quindi fare clic su **Offline**.
16.	Per rimuovere l'indirizzo IP del cluster, fare doppio clic su **Indirizzo IP**, fare clic su **Rimuovi**, quindi fare clic su **Sì** quando richiesto. Non è più possibile connettere la risorsa cluster perché dipende dalla risorsa indirizzo IP. Tuttavia, un gruppo di disponibilità non dipende dall'indirizzo IP o dal nome del cluster per il corretto funzionamento. Pertanto il nome del cluster può rimanere offline.
17.	Per aggiungere i restanti nodi al cluster, fare clic con il pulsante destro del mouse sul nome del cluster nel riquadro sinistro, quindi fare clic su **Aggiungi nodo**.
18.	Nella pagina Prima di iniziare, fare clic su **Avanti**.
19.	Nella pagina Selezione server digitare il nome, quindi fare clic su **Aggiungi** per aggiungere il server SQL secondario e il nodo di maggioranza cluster al cluster. Dopo aver aggiunto i due computer, fare clic su **Avanti**. Se non è possibile aggiungere un computer e il messaggio di errore è "Servizio Registro di sistema remoto non è in esecuzione", effettuare le seguenti operazioni. Accedere al computer, aprire lo snap-in Servizi (Services. msc) e abilitare il Registro di sistema remoto. Per ulteriori informazioni, vedere [Impossibile connettersi al Servizio Registro di sistema remoto](http://technet.microsoft.com/library/bb266998.aspx).
20.	Nella pagina di avviso di convalida, fare clic su **No. Non è necessario il supporto di Microsoft per il cluster e pertanto non desidero eseguire i test di convalida. Facendo clic su Avanti verrà proseguita la creazione del cluster **, quindi fare clic su **Avanti**.
21.	Nella pagina di conferma, fare clic su **Avanti**.
22.	Nella pagina Riepilogo fare clic su **Fine**.
23.	Nel riquadro sinistro fare clic su **Nodi**. Verranno visualizzati tutti i tre computer elencati.

## Abilitare Gruppi di disponibilità AlwaysOn in Azure

Il passaggio successivo consiste nell'abilitare i Gruppi di disponibilità AlwaysOn mediante Gestione configurazione SQL Server. Si noti che un gruppo di disponibilità in SQL Server è diverso da un set di disponibilità di Azure. Un gruppo di disponibilità contiene database altamente disponibili e recuperabili. Un set di disponibilità di Azure consente di allocare le macchine virtuali a diversi domini di errore. Per ulteriori informazioni sulle macchine virtuali e sui domini di errore, vedere [Gestione della disponibilità delle macchine virtuali](virtual-machines-manage-availability.md).

Utilizzare questi passaggi per abilitare i gruppi di disponibilità AlwaysOn di SQL Server.

1.	Accedere al server SQL primario utilizzando l'account**sp\_farm\_db** o un altro account che dispone del ruolo server sysadmin in SQL Server.
2.	Nella schermata Start digitare **Configurazione di SQL Server**, quindi fare clic su **Gestione configurazione SQL Server**.
3.	Nel riquadro sinistro fare clic su **Servizi di SQL Server**.
4.	Nel riquadro del contenuto, fare doppio clic su **SQL Server (MSSQLSERVER)**.
5.	In **Proprietà SQL Server (MSSQLSERVER)**, fare clic sulla scheda **Disponibilità elevata AlwaysOn**, selezionare **Abilita i gruppi di disponibilità AlwaysOn**s fare clic su **Applica**, quindi fare clic su **OK** quando richiesto. Non chiudere ancora la finestra Proprietà.
6.	Fare clic sulla scheda Macchine virtuali > Gestisci disponibilità, quindi digitare [Domain]**\\sqlservice** in **Nome account**. Digitare la password dell'account sqlservice in **Password** e **Confermare la password**, quindi fare clic su **OK**.
7.	Nella finestra di messaggio, fare clic su **Sì** per riavviare il servizio SQL Server.
8.	Accedere al server SQL secondario e ripetere questo processo.

Nel diagramma successivo viene visualizzata la configurazione risultante dal corretto completamento di questa fase, con nomi di computer segnaposto.

![](./media/virtual-machines-workload-intranet-sharepoint-phase3/workload-spsqlao_03.png)

## Passaggio successivo

Per avviare la configurazione di questo carico di lavoro, andare alla [Fase 4: Configurare server SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md).

## Risorse aggiuntive

[Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

[SharePoint con infografica SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: applicazione line-of-business a disponibilità elevata](virtual-machines-workload-high-availability-lob-application.md)

<!----HONumber=Sept15_HO3-->