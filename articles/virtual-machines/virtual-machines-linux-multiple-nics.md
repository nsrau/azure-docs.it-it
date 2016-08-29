<properties
   pageTitle="Configurare più schede di rete in una macchina virtuale di Linux | Microsoft Azure"
   description="Informazioni su come creare una macchina virtuale con più schede di rete collegate utilizzando l'interfaccia della riga di comando di Azure o i modelli di Azure Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/02/2016"
   ms.author="iainfou"/>  

# Creazione di una macchina virtuale con più schede di rete
È possibile creare una macchina virtuale (VM) in Azure con più interfacce di rete virtuale (NIC) collegate. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end, oppure disporre di una rete dedicata a una soluzione di monitoraggio o di backup. In questo articolo vengono presentati i comandi rapidi per creare una macchina virtuale con più schede di rete collegate. Per informazioni dettagliate, incluse quelle sulla creazione di più schede di rete all'interno degli script di Bash, consultare la sezione dedicata alla [distribuzione di macchine virtuali con più schede di rete](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Le differenti [dimensioni della macchina virtuale](virtual-machines-linux-sizes.md) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

>[AZURE.WARNING] È necessario collegare più schede di rete quando si crea una VM, poiché non è possibile aggiungere le schede di rete a una macchina virtuale esistente. È possibile [creare una nuova macchina virtuale basata sui dischi virtuali originali](virtual-machines-linux-copy-vm.md) e creare più schede di rete mentre si distribuisce la macchina virtuale.

## Comandi rapidi
Controllare di aver effettuato l'accesso tramite l'[ interfaccia della riga di comando di Azure](../xplat-cli-install.md) e di usare la modalità Resource Manager (`azure config mode arm`).

Creare prima un gruppo di risorse:

```bash
azure group create TestRG --location WestUS
```

Creare un account di archiviazione in cui salvare le VM:

```bash
azure storage account create teststorage --resource-group TestRG \
    --location WestUS --kind Storage --sku-name PLRS
```

Creare una rete virtuale per connettere le macchine virtuali a:

```bash
azure network vnet create --resource-group TestRG --location WestUS \
    --name TestVNet --address-prefixes 192.168.0.0/16 
```

Creare due subnet per la rete virtuale: una per il traffico front-end e l'altra per il traffico di back-end.

```bash
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name FrontEnd --address-prefix 192.168.1.0/24
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name BackEnd --address-prefix 192.168.2.0/24
```

Creare due schede di rete, collegarne una alla subnet di front-end e l'altra alla subnet di back-end:

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

Infine, creare la macchina virtuale, collegando le due schede di rete create in precedenza:

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Creazione di più schede di rete tramite l'interfaccia della riga di comando di Azure
Se in precedenza è stata creata una macchina virtuale tramite l'interfaccia della riga di comando di Azure, i comandi rapidi dovrebbero essere già noti. Lo stesso procedimento si applica alla creazione di una o più schede di rete. È possibile leggere ulteriori informazioni sulla [distribuzione di più schede di rete tramite l'interfaccia della riga di comando di Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluso lo script del processo di ciclo per creare tutte le schede NIC.

Nell'esempio seguente vengono create due schede di rete, con una scheda di rete che si connette a ogni subnet:

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

In genere è necessario creare anche un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) o un [servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md) per gestire e distribuire il traffico tra le macchine virtuali. Anche in questo caso, i comandi sono gli stessi che si usano quando si lavora con più schede di rete. Le schede di rete create si associano a un gruppo di sicurezza di rete o a un servizio di bilanciamento del carico mediante `azure network nic set`, come nell'esempio riportato di seguito:

```bash
azure network nic set --resource-group TestRG --name NIC1 \
    --network-security-group-name TestNSG
```

In fase di creazione della macchina virtuale, è ora possibile specificare più schede di rete. Anziché utilizzare `--nic-name` per fornire una singola scheda di rete, si utilizza `--nic-names` per fornire un elenco delimitato da virgole di schede di rete. L'utente deve anche fare attenzione quando seleziona la dimensione della macchina virtuale. Esistono dei limiti per quanto riguarda il numero totale di schede di rete che è possibile aggiungere. Ulteriori informazioni sulle [dimensioni delle macchine virtuali di Linux](virtual-machines-linux-sizes.md). Nel seguente esempio viene illustrato come specificare più schede di rete e, successivamente, la dimensione della macchina virtuale che supporta l'utilizzo di più schede di rete (`Standard_DS2_v2`):

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Creazione di più schede di rete utilizzando i modelli di Resource Manager
I modelli di Azure Resource Manager utilizzano i file JSON dichiarativi per definire l'ambiente. È possibile consultare una [panoramica di Azure Resource Manager](../resource-group-overview.md). I modelli di Resource Manager offrono un modo di creare più istanze di una risorsa durante la distribuzione, come ad esempio la creazione di più schede di rete. Utilizzare *Copia* per specificare il numero di istanze da creare:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Ulteriori informazioni sulla [creazione di più istanze utilizzando *Copia*](../resource-group-create-multiple.md).

È inoltre possibile utilizzare un `copyIndex()` per poi aggiungere un numero al nome di una risorsa, che consente di creare `NIC1`, `NIC2`, e così via. Di seguito viene riportato un esempio di aggiunta del valore di indice:

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

È possibile consultare un esempio completo di [creazione di più schede di rete utilizzando i modelli di Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Passaggi successivi
Assicurarsi di consultare [Dimensioni delle macchine virtuali di Linux](virtual-machines-linux-sizes.md) durante il tentativo di creazione di una macchina virtuale con più schede di rete. Prestare attenzione al numero massimo di schede di rete supportato per ogni dimensione della macchina virtuale.

Tenere presente che non è possibile aggiungere altre schede di rete a una macchina virtuale esistente. È necessario creare tutte le schede di rete quando si distribuisce la macchina virtuale. Prestare attenzione quando si pianificano le distribuzioni per assicurarsi di avere la connettività di rete necessaria fin dall'inizio.

<!---HONumber=AcomDC_0817_2016-->