---
title: Dati personalizzati e macchine virtuali di Azure
description: Dettagli sull'uso di dati personalizzati e cloud-init in macchine virtuali di Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759131"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dati personalizzati e cloud-init in macchine virtuali di Azure

## <a name="what-is-custom-data"></a>Informazioni sui dati personalizzati

I clienti spesso chiedono come possono inserire uno script o altri metadati in una macchina virtuale Microsoft Azure in fase di provisioning.  In altri cloud, questo concetto viene spesso definito dati utente.  In Microsoft Azure, è presente una funzionalità simile denominata dati personalizzati. 

I dati personalizzati vengono resi disponibili alla macchina virtuale solo durante il primo avvio o la configurazione iniziale, ovvero "provisioning". Il provisioning è il processo in cui la VM crea parametri, ad esempio nome host, nome utente, password, certificati, dati personalizzati, chiavi e così via, vengono resi disponibili alla macchina virtuale e un agente di provisioning li elabora, ad esempio l' [agente Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) e [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Passaggio di dati personalizzati alla macchina virtuale
Per usare dati personalizzati, è necessario codificare prima il contenuto con Base64 prima di passarlo all'API, a meno che non si stia usando uno strumento dell'interfaccia della riga di comando che esegue la conversione, ad esempio AZ CLI. La dimensione non può superare 64 KB.

Nell'interfaccia della riga di comando è possibile passare i dati personalizzati come file e verranno convertiti in Base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

In Azure Resource Manager (ARM) è disponibile una [funzione Base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Elaborazione di dati personalizzati
Gli agenti di provisioning installati nelle VM gestiscono l'interazione con la piattaforma e lo inseriscono nella file system. 

### <a name="windows"></a>Windows
I dati personalizzati vengono inseriti in *%SystemDrive%\AzureData\CustomData.bin* come file binario, ma non vengono elaborati. Per elaborare il file, è necessario compilare un'immagine personalizzata e scrivere codice per elaborare CustomData. bin.

### <a name="linux"></a>Linux  
Nei sistemi operativi Linux i dati personalizzati vengono passati alla macchina virtuale tramite il file OVF-ENV. XML, che viene copiato nella directory */var/lib/waagent* durante il provisioning.  Le versioni più recenti dell'agente di Microsoft Azure Linux copiano anche i dati con codifica Base64 in */var/lib/waagent/CustomData* e per praticità.

Azure supporta attualmente due agenti di provisioning:
* Agente Linux: per impostazione predefinita l'agente non elaborerà dati personalizzati, sarà necessario creare un'immagine personalizzata con l'agente abilitato. Le impostazioni rilevanti, in base alla [documentazione](https://github.com/Azure/WALinuxAgent#configuration) sono:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Quando si abilitano i dati personalizzati ed è necessario eseguire uno script, il report della macchina virtuale viene ritardato o il provisioning ha avuto esito positivo fino al completamento dello script. Se lo script supera il limite di tempo totale di provisioning della macchina virtuale di 40 minuti, la creazione della macchina virtuale avrà esito negativo. Si noti che, se l'esecuzione dello script non riesce o si verificano errori durante l'esecuzione, non viene considerato un errore di provisioning irreversibile, sarà necessario creare un percorso di notifica per ricevere un avviso per lo stato di completamento dello script.

Per risolvere i problemi relativi all'esecuzione personalizzata dei dati, vedere */var/log/waagent.log*

* cloud-init: per impostazione predefinita, i dati personalizzati vengono elaborati per impostazione predefinita, cloud-init accetta [più formati](https://cloudinit.readthedocs.io/en/latest/topics/format.html) di dati personalizzati, ad esempio configurazione cloud-init, script e così via. Simile all'agente Linux, quando cloud-init elabora i dati personalizzati. Se si verificano errori durante l'esecuzione dell'elaborazione o degli script di configurazione, non viene considerato un errore di provisioning irreversibile ed è necessario creare un percorso di notifica per ricevere un avviso per lo stato di completamento dello script. Tuttavia, diverso dall'agente Linux, cloud-init non attende il completamento delle configurazioni dei dati personalizzate dell'utente prima di segnalare alla piattaforma che la macchina virtuale è pronta. Per altre informazioni su cloud-init in Azure, vedere la [documentazione](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Per risolvere i problemi relativi all'esecuzione personalizzata dei dati, vedere la [documentazione](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)sulla risoluzione dei problemi.


## <a name="faq"></a>Domande frequenti
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>È possibile aggiornare i dati personalizzati dopo la creazione della macchina virtuale?
Per le singole macchine virtuali, i dati personalizzati nel modello di macchina virtuale non possono essere aggiornati, ma per VMSS è possibile aggiornare i dati personalizzati di VMSS tramite l'API REST (non applicabile ai client PS o AZ CLI). Quando si aggiornano i dati personalizzati nel modello VMSS:
* Le istanze esistenti in VMSS non otterranno i dati personalizzati aggiornati, solo fino a quando non vengono ricreati l'immagine.
* Le istanze esistenti in VMSS che vengono aggiornate non otterranno i dati personalizzati aggiornati.
* Le nuove istanze riceveranno i nuovi dati personalizzati.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>È possibile inserire valori sensibili nei dati personalizzati?
Si consiglia di **non** archiviare dati sensibili nei dati personalizzati. Per altre informazioni, vedere [procedure consigliate per la sicurezza e la crittografia di Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>I dati personalizzati sono resi disponibili in IMDS?
No, questa funzionalità non è attualmente disponibile.
