---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - montaggio del disco del sistema operativo | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - montaggio del disco del sistema operativo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: cdd0a11e872d81dfbb35c7a0cbfa2e1f7234dc08
ms.lasthandoff: 03/21/2017

---

# <a name="troubleshoot-a-vms-operating-system-disk"></a>Risolvere i problemi del disco del sistema operativo della VM

Questo script consente di montare il disco del sistema operativo di una macchina virtuale in cui si è verificato un errore o un problema come disco dati in una seconda macchina virtuale. Può essere utile quando si esegue la risoluzione dei problemi del disco o il ripristino di dati. 

Se necessario, installare l'interfaccia della riga di comando di Azure usando l'istruzione presente nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e quindi eseguire `az login` per creare una connessione con Azure. È necessario anche disporre di una macchina virtuale esistente. Aggiornare il nome e gruppo di risorse della macchina virtuale esistente nello script di esempio.

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Creazione rapida della macchina virtuale")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm#show) | Recupera un elenco di macchine virtuali. In questo caso, l'opzione di query viene usata per restituire il disco del sistema operativo della macchina virtuale. Questo valore viene quindi aggiunto a un nome di variabile 'uri'. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm#delete) | Consente di eliminare una macchina virtuale. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Consente di creare una macchina virtuale.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) | Collega un disco a una macchina virtuale. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | Restituisce gli indirizzi IP di una macchina virtuale. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

