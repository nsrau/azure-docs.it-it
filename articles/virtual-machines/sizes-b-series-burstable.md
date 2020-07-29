---
title: 'Serie B: macchine virtuali di Azure'
description: Descrive le dimensioni delle macchine virtuali di Azure della serie B che includono il supporto per burst.
services: virtual-machines
ms.subservice: sizes
author: ayshakeen
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: ayshak
ms.openlocfilehash: e3a5d2228074ed358244b49bdf283c09f777ddee
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292076"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali della serie B con supporto per burst

Le macchine virtuali della serie B sono ideali per i carichi di lavoro che non necessitano continuamente delle prestazioni complete della CPU, ad esempio server Web, modelli di verifica, database di piccole dimensioni e ambienti di compilazione di sviluppo. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre la possibilità di acquistare una dimensione di VM con prestazioni baseline e l'istanza della VM accumula crediti quando il relativo utilizzo è inferiore alla baseline. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline, usando fino al 100% della vCPU quando l'applicazione richiede prestazioni superiori per la CPU.

La serie B è dotata delle dimensioni seguenti per le macchine virtuali:

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Prestazioni base della CPU della VM | Prestazioni massime della CPU della VM | Crediti iniziali | Crediti in banca/ora | Crediti massimi accumulati | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0.5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22,5 | 1920/22,5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls è supportato solo in Linux

## <a name="workload-example"></a>Esempio di carico di lavoro

Si consideri un'applicazione di archiviazione/uscita di Office. L'applicazione richiede picchi di CPU durante l'orario di lavoro, ma non una grande quantità di potenza di calcolo durante le ore di inattività. In questo esempio, il carico di lavoro richiede una macchina virtuale 16vCPU con 64GiB di RAM per lavorare in modo efficiente.

La tabella mostra i dati relativi al traffico orario e il grafico è una rappresentazione visiva del traffico.

Caratteristiche di B16:

Prestazioni massime CPU: 16vCPU * 100% = 1600%

Baseline: 270%

![Grafico dei dati relativi al traffico orario](./media/b-series-burstable/office-workload.png)

| Scenario | Tempo | Utilizzo CPU (%) | Crediti accumulati<sup>1</sup> | Crediti disponibili |
| --- | --- | --- | --- | --- |
| Distribuzione di B16ms | Distribuzione | Distribuzione  | 480 (crediti iniziali) | 480 |
| Nessun traffico | 0:00 | 0 | 162 | 642 |
| Nessun traffico | 1:00 | 0 | 162 | 804 |
| Nessun traffico | 2:00 | 0 | 162 | 966 |
| Nessun traffico | 3:00 | 0 | 162 | 1128 |
| Nessun traffico | 4:00 | 0 | 162 | 1290 |
| Nessun traffico | 5:00 | 0 | 162 | 1452 |
| Traffico ridotto | 6:00 | 270 | 0 | 1452 |
| I dipendenti provengono da Office (è necessario l'app 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| I dipendenti continuano a essere in ufficio (l'app richiede 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Traffico ridotto | 9:00 | 270 | 0 | 240 |
| Traffico ridotto | 10:00 | 100 | 102 | 342 |
| Traffico ridotto | 11:00 | 50 | 132 | 474 |
| Traffico ridotto | 12:00 | 100 | 102 | 576 |
| Traffico ridotto | 13:00 | 100 | 102 | 678 |
| Traffico ridotto | 14:00 | 50 | 132 | 810 |
| Traffico ridotto | 15:00 | 100 | 102 | 912 |
| Traffico ridotto | 16:00 | 100 | 102 | 1014 |
| Estrazione dei dipendenti (richiesta dell'app 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Traffico ridotto | 18:00 | 270 | 0 | 216 |
| Traffico ridotto | 19:00 | 270 | 0 | 216 |
| Traffico ridotto | 20:00 | 50 | 132 | 348 |
| Traffico ridotto | 21:00 | 50 | 132 | 480 |
| Nessun traffico | 22:00 | 0 | 162 | 642 |
| Nessun traffico | 23:00 | 0 | 162 | 804 |

<sup>1</sup> crediti accumulati/crediti utilizzati in un'ora equivale a: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

Per un D16s_v3 con 16 vCPU e 64 GiB di memoria la tariffa oraria è $0,936 all'ora ($673,92 mensile) e per B16ms con 16 vCPU e 64 GiB di memoria, la velocità è di $0,794 all'ora (mensile $547,86). <b>Questo comporta un risparmio del 15%.</b>

## <a name="q--a"></a>Domande e risposte

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>D: Come si ottengono prestazioni baseline del 135% da una VM?

**R**: La quantità pari al 135% è condivisa tra 8 vCPU che costituiscono le dimensioni della VM. Se, ad esempio, l'applicazione usa 4 degli 8 core dedicati all'elaborazione batch e ognuna di queste 4 vCPU è in esecuzione con un livello di utilizzo pari al 30%, la quantità totale di prestazioni della CPU della VM equivale al 120%.  La macchina virtuale, quindi, accumula tempo di credito in base al differenziale del 15% rispetto alle prestazioni baseline.  Quando sono disponibili crediti, la stessa VM può quindi usare il 100% di tutte le 8 vCPU, ottenendo un livello massimo di prestazioni della CPU pari all'800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>D: Come si possono monitorare il saldo e l'utilizzo del credito?

**R**: Nelle prossime settimane verranno introdotte 2 nuove metriche, ovvero la metrica **Credit** che consentirò di visualizzare la quantità di crediti accumulati dalla VM e la metrica **ConsumedCredit** che mostrerà la quantità di crediti di CPU utilizzata dalla VM rispetto alla quantità accumulata.    Sarà possibile visualizzare queste metriche dal riquadro delle metriche nel portale o a livello di programmazione tramite le API di Monitoraggio di Azure.

Per altre informazioni su come accedere ai dati delle metriche per Azure, vedere [Panoramica delle metriche in Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated"></a>D: In che modo vengono accumulati i crediti?

**R**: La velocità di accumulo e di utilizzo delle VM viene impostata in modo che una VM in esecuzione esattamente al rispettivo livello di prestazioni di base non avrà alcun accumulo netto o alcun utilizzo di crediti di burst.  Una VM presenterà un incremento netto in crediti in caso di esecuzione a un livello inferiore al rispettivo livello di prestazioni di base e presenterà una riduzione netta in crediti in caso di utilizzo della CPU da parte della VM a un livello superiore rispetto al rispettivo livello di prestazioni di base.

**Esempio**: Si distribuisce una VM usando le dimensioni B1ms per una piccola applicazione di database relativa a orari e presenze. Queste dimensioni consentono all'applicazione di usare fino al 20% di una vCPU come baseline, che corrisponde a 0,2 crediti al minuto che si possono usare o accumulare.

L'applicazione è occupata all'inizio o alla fine della giornata lavorativa dei dipendenti, tra le 7:00-9:00 e le 16:00-18:00. Durante le altre 20 ore della giornata l'applicazione è solitamente inattiva e usa solo il 10% della vCPU. Per gli orari non di picco, si accumulano 0,2 crediti al minuto ma si usano solo 0,0 l crediti al minuto, pertanto la VM accumulerà 0,1 x 60 = 6 crediti all'ora.  Per le 20 ore non di picco, si accumuleranno 120 crediti.  

Durante le ore di picco l'applicazione utilizza in media il 60% della vCPU, pertanto si accumulano comunque 0,2 crediti al minuto ma si usano 0,6 crediti al minuto, per un costo netto di 0,4 crediti un minuto o 0,4 x 60 = 24 crediti all'ora. Ogni giorno presenta 4 ore di picco di utilizzo, quindi il costo del picco di utilizzo è pari a 4 x 24 = 96 crediti.

Se si considerano i 120 crediti accumulati negli orari non di picco e si sottraggono i 96 crediti usati per le ore di picco, ogni giorni si accumulano 24 crediti che possono essere usati per altri burst delle attività.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>D: come è possibile calcolare I crediti accumulati e usati?

**R**: è possibile utilizzare la formula seguente:

(Prestazioni CPU di base della macchina virtuale-utilizzo CPU)/100 = crediti banca o uso al minuto

ad esempio, nell'istanza precedente la linea di base è il 20% e se si usa il 10% della CPU accumulata (20%-10%)/100 = 0,1 di credito al minuto.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>D: La serie B supporta i dischi dati di Archiviazione Premium?

**R**: Sì, tutte le dimensioni della serie B supportano dischi dati di Archiviazione Premium.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>D: Perché il credito rimanente è impostato su 0 dopo una ridistribuzione o un arresto/avvio?

**R**: quando una VM viene "RIDISTRIBUITA" e viene spostata in un altro nodo, il credito accumulato viene perso. Se la VM viene arrestata o avviata, ma resta nello stesso nodo, mantiene il credito accumulato. Ogni volta che la VM viene avviata per la prima volta in un nodo, ottiene un credito iniziale, che per Standard_B8ms è di 240 minuti.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>D: cosa accade se si distribuisce un'immagine del sistema operativo non supportata in B1ls?

**R** : B1ls supporta solo le immagini Linux e se si distribuisce un'altra immagine del sistema operativo, è possibile che non si ottenga la migliore esperienza utente.

## <a name="other-sizes-and-information"></a>Altre dimensioni e informazioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)

Calcolatore prezzi: [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)

Altre informazioni sui tipi di dischi: [tipi di disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-ssd-preview/)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
