---
title: Riparare una macchina virtuale Linux usando i comandi di riparazione della macchina virtuale di Azure | Microsoft Docs
description: Questo articolo illustra come usare i comandi di riparazione della macchina virtuale di Azure per connettere il disco a un'altra macchina virtuale Linux, in modo da risolvere eventuali errori, e quindi ricreare la macchina virtuale originale.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: da40deb4df55a63f5fecc380500a507b374ca63d
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711143"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Riparare una macchina virtuale Linux usando i comandi di riparazione della macchina virtuale di Azure

Se nella macchina virtuale (VM) Linux in Azure viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire alcuni passaggi per la risoluzione dei problemi sul disco stesso. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo illustra come usare i comandi di riparazione della macchina virtuale di Azure per connettere il disco a un'altra macchina virtuale Linux, in modo da risolvere eventuali errori, e quindi ricreare la macchina virtuale originale.

> [!IMPORTANT]
> Gli script in questo articolo si applicano solo alle macchine virtuali che usano [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Panoramica del processo di riparazione

È ora possibile usare i comandi di riparazione della macchina virtuale di Azure per modificare il disco del sistema operativo per una macchina virtuale e non è più necessario eliminare e ricreare la macchina virtuale.

Per risolvere il problema della macchina virtuale seguire questa procedura:

1. Avviare Azure Cloud Shell
2. Eseguire az extension add/update
3. Eseguire az vm repair create
4. Eseguire passaggi di mitigazione
5. Eseguire az vm repair restore

Per altra documentazione e per istruzioni, vedere [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Esempio di processo di riparazione

1. Avviare Azure Cloud Shell

   Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

   Per aprire Cloud Shell, selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com](https://shell.azure.com).

   Selezionare **Copia** per copiare i blocchi di codice, quindi incollare il codice in Cloud Shell e premere **INVIO** per eseguirli.

   Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire ``az --version`` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Se si usano i comandi `az vm repair` per la prima volta, aggiungere l'estensione dell'interfaccia della riga di comando per la riparazione della macchina virtuale.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se in precedenza sono stati usati i comandi `az vm repair`, applicare tutti gli aggiornamenti all'estensione per la riparazione della macchina virtuale.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Eseguire `az vm repair create`. Questo comando creerà una copia del disco del sistema operativo per la macchina virtuale non funzionante, creerà una macchina virtuale di ripristino in un nuovo gruppo di risorse e collegherà la copia del disco del sistema operativo.  La macchina virtuale di ripristino avrà le stesse dimensioni e la stessa area della macchina virtuale non funzionante specificata.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Eseguire i passaggi di mitigazione necessari nella macchina virtuale di ripristino creata e quindi procedere con il passaggio 5.

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

* Se si sono verificati problemi durante la connessione alla macchina virtuale, vedere [Risolvere i problemi di connessione con Desktop remoto di una macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Per problemi relativi all'accesso alle applicazioni in esecuzione nella macchina virtuale, vedere [Risolvere i problemi di connettività delle applicazioni nelle macchine virtuali in Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
