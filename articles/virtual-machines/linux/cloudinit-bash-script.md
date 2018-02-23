---
title: Usare cloud-init per eseguire uno script Bash in una macchina virtuale Linux in Azure | Microsoft Docs
description: Come usare cloud-init per eseguire uno script Bash in una macchina virtuale Linux durante la creazione con l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 471749563fae5b5de6e98e22ebf2ec5cc9365368
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Usare cloud-init per eseguire uno script Bash in una macchina virtuale Linux in Azure
Questo articolo descrive come usare [cloud-init](https://cloudinit.readthedocs.io) per eseguire uno script Bash esistente in una macchina virtuale o un set di scalabilità di macchine virtuali Linux in fase di provisioning in Azure. Questi script cloud-init vengono eseguiti al primo avvio dopo il provisioning delle risorse da parte di Azure. Per altre informazioni sul funzionamento di cloud-init in modo nativo in Azure e sulle distribuzioni Linux supportate, vedere la [panoramica di cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Eseguire uno script Bash con cloud-init
Con cloud-init non è necessario convertire gli script esistenti in cloud-config, in quanto cloud-init accetta più tipi di input, tra cui uno script Bash.

Se è stata usata l'estensione di Azure di script personalizzati di Linux per eseguire gli script, è possibile eseguire la migrazione degli script in modo da usare cloud-init. Tuttavia, poiché le estensioni di Azure hanno report integrati per avvisare degli errori degli script, una distribuzione di immagini cloud-init NON ha esito negativo se lo script non riesce.

Per visualizzare questa funzionalità in azione, creare un semplice script Bash per i test. Come il file cloud-init `#cloud-config`, questo script deve essere locale rispetto alla posizione in cui verranno eseguiti i comandi AzureCLI per il provisioning della macchina virtuale.  Per questo esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. Immettere `sensible-editor simple_bash.sh` per creare il file e visualizzare un elenco degli editor disponibili. Scegliere #1 per usare l'editor **nano**. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Prima di distribuire l'immagine, è necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare ora una macchina virtuale con [az vm create](/cli/azure/vm#az_vm_create) e specificare il file script Bash con `--custom-data simple_bash.sh` in questo modo:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Verificare che lo script Bash sia stato eseguito
Stabilire una connessione SSH all'indirizzo IP pubblico della VM visualizzata nell'output del comando precedente. Immettere il valore di **publicIpAddress** in questo modo:

```bash
ssh <publicIpAddress>
```

Passare alla directory **/tmp** e verificare che il file myScript.txt sia presente e contenga il testo appropriato.  In caso contrario, è possibile controllare il file **/var/log/cloud-init.log** per altre informazioni.  Cercare la voce seguente:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi cloud-init di modifiche di configurazione, vedere i documenti seguenti:
 
- [Aggiungere un altro utente Linux a una VM](cloudinit-add-user.md)
- [Eseguire uno strumento di gestione di pacchetti per aggiornare pacchetti esistenti al primo avvio](cloudinit-update-vm.md)
- [Modificare il nome host locale della macchina virtuale](cloudinit-update-vm-hostname.md) 
- [Installare un pacchetto dell'applicazione, aggiornare i file di configurazione e inserire le chiavi](tutorial-automate-vm-deployment.md)