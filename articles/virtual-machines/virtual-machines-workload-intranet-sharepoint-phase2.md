<properties
	pageTitle="Farm di SharePoint Server 2013 fase 2 | Microsoft Azure"
	description="Creare e configurare i due controller di dominio di replica di Active Directory nella fase 2 della farm di SharePoint Server 2013 in Azure."
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
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Carico di lavoro di farm intranet di SharePoint Fase 2: Configurare controller di dominio

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Modello di distribuzione di Gestione risorse

In questa fase della distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità SQL Server AlwaysOn in servizi di infrastruttura di Azure, vengono configurati due controller di dominio nella rete virtuale di Azure in Gestione dei servizi. Le richieste Web dei client per le risorse della farm di SharePoint possono quindi essere autenticate nella rete virtuale di Azure, anziché inviare tale traffico di autenticazione attraverso la connessione VPN o Azure ExpressRoute alla rete locale.

È necessario completare questa fase prima di passare alla [Fase 3](virtual-machines-workload-intranet-sharepoint-phase3.md). Per conoscere tutte le fasi, vedere [Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Creare macchine virtuali controller di dominio in Azure

È innanzitutto necessario compilare la colonna **Nome macchina virtuale** della tabella M e modificare le dimensioni delle macchine virtuali secondo necessità nella colonna **Dimensione minima**.

Elemento | Nome macchina virtuale | Immagine della raccolta | Dimensione minima
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primo controller di dominio, nell'esempio DC1) | Windows Server 2012 R2 Datacenter | A2 (Media)
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (secondo controller di dominio, nell'esempio DC2) | Windows Server 2012 R2 Datacenter | A2 (Media)
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primo computer SQL Server, nell'esempio SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (secondo computer SQL Server, nell'esempio SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (nodo di maggioranza per il cluster, nell'esempio MN1) | Windows Server 2012 R2 Datacenter | A1 (Piccola)
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primo server applicazioni SharePoint, nell’esempio APP1) | Versione di valutazione di Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4 (Grandissima)
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (secondo server applicazioni SharePoint, nell’esempio APP2) | Versione di valutazione di Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4 (Grandissima)
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primo server Web SharePoint, nell’esempio WEB1) | Versione di valutazione di Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4 (Grandissima)
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (secondo server Web SharePoint, nell’esempio WEB2) | Versione di valutazione di Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4 (Grandissima)

**Tabella M – Macchine virtuali per la farm intranet di SharePoint 2013 in Azure**

Per un elenco completo delle dimensioni delle macchine virtuali, vedere [Dimensioni per le macchine virtuali](virtual-machines-size-specs.md).

Utilizzare il seguente blocco di comandi di Azure PowerShell per creare le macchine virtuali per i due controller di dominio. Specificare i valori per le variabili, rimuovendo i caratteri < and >. In questo set di comandi di Azure PowerShell vengono utilizzati i valori seguenti:

- Tabella M, per le macchine virtuali
- Tabella V, per le impostazioni di rete virtuale
- Tabella S, per la subnet
- Tabella A, per i set di disponibilità
- Tabella C, per i servizi cloud

È importante ricordare che le tabelle V, S, A e C sono state definite nella [Fase 1: Configurare Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Una volta specificati tutti i valori appropriati, eseguire il blocco risultante al prompt dei comandi di Azure PowerShell.

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>

	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Configurare il primo controller di dominio

Accedere al primo computer controller di dominio utilizzando le credenziali dell'account dell'amministratore locale.

### <a id="logon"></a>Per accedere a una macchina virtuale utilizzando una connessione Desktop remoto

1.	Nel riquadro sinistro del portale di Azure classico, fare clic su **Macchine virtuali**.
2.	Per connettersi a una macchina virtuale, fare clic su **Esecuzione** nella colonna **Stato** accanto al relativo nome.
3.	In fondo alla pagina, nella barra dei comandi, fare clic su **Connetti**.
4.	Nel portale di Azure classico viene indicato che il file .rdp viene recuperato. Fare clic su **OK**.
5.	Viene visualizzata la finestra di dialogo del browser in cui viene chiesto se si desidera aprire o salvare ComputerName.rdp da manage.windowsazure.com. Fare clic su **Apri**.
6.	Nella finestra di dialogo **Connessione Desktop remoto**, fare clic su **Connetti**.
7.	Nella finestra di dialogo **Protezione di Windows**, fare clic su **Usa un altro account**.
8.	In **Nome utente**, digitare il nome della macchina virtuale e il nome utente dell'account amministratore locale creato con la macchina virtuale (un account computer locale). Utilizzare il formato seguente: *NomeComputer*\*NomeAccountAmministratoreLocale*
9.	In **Password**, digitare la password per l'account amministratore locale.
10.	Fare clic su **OK**.
11.	Nella finestra di dialogo **Connessione Desktop remoto**, fare clic su **Sì**. Il desktop della nuova macchina virtuale viene visualizzato in una finestra della sessione Desktop remoto.

Successivamente, è necessario aggiungere il disco dati aggiuntivi al primo controller di dominio.

### <a id="datadisk"></a>Per inizializzare un disco vuoto

1.	Nel riquadro sinistro di Server Manager fare clic su **Servizi file e archiviazione**, quindi scegliere **Dischi**.
2.	Nel riquadro del contenuto, nel gruppo **Dischi**, fare clic sul disco **2** (con la **Partizione** impostata su **Sconosciuto**).
3.	Fare clic su **Attività**, quindi su **Nuovo volume**.
4.	Nella pagina **Operazioni preliminari** della creazione guidata nuovo volume fare clic su **Avanti**.
5.	Nella pagina **Selezionare il server e il disco** fare clic su **Disco 2**, quindi fare clic su **Avanti**. Quando richiesto, fare clic su **OK**.
6.	Nella pagina **Specifica dimensioni del volume** fare clic su **Avanti**.
7.	Nella pagina **Assegnare a una lettera di unità o cartella** fare clic su **Avanti**.
8.	Nella pagina **Selezionare le impostazioni del file system** fare clic su **Avanti**.
9.	Nella pagina **Conferma selezioni**, fare clic su **Crea**.
10.	Una volta completata l'inizializzazione, fare clic su **Chiudi**.

Successivamente, verificare la connettività del primo controller di dominio ai percorsi di rete dell'organizzazione.

### <a id="testconn"></a>Per testare la connettività

1.	Dal desktop, aprire un prompt dei comandi di Windows PowerShell.
2.	Utilizzare il comando **ping** per eseguire il ping dei nomi e degli indirizzi IP delle risorse nella rete dell'organizzazione.

Questa procedura garantisce il corretto funzionamento della risoluzione dei nomi DNS (ovvero, garantisce che la macchina virtuale sia configurata correttamente con i server DNS locali) e che i pacchetti possano essere inviati da e verso la rete virtuale cross-premise.

Successivamente, al prompt dei comandi Windows PowerShell nel primo controller di dominio, eseguire i comandi seguenti:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Il computer verrà riavviato.

## Configurare il secondo controller di dominio

Accedere al secondo computer controller di dominio utilizzando le credenziali dell'account dell'amministratore locale. Per le istruzioni, vedere la procedura [Accesso a una macchina virtuale con una connessione Desktop remoto](#logon).

Successivamente, è necessario aggiungere il disco dati aggiuntivi al secondo controller di dominio. Vedere la procedura [Per inizializzare un disco vuoto](#datadisk).

A questo punto, testare la connettività ai percorsi nella rete dell’organizzazione dal secondo controller di dominio. Vedere la procedura [Per verificare la connettività](#testconn). Questa procedura garantisce il corretto funzionamento della risoluzione dei nomi DNS (ossia garantisce che la macchina virtuale sia configurata correttamente con i server DNS locali) e che i pacchetti possano essere inviati da e verso la rete virtuale cross-premise.

Successivamente, dal prompt dei comandi Windows PowerShell nel secondo controller di dominio, eseguire i comandi seguenti:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Il computer verrà riavviato.

## Configurare le autorizzazioni e gli account farm di SharePoint

La farm di SharePoint richiederà gli account utente seguenti:

- sp\_farm: un account utente per la gestione di farm di SharePoint.
- sp\_farm\_db: un account utente con diritti sysadmin sulle istanze di SQL Server.
- sp\_install: un account utente che dispone dei diritti di amministrazione di dominio necessari per l'installazione di ruoli e funzionalità.
- sqlservice: un account utente che può essere utilizzato per eseguire le istanze SQL Server.

Successivamente, accedere a qualsiasi computer con un account di amministratore di dominio per il dominio di cui i controller di dominio sono membri, aprire un prompt dei comandi di Windows PowerShell a livello di amministratore ed eseguire questi comandi *uno alla volta*:

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Per ogni comando, verrà richiesto di immettere una password. Annotare questi nomi account e password e conservarli in luogo sicuro.

Successivamente, eseguire i passaggi seguente per aggiungere ulteriori proprietà account per i nuovi account utente.

1.	Dalla schermata Start immettere **Utenti di Active Directory**, quindi fare clic su **Utenti e computer di Active Directory**.
2.	Nel riquadro dell'albero, aprire il dominio e fare clic su **Utenti**.
3.	Nel riquadro del contenuto, fare clic con il pulsante destro del mouse su **sp\_install**, quindi fare clic su **Aggiungi a un gruppo**.
4.	Nella finestra di dialogo **Seleziona gruppi**, digitare **domain admins**, quindi fare cli due volte su **OK**.
5.	Nella finestra di dialogo, fare clic su **Visualizza e seleziona funzionalità avanzate**. L'opzione consente di visualizzare tutti i contenitori nascosti e le schede nascoste nelle finestre delle proprietà degli oggetti Active Directory.
6.	Fare clic con il pulsante destro del mouse sul nome di dominio e fare clic su **Proprietà**.
7.	Nella finestra di dialogo **Proprietà**, fare clic sulla scheda **Protezione**, quindi fare clic sul pulsante **Avanzate**.
8.	Nella finestra **Impostazioni di protezione avanzate per <YourDomain>**, fare clic su **Aggiungi**.
9.	Nella finestra **Voce di autorizzazione per <YourDomain>**, fare clic su **Seleziona un'entità**.
10.	Nella casella di testo digitare **<YourDomain>\\sp\_install**, quindi fare clic su **OK**.
11.	Selezionare **Consenti** per **Creare oggetti computer**, quindi fare clic su **OK** tre volte.

Quindi, aggiornare i server DNS per la rete virtuale in modo che Azure assegni alle macchine virtuali gli indirizzi IP dei due nuovi controller di dominio da utilizzare come server DNS. Si noti che questa procedura utilizza i valori di tabella V (per le impostazioni di rete virtuale).

1.	Nel riquadro sinistro del portale di Azure classico, fare clic su **Reti**, quindi fare clic sul nome della rete virtuale (Tabella V - Elemento 1 - Colonna Valore).
2.	Fare clic su **Configure**.
3.	In **Server DNS**, rimuovere le voci corrispondenti ai server DNS che si trovano sulla rete locale.
4.	In **Server DNS**, aggiungere due voci con nomi descrittivi e gli indirizzi IP di questi due elementi di tabella:
 - Tabella V – Elemento 6 – Colonna Valore
 - Tabella V – Elemento 7 – Colonna Valore
5.	Nella barra dei comandi in basso fare clic su **Salva**.
6.	Nel riquadro sinistro del portale di Azure classico, fare clic su **Macchine virtuali**, quindi fare clic sulla colonna **Stato** accanto al nome del primo controller di dominio.
7.	Nella barra dei comandi fare clic su **Riavvia**.
8.	Quando viene avviato il primo controller di dominio, fare clic sulla colonna **Stato** accanto al nome del secondo controller di dominio.
9.	Nella barra dei comandi fare clic su **Riavvia**. Attendere finché non viene avviato il secondo controller di dominio.

Sono stati riavviati i due controller di dominio, pertanto non vengono configurati con i server DNS locali come server DNS. Poiché sono essi stessi server DNS, vengono configurati automaticamente con i server DNS locali come server d'inoltro DNS quando sono promossi a controller di dominio.

Successivamente, è necessario creare un sito di replica di Active Directory per assicurarsi che i server nella rete virtuale di Azure utilizzino i controller di dominio locali. Accedere al controller di dominio primario con l'account sp\_install ed eseguire i comandi seguenti da un prompt dei comandi di Windows PowerShell a livello di amministratore:

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

In questo diagramma viene visualizzata la configurazione risultante dal corretto completamento di questa fase, con nomi di computer segnaposto.

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## Passaggio successivo

Per continuare con la configurazione di questo carico di lavoro, andare a [Fase 3: Configurare l'infrastruttura di SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md).

## Risorse aggiuntive

[Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

[SharePoint con infografica SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: applicazione line-of-business a disponibilità elevata](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=AcomDC_1203_2015-->