<properties 
	pageTitle="Applicazione line-of-business fase 3 | Microsoft Azure" 
	description="Creare i computer e il cluster SQL Server e abilitare i gruppi di disponibilità nella fase 3 dell'applicazione line-of-business di Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Carico di lavoro dell'applicazione line-of-business - Fase 3: Configurare l'infrastruttura di SQL Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


In questa fase della distribuzione di un'applicazione line-of-business a disponibilità elevata in servizi di infrastruttura di Azure vengono configurati i due computer che eseguono SQL Server e il computer del nodo di maggioranza cluster, che vengono quindi combinati in un cluster Windows Server.

È necessario completare questa fase prima di passare alla [Fase 4](virtual-machines-workload-high-availability-LOB-application-phase4.md). Per informazioni su tutte le fasi, vedere [Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

> [AZURE.NOTE]In queste istruzioni viene usata un'immagine di SQL Server della raccolta immagini di Azure e vengono addebitati i costi per l'utilizzo della licenza di SQL Server. È inoltre possibile creare macchine virtuali in Azure e installare licenze di SQL Server, ma è necessario disporre di Software Assurance e della mobilità delle licenze per utilizzare la licenza di SQL Server in una macchina virtuale, compresa una macchina virtuale di Azure. Per ulteriori informazioni sull'installazione di SQL Server in una macchina virtuale, vedere [Installazione per SQL Server](https://msdn.microsoft.com/library/bb500469.aspx).

## Creare le macchine virtuali del cluster SQL Server in Azure

Sono presenti due macchine virtuali che eseguono SQL Server. Una macchina virtuale contiene la replica primaria del database di un gruppo di disponibilità, mentre l'altra contiene la replica secondaria (backup). Il backup viene usato per supportare la disponibilità elevata. Un'altra macchina virtuale è per il nodo di maggioranza cluster.

Usare il seguente blocco di comandi di PowerShell per creare le macchine virtuali per i tre server. Specificare i valori per le variabili, rimuovendo i caratteri < and >. Si noti che in questo set di comandi di PowerShell vengono usati i valori delle tabelle seguenti:

- Tabella M, per le macchine virtuali
- Tabella V, per le impostazioni di rete virtuale
- Tabella S, per la subnet
- Tabella ST, per gli account di archiviazione
- Tabella A, per i set di disponibilità

È importante ricordare che la Tabella M è stata definita nella [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) e le Tabelle V, S, ST e A sono state definite nella [Fase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

> [AZURE.NOTE]Questo articolo contiene comandi per le versioni di Azure PowerShell fino alle versione 1.0.0 *esclusa* e versioni successive. È possibile controllare la versione di Azure PowerShell con il comando **Get-Module azure | format-table version**. I blocchi di comando di Azure PowerShell in questo articolo sono ora testati e aggiornati per supportare i nuovi cmdlet nelle versioni di Azure PowerShell 1.0.0 e versioni successive. Grazie per la pazienza dimostrata.

Dopo aver specificato tutti i valori appropriati, eseguire il blocco risultante al prompt di Azure PowerShell.

	# Set up subscription and key variables
	$subscr="<name of the Azure subscription>"
	Set-AzureSubscription -SubscriptionName $subscr
	Switch-AzureMode AzureResourceManager
	$rgName="<your resource group name>"
	$locName="<Azure location of your resource group>"
	# Change to the premium storage account
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 2 – Availability set name column>"
	
	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-SQLDataDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first SQL Server computer." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second SQL Server virtual machine
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Change to the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"
	
	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the cluster majority node server." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Poiché queste macchine virtuali sono per un'applicazione intranet, non sono assegnate a un indirizzo IP pubblico o a un'etichetta di nome di dominio DNS ed esposti in Internet. Tuttavia, questo significa anche che non è possibile connettersi a esse dal portale di anteprima di Azure. Il pulsante **Connetti** non è disponibile quando si visualizzano le proprietà della macchina virtuale. Utilizzare l'accessorio connessione Desktop remoto o un altro strumento di Desktop remoto per connettersi alla macchina virtuale utilizzando l’indirizzo IP privato o il nome DNS di intranet.

## Configurare i computer che eseguono SQL Server

Per ogni macchina virtuale che esegue SQL Server usare il client desktop remoto preferito e creare una connessione Desktop remoto. Usare il nome computer o il nome DNS della Intranet e le credenziali dell'account amministratore locale.

Aggiungere ogni macchina virtuale che esegue SQL Server al dominio AD DS appropriato eseguendo i comandi seguenti al prompt di Windows PowerShell.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Si noti che è necessario fornire le credenziali dell'account di dominio dopo aver immesso il comando Add-Computer.

Dopo il riavvio per riconnettersi alle macchine virtuali usare un account con privilegi di amministratore locale.


Eseguire la procedura seguente due volte, una per ogni macchina virtuale che esegue SQL Server, per aggiungere il disco dati aggiuntivo e creare cartelle nel nuovo volume.

### Per inizializzare un disco vuoto e aggiungere cartelle

1. Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2. Nel riquadro del contenuto, nel gruppo **Dischi**, fare clic sul disco **2** (con la **Partizione** impostata su **Sconosciuto**).
3. Fare clic su **Attività**, quindi su **Nuovo volume**.
4. Nella pagina Operazioni preliminari della creazione guidata nuovo volume fare clic su **Avanti**.
5. Nella pagina Selezionare il server e il disco fare clic su **Disco 2**, quindi fare clic su **Avanti**. Quando richiesto, fare clic su **OK**.
6. Nella pagina Specifica dimensioni del volume fare clic su **Avanti**.
7. Nella pagina Assegnare a una lettera di unità o cartella fare clic su **Avanti**.
8. Nella pagina Selezionare le impostazioni del file system fare clic su **Avanti**.
9. Nella pagina Conferma selezioni, fare clic su **Crea**.
10. Al termine, fare clic su **Chiudi**.
11. Eseguire i comandi seguenti da un prompt di Windows PowerShell:
	- md f:\\Data
	- md f:\\Log
	- md f:\\Backup

Usare due volte la procedura seguente, una per ogni macchina virtuale che esegue SQL Server, per configurarle per l'uso dell'unità F: per i nuovi database e per account e autorizzazioni.

1. Nella schermata Start digitare **SQL Studio**, quindi fare clic su **SQL Server 2014 Management Studio**.
2. In **Connetti al Server**, fare clic su **Connetti**.
3. Nel riquadro sinistro, fare clic con il pulsante destro del mouse sul nodo principale (l'istanza predefinita denominata dopo il computer), quindi fare clic su **Proprietà**.
4.	In **Proprietà Server** fare clic su **Impostazioni database**.
5.	In **Percorsi predefiniti database**, impostare i valori seguenti: 
	- Per **Dati**, impostare il percorso **F:\\Data**.
	- Per **Log**, impostare il percorso **F:\\Log**.
	- Per **Backup**, impostare il percorso **F:\\Backup**.
	- Solo i nuovi database useranno questi percorsi.
6.	Fare clic su **OK** per chiudere la finestra.
7.	Nel riquadro sinistro, espandere la **cartella Sicurezza**.
8.	Fare clic con il pulsante destro del mouse su **Account di accesso**, quindi scegliere **Nuovo account di accesso**.
9.	In **Nome account di accesso** digitare *dominio*\\sqladmin (in cui *dominio* è il nome del dominio in cui è stato creato l'account sqladmin nella [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md)). 
10.	In **Selezione pagina**, fare clic su **Ruoli server**, quindi fare clic **sysadmin** e infine su **OK**.
11.	Chiudere SQL Server 2014 Management Studio.

Usare la procedura seguente due volte, una volta per ogni istanza di SQL Server, per consentire le connessioni Desktop remoto tramite l'account sqladmin.

1.	Nella schermata Start, fare doppio clic su **Computer**, quindi fare clic su **Proprietà**.
2.	Nella finestra **Sistem** fare clic su **Impostazioni di connessione remota**.
3.	Nella sezione **Desktop remoto** fare clic su **Selezione utenti**, quindi su **Aggiungi**.
4.	In **Immettere i nomi di oggetto da selezionare** digitare [domain]**\\sp\_farm\_db**, quindi fare clic su **OK** per tre volte.

Il servizio SQL Server richiede una porta usata dai client per accedere al server di database. È necessario inoltre che le porte per la connessione con SQL Server Management Studio e la gestione del gruppo di disponibilità elevata. A questo punto, eseguire per due volte il comando seguente a un prompt di Windows PowerShell con privilegi di amministratore (una volta per ogni macchina virtuale di SQL Server) per aggiungere una regola firewall che consenta questo tipo di traffico in ingresso.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 4234, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Per ognuna delle macchine virtuali SQL Server disconnettersi come amministratore locale.

Per informazioni sull'ottimizzazione delle prestazioni di SQL Server in Azure, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure￼￼￼￼￼￼￼.￼](virtual-machines-sql-server-performance-best-practices.md). È anche possibile disabilitare l'archiviazione con ridondanza geografica per l'account di archiviazione dell'applicazione line-of-business e usare gli spazi di archiviazione per ottimizzare gli IOPS.

## Configurare il server del nodo di maggioranza cluster

Usare il client desktop remoto preferito e creare una connessione Desktop remoto alla macchina virtuale del server del nodo di maggioranza cluster. Usare il nome computer o il nome DNS della Intranet e le credenziali dell'account amministratore locale.

Per aggiungere il server del nodo di maggioranza cluster al dominio AD DS appropriato, usare i comandi seguenti al prompt di Windows PowerShell.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Si noti che è necessario fornire le credenziali dell'account di dominio quando si esegue il comando **Add-Computer**.

Dopo il riavvio per riconnettersi usare un account con privilegi di amministratore locale.

## Creare il cluster della funzionalità di clustering di failover di Windows Server

I gruppi di disponibilità AlwaysOn di SQL Server si basano sulla funzionalità di clustering di failover di Windows Server. La funzionalità consente a più macchine di partecipare come gruppo in un cluster. Quando un computer presenta un guasto, una seconda macchina è pronta a sostituirla. Pertanto la prima attività consiste nell'abilitare la funzionalità di clustering di failover in tutti i computer partecipanti, che includono:

- Macchina virtuale primaria che esegue SQL Server
- Macchina virtuale secondaria che esegue SQL Server
- Nodo di maggioranza cluster

Con il cluster di failover sono necessarie almeno tre macchine virtuali. Due macchine ospitano SQL Server, in cui la macchina virtuale secondaria è una replica secondaria asincrona, in modo da evitare completamente perdite di dati in caso di malfunzionamento della macchina primaria. Nella terza macchina virtuale non è necessario ospitare SQL Server. Il nodo di maggioranza cluster funziona come un quorum di controllo nella funzionalità clustering di failover di Windows Server. Poiché il cluster con la funzionalità cluster di failover di Windows Server si basa su un quorum per monitorare l'integrità, deve sempre essere una maggioranza per assicurarsi che il tale cluster sia online. Se solo due macchine si trovano in un cluster e uno ha esito negativo, potrebbe non esserci maggioranza quando solo uno di due ha esito negativo. Per ulteriori informazioni, vedere [Modalità Quorum della funzionalità clustering di Windows Server e configurazione del voto (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Per le macchine virtuali di SQL Server e per il nodo di maggioranza cluster eseguire il comando seguente a un prompt di Windows PowerShell con privilegi di amministratore:

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

A causa di un comportamento corrente non conforme a RFC da parte del DHCP in Azure, la creazione di un cluster di failover di Windows Server può avere esito negativo. Per altre informazioni, cercare "Comportamento del cluster di failover di Windows Server nella rete di Azure" nella disponibilità elevata e nel ripristino di emergenza per SQL Server in macchine virtuali di Azure. Tuttavia, esiste una soluzione alternativa. Usare i passaggi seguenti per testare il cluster:

1.	Accedere alla macchina virtuale SQL Server primaria con l'account sqladmin creato nella [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).
2.	Dalla schermata Start digitare **Failover**, quindi fare clic su **Gestione cluster di failover**.
3.	Nel riquadro sinistro, fare clic con il pulsante destro del mouse su **Gestione cluster di failover**, quindi fare clic su **Crea cluster**.
4.	Nella pagina **Prima di iniziare**, fare clic su **Avanti**.
5.	Nella pagina **Selezione server** digitare il nome del computer SQL Server primario, fare clic su **Aggiungi**, quindi scegliere **Avanti**.
6.	Nella pagina **Avviso di convalida** fare clic su **No. Non è necessario il supporto di Microsoft per il cluster e pertanto non desidero eseguire i test di convalida. Facendo clic su Avanti verrà proseguita la creazione del cluster.**, quindi fare clic su **Avanti**.
7.	ìNella casella di testo **Nome cluster** della pagina **Punto di accesso per l'amministrazione del cluster**, digitare il nome del cluster, quindi fare clic su **Avanti**.
8.	Nella pagina di **Conferma**, fare clic su **Avanti** per iniziare la creazione del cluster. 
9.	Nella pagina **Riepilogo** fare clic su **Fine**.
10.	Nel riquadro sinistro fare clic sul nuovo cluster. Nella sezione **Risorse principali del cluster** del riquadro del contenuto, aprire il nome del cluster del server. La risorsa **Indirizzo IP** verrà visualizzata nello stato **Non riuscito**. Non è possibile connettere la risorsa indirizzo IP perché al cluster è assegnato lo stesso indirizzo IP della macchina. Il risultato è un indirizzo duplicato. 
11.	Fare clic con il pulsante destro del mouse sulla risorsa **Indirizzo IP** non riuscita, quindi fare clic su **Proprietà**.
12.	Nella finestra di dialogo **Proprietà dell'indirizzo IP** fare clic su **Indirizzo IP statico**.
13.	Digitare un indirizzo IP inutilizzato nell'intervallo di indirizzi corrispondente alla subnet in cui si trova il server SQL, quindi fare clic su **OK**.
14.	Fare clic con il pulsante destro del mouse sulla risorsa Indirizzo IP non riuscita, quindi fare clic su **Connetti**. Attendere finché entrambe le risorse non siano online. Quando la risorsa del nome cluster torna online, il controller di dominio viene aggiornato con un nuovo account del computer di Active Directory. L'account di Active Directory viene successivamente usato per eseguire il servizio cluster del gruppo di disponibilità.
15.	Una volta creato l'account di Active Directory, disconnettere il nome del cluster. Fare clic con il pulsante del mouse sul nome del cluster in **Risorse principali del cluster**, quindi fare clic su **Offline**.
16.	Per rimuovere l'indirizzo IP del cluster, fare doppio clic su **Indirizzo IP**, fare clic su **Rimuovi**, quindi fare clic su **Sì** quando richiesto. Non è più possibile connettere la risorsa cluster perché dipende dalla risorsa indirizzo IP. Tuttavia, un gruppo di disponibilità non dipende dall'indirizzo IP o dal nome del cluster per il corretto funzionamento. Pertanto il nome del cluster può rimanere offline.
17.	Per aggiungere i restanti nodi al cluster, fare clic con il pulsante destro del mouse sul nome del cluster nel riquadro sinistro, quindi fare clic su **Aggiungi nodo**.
18.	Nella pagina **Prima di iniziare**, fare clic su **Avanti**. 
19.	Nella pagina **Selezione server** digitare il nome, quindi fare clic su **Aggiungi** per aggiungere il server SQL secondario e il nodo di maggioranza cluster al cluster. Dopo aver aggiunto i due computer, fare clic su **Avanti**. Se non è possibile aggiungere un computer e il messaggio di errore è "Servizio Registro di sistema remoto non è in esecuzione", effettuare le seguenti operazioni. Accedere al computer, aprire lo snap-in Servizi (Services. msc) e abilitare il Registro di sistema remoto. Per ulteriori informazioni, vedere [Impossibile connettersi al Servizio Registro di sistema remoto](http://technet.microsoft.com/library/bb266998.aspx). 
20.	Nella pagina **Avviso di convalida** fare clic su **No. Non è necessario il supporto di Microsoft per il cluster e pertanto non desidero eseguire i test di convalida. Facendo clic su Avanti verrà proseguita la creazione del cluster.**, quindi fare clic su **Avanti**. 
21.	Nella pagina di **Conferma**, fare clic su **Avanti**.
22.	Nella pagina **Riepilogo** fare clic su **Fine**.
23.	Nel riquadro sinistro fare clic su **Nodi**. Verranno visualizzati tutti i tre computer elencati.

## Abilitare Gruppi di disponibilità AlwaysOn in Azure

Il passaggio successivo consiste nell'abilitare i Gruppi di disponibilità AlwaysOn mediante Gestione configurazione SQL Server. Si noti che un gruppo di disponibilità in SQL Server è diverso da un set di disponibilità di Azure. Un gruppo di disponibilità contiene database altamente disponibili e recuperabili. Un set di disponibilità di Azure consente di allocare le macchine virtuali a diversi domini di errore. Per ulteriori informazioni sulle macchine virtuali e sui domini di errore, vedere [Gestione della disponibilità delle macchine virtuali](virtual-machines-manage-availability.md).

Usare questi passaggi per abilitare i gruppi di disponibilità AlwaysOn di SQL Server.

1.	Accedere alla macchina virtuale SQL Server primaria con l'account sqladmin creato nella [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).
2.	Nella schermata Start digitare **Configurazione di SQL Server**, quindi fare clic su **Gestione configurazione SQL Server**.
3.	Nel riquadro sinistro fare clic su **Servizi di SQL Server**.
4.	Nel riquadro del contenuto, fare doppio clic su **SQL Server (MSSQLSERVER)**.
5.	In **Proprietà SQL Server (MSSQLSERVER)**, fare clic sulla scheda **Disponibilità elevata AlwaysOn**, selezionare **Abilita i gruppi di disponibilità AlwaysOn**s fare clic su **Applica**, quindi fare clic su **OK** quando richiesto. Non chiudere ancora la finestra Proprietà. 
6.	Fare clic sulla scheda Macchine virtuali > Gestisci disponibilità, quindi digitare [Domain]**\\sqlservice** in **Nome account**. Digitare la password dell'account sqlservice in **Password** e **Confermare la password**, quindi fare clic su **OK**.
7.	Nella finestra di messaggio, fare clic su **Sì** per riavviare il servizio SQL Server.
8.	Accedere alla macchina virtuale SQL Server secondaria con l'account sqladmin e ripetere i passaggi da 2 a 7. 

In questo diagramma viene visualizzata la configurazione risultante dal corretto completamento di questa fase, con nomi di computer segnaposto.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase3/workload-lobapp-phase3.png)

## Passaggio successivo

Per continuare con la configurazione di questo carico di lavoro, passare a [Fase 4: Configurare i server Web](virtual-machines-workload-high-availability-LOB-application-phase4.md).

## Risorse aggiuntive

[Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Progetti dell'architettura per applicazioni line-of-business](http://msdn.microsoft.com/dn630664)

[Configurare un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: farm di SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Oct15_HO3-->