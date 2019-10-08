---
title: Esercitazione - Monitorare le macchine virtuali Linux in Azure | Microsoft Docs
description: Questa esercitazione illustra come monitorare le prestazioni e i componenti delle applicazioni individuati in esecuzione nelle macchine virtuali Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9c6458eea2b1352e7d13ea6691eac4498182ecd3
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679405"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Esercitazione: Monitorare una macchina virtuale Linux in Azure

Monitoraggio di Azure usa gli agenti per raccogliere dati di avvio e sulle prestazioni dalle macchine virtuali di Azure, archiviare tali dati in Archiviazione di Azure e renderli accessibili tramite il portale, il modulo Azure PowerShell e l'interfaccia della riga di comando di Azure. Monitoraggio di Azure per le macchine virtuali include anche il monitoraggio avanzato, con la raccolta delle metriche delle prestazioni e l'individuazione dei componenti delle applicazioni installati nella macchina virtuale e comprende i grafici delle prestazioni e la mappa delle dipendenze.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host della macchina virtuale
> * Abilita Monitoraggio di Azure per le macchine virtuali
> * Visualizzare le metriche delle prestazioni della macchina virtuale
> * Creare un avviso

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Creare una macchina virtuale

Per visualizzare la diagnostica e le metriche in azione, è necessaria una macchina virtuale. Creare prima un gruppo di risorse con [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupMonitor* nella località *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Creare quindi una macchina virtuale con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). L'esempio seguente crea una macchina virtuale denominata *myVM* e genera chiavi SSH *~/.ssh/* , se non esistono ancora:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Abilitare la diagnostica di avvio

All'avvio delle macchine virtuali Linux, l'estensione diagnostica di avvio acquisisce l'output e lo memorizza nell'Archiviazione di Azure. Questi dati possono essere usati per risolvere i problemi di avvio della macchina virtuale. La diagnostica di avvio non viene abilitata automaticamente quando si crea una macchina virtuale Linux tramite l'interfaccia della riga di comando di Azure.

Prima di abilitare la diagnostica di avvio, è necessario creare un account di archiviazione per l'archiviazione dei log di avvio. Gli account di archiviazione devono avere un nome univoco globale, che abbia tra i 3 e i 24 caratteri e devono contenere solo numeri e lettere minuscole. Creare un account di archiviazione con il comando [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). In questo esempio, viene usata una stringa casuale per creare un nome univoco per l'account di archiviazione.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Quando si abilita la diagnostica di avvio, è necessario l'URI per il contenitore di archiviazione BLOB. Il comando seguente esegue una query sull'account di archiviazione per restituire questo URI. Il valore URI viene archiviato nei nomi di variabile *bloburi* e verrà usato nel passaggio successivo.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Abilitare ora la diagnostica di avvio con [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Il valore `--storage` è l'URI del BLOB raccolto nel passaggio precedente.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Visualizzare la diagnostica di avvio

Quando la diagnostica di avvio è abilitata, ogni volta che si arresta e si avvia la macchina virtuale, in un file log vengono scritte le informazioni sul processo di avvio. Per questo esempio, deallocare prima la macchina virtuale con il comando [az vm deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) come illustrato di seguito:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Ora avviare la macchina virtuale con il comando [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) come illustrato di seguito:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

È possibile ottenere i dati della diagnostica di avvio per *myVM* con il comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) come illustrato di seguito:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Visualizzare le metriche host

Una macchina virtuale Linux è un host dedicato in Azure con cui interagisce. Le metriche per l'host vengono raccolte automaticamente e possono essere visualizzate nel portale di Azure come indicato di seguito:

1. Nel portale di Azure selezionare **Gruppi di risorse**, scegliere **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.
1. Per visualizzare le prestazioni della macchina virtuale host, selezionare **Metriche** nella finestra della macchina virtuale, quindi selezionare una qualsiasi metrica *[Host]* in **Metriche disponibili**.

    ![Visualizzare le metriche host](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Abilitare il monitoraggio avanzato

Per abilitare il monitoraggio della macchina virtuale di Azure con Monitoraggio di Azure per le macchine virtuali:

1. Nel portale di Azure, fare clic su **Gruppi di risorse** selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.

2. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)** .

3. Nella pagina **Insights (anteprima)** selezionare **Prova adesso**.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  

    Nell'elenco sono preselezionati l'area di lavoro predefinita e la località in cui è distribuita la macchina virtuale nella sottoscrizione. 

    >[!NOTE]
    >Per creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio della macchina virtuale, vedere [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). L'area di lavoro Log Analytics deve risiedere in una delle [aree supportate](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Dopo aver abilitato il monitoraggio, potrebbe essere necessario attendere alcuni minuti prima di poter visualizzare le metriche delle prestazioni per la macchina virtuale.

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Visualizzare le metriche delle prestazioni della macchina virtuale

Monitoraggio di Azure per le macchine virtuali include un set di grafici delle prestazioni che rappresentano diversi indicatori di prestazioni chiave (KPI) per stabilire l'efficacia delle prestazioni di una macchina virtuale. Per accedere alla macchina virtuale, seguire questa procedura.

1. Nel portale di Azure, fare clic su **Gruppi di risorse** selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.

2. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)** .

3. Selezionare la scheda **Prestazioni**.

Questa pagina non solo include i grafici di utilizzo delle prestazioni, ma anche una tabella che mostra capacità, utilizzo e media totale di ogni disco logico individuato per ogni misura.

## <a name="create-alerts"></a>Creare avvisi

È possibile creare avvisi basati sulle metriche di prestazioni specifiche. Gli avvisi possono essere usati, ad esempio, per inviare una notifica quando l'uso medio della CPU supera una determinata soglia o lo spazio su disco disponibile è inferiore a una determinata quantità. Gli avvisi vengono visualizzati nel portale di Azure o possono essere inviati tramite posta elettronica. In risposta agli avvisi generati, è anche possibile attivare i runbook di Automazione di Azure o App per la logica di Azure.

L'esempio seguente crea un avviso per l'uso medio della CPU.

1. Nel portale di Azure, fare clic su **Gruppi di risorse** selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.

2. Nel pannello della macchina virtuale fare clic su **Regole di avviso**, quindi fare clic su **Aggiungi avviso per la metrica** nella parte superiore del pannello degli avvisi.

3. Inserire un **Nome** per l'avviso, ad esempio *myAlertRule*

4. Per attivare un avviso quando la percentuale di CPU supera 1.0 per cinque minuti, lasciare tutte le altre impostazioni predefinite selezionate.

5. Facoltativamente, è possibile selezionare la casella per *Invia messaggio di posta elettronica a proprietari, collaboratori e lettori* per inviare una notifica tramite posta elettronica. L'azione predefinita è di presentare una notifica nel portale.

6. Fare clic sul pulsante **OK**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state configurate e visualizzate le prestazioni della macchina virtuale. Si è appreso come:

> [!div class="checklist"]
> * Creare un gruppo di risorse e una macchina virtuale
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host
> * Abilita Monitoraggio di Azure per le macchine virtuali
> * Visualizzare le metriche della macchina virtuale
> * Creare un avviso

Passare all'esercitazione successiva per informazioni sul Centro sicurezza di Azure.

> [!div class="nextstepaction"]
> [Gestire la sicurezza delle VM](../../security/fundamentals/overview.md)
