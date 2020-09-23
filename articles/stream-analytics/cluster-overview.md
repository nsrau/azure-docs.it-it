---
title: Panoramica dei cluster di Analisi di flusso di Azure (anteprima)
description: Informazioni sull'offerta dedicata di cluster di Analisi di flusso a tenant singolo.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 613cf7d9b68fe42c26f2c01cb1fb5dd1da1e1fb5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944345"
---
# <a name="overview-of-azure-stream-analytics-cluster-preview"></a>Panoramica del cluster di Analisi di flusso di Azure (anteprima)

Il cluster di Analisi di flusso di Azure è una distribuzione a tenant singolo per casi d'uso di streaming complessi e impegnativi. Su scala completa, i cluster di Analisi di flusso possono elaborare più di 200 MB di dati al secondo in tempo reale. I processi di Analisi di flusso in esecuzione in cluster dedicati possono sfruttare tutte le funzionalità dell'offerta standard e includono il supporto per la connettività con collegamento privato agli input e agli output.

I cluster di Analisi di flusso vengono fatturati in base a unità di streaming, che rappresentano la quantità di risorse di CPU e memoria allocate. L'unità di streaming è uguale per le offerte standard e dedicata. È possibile acquistare 36, 72, 108, 144, 180 o 216 unità di streaming per ogni cluster. Un cluster di Analisi di flusso può fungere da piattaforma di streaming per l'organizzazione e può essere condiviso da team diversi che lavorano su vari casi d'uso.

## <a name="what-are-stream-analytics-clusters"></a>Che cosa sono i cluster di Analisi di flusso

I cluster di Analisi di flusso sono basati sullo stesso motore dei processi di Analisi di flusso in esecuzione in un ambiente multi-tenant. Il cluster dedicato a tenant singolo offre le funzionalità seguenti:

* Hosting a tenant singolo senza interferenze da altri tenant. Le risorse sono effettivamente "isolate" e offrono prestazioni migliori quando si verificano picchi del traffico.

* Il cluster può essere dimensionato con un numero di unità di streaming compreso tra 36 e 216 quando le esigenze di utilizzo aumentano nel corso del tempo.

* Supporto per reti virtuali, che consente ai processi di analisi di flusso di connettersi ad altre risorse in modo sicuro usando endpoint privati.

* Possibilità di creare funzioni C# definite dall'utente e deserializzatori personalizzati in qualsiasi area.

* Nessun costo di manutenzione, consentendo di dedicare le risorse alla creazione di soluzioni di analisi in tempo reale.

## <a name="how-to-get-started"></a>Come iniziare

È possibile [creare un cluster di Analisi di flusso](create-cluster.md) tramite il [portale di Azure](https://aka.ms/asaclustercreateportal). Per eventuali domande o per richiedere assistenza con l'onboarding, è possibile contattare il [team di Analisi di flusso](mailto:askasa@microsoft.com).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Come fare a scegliere tra un cluster di Analisi di flusso e un processo di Analisi di flusso?

Il modo più semplice per iniziare è creare e sviluppare un processo di Analisi di flusso per acquisire familiarità con il servizio e vedere in che misura soddisfa i propri requisiti di analisi.

I processi di Analisi di flusso da soli non supportano le reti virtuali. Se gli input o gli output sono protetti da un firewall o da una rete virtuale di Azure, sono disponibili le due opzioni seguenti:

* Se il computer locale ha accesso alle risorse di input e di output protette da una rete virtuale, ad esempio Hub eventi di Azure o Database SQL di Azure, è possibile [installare gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-tools-for-visual-studio-install.md). È possibile sviluppare e [testare i processi di Analisi di flusso in locale](stream-analytics-live-data-local-testing.md) nel dispositivo senza sostenere alcun costo. Quando si è pronti a usare Analisi di flusso nell'architettura, è possibile creare un cluster di Analisi di flusso, configurare gli endpoint privati ed eseguire i processi su larga scala.

* È possibile creare un cluster di Analisi di flusso, configurarlo con gli endpoint privati necessari per la pipeline ed eseguire i processi di Analisi di flusso al suo interno.

### <a name="what-performance-can-i-expect"></a>Quali prestazioni è possibile prevedere?

L'unità di streaming è uguale per le offerte standard e dedicata. Un singolo processo che usa un cluster completo con 36 unità di streaming può raggiungere una velocità effettiva di circa 36 MB/secondo con latenza in millisecondi. Il numero esatto dipende dal formato degli eventi e dal tipo di analisi. Essendo dedicato, il cluster di Analisi di flusso offre garanzie di prestazioni più affidabili. Tutti i processi in esecuzione nel cluster appartengono solo all'utente.

### <a name="can-i-scale-my-cluster"></a>È possibile dimensionare il cluster?

Sì. È possibile configurare facilmente la capacità del cluster, ovvero di [aumentarla o ridurla](scale-cluster.md) in base alle esigenze per soddisfare esigenze mutevoli.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>È possibile eseguire i processi esistenti in questi nuovi cluster creati?

Sì. È possibile collegare i processi esistenti al cluster di Analisi di flusso appena creato ed eseguirli come di consueto. Non è necessario ricreare da zero i processi di Analisi di flusso esistenti.

### <a name="how-much-will-these-clusters-cost-me"></a>Qual è il costo di questi cluster?

Il costo dei cluster di Analisi di flusso dipende dalla capacità in unità di streaming scelta. I cluster vengono fatturati su base oraria e non sono previsti costi aggiuntivi per ogni processo in esecuzione al loro interno. Per gli aggiornamenti sulla fatturazione degli endpoint privati, vedere la [pagina dei prezzi del servizio Collegamento privato](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>A quali input e output è possibile connettersi privatamente dal cluster di Analisi di flusso?

Analisi di flusso supporta diversi tipi di input e di output. Qualsiasi servizio che supporta i collegamenti privati di Azure può connettersi ai processi privatamente. È possibile [creare endpoint privati](private-endpoints.md) nel cluster che consentono l'accesso dei processi alle risorse di input e output.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata presentata una panoramica del cluster di Analisi di flusso di Azure. Successivamente, è possibile creare il proprio cluster ed eseguire processi di Analisi di flusso: 

* [Creare un cluster di Analisi di flusso](create-cluster.md)
* [Gestire gli endpoint privati in un cluster di Analisi di flusso di Azure](private-endpoints.md)
* [Gestire i processi di Analisi di flusso in un cluster di Analisi di flusso](manage-jobs-cluster.md)
* [Creare un processo di Analisi di flusso.](stream-analytics-quick-create-portal.md)
