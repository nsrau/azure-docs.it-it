---
title: Risolvere i problemi relativi alle connessioni con Azure Network Watcher - Portale di Azure | Microsoft Docs
description: "Informazioni su come usare la funzionalità di risoluzione dei problemi di connessione di Azure Network Watcher con il portale di Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 8d3a537523cce3457c18c7563e885a3f7348326f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Risolvere i problemi relativi alle connessioni con Azure Network Watcher usando il portale di Azure

> [!div class="op_single_selector"]
> - [di Microsoft Azure](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-connectivity-cli.md)
> - [API REST di Azure](network-watcher-connectivity-rest.md)

Informazioni su come usare la risoluzione dei problemi di connessione per verificare se è possibile stabilire una connessione TCP diretta da una macchina virtuale a uno specifico endpoint.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente disponga delle risorse seguenti:

* Un'istanza di Network Watcher nell'area di cui si vuole risolvere il problema di connessione.
* Risolvere i problemi di connessione con le macchine virtuali.

> [!IMPORTANT]
> Per la risoluzione dei problemi di connessione è richiesta un'estensione macchina virtuale `AzureNetworkWatcherExtension`. Per installare l'estensione in una VM Windows, vedere [Estensione macchina virtuale agente Azure Network Watcher per Windows](../virtual-machines/windows/extensions-nwa.md) e per una VM Linux VM vedere [Estensione macchina virtuale Azure Network Watcher Agent per Linux](../virtual-machines/linux/extensions-nwa.md).

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

Per stabilire se un traffico specificato è consentito all'interno o all'esterno di una macchina virtuale, vedere [Check IP flow verify](network-watcher-check-ip-flow-verify-portal.md) (Controllare la verifica del flusso IP).

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png