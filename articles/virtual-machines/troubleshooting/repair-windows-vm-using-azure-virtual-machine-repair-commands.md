---
title: Ripristinare una VM Windows usando i comandi di ripristino della macchina virtuale di Azure | Microsoft Docs
description: Questo articolo illustra come usare i comandi di ripristino delle macchine virtuali di Azure per connettere il disco a un'altra VM Windows per risolvere eventuali errori e quindi ricompilare la VM originale.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: d942f3861eb2fcc4e096248d495b2db2d8119ea1
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71132104"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Ripristinare una VM Windows usando i comandi di ripristino della macchina virtuale di Azure

Se la macchina virtuale (VM) Windows in Azure rileva un errore di avvio o del disco, potrebbe essere necessario eseguire la mitigazione sul disco. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo illustra come usare i comandi di ripristino delle macchine virtuali di Azure per connettere il disco a un'altra VM Windows per risolvere eventuali errori e quindi ricompilare la VM originale.

> [!IMPORTANT]
> Gli script in questo articolo si applicano solo alle macchine virtuali che usano [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Panoramica del processo di ripristino

È ora possibile usare i comandi di ripristino delle macchine virtuali di Azure per modificare il disco del sistema operativo per una macchina virtuale e non è più necessario eliminare e ricreare la macchina virtuale.

Per risolvere il problema della macchina virtuale, seguire questa procedura:

1. Avviare Azure Cloud Shell
2. Eseguire AZ Extension Add/Update.
3. Eseguire AZ VM Repair create.
4. Eseguire AZ VM Repair Run.
5. Eseguire AZ VM Repair Restore.

Per ulteriori documentazione e istruzioni, vedere [AZ VM Repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Esempio di processo di ripristino

> [!NOTE]
> * Per l'esecuzione dello script è necessaria la connettività in uscita dalla macchina virtuale (porta 443).
> * Solo uno script può essere eseguito alla volta.
> * Non è possibile annullare uno script in esecuzione.
> * Il tempo massimo di esecuzione di uno script è di 90 minuti, dopo il quale si otterrà il timeout.

1. Avviare Azure Cloud Shell

   Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include gli strumenti di Azure comuni preinstallati e configurati per l'uso con l'account.

   Per aprire la Cloud Shell, selezionare **prova** dall'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda del browser separata visitando [https://shell.azure.com](https://shell.azure.com).

   Selezionare **copia** per copiare i blocchi di codice, quindi incollare il codice nel cloud Shell e premere **invio** per eseguirlo.

   Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire ``az --version`` per trovare la versione. Per installare o aggiornare l'interfaccia della riga di comando di Azure, vedere [installare l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli)della riga di comando di Azure.

2. Se è la prima volta che si usano i `az vm repair` comandi, aggiungere l'estensione dell'interfaccia della riga di comando per la riparazione della VM.

   ```azurepowershell-interactive
   az extension add -n vm-repair
   ```

   Se in precedenza sono stati usati `az vm repair` i comandi, applicare tutti gli aggiornamenti all'estensione per la riparazione della VM.

   ```azurepowershell-interactive
   az extension update -n vm-repair
   ```

3. Eseguire `az vm repair create`. Questo comando creerà una copia del disco del sistema operativo per la macchina virtuale non funzionante, creerà una macchina virtuale di ripristino e collegherà il disco.

   ```azurepowershell-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Eseguire `az vm repair run`. Questo comando eseguirà lo script di ripristino specificato sul disco collegato tramite la macchina virtuale di ripristino.

   ```azurepowershell-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. Eseguire `az vm repair restore`. Questo comando scambia il disco del sistema operativo riparato con il disco del sistema operativo originale della macchina virtuale.

   ```azurepowershell-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verificare e abilitare la diagnostica di avvio

L'esempio seguente abilita l'estensione di diagnostica nella macchina virtuale denominata ``myVMDeployed`` nel gruppo di risorse ``myResourceGroup``:

Interfaccia della riga di comando di Azure

```azurepowershell-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Passaggi successivi

* Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere [risolvere i problemi di connettività delle applicazioni nelle macchine virtuali in Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
