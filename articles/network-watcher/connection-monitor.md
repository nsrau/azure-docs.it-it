---
title: Monitorare le connessioni di rete con Azure Network Watcher - Portale di Azure | Microsoft Docs
description: Informazioni su come monitorare la connettività di rete con Azure Network Watcher usando il portale di Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Monitorare le connessioni di rete con Azure Network Watcher usando il portale di Azure

Informazioni su come usare il monitoraggio della connessione per monitorare la connettività di rete tra una macchina virtuale di Azure e un indirizzo IP. Il monitoraggio connessione fornisce il monitoraggio a livello di connessione. Una connessione è definita come una combinazione di origine e indirizzo IP e porta di origine e di destinazione. Il monitoraggio connessione consente scenari come il monitoraggio della connettività da una macchina virtuale in una rete virtuale a una macchina virtuale che esegue il server SQL nella stessa rete virtuale o in una diversa, sulla porta 1433. Il monitoraggio connessione fornisce la latenza della connessione come una metrica per il monitoraggio di Azure, registrata ogni 60 secondi, oltre a offrire una topologia hop-by-hop e a identificare eventuali problemi di configurazione con conseguenze sulla connessione.


## <a name="prerequisites"></a>prerequisiti

Prima di completare i passaggi descritti in questo articolo, è necessario soddisfare i prerequisiti seguenti:

* Un'istanza di Network Watcher nell'area di cui si vuole monitorare una connessione. Se non si dispone già di un'istanza, è possibile crearne una completando i passaggi descritti in [Creare un'istanza di Azure Network Watcher](network-watcher-create.md).
* Una macchina virtuale da cui eseguire il monitoraggio.
* `AzureNetworkWatcherExtension` installato nella macchina virtuale da cui si vuole monitorare una connessione. Per installare l'estensione in una macchina virtuale Windows, vedere [Estensione macchina virtuale agente Azure Network Watcher per Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), mentre per installare l'estensione in una macchina virtuale Linux, vedere [Estensione macchina virtuale Azure Network Watcher Agent per Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere al [portale di Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione

La procedura seguente abilita il monitoraggio di connessione sulle porte 80 e 1433 di una macchina virtuale di destinazione:

1. Sul lato sinistro del portale, selezionare **Altri servizi**.
2. Iniziare a digitare *network watcher*. Quando **Network Watcher** viene visualizzato tra i risultati della ricerca, selezionarlo.
3. In **MONITORAGGIO** selezionare **Monitoraggio connessione (anteprima)**. Le funzionalità nella versione di anteprima non offrono lo stesso livello di affidabilità o disponibilità in base all'area geografica delle funzionalità della versione generale.
4. Selezionare **+ Aggiungi**.
5. Immettere o selezionare le informazioni per la connessione da monitorare e quindi selezionare **Aggiungi**. Nell'esempio illustrato nella figura seguente, la connessione monitorata è compresa tra le macchine virtuali *MultiTierApp0* e *Database0*:

    ![Aggiungere un monitoraggio connessione](./media/connection-monitor/add-connection-monitor.png)

    Ha inizio il monitoraggio. Il monitoraggio della connessione esegue il probe ogni 60 secondi.

## <a name="view-connection-monitoring"></a>Visualizzare il monitoraggio connessione

1. Completare i passaggi da 1 a 3 in [Creare un monitoraggio della connessione](#create-a-connection-monitor) per visualizzare il monitoraggio connessione.
2. L'immagine seguente mostra i dettagli della connessione AppToDB(80). Lo **stato** è raggiungibile. La **Visualizzazione grafico** mostra **Tempo di round trip medio** e **% di probe non riusciti**. Il grafico fornisce informazioni hop-by-hop e non mostra nessun problema che influisce negativamente sulla raggiungibilità della destinazione.

    ![Visualizzare il monitoraggio connessione](./media/connection-monitor/view-connection-monitor.png)

3. Visualizzando il monitor *AppToDB(1433)*, mostrato nell'immagine seguente, si noterà che, per la stesse macchine virtuali di origine e di destinazione, lo stato è raggiungibile tramite la porta 1433. La **Visualizzazione griglia** in questo scenario fornisce le informazioni hop-by-hop e il problema che influisce sulla raggiungibilità. In questo caso, una regola NSG blocca tutto il traffico sulla porta 1433 al secondo hop.

    ![Visualizzare il monitoraggio connessione](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come automatizzare le acquisizioni di pacchetti tramite gli avvisi della macchina virtuale, leggere l'articolo su come [creare un'acquisizione di pacchetti attivata da un avviso](network-watcher-alert-triggered-packet-capture.md).
- Stabilire se un traffico specificato è consentito all'interno o all'esterno della macchina virtuale tramite la [verifica del flusso IP](network-watcher-check-ip-flow-verify-portal.md).