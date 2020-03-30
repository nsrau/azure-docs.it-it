---
title: Dati personalizzati e macchine virtuali di AzureCustom data and Azure Virtual Machines
description: Dettagli sull'uso di dati personalizzati e Cloud-Init in macchine virtuali di AzureDetails on using Custom data and Cloud-Init on Azure Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109628"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dati personalizzati e Cloud-Init nelle macchine virtuali di AzureCustom data and Cloud-Init on Azure Virtual Machines

## <a name="what-is-custom-data"></a>Che cosa sono i dati personalizzati?

I clienti spesso chiedono come possono inserire uno script o altri metadati in una macchina virtuale di Microsoft Azure in fase di provisioning.  In altri cloud, questo concetto viene spesso definito dati utente.  In Microsoft Azure è presente una funzionalità simile denominata dati personalizzati. 

I dati personalizzati vengono resi disponibili per la macchina virtuale solo durante il primo avvio/configurazione iniziale, chiamiamo questo 'provisioning'. Il provisioning è il processo in cui i parametri di creazione della macchina virtuale (ad esempio, nome host, nome utente, password, certificati, dati personalizzati, chiavi e così via) vengono resi disponibili per la macchina virtuale e un agente di provisioning li elabora, ad esempio [l'agente Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) e [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Passaggio di dati personalizzati alla macchina virtualePassing custom data to the VM
Per usare i dati personalizzati, è necessario codificare il contenuto in base64 prima di passarlo all'API, a meno che non si utilizzi uno strumento CLI che esegue la conversione per l'utente, ad esempio l'interfaccia della riga di comando. La dimensione non può superare i 64 KB.

In CLI è possibile passare i dati personalizzati come file e verranno convertiti in base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

In Azure Resource Manager (ARM) è presente una [funzione base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

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
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>Elaborazione di dati personalizzati
Gli agenti di provisioning installati nelle macchine virtuali gestiscono l'interfacciamento con la piattaforma e la inserisce nel file system. 

### <a name="windows"></a>WINDOWS
I dati personalizzati vengono inseriti in *%SYSTEMDRIVE% , AzureData , CustomData.bin* come file binario, ma non vengono elaborati. Se si desidera elaborare questo file, è necessario compilare un'immagine personalizzata e scrivere codice per elaborare il file CustomData.bin.

### <a name="linux"></a>Linux  
Nel sistema operativo Linux, i dati personalizzati vengono passati alla macchina virtuale tramite il file ovf-env.xml, che viene copiato nella directory */var/lib/waagent* durante il provisioning.  Le versioni più recenti dell'agente Linux di Microsoft Azure copieranno anche i dati con codifica base64 in */var/lib/waagent/CustomData* per comodità.

Azure supporta attualmente due agenti di provisioning:Azure currently supports two provisioning agents:
* Agente Linux: per impostazione predefinita l'agente non elaborerà i dati personalizzati, sarà necessario creare un'immagine personalizzata con l'abilitazione. Le impostazioni pertinenti, in base alla [documentazione,](https://github.com/Azure/WALinuxAgent#configuration) sono:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Quando si abilitano i dati personalizzati e si esegue uno script, si ritarderà la segnalazione della macchina virtuale pronta o che il provisioning è riuscito fino al completamento dello script. Se lo script supera la quantità totale di tempo di provisioning della macchina virtuale di 40 minuti, la creazione della macchina virtuale avrà esito negativo. Si noti che se lo script non viene eseguito o gli errori durante l'esecuzione non viene considerato un errore di provisioning irreversibile, sarà necessario creare un percorso di notifica per avvisare l'utente per lo stato di completamento dello script.

Per risolvere i problemi di esecuzione dei dati personalizzati, *esaminare /var/log/waagent.log*

* cloud-init - Per impostazione predefinita elaborerà i dati personalizzati per impostazione predefinita, cloud-init accetta [più formati](https://cloudinit.readthedocs.io/en/latest/topics/format.html) di dati personalizzati, come la configurazione cloud-init, script e così via. Simile all'agente Linux, quando cloud-init elabora i dati personalizzati. Se si verificano errori durante l'esecuzione dell'elaborazione della configurazione o degli script, non viene considerato un errore irreversibile di provisioning e sarà necessario creare un percorso di notifica per avvisare l'utente per lo stato di completamento dello script. Tuttavia, diverso dall'agente Linux, cloud-init non attende le configurazioni dei dati personalizzati dell'utente per completare prima di segnalare alla piattaforma che la macchina virtuale è pronta. Per altre informazioni su cloud-init in azure, vedere la [documentazione](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Per risolvere i problemi relativi all'esecuzione dei dati personalizzati, consultare la [documentazione relativa](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)alla risoluzione dei problemi .


## <a name="faq"></a>Domande frequenti
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>È possibile aggiornare i dati personalizzati dopo la creazione della macchina virtuale?
Per le singole macchine virtuali, i dati personalizzati nel modello di macchina virtuale non possono essere aggiornati, ma per VMSS, è possibile aggiornare i dati personalizzati di VMSS tramite l'API REST (non applicabile per i client PS o a . Quando si aggiornano i dati personalizzati nel modello VMSS:When you update custom data in the VMSS model:
* Le istanze esistenti in VMSS non otterranno i dati personalizzati aggiornati, ma fino a quando non vengono ricreate.
* Le istanze esistenti in VMSS aggiornate non otterranno i dati personalizzati aggiornati.
* Le nuove istanze riceveranno i nuovi dati personalizzati.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>È possibile inserire valori sensibili nei dati personalizzati?
Si consiglia di **non** archiviare dati sensibili in dati personalizzati. Per altre informazioni, vedere Procedure consigliate per la sicurezza e la crittografia di Azure.For more information, see [Azure Security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>I dati personalizzati sono resi disponibili in IMDS?
No, questa funzione non è attualmente disponibile.
