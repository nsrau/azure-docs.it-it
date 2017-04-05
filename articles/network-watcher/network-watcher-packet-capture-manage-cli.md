---
title: Gestire le acquisizioni di pacchetti con Azure Network Watcher - Interfaccia della riga di comando di Azure | Documentazione Microsoft
description: "Questa pagina illustra come gestire la funzionalità di acquisizione di pacchetti di Network Watcher tramite l&quot;interfaccia della riga di comando di Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: e2f37514cc2cc45192783d2f542320879016df37
ms.lasthandoff: 03/28/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli"></a>Gestire le acquisizioni di pacchetti con Azure Network Watcher tramite l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI](network-watcher-packet-capture-manage-cli.md)
> - [API REST di Azure](network-watcher-packet-capture-manage-rest.md)

Il servizio di acquisizione di pacchetti di Network Watcher consente di creare sessioni di acquisizione per registrare il traffico da e verso una macchina virtuale. Sono disponibili filtri per la sessione di acquisizione per garantire che venga acquisito solo il traffico desiderato. Il servizio di acquisizione di pacchetti consente di individuare eventuali anomalie di rete in modo proattivo e reattivo. Altri utilizzi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora. La possibilità di attivare da remoto l'acquisizione di pacchetti evita di dover eseguire manualmente questa operazione sul computer desiderato, consentendo un notevole risparmio di tempo.

Questo articolo usa l'interfaccia della riga di comando di Azure 1.0 multipiattaforma, disponibile per Windows, Mac e Linux. Network Watcher usa attualmente l'interfaccia della riga di comando di Azure 1.0 per il supporto dell'interfaccia della riga di comando.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

Questo articolo illustra le diverse attività di gestione attualmente disponibili per l'acquisizione di pacchetti.

- [**Avviare un'acquisizione di pacchetti**](#start-a-packet-capture)
- [**Interrompere un'acquisizione di pacchetti**](#stop-a-packet-capture)
- [**Eliminare un'acquisizione di pacchetti**](#delete-a-packet-capture)
- [**Scaricare un'acquisizione di pacchetti**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente disponga delle risorse seguenti:

- Un'istanza di Network Watcher nell'area in cui creare un'acquisizione di pacchetti.
- Una macchina virtuale con l'estensione di acquisizione di pacchetti abilitata.

> [!IMPORTANT]
> L'acquisizione di pacchetti richiede un agente in esecuzione nella macchina virtuale. L'agente viene installato come estensione. Per informazioni sulle estensioni di VM, leggere l'articolo sulle [estensioni e funzionalità della macchina virtuale](../virtual-machines/virtual-machines-windows-extensions-features.md).

## <a name="install-vm-extension"></a>Installare un'estensione di macchina virtuale

### <a name="step-1"></a>Passaggio 1

Eseguire il cmdlet `azure vm extension set` per installare l'agente di acquisizione di pacchetti sulla macchina virtuale guest.

Per le macchine virtuali Windows:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentWindows -o 1.4
```

Per le macchine virtuali Linux:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentLinux -o 1.4
````

### <a name="step-2"></a>Passaggio 2

Per verificare che l'agente sia stato installato, eseguire il cmdlet `vm extension get` e passare il gruppo di risorse e il nome della macchina virtuale. Controllare l'elenco risultante per verificare l'installazione dell'agente.

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

L'esempio seguente riporta una possibile risposta all'esecuzione di `azure vm extension get`.

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                     Version  State
data:    ------------------------------  -----------------------  -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentTest  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>Avviare un'acquisizione di pacchetti

Dopo aver completato i passaggi precedenti, l'agente di acquisizione di pacchetti è installato nella macchina virtuale.

### <a name="step-1"></a>Passaggio 1

Il passaggio successivo consente di recuperare l'istanza di Network Watcher. Questa variabile viene passata al cmdlet `network watcher show` nel passaggio 4.

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>Passaggio 2

Recuperare un account di archiviazione. L'account di archiviazione viene usato per archiviare il file di acquisizione di pacchetti.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Passaggio 3

È possibile usare i filtri per limitare i dati archiviati dall'acquisizione di pacchetti. L'esempio seguente imposta un'acquisizione di pacchetto con diversi filtri.  I primi tre filtri acquisiscono il traffico TCP in uscita solo dall'indirizzo IP 10.0.0.3 verso le porte di destinazione 20, 80 e 443.  L'ultimo filtro acquisisce solo il traffico UDP.

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Per un'acquisizione di pacchetti è possibile definire più filtri. Se si usa una struttura di filtro complessa, è preferibile usare i filtri come un file JSON, così da evitare errori di sintassi. Usare ad esempio il flag "-r" (invece del flag "-f") e passare il percorso di un file JSON contenente i filtri seguenti:

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


L'esempio seguente riporta l'output previsto dall'esecuzione del cmdlet `network watcher packet-capture create`.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>Ottenere un'acquisizione di pacchetti

L'esecuzione del cmdlet `network watcher packet-capture show` consente di recuperare lo stato di un'acquisizione di pacchetti attualmente in esecuzione o completata.

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

L'esempio seguente riporta l'output ottenuto dall'esecuzione del cmdlet `network watcher packet-capture show`. L'esempio seguente mostra il risultato ottenuto al completamento dell'acquisizione di pacchetti. Il valore PacketCaptureStatus è Stopped, mentre il valore StopReason corrisponde a TimeExceeded. Questo valore indica che l'acquisizione di pacchetti ha avuto esito positivo ed è stata eseguita per il tempo necessario.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>Interrompere un'acquisizione di pacchetti

L'esecuzione del cmdlet `network watcher packet-capture stop` consente di interrompere un'acquisizione di pacchetti in corso.

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> Il cmdlet non restituisce alcuna risposta se eseguito in una sessione di acquisizione in corso o in una sessione esistente che è già stata interrotta.

## <a name="delete-a-packet-capture"></a>Eliminare un'acquisizione di pacchetti

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> L'eliminazione di un'acquisizione di pacchetti non elimina il file nell'account di archiviazione.

## <a name="download-a-packet-capture"></a>Scaricare un'acquisizione di pacchetti

Dopo aver completato la sessione di acquisizione di pacchetti, è possibile scaricare il file di acquisizione nell'archiviazione BLOB o in un file locale nella macchina virtuale. La posizione di archiviazione dell'acquisizione di pacchetti viene definita al momento della creazione della sessione. Uno strumento utile per accedere ai file di acquisizione salvati in un account di archiviazione è Esplora archivi di Microsoft Azure, disponibile qui: http://storageexplorer.com/

Se viene specificato un account di archiviazione, i file di acquisizione di pacchetti vengono salvati in un account di archiviazione nel percorso seguente:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come automatizzare le acquisizioni di pacchetti tramite gli avvisi della macchina virtuale, leggere l'articolo su come [creare un'acquisizione di pacchetti attivata da un avviso](network-watcher-alert-triggered-packet-capture.md).

Per stabilire se un traffico specificato è consentito all'interno o all'esterno di una macchina virtuale, vedere [Check IP flow verify](network-watcher-check-ip-flow-verify-portal.md) (Controllare la verifica del flusso IP).

<!-- Image references -->

