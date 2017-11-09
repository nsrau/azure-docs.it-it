---
title: Gestire le acquisizioni di pacchetti con Azure Network Watcher - Interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: "Questa pagina illustra come gestire la funzionalità di acquisizione di pacchetti di Network Watcher usando l'interfaccia della riga di comando di Azure 2.0"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 66f53f97220f8fd23fa38bece0025f8b48289e23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-20"></a>Gestire le acquisizioni di pacchetti con Azure Network Watcher usando l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-packet-capture-manage-cli.md)
> - [API REST di Azure](network-watcher-packet-capture-manage-rest.md)

Il servizio di acquisizione di pacchetti di Network Watcher consente di creare sessioni di acquisizione per registrare il traffico da e verso una macchina virtuale. Sono disponibili filtri per la sessione di acquisizione per garantire che venga acquisito solo il traffico desiderato. Il servizio di acquisizione di pacchetti consente di individuare eventuali anomalie di rete in modo proattivo e reattivo. Altri utilizzi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora. La possibilità di attivare da remoto l'acquisizione di pacchetti evita di dover eseguire manualmente questa operazione sul computer desiderato, consentendo un notevole risparmio di tempo.

Questo articolo usa l'interfaccia della riga di comando di nuova generazione per il modello di distribuzione di gestione delle risorse, ovvero l'interfaccia della riga di comando di Azure 2.0, disponibile per Windows, Mac e Linux.

Per eseguire i passaggi indicati in questo articolo è necessario [installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows (interfaccia della riga di comando di Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

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
> L'acquisizione di pacchetti richiede un agente in esecuzione nella macchina virtuale. L'agente viene installato come estensione. Per informazioni sulle estensioni di VM, leggere l'articolo sulle [estensioni e funzionalità della macchina virtuale](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Installare un'estensione di macchina virtuale

### <a name="step-1"></a>Passaggio 1

Eseguire il cmdlet `az vm extension set` per installare l'agente di acquisizione di pacchetti sulla macchina virtuale guest.

Per le macchine virtuali Windows:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Per le macchine virtuali Linux:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
````

### <a name="step-2"></a>Passaggio 2

Per verificare che l'agente sia stato installato, eseguire il cmdlet `vm extension show` e passare il gruppo di risorse e il nome della macchina virtuale. Controllare l'elenco risultante per verificare l'installazione dell'agente.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

L'esempio seguente riporta una possibile risposta all'esecuzione di `az vm extension show`.

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Avviare un'acquisizione di pacchetti

Dopo aver completato i passaggi precedenti, l'agente di acquisizione di pacchetti è installato nella macchina virtuale.

### <a name="step-1"></a>Passaggio 1

Il passaggio successivo consente di recuperare l'istanza di Network Watcher. Il nome dell'istanza di Network Watcher viene passato al cmdlet `az network watcher show` nel passaggio 4.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>Passaggio 2

Recuperare un account di archiviazione. L'account di archiviazione viene usato per archiviare il file di acquisizione di pacchetti.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Passaggio 3

È possibile usare i filtri per limitare i dati archiviati dall'acquisizione di pacchetti. L'esempio seguente imposta un'acquisizione di pacchetto con diversi filtri.  I primi tre filtri acquisiscono il traffico TCP in uscita solo dall'indirizzo IP 10.0.0.3 verso le porte di destinazione 20, 80 e 443.  L'ultimo filtro acquisisce solo il traffico UDP.

```azurecli
az network watcher packet-capture create --resource-group {resoureceurceGroupName} --vm {vmName} --name packetCaptureName --storage-account gwteststorage123abc --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

L'esempio seguente riporta l'output previsto dall'esecuzione del cmdlet `az network watcher packet-capture create`.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Ottenere un'acquisizione di pacchetti

L'esecuzione del cmdlet `az network watcher packet-capture show` consente di recuperare lo stato di un'acquisizione di pacchetti attualmente in esecuzione o completata.

```azurecli
az network watcher packet-capture show --name packetCaptureName --location westcentralus
```

L'esempio seguente riporta l'output ottenuto dall'esecuzione del cmdlet `az network watcher packet-capture show`. L'esempio seguente mostra il risultato ottenuto al completamento dell'acquisizione di pacchetti. Il valore PacketCaptureStatus è Stopped, mentre il valore StopReason corrisponde a TimeExceeded. Questo valore indica che l'acquisizione di pacchetti ha avuto esito positivo ed è stata eseguita per il tempo necessario.

```
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/providers/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapt
ure_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="stop-a-packet-capture"></a>Interrompere un'acquisizione di pacchetti

L'esecuzione del cmdlet `az network watcher packet-capture stop` consente di interrompere un'acquisizione di pacchetti in corso.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Il cmdlet non restituisce alcuna risposta se eseguito in una sessione di acquisizione in corso o in una sessione esistente che è già stata interrotta.

## <a name="delete-a-packet-capture"></a>Eliminare un'acquisizione di pacchetti

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
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
