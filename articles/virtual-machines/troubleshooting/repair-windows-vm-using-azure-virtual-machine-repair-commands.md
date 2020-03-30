---
title: Ripristinare una macchina virtuale Windows usando i comandi di ripristino della macchina virtuale di Azure. Documenti Microsoft
description: Questo articolo descrive in dettaglio come usare i comandi di ripristino della macchina virtuale di Azure per connettere il disco a un'altra macchina virtuale Windows per correggere eventuali errori, quindi ricompilare la macchina virtuale originale.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 2055558ef80a641084a7cf9d299281497d282936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060682"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Riparare una macchina virtuale Windows usando i comandi di riparazione della macchina virtuale di Azure

Se la macchina virtuale Windows (VM) in Azure rileva un errore di avvio o disco, potrebbe essere necessario eseguire l'attenuazione del disco stesso. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo descrive in dettaglio come usare i comandi di ripristino della macchina virtuale di Azure per connettere il disco a un'altra macchina virtuale Windows per correggere eventuali errori, quindi ricompilare la macchina virtuale originale.

> [!IMPORTANT]
> Gli script in questo articolo si applicano solo alle macchine virtuali che usano [Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="repair-process-overview"></a>Panoramica del processo di riparazione

È ora possibile usare i comandi di ripristino della macchina virtuale di Azure per modificare il disco del sistema operativo per una macchina virtuale e non è più necessario eliminare e ricreare la macchina virtuale.

Seguire questi passaggi per risolvere il problema della macchina virtuale:Follow these steps to troubleshoot the VM issue:

1. Avviare Azure Cloud Shell
2. Eseguire az extension add/update.
3. Eseguire az vm repair create.
4. Eseguire az vm repair run.
5. Eseguire az vm repair restore restore.

Per ulteriori documentazione e istruzioni, vedere [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Esempio di processo di riparazione

> [!NOTE]
> * Per l'esecuzione dello script è necessaria la connettività in uscita dalla macchina virtuale (porta 443).
> * È possibile eseguire un solo script alla volta.
> * Uno script in esecuzione non può essere annullato.
> * Il tempo massimo di esecuzione di uno script è 90 minuti, dopo i quali si stimerà.

1. Avviare Azure Cloud Shell

   Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

   Per aprire Cloud Shell, selezionare **Provanellto** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in [https://shell.azure.com](https://shell.azure.com)una scheda separata del browser visitando .

   Selezionare **Copia** per copiare i blocchi di codice, quindi incollare il codice in Cloud Shell e selezionare **Invio** per eseguirlo.

   Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire ``az --version`` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando di Azure, vedere Installare l'interfaccia della riga di comando di Azure.If you need to install or upgrade your Azure CLI, see [Install Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli)

2. Se è la prima volta `az vm repair` che si utilizzano i comandi, aggiungere l'estensione cli-to-vm-repair.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se i `az vm repair` comandi sono stati utilizzati in precedenza, applicare eventuali aggiornamenti all'estensione vm-repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Eseguire `az vm repair create`. Questo comando creerà una copia del disco del sistema operativo per la macchina virtuale non funzionale, creerà una macchina virtuale di ripristino e allegherà il disco.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Eseguire `az vm repair run`. Questo comando eseguirà lo script di ripristino specificato sul disco collegato tramite la macchina virtuale di ripristino.

   ```azurecli-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. Eseguire `az vm repair restore`. Questo comando scambia il disco del sistema operativo riparato con il disco del sistema operativo originale della macchina virtuale.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verificare e abilitare la diagnostica di avvio

L'esempio seguente abilita l'estensione di diagnostica nella macchina virtuale denominata ``myVMDeployed`` nel gruppo di risorse ``myResourceGroup``:

Interfaccia della riga di comando di Azure

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Passaggi successivi

* Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Per problemi con l'accesso alle applicazioni in esecuzione nella macchina virtuale, vedere Risolvere i problemi di connettività delle applicazioni nelle macchine virtuali in Azure.For issues with accessing applications running on your VM, see [Troubleshoot application connectivity issues on virtual machines in Azure.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)
* Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
