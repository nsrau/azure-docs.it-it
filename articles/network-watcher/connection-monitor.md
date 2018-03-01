---
title: Monitorare le connessioni di rete con Azure Network Watcher - Portale di Azure | Microsoft Docs
description: "Informazioni su come monitorare la connettività di rete con Azure Network Watcher usando il portale di Azure."
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
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Monitorare le connessioni di rete con Azure Network Watcher usando il portale di Azure

Informazioni su come usare il monitoraggio della connessione per monitorare la connettività di rete tra una macchina virtuale di Azure e un indirizzo IP. L'indirizzo IP può essere assegnato a un'altra risorsa di Azure o a una risorsa Internet o locale.

## <a name="prerequisites"></a>prerequisiti

Prima di completare i passaggi descritti in questo articolo, è necessario soddisfare i prerequisiti seguenti:

* Un'istanza di Network Watcher nell'area di cui si vuole monitorare una connessione. Se non si dispone già di un'istanza, è possibile crearne una completando i passaggi descritti in [Creare un'istanza di Azure Network Watcher](network-watcher-create.md).
* Una macchina virtuale da cui eseguire il monitoraggio.
* `AzureNetworkWatcherExtension` installato nella macchina virtuale da cui si vuole monitorare una connessione. Per installare l'estensione in una macchina virtuale Windows, vedere [Estensione macchina virtuale agente Azure Network Watcher per Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), mentre per installare l'estensione in una macchina virtuale Linux, vedere [Estensione macchina virtuale Azure Network Watcher Agent per Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere al [portale di Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione

1. Sul lato sinistro del portale, selezionare **Altri servizi**.
2. Iniziare a digitare *network watcher*. Quando **Network Watcher** viene visualizzato tra i risultati della ricerca, selezionarlo.
3. In **MONITORAGGIO** selezionare **Monitoraggio connessione (anteprima)**. Le funzionalità nella versione di anteprima non offrono lo stesso livello di affidabilità o disponibilità in base all'area geografica delle funzionalità della versione generale.
4. Selezionare **+ Aggiungi**.
5. Immettere o selezionare le informazioni appropriate per la connessione che si desidera monitorare e quindi selezionare **Aggiungi**. In questo esempio, una connessione tra le macchine virtuali *myVmSource* e *myVmDestination* viene monitorata tramite la porta 80.
    
    |  Impostazione                                 |  Valore               |
    |  -------------------------------------   |  ------------------- |
    |  NOME                                    |  myConnectionMonitor |
    |  Macchina virtuale di origine                  |  myVmSource          |
    |  Porta di origine                             |                      |
    |  Destinazione, Selezionare una macchina virtuale   |  myVmDestination     |
    |  Porta di destinazione                        |  80                  |

6. Ha inizio il monitoraggio. Il monitoraggio della connessione esegue il probe ogni 60 secondi.
7. Il monitoraggio della connessione mostra il tempo di round trip medio e la percentuale di probe non riusciti. È possibile visualizzare i dati di monitoraggio in una griglia o un grafico.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come automatizzare le acquisizioni di pacchetti tramite gli avvisi della macchina virtuale, leggere l'articolo su come [creare un'acquisizione di pacchetti attivata da un avviso](network-watcher-alert-triggered-packet-capture.md).

- Stabilire se un traffico specificato è consentito all'interno o all'esterno della macchina virtuale tramite la [verifica del flusso IP](network-watcher-check-ip-flow-verify-portal.md).