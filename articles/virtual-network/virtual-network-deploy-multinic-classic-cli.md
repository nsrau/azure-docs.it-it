<properties
   pageTitle="Distribuire più macchine virtuali con funzionalità multi-NIC mediante l’interfaccia della riga di comando di Azure nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come distribuire più macchine virtuali con funzionalità Multi-NIC mediante l’interfaccia della riga di comando di Azure nel modello di distribuzione classica"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Distribuire le macchine virtuali con funzionalità Multi-NIC (classiche) tramite l’interfaccia della riga di comando di Azure di Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Informazioni su come [eseguire questa procedura con il modello di Resource Manager](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Attualmente, non è possibile avere macchine virtuali con una singola scheda di rete e macchine virtuali con più schede di rete nello stesso servizio cloud. Per questo motivo, è necessario implementare i server back-end in un servizio cloud diverso rispetto a tutti gli altri componenti dello scenario. La procedura seguente usa un servizio cloud denominato *IaaSStory* per la risorsa principale e *IaaSStory-BackEnd* per i server di back-end.

## Prerequisiti

Prima di distribuire i server back-end, è necessario distribuire il servizio cloud principale con tutte le risorse necessarie per questo scenario. Come minimo, è necessario creare una rete virtuale con una subnet per il back-end. Visitare [Creare una rete virtuale tramite l’interfaccia della riga di comando di Azure](virtual-networks-create-vnet-classic-cli.md) per informazioni su come distribuire una rete virtuale.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Distribuire le macchine virtuali di back-end

Le macchine virtuali di back-end dipendono dalla creazione di risorse elencate di seguito.

- **Account di archiviazione per i dischi dati**. Per migliorare le prestazioni, i dischi dati sui server di database utilizzano la tecnologia SSD (Solid State Drive), che richiede un account di archiviazione premium. Verificare che la posizione di Azure distribuita supporti l'archiviazione premium.
- **Schede di rete**. Ogni macchina virtuale ha due schede di rete, una per l'accesso al database e una per la gestione.
- **Set di disponibilità**. Tutti i server di database vengono aggiunti a un singolo set di disponibilità, per garantire che almeno una delle macchine virtuali sia attiva e in esecuzione durante la manutenzione.

### Passaggio 1 - avviare lo script

È possibile scaricare lo script di bash completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Attenersi alla procedura seguente per modificare lo script da usare nell'ambiente.

1. Modificare i valori delle variabili indicate di seguito in base al gruppo di risorse esistente distribuito in precedenza in [Prerequisiti](#Prerequisites).

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione di back-end.

		backendCSName="IaaSStory-Backend"
		prmStorageAccountName="iaasstoryprmstorage"
		image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskPrefix="db"
		dataDiskName="datadisk"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

### Passaggio 2 - creare le risorse necessarie per le macchine virtuali

1. Creare un nuovo servizio cloud per tutte le macchine virtuali di back-end. Si noti l'utilizzo della `$backendCSName` variabile per il nome del gruppo di risorse e `$location` per l'area di Azure.

		azure service create --serviceName $backendCSName \
		    --location $location

2. Creare un account di archiviazione premium per i dischi dati e del sistema operativo da usare mediante le macchine virtuali.

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS

### Passaggio 3 - creare macchine virtuali con più NIC

1. Avviare un ciclo per creare più macchine virtuali, in base alle `numberOfVMs` variabili.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. Per ogni macchina virtuale, specificare il nome e l’indirizzo IP di ciascuna delle due schede di rete.

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x

		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. Creare la macchina virtuale Si noti l'utilizzo del parametro `--nic-config`, contenente un elenco di tutte le schede di rete con nome, subnet e indirizzo IP.

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Per ogni macchina virtuale, creare due dischi dati.

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### Passaggio 4 - eseguire lo script.

Una volta scaricato e modificato lo script in base alle esigenze, eseguire lo script per creare macchine virtuali del database di back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal terminale **Bash**. Verrà visualizzato l'output iniziale, come illustrato di seguito.

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name IaaSStory-Backend
		info:    service create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM

2. Dopo alcuni minuti, l'esecuzione verrà terminata e il resto dell'output verrà visualizzato come mostrato di seguito.

		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM
		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0629_2016-->