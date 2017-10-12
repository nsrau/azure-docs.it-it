---
title: Visualizzare modelli di traffico di rete con Azure Network Watcher e strumenti open source | Microsoft Docs
description: Questo articolo illustra come usare l'acquisizione pacchetti di Network Watcher con CapAnalysis per visualizzare i modelli di traffico da e verso le macchine virtuali.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 61abda6053fe743e294f309df3a6e1041052ec6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualizzare i modelli di traffico di rete da e verso le macchine virtuali usando strumenti open source

L'acquisizione pacchetti contiene dati della rete che permettono di eseguire l'analisi della rete e l'ispezione approfondita dei pacchetti. Sono disponibili diversi strumenti open source che è possibile usare per analizzare le acquisizioni pacchetti e ottenere informazioni dettagliate sulla rete. Uno di questi strumenti è CapAnalysis, uno strumento di visualizzazione dell'acquisizione pacchetti open source. La visualizzazione dei dati di acquisizione pacchetti permette di ottenere rapidamente informazioni approfondite sui modelli e le anomalie all'interno della rete e di condividere tali informazioni in un modo facilmente utilizzabile.

Azure Network Watcher offre la possibilità di acquisire dati importanti eseguendo l'acquisizione pacchetti sulla rete. Questo articolo contiene una procedura dettagliata su come visualizzare e ottenere informazioni approfondite dalle acquisizioni pacchetti usando CapAnalysis con Network Watcher.

## <a name="scenario"></a>Scenario

Una semplice applicazione Web è distribuita in una macchina virtuale in Azure e si vogliono usare strumenti open source per visualizzarne il traffico di rete e identificare rapidamente i modelli di flusso e le eventuali anomalie. Network Watcher permette di ottenere un'acquisizione pacchetti dell'ambiente di rete e di memorizzarla direttamente nell'account di archiviazione. CapAnalysis può quindi inserire l'acquisizione pacchetto direttamente dal BLOB di archiviazione e visualizzarne il contenuto.

![scenario][1]

## <a name="steps"></a>Passi

### <a name="install-capanalysis"></a>Installare CapAnalysis

Per installare CapAnalysis in una macchina virtuale, vedere le istruzioni ufficiali all'indirizzo https://www.capanalysis.net/ca/how-to-install-capanalysis.
Per accedere a CapAnalysis in modalità remota, è necessario aprire la porta 9877 nella macchina virtuale mediante l'aggiunta di una nuova regola di sicurezza in ingresso. Per altre informazioni sulla creazione di regole in gruppi di sicurezza di rete, vedere [Creare regole in un gruppo di sicurezza di rete esistente](../virtual-network/virtual-networks-create-nsg-arm-pportal.md#create-rules-in-an-existing-nsg). Dopo aver aggiunto la regola, dovrebbe essere possibile accedere a CapAnalysis da `http://<PublicIP>:9877`.

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Usare Azure Network Watcher per avviare una sessione di acquisizione pacchetti

Network Watcher consente di acquisire pacchetti per registrare il traffico da e verso una macchina virtuale. Per istruzioni su come avviare una sessione di acquisizione pacchetti, vedere l'articolo relativo alla [gestione dell'acquisizione pacchetti con Network Watcher](network-watcher-packet-capture-manage-portal.md). L'acquisizione pacchetti può essere memorizzata in un BLOB di archiviazione a cui accedere con CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Caricare un'acquisizione pacchetti in CapAnalysis
È possibile caricare direttamente un'acquisizione pacchetti eseguita da Network Watcher usando la scheda Importa dall'URL e specificando un collegamento al BLOB di archiviazione in cui è memorizzata l'acquisizione pacchetti.

Quando si fornisce un collegamento a CapAnalysis, assicurarsi di aggiungere un token di firma di accesso condiviso per l'URL del BLOB di archiviazione.  A questo scopo, passare a Firma di accesso condiviso dall'account di archiviazione, definire le autorizzazioni consentite e fare clic sul pulsante Genera firma di accesso condiviso per creare un token. È quindi possibile aggiungere tale token di firma di accesso condiviso all'URL del BLOB di archiviazione dell'acquisizione pacchetti.

L'URL risultante avrà un aspetto simile al seguente: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Analizzare le acquisizioni pacchetti

CapAnalysis offre varie opzioni di visualizzazione dell'acquisizione pacchetti, ognuna delle quali consente l'analisi da una prospettiva diversa. Questi riepiloghi visivi permettono di comprendere le tendenze del traffico di rete e individuare rapidamente eventuali attività inconsuete. Alcune di queste funzionalità sono riportate nell'elenco seguente:

1. Tabelle di flusso

    Questa tabella contiene l'elenco dei flussi nei dati del pacchetto, il timestamp e i diversi protocolli associati ai flussi, nonché gli indirizzi IP di origine e di destinazione.

    ![Pagina dei flussi di CapAnalysis][5]

1. Panoramica dei protocolli

    Questo riquadro permette di visualizzare rapidamente la distribuzione del traffico di rete nei vari protocolli e nelle aree geografiche.

    ![Panoramica dei protocolli di CapAnalysis][6]

1. Statistiche

    Questo riquadro permette di visualizzare le statistiche sul traffico di rete, vale a dire i byte inviati e ricevuti dagli indirizzi IP di origine e di destinazione, i flussi per ognuno degli indirizzi IP di origine e di destinazione, il protocollo usato per i vari flussi e la durata dei flussi.

    ![Statistiche di CapAnalysis][7]

1. Mappa geografica

    Questo riquadro consente di visualizzare una mappa del traffico di rete, con gradazioni di colore che indicano il volume di traffico da ogni paese. È possibile selezionare i paesi evidenziati per visualizzare altre statistiche sui flussi, ad esempio la proporzione di dati inviati e ricevuti dagli indirizzi IP in un determinato paese.

    ![Mappa geografica][8]

1. Filtri

    CapAnalysis include un set di filtri per l'analisi rapida di pacchetti specifici. Ad esempio, è possibile scegliere di filtrare i dati in base al protocollo per ottenere informazioni specifiche su tale subset del traffico.

    ![filters][11]

    Per altre informazioni su tutte le funzionalità di CapAnalysis, vedere [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about).

## <a name="conclusion"></a>Conclusione

La funzionalità di acquisizione pacchetti di Network Watcher permette di acquisire i dati necessari per eseguire l'analisi della rete e comprendere meglio il traffico di rete. In questo scenario è stato illustrato come integrate facilmente le acquisizioni pacchetti di Network Watcher con strumenti di visualizzazione open source. L'uso di strumenti open source come CapAnalysis per visualizzare le acquisizioni pacchetti permette di eseguire ispezioni approfondite dei pacchetti e di identificare rapidamente le tendenze all'interno del traffico di rete.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui registri dei flussi dei gruppi di sicurezza di rete, vedere [Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md)

Per informazioni su come visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI, vedere [Visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
