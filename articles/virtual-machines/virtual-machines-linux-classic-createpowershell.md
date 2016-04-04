<properties
	pageTitle="Creare una macchina virtuale di Linux tramite Azure PowerShell | Microsoft Azure"
	description="Informazioni su come creare e preconfigurare una macchina virtuale Linux tramite Azure PowerShell"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="cynthn"/>

# Creare e preconfigurare una macchina virtuale Linux tramite Azure PowerShell

> [AZURE.SELECTOR]
- [Interfaccia della riga di comando di Azure](virtual-machines-linux-cli-create.md)
- [PowerShell](virtual-machines-linux-classic-createpowershell.md)


<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.
 
La procedura mostra come creare una macchina virtuale Linux utilizzando un approccio basato sul completamento di valori predefiniti per la creazione di set di comandi di Azure PowerShell. Questo approccio può essere utile se non si è esperti di Azure PowerShell o per sapere semplicemente quali valori specificare per una corretta configurazione.

Si compilerà il set di comandi copiando i blocchi dei set di comando nel file di testo o in PowerShell ISE, quindi compilando i valori delle variabili e rimuovendo i caratteri < and >. Vedere i due [esempi](#examples) alla fine di questo articolo per avere un'idea del risultato finale.

Per l'argomento associato per le macchine virtuali basate su Windows, vedere [Utilizzo di Azure PowerShell per creare macchine virtuali basate su Windows](virtual-machines-windows-classic-create-powershell.md).

## Installare Azure PowerShell

Se non è già stato fatto, [Installare e configurare Azure PowerShell](powershell-install-configure.md). Quindi, aprire un prompt dei comandi di Azure PowerShell.

## Impostare l'account di archiviazione e la sottoscrizione

Impostare la sottoscrizione di Azure e l'account di archiviazione eseguendo questi comandi al prompt dei comandi di Azure PowerShell.

È possibile ottenere il nome della sottoscrizione corretto dalla proprietà **SubscriptionName** dell'output del comando **Get-AzureSubscription**.

È possibile ottenere il nome dell'account di archiviazione corretto dalla proprietà **Label** dell'output del comando **Get-AzureStorageAccount** dopo aver eseguito il comando Select-AzureSubscription.

Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount


## Individuare l'immagine da usare

Successivamente, è necessario determinare il valore ImageFamily per l'immagine da utilizzare. Con questo comando è possibile ottenere l'elenco di valori ImageFamily disponibili.

	Get-AzureVMImage | select ImageFamily -Unique

Di seguito sono riportati alcuni esempi di valori ImageFamily per i computer basati su Linux:

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Aprire una nuova istanza dell'editor di testo preferito oppure l'istanza preferita dell'ambiente PowerShell Integrated Scripting Environment (ISE). Copiare quanto segue nel nuovo file di testo o in PowerShell ISE, sostituendo il valore ImageFamily.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Specificare il nome, le dimensioni e, facoltativamente, il set di disponibilità

Avviare il set di comandi scegliendo uno dei due seguenti blocchi di comandi (obbligatorio).

**Opzione 1**: specificare un nome di macchina virtuale e una dimensione.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

**Opzione 2**: specificare un nome, la dimensione e il nome del set di disponibilità.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Per i valori InstanceSize per le macchine virtuali di serie D-, DS- o G-, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).


## Impostazione delle opzioni di protezione dell’accesso utente

**Opzione 1**: Specificare il nome utente e la password Linux iniziali (obbligatorio). Scegliere una password complessa. Per verificarne il livello di complessità, vedere [Controllo password: utilizzo di password complesse](https://www.microsoft.com/security/pc-security/password-checker.aspx).

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

**Opzione 2**: specificare un set di coppie di chiavi SSH già distribuite nella sottoscrizione.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Per altre informazioni, vedere [Come usare SSH con Linux in Azure](virtual-machines-linux-ssh-from-linux.md).

**Opzione 3**: specificare un elenco di chiavi pubbliche SSH già distribuite nella sottoscrizione.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Per altre opzioni di preconfigurazione per le macchine virtuali basate su Linux, vedere la sintassi per il set di parametri **Linux** in [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).


## Facoltativo: assegnare un DIP statico

Facoltativamente, assegnare alla macchina virtuale un indirizzo IP specifico, noto come DIP statico.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

È possibile verificare la disponibilità di uno specifico indirizzo IP con il comando seguente:

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

## Facoltativo: assegnare la macchina virtuale a una subnet specifica 

Assegnare la macchina virtuale a una subnet specifica in una rete virtuale di Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

	
## Facoltativo: aggiungere un disco dati
	
Aggiungere quanto segue al comando impostato per aggiungere un disco dati alla macchina virtuale.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

## Facoltativo: aggiungere la macchina virtuale a un carico bilanciato esistente 

Aggiungere quanto segue al comando impostato per aggiungere la macchina virtuale a un set con carico bilanciato esistente per il traffico esterno.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

## Decidere come avviare il processo di creazione della macchina virtuale 

Aggiungere un blocco al comando per avviare il processo di creazione della macchina virtuale scegliendo uno di questi blocchi di comando.

**Opzione 1**: creare la macchina virtuale in un servizio cloud esistente.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Il nome breve del servizio cloud è il nome visualizzato nell'elenco dei servizi cloud nel portale di Azure classico o nell'elenco dei gruppi di risorse nel portale di Azure classico.

**Opzione 2**: creare la macchina virtuale in un servizio cloud e in una rete virtuale esistenti.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Eseguire il set di comandi

Esaminare il set di comandi di Azure PowerShell che è stato compilato in un editor di testo o PowerShell ISE e assicurarsi di avere specificato tutte le variabili e che abbiano i valori corretti. Assicurarsi inoltre che siano stati rimossi tutti i caratteri < and >.

Copiare il set di comandi negli Appunti, quindi fare clic con il pulsante destro del mouse sul prompt dei comandi aperto di Azure PowerShell. Il set di comandi verrà emesso come una serie di comandi di PowerShell e verrà creata la macchina virtuale di Azure.

Dopo aver creato la macchina virtuale, vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-classic-log-on.md).

Se si desidera riutilizzare il set di comandi, è possibile:

- Salvare questo set di comandi come file di script di PowerShell (*.ps1)
- Salvare questo set di comandi come Runbook di automazione di Azure nella sezione **Automazione** del portale di Azure classico.

## <a id="examples"></a>Esempi:

Di seguito sono riportati due esempi di utilizzo dei passaggi precedenti per compilare i set di comandi di Azure PowerShell per la creazione di macchine virtuali basate su Linux in Azure.

### Esempio 1

Un set di comandi di PowerShell è necessario per creare la macchina virtuale Linux iniziale per un server MySQL che:

- Usa l'immagine Ubuntu Server 12.10
- È denominato AZMYSQL1
- Dispone di un disco dati aggiuntivo di 500 GB.
- Ha l'indirizzo IP statico 192.168.244.4
- Si trovi nella subnet BackEnd della rete virtuale AZDatacenter.
- Si trovi nel servizio cloud Azure-TailspinToys.

Ecco il set di comandi corrispondente di Azure PowerShell per creare la macchina virtuale, con righe vuote tra ogni blocco per migliorare la leggibilità.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Esempio 2

Un set di comandi di PowerShell è necessario per creare una macchina virtuale Linux per un server Apache che:

- Usa l'immagine SUSE Linux Enterprise Server 12
- Sia denominato LOB1.
- Disponga di un disco dati aggiuntivo di 50 GB.
- È membro del set di bilanciamento del carico LOBServers per il traffico Web standard
- Si trovi nella subnet FrontEnd della rete virtuale AZDatacenter.
- Si trovi nel servizio cloud Azure-TailspinToys.

Ecco il set di comandi corrispondente di Azure PowerShell per creare la macchina virtuale.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Risorse aggiuntive

[Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/)

[Domande frequenti sulle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Panoramica delle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Come installare e configurare Azure PowerShell](powershell-install-configure.md)

[Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-classic-log-on.md)

[Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](virtual-machines-windows-classic-create-powershell.md)

<!---HONumber=AcomDC_0323_2016-->