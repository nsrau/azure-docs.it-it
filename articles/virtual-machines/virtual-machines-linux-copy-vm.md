<properties
	pageTitle="Creare una copia della VM Linux di Azure | Microsoft Azure"
	description="Scopri come creare una copia della VM Linux di Azure nel modello di distribuzione Resource Manager"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="cynthn"/>

# Creare la copia di una macchina virtuale Linux eseguita in Azure


Questo articolo descrive come creare una copia di una macchina virtuale (VM) di Azure che esegue Linux nel modello di distribuzione Resource Manager. Per prima cosa si esegue la copia dei dischi dati e del sistema operativo in un nuovo contenitore, quindi si configurano le risorse di rete e si crea la nuova macchina virtuale.

È anche possibile eseguire le operazioni di [Caricamento e creazione di una VM da un'immagine disco personalizzata](virtual-machines-linux-upload-vhd.md).


## Prima di iniziare

Accertarsi che prima di iniziare la procedura siano soddisfatti i prerequisiti seguenti:

- Nel computer è stata scaricata e installata l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

- Sono anche necessarie alcune informazioni sulla VM Linux di Azure esistente:

| Informazioni sulla VM di origine | Informazioni sulla collocazione |
|------------|-----------------|
| Nome della VM | `azure vm list` |
| Nome del gruppo di risorse | `azure vm list` |
| Location | `azure vm list` |
| Nome dell'account di archiviazione | `azure storage account list -g <resourceGroup>` |
| Nome del contenitore | `azure storage container list -a <sourcestorageaccountname>` |
| Nome del file VHD della VM di origine | `azure storage blob list --container <containerName>` |



- Sarà necessario scegliere alcune informazioni sulla nuova VM: <br> -Nome contenitore <br> -Nome VM <br> -Dimensioni VM <br> -Nome rete virtuale <br> -Nome subnet <br> -Nome IP <br> -Nome NIC
	

## Effettuare l'accesso e impostare la sottoscrizione

1. Effettuare l'accesso all'interfaccia della riga di comando.
		
		azure login

2. Verificare di essere in modalità Resource Manager.
	
		azure config mode arm

3. Impostare la sottoscrizione corretta. Per un elenco di tutte le sottoscrizioni, consultare l'elenco degli account Azure.

		azure account set <SubscriptionId>



## Arrestare la VM 

Arrestare e deallocare la VM di origine. Per un elenco di tutte le VM presenti nella sottoscrizione e dei nomi dei relativi gruppi di risorse, consultare l'elenco delle VM Azure.
	
		azure vm stop <ResourceGroup> <VmName>
		azure vm deallocate <ResourceGroup> <VmName>




## Copiare il file VHD


Per copiare il file VHD dall'archiviazione di origine a quella di destinazione, è possibile usare `azure storage blob copy start`. In questo esempio il file VHD viene copiato nello stesso account di archiviazione, ma in un contenitore diverso.

Per copiare il file VHD in un altro contenitore dello stesso account di archiviazione, digitare:

		azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
		

## Configurare la rete virtuale per la nuova VM

Configurare una rete virtuale e una scheda NIC per la nuova VM.

	azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

	azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

	azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

	azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## Creare la nuova VM 

A questo punto è possibile creare una VM dal disco rigido virtuale caricato [usando un modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) oppure tramite l'interfaccia della riga di comando, specificando l'URI del disco copiato come indicato di seguito:

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## Passaggi successivi

Per altre informazioni su come usare l'interfaccia della riga di comando di Azure per gestire la nuova macchina virtuale, vedere [Comandi dell'interfaccia della riga di comando Azure per Azure Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0803_2016-->