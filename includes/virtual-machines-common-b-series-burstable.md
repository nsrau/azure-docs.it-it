---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 6a3e2034792fdc0a4a8fed7885c7d5ad78ea24d9
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501247"
---
La famiglia di macchine virtuali della serie B consente di scegliere le dimensioni di VM che offrono le prestazioni di livello base necessarie per il carico di lavoro, con la possibilità di aumentare le prestazioni della CPU fino al 100% per una vCPU con processore Intel® Broadwell E5-2673 v4 2.3 GHz o Intel® Haswell 2.4 GHz E5-2673 v3.

Le macchine virtuali della serie B sono ideali per carichi di lavoro che non necessitano delle prestazioni complete della CPU in modo continuo, ad esempio server Web, modelli di verifica, database di piccole dimensioni e ambienti di sviluppo build. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre la possibilità di acquistare una dimensione di VM con prestazioni baseline e l'istanza della VM accumula crediti quando il relativo utilizzo è inferiore alla baseline. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline, usando fino al 100% della vCPU quando l'applicazione richiede prestazioni superiori per la CPU.

La serie B è disponibile in dimensioni di macchina virtuale seguenti:

| Dimensione             | vCPU  | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Prestazioni base della CPU della VM | Prestazioni massime della CPU della VM | Crediti iniziali | Crediti accumulati/Ora | Crediti massimi accumulati | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                  | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls è supportato solo in Linux

## <a name="workload-example"></a>Esempio di carico di lavoro

Si consideri un'applicazione di check-in/out di office. L'applicazione deve picchi della CPU durante le ore lavorative, ma non una grande quantità di potenza di calcolo durante gli orari di minore. In questo esempio, il carico di lavoro richiede una macchina virtuale 16vCPU con 64GiB di RAM per lavorare in modo efficiente.

La tabella mostra i dati di traffico oraria e il grafico è una rappresentazione visiva del traffico.

B16 caratteristiche:

Prestazioni di CPU massimo: 16vCPU * 100% = 1600%

Linea di base: 270%

![Grafico dei dati sul traffico di ogni ora](./media/virtual-machines-common-b-series-burstable/office-workload.png)

| Scenario | Time | Utilizzo della CPU (%) | I crediti accumulati<sup>1</sup> | Crediti disponibili |
| --- | --- | --- | --- | --- |
| Distribuzione B16ms | Distribuzione | Distribuzione  | 480 (crediti iniziali) | 480 |
| Nessun traffico | 0:00 | 0 | 162 | 642 |
| Nessun traffico | 1:00 | 0 | 162 | 804 |
| Nessun traffico | 2:00 | 0 | 162 | 966 |
| Nessun traffico | 3:00 | 0 | 162 | 1128 |
| Nessun traffico | 4:00 | 0 | 162 | 1290 |
| Nessun traffico | 5:00 | 0 | 162 | 1452 |
| Traffico ridotto | 6:00 | 270 | 0 | 1452 |
| I dipendenti si per office (l'app deve 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| I dipendenti continuano presto disponibili in office (l'app deve 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Traffico ridotto | 9:00 | 270 | 0 | 240 |
| Traffico ridotto | 10:00 | 100 | 102 | 342 |
| Traffico ridotto | 11:00 | 50 | 132 | 474 |
| Traffico ridotto | 12:00 | 100 | 102 | 576 |
| Traffico ridotto | 13:00 | 100 | 102 | 678 |
| Traffico ridotto | 14:00 | 50 | 132 | 810 |
| Traffico ridotto | 15:00 | 100 | 102 | 912 |
| Traffico ridotto | 16:00 | 100 | 102 | 1014 |
| Dipendenti il check-out (app esigenze 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Traffico ridotto | 18:00 | 270 | 0 | 216 |
| Traffico ridotto | 19:00 | 270 | 0 | 216 |
| Traffico ridotto | 20:00 | 50 | 132 | 348 |
| Traffico ridotto | 21:00 | 50 | 132 | 480 |
| Nessun traffico | 22:00 | 0 | 162 | 642 |
| Nessun traffico | 23:00 | 0 | 162 | 804 |

<sup>1</sup> crediti accumulati/crediti usati in un'ora è equivalente a: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Per un D16s_v3 con 16 Vcpu e 64 GiB di memoria alla tariffa oraria è $0,936 ogni ora (mensile $673.92) e per B16ms con 16 Vcpu e 64 GiB di memoria la frequenza è $0.794 ogni ora (mensile $547.86). <b> Di conseguenza 15% di risparmio.</b>

## <a name="q--a"></a>Domande e risposte

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>D: Come ottenere prestazioni baseline 135% da una macchina virtuale?
**R**: 135% è condivisa tra 8 vCPU che costituiscono le dimensioni VM. Se, ad esempio, l'applicazione usa 4 degli 8 core dedicati all'elaborazione batch e ognuna di queste 4 vCPU è in esecuzione con un livello di utilizzo pari al 30%, la quantità totale di prestazioni della CPU della VM equivale al 120%.  La macchina virtuale, quindi, accumula tempo di credito in base al differenziale del 15% rispetto alle prestazioni baseline.  Quando sono disponibili crediti, la stessa VM può quindi usare il 100% di tutte le 8 vCPU, ottenendo un livello massimo di prestazioni della CPU pari all'800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>D: Come si monitora il saldo del credito e al consumo
**R**: 2 nuove metriche verranno introdotti nelle prossime settimane, il **carta di credito** metrica modo sarà possibile visualizzare quanti crediti accumulati la macchina virtuale e il **ConsumedCredit** metrica mostrerà quanti crediti CPU dispone di una macchina virtuale utilizzato dalla banca.    Sarà possibile visualizzare queste metriche dal riquadro delle metriche nel portale o a livello di programmazione tramite le API di Monitoraggio di Azure.

Per altre informazioni su come accedere ai dati delle metriche per Azure, vedere [Panoramica delle metriche in Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>D: Come vengono accumulati i crediti?
**R**: Le tariffe di accumulo e l'utilizzo della macchina virtuale vengono impostate in modo che disporrà di una macchina virtuale che esegue esattamente del livello di prestazioni di base né un accumulo netto o alcun utilizzo di crediti di burst.  Una VM presenterà un incremento netto in crediti in caso di esecuzione a un livello inferiore al rispettivo livello di prestazioni di base e presenterà una riduzione netta in crediti in caso di utilizzo della CPU da parte della VM a un livello superiore rispetto al rispettivo livello di prestazioni di base.

**Esempio**:  È possibile distribuire una macchina virtuale usando le dimensioni B1ms per il tempo di piccole e applicazione di database attendance. Queste dimensioni consentono all'applicazione di usare fino al 20% di una vCPU come baseline, che corrisponde a 0,2 crediti al minuto che si possono usare o accumulare. 

L'applicazione è occupata all'inizio o alla fine della giornata lavorativa dei dipendenti, tra le 7:00-9:00 e le 16:00-18:00. Durante le altre 20 ore della giornata l'applicazione è solitamente inattiva e usa solo il 10% della vCPU. Per gli orari non di picco, si accumulano 0,2 crediti al minuto ma si usano solo 0,0 l crediti al minuto, pertanto la VM accumulerà 0,1 x 60 = 6 crediti all'ora.  Per le 20 ore non di picco, si accumuleranno 120 crediti.  

Durante le ore di picco l'applicazione utilizza in media il 60% della vCPU, pertanto si accumulano comunque 0,2 crediti al minuto ma si usano 0,6 crediti al minuto, per un costo netto di 0,4 crediti un minuto o 0,4 x 60 = 24 crediti all'ora. Ogni giorno presenta 4 ore di picco di utilizzo, quindi il costo del picco di utilizzo è pari a 4 x 24 = 96 crediti.

Se si considerano i 120 crediti accumulati negli orari non di picco e si sottraggono i 96 crediti usati per le ore di picco, ogni giorni si accumulano 24 crediti che possono essere usati per altri burst delle attività.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>D: Come è possibile calcolare i crediti accumulati e usato?
**R**: È possibile utilizzare la formula seguente: 

(Di base prestazioni CPU della VM - utilizzo CPU) / 100 = bank crediti o utilizzo al minuto

la linea di base, ad esempio, in precedenza istanza è 20% e se si utilizza 10% della CPU si accumulano (20% - 10%) / 100 = 0,1 di credito al minuto.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>D: La serie B supporta i dischi dati di archiviazione Premium?
**R**: Sì, tutte le dimensioni della serie B supportano dischi dati di archiviazione Premium.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>D: Il motivo per cui è viene impostato il credito rimanente su 0 dopo la distribuzione o un avvio/arresto?
**A** : Quando una macchina virtuale è "REDPLOYED" e la VM viene spostata in un altro nodo, il credito accumulato è perso. Se la VM viene arrestata o avviata, ma resta nello stesso nodo, mantiene il credito accumulato. Ogni volta che la VM viene avviata per la prima volta in un nodo, ottiene un credito iniziale, che per Standard_B8ms è di 240 minuti.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>D: Cosa accade se si distribuisce un'immagine del sistema operativo non supportata nel B1ls?
**A** : B1ls supporta solo le immagini Linux e se si distribuisce qualsiasi altra immagine del sistema operativo si potrebbe ottenere la migliore esperienza dei clienti.
