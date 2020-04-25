---
title: Vantaggi dell'utilizzo di Azure NetApp Files per l'automazione della progettazione elettronica | Microsoft Docs
description: Viene illustrato il Azure NetApp Files della soluzione che consente di soddisfare le esigenze del settore di progettazione di semiconduttori e chip. Presenta scenari di test che eseguono un benchmark di settore standard per l'automazione della progettazione elettronica (EDA) utilizzando Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: 8a287ec5cd33c9f2a96af7ad8162f7c8f54df118
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134169"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>Vantaggi dell'utilizzo di Azure NetApp Files per l'automazione della progettazione elettronica

Time-to-Market (TTM) è una considerazione critica per il settore di progettazione di semiconduttori e chip. Il settore presenta una larghezza di banda elevata e una bassa latenza per l'archiviazione. Questo articolo illustra la soluzione Azure NetApp Files fornisce per soddisfare le esigenze del settore. Sono disponibili scenari di test che eseguono un benchmark di settore standard per l'automazione della progettazione elettronica (EDA) con Azure NetApp Files. 

## <a name="test-scenario-configurations"></a>Configurazioni dello scenario di test

I test includono tre scenari con le configurazioni seguenti. 

|    Scenario    |    Volumi    |    Client<br> D16s_v3 SLES15  |
|----------------|---------------|--------------------------------|
|    Uno         |    1          |    1                           |
|    Due         |    6          |    24                          |
|    Tre       |    12         |    24                          |

Il primo scenario risolve il modo in cui è possibile guidare un singolo volume.  

Il secondo e il terzo scenario valutano i limiti di un singolo endpoint Azure NetApp Files. Analizzano i potenziali vantaggi dei limiti superiori e della latenza di I/O.

## <a name="test-scenario-results"></a>Risultati dello scenario di test

Nella tabella seguente vengono riepilogati i risultati degli scenari di test.

|    Scenario       |    Velocità di I/O<br>  a 2 ms     |    Velocità di I/O<br>  al perimetro     |    Velocità effettiva<br>  a 2 ms     |    Velocità effettiva<br>  al perimetro     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 volume       |    39.601                 |    49.502                      |    692 MiB/s                 |    866 MiB/s                      |
|    6 volumi      |    255.613                |    317.000                     |    4.577 MiB/s               |    5.568 MiB/s                    |
|    12 volumi     |    256.612                |    319.196                     |    4.577 MiB/s               |    5.709 MiB/s                    |

Lo scenario a volume singolo rappresenta la configurazione di base dell'applicazione. Si tratta dello scenario di base per gli scenari di test seguiti.  

Lo scenario con sei volumi Mostra un aumento lineare (600%) rispetto al carico di lavoro a volume singolo.  È possibile accedere a tutti i volumi all'interno di una singola rete virtuale tramite un singolo indirizzo IP.  

Nello scenario con 12 volumi viene illustrata una riduzione generale della latenza rispetto allo scenario con sei volumi. Ma non ha un aumento corrispondente della velocità effettiva ottenibile.   

Il grafico seguente illustra la latenza e la frequenza delle operazioni per il carico di lavoro EDA in Azure NetApp Files.  

![Latenza e frequenza operativa per il carico di lavoro EDA in Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

Il grafico seguente illustra la latenza e la velocità effettiva del carico di lavoro EDA in Azure NetApp Files.  

![Latenza e velocità effettiva per il carico di lavoro EDA in Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>Layout degli scenari di test 

La tabella seguente riepiloga il layout degli scenari di test.

|    Scenario di test     |    Numero totale di directory     |    Numero totale di file     |
|----------------------|------------------------------------|------------------------------|
|    1 volume          |    88.000                          |    880.000                   |
|    6 volumi         |    568.000                         |    5.680.000                 |
|    12 volumi        |    568.000                         |    5.680.000                 |

Il carico di lavoro completo è costituito da una combinazione di fasi funzionali e fisiche eseguite simultaneamente. Rappresenta un flusso tipico da un set di strumenti EDA a un altro.   

La fase funzionale è costituita dalle specifiche iniziali e da una progettazione logica. La fase fisica si verifica quando la progettazione logica viene convertita in un chip fisico. Durante le fasi di disconnessione e di Tape-out, i controlli finali vengono completati e la progettazione viene recapitata a una fonderia per la produzione.  

Le fasi funzionali includono una combinazione di I/O sequenziali e casuali di lettura e scrittura. Le fasi funzionali sono con utilizzo intensivo dei metadati, ad esempio le chiamate a file stat e Access. Sebbene le operazioni sui metadati siano effettivamente senza dimensioni, le operazioni di lettura e scrittura sono comprese tra meno di 1 K e 16 K. La maggior parte delle letture è compresa tra 4 K e 16 K.  La maggior parte delle Scritture è di 4 K o meno.  Le fasi fisiche sono costituite interamente da operazioni di lettura e scrittura sequenziali, con una combinazione di dimensioni di OP 32 K e 64 K.  

Nei grafici precedenti, la maggior parte della velocità effettiva deriva dalle fasi fisiche sequenziali del carico di lavoro. L'i/O deriva dalla piccola fase funzionale casuale e a elevato utilizzo di metadati. Entrambe le fasi si verificano in parallelo. 

In conclusione, è possibile associare le risorse di calcolo di Azure a Azure NetApp Files per la progettazione di EDA per ottenere una larghezza di banda scalabile. 

## <a name="next-steps"></a>Passaggi successivi

- [Architetture di soluzioni con Azure NetApp Files](azure-netapp-files-solution-architectures.md)
