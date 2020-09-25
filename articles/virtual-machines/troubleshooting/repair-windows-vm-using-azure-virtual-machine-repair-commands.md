---
title: Riparare una macchina virtuale Windows usando i comandi di riparazione della macchina virtuale di Azure | Microsoft Docs
description: Questo articolo illustra come usare i comandi di riparazione della macchina virtuale di Azure per connettere il disco a un'altra VM Windows per risolvere eventuali errori e quindi ricreare la VM originale.
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
ms.openlocfilehash: 66dfd198b543ec49fabe381b50174b182cf070c7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336038"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Riparare una macchina virtuale Windows usando i comandi di riparazione della macchina virtuale di Azure

Se nella macchina virtuale (VM) Windows in Azure viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire la mitigazione sul disco stesso. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo illustra come usare i comandi di riparazione della macchina virtuale di Azure per connettere il disco a un'altra VM Windows per risolvere eventuali errori e quindi ricreare la VM originale.

> [!IMPORTANT]
> * Gli script in questo articolo si applicano solo alle VM che usano [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
> * Per l'esecuzione dello script è necessaria la connettività in uscita dalla macchina virtuale (porta 443).
> * È possibile eseguire un solo script alla volta.
> * Non è possibile annullare uno script in esecuzione.
> * Il tempo massimo in cui può essere eseguito uno script è 90 minuti, dopo di che si verifica il timeout.
> * Per le macchine virtuali che usano crittografia dischi di Azure, sono supportati solo i dischi gestiti crittografati con crittografia a passaggio singolo (con o senza KEK).


## <a name="repair-process-overview"></a>Panoramica del processo di riparazione

È ora possibile usare i comandi di riparazione della macchina virtuale di Azure per modificare il disco del sistema operativo per una macchina virtuale e non è più necessario eliminare e ricreare la macchina virtuale.

Seguire questa procedura per risolvere il problema della macchina virtuale:

1. Avviare Azure Cloud Shell
2. Eseguire az extension add/update.
3. Eseguire az vm repair create.
4. Eseguire az vm repair run.
5. Eseguire az vm repair restore.

Per ulteriori documenti e istruzioni, vedere [az vm repair](/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Esempio di processo di riparazione

1. Avviare Azure Cloud Shell

   Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

   Per aprire Cloud Shell, selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com](https://shell.azure.com).

   Selezionare **Copia** per copiare i blocchi di codice, quindi incollare il codice in Cloud Shell e premere **INVIO** per eseguirli.

   Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire ``az --version`` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
   
   Se è necessario eseguire l'accesso a Cloud Shell con un account diverso da quello attualmente connesso al portale di Azure con, è possibile usare il comando ``az login`` [AZ login Reference](/cli/azure/reference-index?view=azure-cli-latest#az-login).  Per spostarsi tra le sottoscrizioni associate all'account, è possibile usare ``az account set --subscription`` [AZ account Set Reference](/cli/azure/account?view=azure-cli-latest#az-account-set).

2. Se si usano i comandi `az vm repair` per la prima volta, aggiungere l'estensione dell'interfaccia della riga di comando per la riparazione della macchina virtuale.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se in precedenza sono stati usati i comandi `az vm repair`, applicare tutti gli aggiornamenti all'estensione per la riparazione della macchina virtuale.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Eseguire `az vm repair create`. Questo comando creerà una copia del disco del sistema operativo per la macchina virtuale non funzionante, creerà una macchina virtuale di ripristino in un nuovo gruppo di risorse e collegherà la copia del disco del sistema operativo.  La macchina virtuale di ripristino avrà le stesse dimensioni e la stessa area della macchina virtuale non funzionante specificata. Il gruppo di risorse e il nome della VM usati in tutti i passaggi varranno per la VM non funzionale. Se la macchina virtuale usa crittografia dischi di Azure, il comando tenterà di sbloccare il disco crittografato in modo che sia accessibile quando viene collegato alla macchina virtuale di ripristino.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password 'password!234' --verbose
   ```

4. Eseguire `az vm repair run`. Questo comando eseguirà lo script di ripristino specificato sul disco collegato tramite la macchina virtuale di ripristino. Se la guida alla risoluzione dei problemi in uso specifica un ID di esecuzione, usarlo qui, altrimenti è possibile usare `az vm repair list-scripts` per visualizzare gli script di ripristino disponibili. Il gruppo di risorse e il nome della VM usati qui sono per la VM non funzionale usata nel passaggio 3.

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id win-hello-world --verbose
   ```

5. Eseguire `az vm repair restore`. Questo comando scambia il disco del sistema operativo riparato con il disco del sistema operativo originale della macchina virtuale. Il gruppo di risorse e il nome della VM usati qui sono per la VM non funzionale usata nel passaggio 3.

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

* Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](./troubleshoot-rdp-connection.md).
* Per problemi relativi all'accesso alle applicazioni in esecuzione nella macchina virtuale, vedere [Risolvere i problemi di connettività delle applicazioni nelle macchine virtuali in Azure](./troubleshoot-app-connection.md).
* Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).
