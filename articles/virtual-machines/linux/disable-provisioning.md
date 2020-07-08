---
title: Disabilitare o rimuovere l'agente di provisioning
description: Informazioni su come disabilitare o rimuovere l'agente di provisioning in macchine virtuali e immagini Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 133de199c240cbc4ea7246a29e65347d53c50545
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045757"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Disabilitare o rimuovere l'agente Linux da macchine virtuali e immagini

Prima di rimuovere l'agente Linux, è necessario comprendere quale macchina virtuale non sarà in grado di eseguire dopo la rimozione dell'agente Linux.

Le [estensioni](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) delle macchine virtuali (VM) di Azure sono piccole applicazioni che forniscono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure, le estensioni vengono installate e gestite dal piano di controllo di Azure. È compito dell' [agente Linux di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) elaborare i comandi dell'estensione della piattaforma e garantire lo stato corretto dell'estensione all'interno della macchina virtuale.

La piattaforma Azure ospita diverse estensioni, tra cui applicazioni di utilità, sicurezza, monitoraggio e configurazione delle VM. È disponibile un'ampia scelta di estensioni per prima e di terze parti, esempi di scenari principali per cui vengono usate le estensioni:
* Supporto dei servizi di Azure di terze parti, ad esempio backup di Azure, monitoraggio, crittografia del disco, sicurezza, replica del sito e altro.
* Reimpostazioni SSH/password
* Configurazione della macchina virtuale: esecuzione di script personalizzati, installazione di chef, agenti Puppet e così via.
* Prodotti di terze parti, ad esempio prodotti AV, strumenti di vulnerabilità VM, strumenti di monitoraggio delle VM e delle app.
* Le estensioni possono essere aggregate con una nuova distribuzione di VM. Possono, ad esempio, fare parte di una distribuzione più ampia, in le applicazioni vengono configurate durante il provisioning della VM, o venire eseguite nei sistemi gestiti dalle estensioni supportate dopo la distribuzione.

## <a name="disabling-extension-processing"></a>Disabilitazione dell'elaborazione delle estensioni

Esistono diversi modi per disabilitare l'elaborazione dell'estensione, a seconda delle esigenze, ma prima di continuare è **necessario** rimuovere tutte le estensioni distribuite nella macchina virtuale, ad esempio usando AZ CLI, è possibile [elencare](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-list) ed [eliminare](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-delete):

```bash
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Se non si esegue questa operazione, la piattaforma tenterà di inviare la configurazione dell'estensione e il timeout dopo 40min.

### <a name="disable-at-the-control-plane"></a>Disabilitare sul piano di controllo
Se non si è certi che saranno necessarie estensioni in futuro, è possibile lasciare l'agente Linux installato nella macchina virtuale, quindi disabilitare la funzionalità di elaborazione delle estensioni dalla piattaforma. Questa opzione è disponibile nella `Microsoft.Compute` versione API `2018-06-01` o versione successiva e non presenta una dipendenza dalla versione dell'agente Linux installata.

```bash
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
È possibile riabilitare con facilità l'elaborazione dell'estensione dalla piattaforma, con il comando precedente, ma impostarla su' true '.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Rimuovere l'agente Linux da una macchina virtuale in esecuzione

Assicurarsi di aver **rimosso** tutte le estensioni esistenti dalla macchina virtuale prima di, come indicato in precedenza.

### <a name="step-1-remove-the-azure-linux-agent"></a>Passaggio 1: rimuovere l'agente Linux di Azure

Se si rimuove solo l'agente Linux e non gli elementi di configurazione associati, è possibile reinstallare in un secondo momento. Per rimuovere l'agente Linux di Azure, eseguire uno dei seguenti elementi come root:

#### <a name="for-ubuntu-1804"></a>Per Ubuntu >= 18,04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Per RedHat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Per SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Passaggio 2: (facoltativo) rimuovere gli artefatti dell'agente Linux di Azure
> [!IMPORTANT] 
>
> È possibile rimuovere tutti gli elementi associati dell'agente Linux, ma ciò significa che non sarà possibile reinstallarlo in un secondo momento. Si consiglia pertanto di disabilitare prima di tutto l'agente Linux, rimuovendo l'agente Linux usando il solo sopra. 

Se si è certi che l'agente Linux non verrà mai reinstallato, è possibile eseguire le operazioni seguenti:

#### <a name="for-ubuntu-1804"></a>Per Ubuntu >= 18,04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Per RedHat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Per SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Preparazione di un'immagine senza l'agente Linux
Se si dispone di un'immagine che contiene già cloud-init e si desidera rimuovere l'agente Linux, ma ancora eseguire il provisioning tramite cloud-init, eseguire i passaggi descritti nel passaggio 2 (e, facoltativamente, passaggio 3) come radice per rimuovere l'agente Linux di Azure e quindi rimuovere la configurazione cloud-init e i dati memorizzati nella cache e preparare la macchina virtuale per creare un'immagine

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Effettuare il deprovisioning e creare un'immagine
L'agente Linux è in grado di eliminare alcuni dei metadati delle immagini esistenti, con il passaggio "waagent-deprovision + User", tuttavia, dopo che è stato rimosso, sarà necessario eseguire azioni come la seguente e rimuovere eventuali altri dati sensibili.

- Rimuovi tutte le chiavi host SSH esistenti

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Eliminare l'account amministratore

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Elimina la password radice

   ```bash
   passwd -d root
   ```

Dopo aver completato il precedente, è possibile creare l'immagine personalizzata usando l'interfaccia della riga di comando di Azure.


**Creare un'immagine gestita normale**
```bash
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Creare una versione dell'immagine in una raccolta di immagini condivise**

```bash
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Creazione di una macchina virtuale da un'immagine che non contiene un agente Linux
Quando si crea la VM dall'immagine senza agente Linux, è necessario assicurarsi che la configurazione della distribuzione della macchina virtuale indichi che le estensioni non sono supportate in questa macchina virtuale.

> [!NOTE] 
> 
> Se non si esegue questa operazione, la piattaforma tenterà di inviare la configurazione dell'estensione e il timeout dopo 40min.

Per distribuire la VM con le estensioni disabilitate, è possibile usare l'interfaccia della riga di comando di Azure con [--enable-agent](https://docs.microsoft.com/cli/azure/vm#az-vm-create).

```bash
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

In alternativa, è possibile usare i modelli di Azure Resource Manager (ARM), impostando `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [provisioning di Linux](provisioning.md).
