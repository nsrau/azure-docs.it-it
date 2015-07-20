<properties 
	pageTitle="Carico di lavoro di Farm intranet di SharePoint Fase 4: Configurare i server SharePoint" 
	description="In questa fase della distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità di SQL Server AlwaysOn in servizi di infrastruttura di Azure si creano le macchine virtuali del server SharePoint e una nuova farm SharePoint." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Carico di lavoro di Farm intranet di SharePoint Fase 4: Configurare i server SharePoint

In questa fase della distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità di SQL Server AlwaysOn in servizi di infrastruttura di Azure, è necessario compilare i livelli applicazione e web della farm di SharePoint e creare la farm con la configurazione guidata SharePoint.

È necessario completare questa fase prima di passare alla [Fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md). Per conoscere tutte le fasi, vedere [Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Creare le macchine virtuali del server SharePoint in Azure

Esistono quattro macchine virtuali del server SharePoint. Due macchine virtuali di SharePoint Server sono per i server web front-end e due per l'amministrazione e l'hosting di applicazioni SharePoint. Due server SharePoint in ogni livello garantiscono un'elevata disponibilità.

Utilizzare il seguente blocco di comandi di PowerShell per creare le macchine virtuali per i quattro server SharePoint. Specificare i valori per le variabili, rimuovendo i caratteri < and >. Si noti che in questo set di comandi di PowerShell vengono utilizzati i valori delle tabelle seguenti:

- Tabella M, per le macchine virtuali
- Tabella V, per le impostazioni di rete virtuale
- Tabella S, per la subnet
- Tabella A, per i set di disponibilità
- Tabella C, per i servizi cloud

È importante ricordare che la tabella M è stata definita nella [Fase 2: configurare i controller di dominio](virtual-machines-workload-intranet-sharepoint-phase2.md) mentre le tabelle V, S, A e C nella [Fase 1: configurare Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Una volta specificati tutti i valori appropriati, eseguire il blocco risultante al prompt dei comandi di Azure PowerShell.

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

Utilizzare la procedura [Accesso a una macchina virtuale con una connessione Desktop remoto](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) quattro volte, una volta per ogni server SharePoint, per accedere utilizzando le credenziali dell’account [Domain]\\sp_farm_db create nella [Fase 2: Configurare i controller di dominio](virtual-machines-workload-intranet-sharepoint-phase2.md).

Utilizzare la procedura [Per verificare la connettività](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) quattro volte, una per ogni server SharePoint, per testare la connettività ai percorsi nella rete dell'organizzazione.

## Configurare la farm di SharePoint

Utilizzare questi passaggi per configurare il primo server SharePoint nella farm.

1.	Dal desktop del primo server applicazioni SharePoint, fare doppio clic su **Configurazione guidata Prodotti SharePoint 2013**. Quando viene richiesto di consentire al programma di apportare modifiche al computer, fare clic su **Sì**.
2.	Nella pagina Prodotti SharePoint, fare clic su**Avanti**.
3.	Verrà visualizzata la finestra di dialogo **Configurazione guidata Prodotti SharePoint**, con un avviso indicante che i servizi (ad esempio IIS) verranno riavviati o reimpostati. Fare clic su **Sì**.
4.	Nella pagina Connessione a una server farm, selezionare **Crea una nuova server farm** e fare clic su **Avanti**.
5.	Nella pagina Imposta server database di configurazione:
- In **Server database**, digitare il nome del server SQL primario. 
- In **Nome utente**, digitare [Domain]**\\sp_farm_db** (creato nella [Fase 2: Configurare i controller di dominio](virtual-machines-workload-intranet-sharepoint-phase2.md)). È importante ricordare che l'account sp_farm_db dispone dei privilegi sysadmin in SQL server. 
- In **Password**, digitare la password dell'account sp_farm_db.
6.	Fare clic su **Avanti**.
7.	Nella pagina Specifica impostazioni di sicurezza della farm, digitare due volte una passphrase. Registrare la passphrase e archiviarla in un luogo sicuro per riferimento futuro. Fare clic su **Avanti**.
8.	Nella pagina Configurazione applicazione Web Amministrazione centrale SharePoint, fare clic su **Avanti**.
9.	Verrà visualizzata la pagina Completamento Configurazione guidata Prodotti SharePoint. Fare clic su **Avanti**.
10.	Verrà visualizzata la pagina Configurazione di Prodotti SharePoint in corso. Attendere il completamento del processo di configurazione, circa 8 minuti.
11.	Una volta configurata la farm, fare clic su **Fine**. Verrà avviato il nuovo sito Web di amministrazione.
12.	Per avviare la configurazione della farm di SharePoint, fare clic su **Avvia la procedura guidata**.

Eseguire la procedura seguente nel secondo server applicazioni di SharePoint e nei due server web front-end.

1.	Dal desktop fare doppio clic su **Configurazione guidata Prodotti SharePoint 2013**. Quando viene richiesto di consentire al programma di apportare modifiche al computer, fare clic su **Sì**.
2.	Nella pagina Prodotti SharePoint, fare clic su**Avanti**.
3.	Verrà visualizzata la finestra di dialogo Configurazione guidata Prodotti SharePoint, con un avviso indicante che i servizi (ad esempio IIS) verranno riavviati o reimpostati. Fare clic su **Sì**.
4.	Nella pagina Connessione a una server farm, fare clic su **Connetti a una server farm esistente** e selezionare **Avanti**.
5.	Nella pagina Imposta server database di configurazione, digitare il nome del server SQL primario in **Server database**, quindi fare clic su **Recupera nomi database**. 
6.	Fare clic su **SharePoint_Config** nell’elenco di nomi di database, quindi fare clic su **Avanti**. 
7.	Nella pagina Specifica impostazioni di sicurezza della farm digitare la passphrase della procedura precedente. Fare clic su **Avanti**.
8.	Verrà visualizzata la pagina Completamento Configurazione guidata Prodotti SharePoint. Fare clic su **Avanti**.
9.	Nella pagina Configurazione completata, fare clic su **Fine**. 

Quando in SharePoint viene creata la farm, viene configurato un set di account di accesso server nella macchina virtuale SQL Server primario. SQL Server 2012 introduce il concetto di utenti con password per i database indipendenti. Il database stesso archivia tutti i metadati del database e le informazioni degli utenti, e non è necessario che un utente definito in questo database abbia un account di accesso corrispondente. Le informazioni contenute in questo database vengono replicate dal gruppo di disponibilità e sono disponibili dopo un failover. Per ulteriori informazioni, vedere [Database indipendenti](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Tuttavia, per impostazione predefinita i database di SharePoint non sono database indipendenti. Pertanto, sarà necessario configurare manualmente il server SQL secondario in modo che abbia lo stesso set di account di accesso per gli account farm di SharePoint server SQL primario. Connettendosi a entrambi i server allo stesso tempo, è possibile eseguire la sincronizzazione da SQL Server Management Studio.

Al termine di questa installazione iniziale, sono disponibili ulteriori opzioni di configurazione per le funzionalità della farm di SharePoint. Per ulteriori informazioni, vedere [Pianificazione per SharePoint 2013 nei servizi infrastruttura di Azure](http://msdn.microsoft.com/library/dn275958.aspx)

## Configurare il bilanciamento del carico interno

È possibile configurare il bilanciamento del carico interno in modo che il traffico dei client alla farm di SharePoint venga distribuito uniformemente ai due server web front-end. A tal fine è necessario specificare un’istanza di bilanciamento del carico interno con un nome e un proprio indirizzo IP, assegnato dallo spazio degli indirizzi di subnet. Per stabilire se un indirizzo IP scelto per il bilanciamento del carico interno è disponibile, utilizzare i seguenti comandi di Azure PowerShell:

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

Se il campo **IsAvailable** nella visualizzazione del comando **Test-AzureStaticVNetIP** è **True**, è possibile utilizzare l'indirizzo IP.

Eseguire questo set di comandi nel prompt dei comandi di Azure PowerShell nel computer locale:

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
	
	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.
	
	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
	
	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

Successivamente, aggiungere un record di indirizzo DNS all'infrastruttura DNS dell'organizzazione che risolve il nome di dominio completo della farm di SharePoint (ad esempio sp.corp.contoso.com) nell'indirizzo IP assegnato all'istanza del servizio di bilanciamento del carico interno (il valore di **$IP** nel blocco di comandi PowerShell di Azure precedente).

Questa è la configurazione risultante dal corretto completamento di questa fase.

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Passaggi successivi

Per continuare con la configurazione di questo carico di lavoro, passare a [Fase 5: Creare il gruppo di disponibilità e aggiungere i database SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md).

## Risorse aggiuntive

[Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

[SharePoint con infografica SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->