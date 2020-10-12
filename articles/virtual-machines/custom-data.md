---
title: Dati personalizzati e Macchine virtuali di Microsoft Azure
description: Informazioni dettagliate sull'uso di dati personalizzati e cloud-init in Macchine virtuali di Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284915"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dati personalizzati e cloud-init in Macchine virtuali di Azure

Potrebbe essere necessario inserire uno script o altri metadati in una macchina virtuale Microsoft Azure in fase di provisioning.  In altri cloud, questo concetto viene spesso indicato con il termine dati utente.  In Microsoft Azure, è presente una funzionalità simile denominata dati personalizzati. 

I dati personalizzati vengono resi disponibili alla macchina virtuale solo durante il primo avvio o la configurazione iniziale, vale a dire durante il "provisioning". Il provisioning è il processo in cui i parametri di creazione della VM (ad esempio nome host, nome utente, password, certificati, dati personalizzati, chiavi, ecc.) vengono resi disponibili alla VM ed elaborati da un agente di provisioning, ad esempio [Agente Linux](./extensions/agent-linux.md) e [cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Passaggio dei dati personalizzati alla VM
Per usare i dati personalizzati, è necessario codificare prima il contenuto con base64 prima di passarlo all'API, a meno che non si stia usando uno strumento dell'interfaccia della riga di comando che esegue la conversione in automatico, ad esempio AZ CLI. Le dimensioni del file non possono superare i 64 kB.

Nell'interfaccia della riga di comando è possibile passare i dati personalizzati come file, che verrà convertito in base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

In Azure Resource Manager (ARM) è presente una [funzione base64](../azure-resource-manager/templates/template-functions-string.md#base64).

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

## <a name="processing-custom-data"></a>Elaborazione dei dati personalizzati
Gli agenti di provisioning installati nelle VM gestiscono l'interazione con la piattaforma e inseriscono il file dei dati personalizzati nel file system. 

### <a name="windows"></a>Windows
I dati personalizzati vengono inseriti in *%SYSTEMDRIVE%\AzureData\CustomData.bin* come file binario, ma non vengono elaborati. Per elaborare il file, è necessario compilare un'immagine personalizzata e scrivere codice per elaborare CustomData.bin.

### <a name="linux"></a>Linux  
Nei sistemi operativi Linux, i dati personalizzati vengono passati alla macchina virtuale tramite il file ovf-env.xml, che viene copiato nella directory */var/lib/waagent* durante il provisioning.  Le versioni più recenti dell'agente Linux di Microsoft Azure copiano i dati con codifica base64 anche in */var/lib/waagent/CustomData* per praticità.

Azure supporta attualmente due agenti di provisioning:
* Agente Linux: per impostazione predefinita l'agente non elaborerà i dati personalizzati. Sarà necessario creare un'immagine personalizzata con l'agente abilitato. Le impostazioni rilevanti, in base alla [documentazione](https://github.com/Azure/WALinuxAgent#configuration) sono:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Quando si abilitano i dati personalizzati e si esegue uno script, la segnalazione da parte della VM della disponibilità o dell’esito positivo del provisioning verrà ritardata fino al completamento dello script. Se lo script supera il limite di tempo totale di provisioning della macchina virtuale consentito di 40 minuti, la creazione della macchina virtuale avrà esito negativo. Notare che, se l'esecuzione dello script non riesce o si verificano errori durante l'esecuzione, non viene considerato un errore di provisioning irreversibile, sarà necessario creare un percorso di notifica per ricevere un avviso per lo stato di completamento dello script.

Per risolvere i problemi di esecuzione dei dati personalizzati, esaminare il file */var/log/waagent.log*

* cloud-init: elabora i dati personalizzati per impostazione predefinita. Cloud-init accetta [più formati](https://cloudinit.readthedocs.io/en/latest/topics/format.html) di dati personalizzati, ad esempio la configurazione di cloud-init, gli script e così via. Come avviene con l'agente Linux, quando cloud-init elabora i dati personalizzati. Se si verificano errori durante l'esecuzione dell'elaborazione o degli script di configurazione, non viene considerato un errore di provisioning irreversibile ed è necessario creare un percorso di notifica per ricevere un avviso per lo stato di completamento dello script. Tuttavia, a differenza dell'agente Linux, cloud-init non attende il completamento delle configurazioni dei dati personalizzati dell'utente prima di segnalare alla piattaforma che la macchina virtuale è pronta. Per altre informazioni su cloud-init in Azure, esaminare la [documentazione](./linux/using-cloud-init.md).


Per risolvere i problemi relativi all'esecuzione dei dati personalizzati, vedere la [documentazione](./linux/using-cloud-init.md#troubleshooting-cloud-init) relativa alla risoluzione dei problemi.


## <a name="faq"></a>Domande frequenti
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>È possibile aggiornare i dati personalizzati dopo la creazione della macchina virtuale?
Per le singole macchine virtuali, i dati personalizzati nel modello di macchina virtuale non possono essere aggiornati, ma, per il set di scalabilità di macchine virtuali di Microsoft Azure, è possibile aggiornare i dati personalizzati del set di scalabilità di macchine virtuali tramite [API REST](/rest/api/compute/virtualmachinescalesets/update) (non applicabile ai client PS o AZ CLI). Quando si aggiornano i dati personalizzati nel modello del set di scalabilità di macchine virtuali:
* Le istanze esistenti nel set di scalabilità di macchine virtuali otterranno i dati personalizzati aggiornati solo quando ne verrà ricreata l'immagine.
* Le istanze esistenti nel set di scalabilità di macchine virtuali che vengono sottoposte ad upgrade non otterranno i dati personalizzati aggiornati.
* Le nuove istanze riceveranno i nuovi dati personalizzati.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>È possibile inserire valori sensibili nei dati personalizzati?
Si consiglia di **non** archiviare dati sensibili nei dati personalizzati. Per altre informazioni, vedere [Procedure consigliate per la sicurezza e la crittografia di Azure](../security/fundamentals/data-encryption-best-practices.md).


### <a name="is-custom-data-made-available-in-imds"></a>I dati personalizzati sono resi disponibili in IMDS?
No. Questa funzionalità non è attualmente disponibile.
