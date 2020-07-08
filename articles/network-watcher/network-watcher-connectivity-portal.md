---
title: Risolvere i problemi relativi alle connessioni-portale di Azure
titleSuffix: Azure Network Watcher
description: Informazioni su come usare la funzionalità di risoluzione dei problemi di connessione di Azure Network Watcher con il portale di Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: a16e99df57da66d2633947f8aecdf1d2cef9ab15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737411"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Risolvere i problemi relativi alle connessioni con Azure Network Watcher usando il portale di Azure

> [!div class="op_single_selector"]
> - [Portale](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Interfaccia della riga di comando di Azure](network-watcher-connectivity-cli.md)
> - [API REST di Azure](network-watcher-connectivity-rest.md)

Informazioni su come usare la risoluzione dei problemi di connessione per verificare se è possibile stabilire una connessione TCP diretta da una macchina virtuale a uno specifico endpoint.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente disponga delle risorse seguenti:

* Un'istanza di Network Watcher nell'area di cui si vuole risolvere il problema di connessione.
* Risolvere i problemi di connessione con le macchine virtuali.

> [!IMPORTANT]
> Per la risoluzione dei problemi di connessione, è necessario che nella macchina virtuale da cui si risolvono i problemi sia installata l'estensione `AzureNetworkWatcherExtension`. Per installare l'estensione in una VM Windows, vedere [Estensione macchina virtuale agente Azure Network Watcher per Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e per una VM Linux VM vedere [Estensione macchina virtuale Azure Network Watcher Agent per Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Non è richiesta l'estensione nell'endpoint di destinazione.

## <a name="check-connectivity-to-a-virtual-machine"></a>Controllare la connettività a una macchina virtuale

Questo esempio controlla la connettività a una macchina virtuale di destinazione sulla porta 80.

Passare a Network Watcher e fare clic su **Risoluzione dei problemi di connessione**. Selezionare la macchina virtuale di cui si vuole verificare la connettività. Nella sezione **Destinazione** fare clic su **Selezionare una macchina virtuale** e scegliere la macchina virtuale e la porta da testare.

Quando si fa clic su **Verifica**, viene verificata la connettività tra le macchine virtuali sulla porta specificata. Nell'esempio, la macchina virtuale di destinazione non è raggiungibile e viene visualizzato un elenco di hop.

![Verificare i risultati di connettività per una macchina virtuale][1]

## <a name="check-remote-endpoint-connectivity"></a>Verificare la connettività dell'endpoint remoto

Per verificare la connettività e la latenza in un endpoint remoto, scegliere il pulsante di opzione **Specificare manualmente** nella sezione **Destinazione**, immettere l'URL e la porta e fare clic su **Verifica**.  Questa procedura può essere usata per endpoint remoti come siti Web ed endpoint di archiviazione.

![Verificare i risultati di connettività per un sito Web][2]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come automatizzare le acquisizioni di pacchetti tramite gli avvisi della macchina virtuale, leggere l'articolo su come [creare un'acquisizione di pacchetti attivata da un avviso](network-watcher-alert-triggered-packet-capture.md).

Individuare se un determinato traffico è consentito all'interno o all'esterno della macchina virtuale visitando la verifica del [flusso IP](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png