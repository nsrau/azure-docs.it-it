---
title: "Convertire una macchina virtuale di Azure in un set di scalabilità | Microsoft Docs"
description: "Creare e distribuire un set di scalabilità di macchine virtuali Linux tramite l&quot;interfaccia della riga di comando di Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>Convertire una macchina virtuale di Azure esistente in un set di scalabilità

In questa esercitazione viene illustrato come usare l'interfaccia della riga di comando di Azure 2.0 per convertire una macchina virtuale in un set di scalabilità di macchine virtuali. È inclusa anche la procedura per automatizzare la configurazione delle macchine virtuali nel set di scalabilità. Per altre informazioni su come installare l'interfaccia della riga di comando di Azure 2.0, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli.md). Per altre informazioni sui set di scalabilità, vedere [Set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

## <a name="step-1---deprovision-the-vm"></a>Passaggio 1: eseguire il deprovisioning della macchina virtuale

Usare SSH per connettersi alla macchina virtuale.

Eseguire il deprovisioning della macchina virtuale usando l'agente della macchina virtuale di Azure per eliminare file e dati. Per una panoramica dettagliata sul deprovisioning, vedere [Come generalizzare e acquisire una macchina virtuale Linux](capture-image.md).

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>Passaggio 2: acquisire un'immagine di macchina virtuale

Per una panoramica dettagliata sull'acquisizione, vedere [Come generalizzare e acquisire una macchina virtuale Linux](capture-image.md).

Deallocare la macchina virtuale con [az vm deallocate](/cli/azure/vm#deallocate):

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Generalizzare la macchina virtuale con [az vm generalize](/cli/azure/vm#generalize):

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

Creare un'immagine dalla risorsa macchina virtuale con [az image create](/cli/azure/image#create):

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>Passaggio 3: creare il set di scalabilità

Ottenere l'**ID** dell'immagine.

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

Creare una macchina virtuale dalla risorsa di immagine con [az vm create](/cli/azure/vmss#create):

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

Questo comando associa anche un disco dati di 10 GB. Tenere presente che, a seconda delle dimensioni della macchina virtuale definite (è stata usata l'opzione **Standard_DS1_v2**), il numero di dischi dati consentito è diverso. Per altre informazioni, rivedere le [dimensioni della macchina virtuale](sizes.md).

Al termine, connettersi al set di scalabilità definito. Ottenere un elenco di indirizzi IP per le istanze di SSH con [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info):

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

A questo punto è possibile connettersi all'istanza della macchina virtuale per inizializzare il disco dati

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>Passaggio 4: inizializzare il disco dati

Quando si è connessi alla macchina virtuale, eseguire la partizione del disco con `fdisk`:

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

Scrivere un file system nella partizione con il comando `mkfs`:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montare il nuovo disco in modo che sia accessibile nel sistema operativo:

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

È possibile ora accedere al disco tramite il punto di montaggio dell'unità dati, che può essere verificato con `ls /datadrive/`.

Chiudere la sessione SSH.


## <a name="step-5---configure-firewall"></a>Passaggio 5: configurare il firewall

Aprire una porta nel firewall per il server Web ospitato dal set di scalabilità. Quando è stato creato il set di scalabilità, è stato anche creato un bilanciamento del carico ed è stato usato **SSH** per le singole macchine virtuali. Per aprire una porta, sono necessarie due informazioni, che possono essere ottenute tramite l'interfaccia della riga di comando di Azure.

* **Pool di indirizzi IP front-end**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **Pool di indirizzi IP back-end**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

Con questi due nomi, è possibile aprire la porta **80**.

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>Passaggio 6: automatizzare la configurazione

Il disco dati deve essere configurato in ogni istanza di macchina virtuale. È possibile automatizzare la configurazione della macchina virtuale con l'estensione **CustomScript**.

Creare prima uno script *sh* che includa i comandi di formato disco.

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

Caricare quindi il file script a cui può accedere l'estensione **CustomScript**. Una copia è disponibile [qui](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4).

Creare un file locale denominato **settings.json** e inserirvi il seguente blocco JSON. La proprietà `flieUris` deve essere impostata sul percorso in cui è stato caricato il file script.

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

Distribuire questo comando al set di scalabilità con l'estensione **CustomScript**, che fa riferimento al file **settings.json** appena creato.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

Questa estensione viene eseguita automaticamente in tutte le istanze correnti e in tutte le istanze create successivamente tramite scalabilità.

## <a name="step-7---configure-autoscale-rules"></a>Passaggio 7: configurare le regole di scalabilità automatica

Attualmente non è possibile impostare le regole di scalabilità automatica nell'interfaccia della riga di comando di Azure. Usare il [Portale di Azure](https://portal.azure.com) per configurare la scalabilità automatica.

## <a name="step-8---management-tasks"></a>Passaggio 8: attività di gestione

Nel ciclo di vita del set di scalabilità, potrebbe essere necessario eseguire una o più attività di gestione. Se si vuole anche creare script che consentano di automatizzare diverse attività del ciclo di vita, l'interfaccia della riga di comando di Azure offre un modo rapido per eseguire tali operazioni. Di seguito vengono illustrate alcune attività comuni.

### <a name="get-connection-info"></a>Ottenere informazioni sulla connessione

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>Impostare il conteggio delle istanze (scalabilità manuale)

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>Eliminare un gruppo di risorse

Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su alcune funzionalità del set di scalabilità di macchine virtuali illustrate in questa esercitazione, vedere le informazioni seguenti:

- [Informazioni sui set di scalabilità di macchine virtuali in Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Panoramica di Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Controllare il flusso del traffico di rete con i gruppi di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)
