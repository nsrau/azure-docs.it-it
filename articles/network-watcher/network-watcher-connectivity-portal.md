---
title: "Verificare la connettività con Azure Network Watcher - Portale di Azure | Microsoft Docs"
description: "Questa pagina descrive come verificare la connettività con Network Watcher usando il portale di Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 84774d0f40e06a819ca6de6cf0be68e17ba474e4
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---

# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>Verificare la connettività con Azure Network Watcher usando il portale di Azure

> [!div class="op_single_selector"]
> - [Portale](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-connectivity-cli.md)
> - [API REST di Azure](network-watcher-connectivity-rest.md)

Informazioni su come usare la connettività per verificare se è possibile stabilire una connessione TCP diretta da una macchina virtuale a uno specifico endpoint.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente disponga delle risorse seguenti:

* Un'istanza di Network Watcher nell'area di cui si vuole controllare la connettività.

* Macchine virtuali con cui controllare la connettività.

> [!IMPORTANT]
> Il controllo della connettività richiede un'estensione macchina virtuale `AzureNetworkWatcherExtension`. Per installare l'estensione in una VM Windows, vedere [Estensione macchina virtuale agente Azure Network Watcher per Windows](../virtual-machines/windows/extensions-nwa.md) e per una VM Linux VM vedere [Estensione macchina virtuale Azure Network Watcher Agent per Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="check-connectivity-to-a-virtual-machine"></a>Controllare la connettività a una macchina virtuale

Questo esempio controlla la connettività a una macchina virtuale di destinazione sulla porta 80.

Passare a Network Watcher e fare clic su **Verifica della connettività (anteprima)**. Selezionare la macchina virtuale di cui si vuole verificare la connettività. Nella sezione **Destinazione** fare clic su **Selezionare una macchina virtuale** e scegliere la macchina virtuale e la porta da testare.

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

