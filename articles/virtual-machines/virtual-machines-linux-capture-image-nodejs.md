---
title: Acquisire una VM Linux di Azure da usare come modello | Documentazione Microsoft
description: Informazioni su come acquisire e &quot;generalizzare&quot; l&quot;immagine di una VM di Azure basata su Linux creata con il modello di distribuzione Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4505bb5f572add13c21df06fc7997358eaae6352
ms.lasthandoff: 03/21/2017


---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Acquisire una VM Linux in esecuzione su Azure
Seguire i passaggi descritti in questo articolo per generalizzare e acquisire la macchina virtuale Linux di Azure (VM) nel modello di distribuzione Azure Resource Manager. Quando si generalizza la macchina virtuale, si rimuovono le informazioni sull'account personale e si prepara la macchina virtuale da usare come immagine. Si acquisisce quindi l'immagine di un disco rigido virtuale generalizzato (VHD) per il sistema operativo, i dischi rigidi virtuali per i dischi dati collegati, e un [modello di Resource Manager](../azure-resource-manager/resource-group-overview.md) per nuove distribuzioni di macchine virtuali. Questo articolo descrive come acquisire un'immagine di macchina virtuale con l'interfaccia della riga di comando di Azure 1.0 tramite i dischi non gestiti. È anche possibile [acquisire una macchina virtuale tramite Azure Managed Disks con l'interfaccia della riga di comando di Azure 2.0 ](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). La funzionalità Managed Disks viene gestita dalla piattaforma Azure e non richiede alcuna pianificazione o alcuna posizione per l'archiviazione. Per altre informazioni, vedere [Azure Managed Disks Overview](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Panoramica di Azure Managed Disks). 

Per creare macchine virtuali usando l'immagine, configurare le risorse di rete per ogni nuova macchina virtuale e usare il modello (un file JavaScript Object Notation o JSON) per distribuirlo dalle immagini del disco rigido virtuale acquisite. In questo modo è possibile replicare una macchina virtuale con la configurazione corrente del software, simile al modo in cui si usano le immagini in Azure Marketplace.

> [!TIP]
> Per creare una copia della VM Linux esistente con il relativo stato specializzato per il backup o il debug, vedere [Creare una copia di una macchina virtuale Linux in esecuzione su Azure](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per caricare un disco rigido virtuale Linux da una macchina virtuale locale, vedere [Caricare e creare una VM Linux da un'immagine disco personalizzata](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#before-you-begin): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa

## <a name="before-you-begin"></a>Prima di iniziare
Accertarsi che siano soddisfatti i prerequisiti seguenti:

* **Macchina virtuale Azure creata nel modello di distribuzione Resource Manager**: se non è stata creata una VM Linux, è possibile usare il [portale](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), l'[interfaccia della riga di comando di Azure](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oppure i [modelli di Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
  
    Configurare la macchina virtuale in base alle esigenze. Ad esempio, [aggiungere dischi dati](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), applicare aggiornamenti e installare applicazioni. 
* **Interfaccia della riga di comando di Azure**: installare l'[interfaccia della riga di comando di Azure](../cli-install-nodejs.md) sul computer locale.

## <a name="step-1-remove-the-azure-linux-agent"></a>Passaggio 1: Rimuovere l'agente Linux di Azure
Eseguire innanzitutto il comando **waagent** con il parametro **deprovision** sulla VM Linux. Questo comando elimina file e dati per preparare la VM per la generalizzazione. Per informazioni dettagliate, vedere [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Connettersi alla VM Linux tramite un client SSH.
2. Nella finestra SSH digitare il comando seguente:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Eseguire questo comando solo su una VM che si intende acquisire come immagine. Ciò non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione.
 
3. Digitare **y** per continuare. È possibile aggiungere il parametro **-force** per evitare questo passaggio di conferma.
4. Dopo aver eseguito il comando, digitare **exit**. Questo passaggio chiude il client SSH.

## <a name="step-2-capture-the-vm"></a>Passaggio 2: Acquisire la macchina virtuale
Usare l'interfaccia della riga di comando di Azure per generalizzare e acquisire la macchina virtuale. Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono **myResourceGroup**, **myVnet** e **myVM**.

1. Dal computer locale aprire l'interfaccia della riga di comando di Azure ed [eseguire l'accesso alla sottoscrizione di Azure](../xplat-cli-connect.md). 
2. Verificare di essere in modalità Resource Manager.
   
    ```azurecli
    azure config mode arm
    ```
3. Arrestare la VM di cui è già stato effettuato il deprovisioning usando il comando seguente:
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Generalizzare la VM con il comando seguente:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Eseguire ora il comando **azure vm capture**, che consente di acquisire la macchina virtuale. Nell'esempio seguente i dischi rigidi virtuali dell'immagine vengono acquisiti con i nomi che iniziano con **MyVHDNamePrefix** e l'opzione **-t** specifica un percorso al modello **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > Per impostazione predefinita, il file VHD dell'immagine viene creato nello stesso account di archiviazione della VM originale usata. Usare lo *stesso account di archiviazione* per archiviare i dischi rigidi virtuali delle nuove VM eventualmente create dall'immagine. 

6. Per trovare la posizione di un'immagine acquisita, aprire il modello JSON in un editor di testo. In **storageProfile** trovare l'**URI** dell'**immagine** ubicata nel contenitore **system**. Ad esempio, l'URI dell'immagine del disco del sistema operativo è simile a `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passaggio 3: Distribuire una VM dall'immagine acquisita
Usare ora l'immagine con un modello per creare una VM Linux. Questi passaggi mostrano come usare l'interfaccia della riga di comando di Azure e il modello di file JSON acquisito in precedenza per creare la VM in una nuova rete virtuale.

### <a name="create-network-resources"></a>Creare risorse di rete
Per usare il modello, prima di tutto è necessario configurare una rete virtuale e una scheda di interfaccia di rete per la nuova VM. Per queste risorse si consiglia di creare un gruppo di risorse nel percorso in cui è archiviata l'immagine della VM. Eseguire comandi simili ai seguenti, sostituendo i nomi delle proprie risorse e una posizione di Azure appropriata ("centralus" in questi comandi):

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Ottenere l'ID della scheda di interfaccia di rete
Per distribuire una VM dall'immagine usando il file JSON salvato durante l'acquisizione, è necessario l'ID della scheda di interfaccia di rete. Per ottenerlo, eseguire il comando seguente:

```azurecli
azure network nic show myResourceGroup1 myNIC
```

L'**ID** dell'output è simile a `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>Creare una macchina virtuale
Eseguire ora il comando seguente per creare la VM dall'immagine della VM acquisita. Usare il parametro **-f** per specificare il percorso al file JSON del modello che è stato salvato.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

Nel comando di output viene chiesto di specificare un nuovo nome di VM, il nome e la password amministratore, e l'ID della scheda di interfaccia di rete creata in precedenza.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

Se la distribuzione viene eseguita correttamente, vengono visualizzate le informazioni seguenti:

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Verificare la distribuzione
Connettersi ora con SSH alla macchina virtuale creata per verificare la distribuzione e iniziare a usare la nuova VM. Per connettersi con SSH, trovare l'indirizzo IP della VM creata eseguendo il comando seguente:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

L'indirizzo IP pubblico viene elencato nell'output del comando. Per impostazione predefinita, ci si connette alla VM Linux con SSH sulla porta 22.

## <a name="create-additional-vms"></a>Creare altre macchine virtuali
Usare l'immagine acquisita e il modello per distribuire altre VM seguendo i passaggi riportati nella sezione precedente. Altre opzioni per creare macchine virtuali dall'immagine includono l'uso di un modello di Guida introduttiva o l'esecuzione del comando **azure vm create**.

### <a name="use-the-captured-template"></a>Usare il modello acquisito
Per usare il modello e l'immagine acquisiti, seguire questi passaggi (indicati nella sezione precedente):

* Assicurarsi che l'immagine della VM si trovi nello stesso account di archiviazione che ospita il disco rigido virtuale della VM.
* Copiare il file JSON del modello e specificare un nome univoco per il disco del sistema operativo del disco rigido virtuale della nuova VM (o dei dischi rigidi virtuali). Ad esempio, in **storageProfile**, sotto **vhd**, in **uri**, specificare un nome univoco per il disco rigido virtuale **osDisk**, simile a `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Creare una scheda di interfaccia di rete nella stessa rete virtuale o in una rete diversa.
* Usando il file JSON del modello modificato, creare una distribuzione nel gruppo di risorse in cui è stata configurata la rete virtuale.

### <a name="use-a-quickstart-template"></a>Uso di un modello di Guida introduttiva
Per configurare automaticamente la rete quando si crea una VM dall'immagine, è possibile specificare le risorse necessarie in un modello. Ad esempio, vedere il [modello&101;-vm-from-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) in GitHub. Questo modello crea una VM dall'immagine personalizzata e la rete virtuale, l'indirizzo IP pubblico e le risorse NIC necessari. Per una procedura dettagliata sull'uso del modello nel portale di Azure, vedere la pagina che illustra [Come creare una macchina virtuale da un'immagine personalizzata con un modello di Resource Manager](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Usare il comando azure vm create
In genere, per creare una VM dall'immagine è più facile usare un modello di Resource Manager. È però possibile creare la VM *in modo imperativo* usando il comando **azure vm create** con il parametro **-Q** (**--image-urn**). Se si sceglie questo metodo, passare anche il parametro **-d** (**--os-disk-vhd**) per specificare il percorso del file con estensione VHD del sistema operativo per la nuova VM. Questo file deve trovarsi nel contenitore dei VHD dell'account di archiviazione in cui è archiviato il file VHD dell'immagine. Il comando copia automaticamente il VHD per la nuova VM nel contenitore **vhds**.

Prima di eseguire il comando **azure vm create** per l'immagine, completare la procedura seguente:

1. Creare un gruppo di risorse o identificarne uno esistente per la distribuzione.
2. Creare una risorsa indirizzo IP pubblico e una risorsa NIC per la nuova VM. Per conoscere i passaggi per creare una rete virtuale, un indirizzo IP pubblico e una scheda di interfaccia di rete usando l'interfaccia della riga di comando, vedere più sopra in questo articolo. **azure vm create** può anche creare una scheda di interfaccia di rete, ma è necessario passare i parametri aggiuntivi per una rete e una subnet virtuali.

Eseguire quindi un comando che passa gli URI sia al nuovo file VHD del sistema operativo sia all'immagine esistente. In questo esempio viene creata una VM di dimensioni Standard_A1 nell'area Stati Uniti orientali.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Per altre opzioni del comando, eseguire `azure help vm create`.

## <a name="next-steps"></a>Passaggi successivi
Per gestire le VM con l'interfaccia della riga di comando, vedere le attività in [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


