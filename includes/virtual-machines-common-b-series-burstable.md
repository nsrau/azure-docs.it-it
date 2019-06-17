---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: ecf70bbbeae8fd68309f3343615f021038fb10b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111441"
---
La famiglia di macchine virtuali della serie B consente di scegliere le dimensioni di VM che offrono le prestazioni di livello base necessarie per il carico di lavoro, con la possibilità di aumentare le prestazioni della CPU fino al 100% per una vCPU con processore Intel® Broadwell E5-2673 v4 2.3 GHz o Intel® Haswell 2.4 GHz E5-2673 v3.

Le macchine virtuali della serie B sono ideali per carichi di lavoro che non necessitano delle prestazioni complete della CPU in modo continuo, ad esempio server Web, modelli di verifica, database di piccole dimensioni e ambienti di sviluppo build. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre la possibilità di acquistare una dimensione di VM con prestazioni baseline e l'istanza della VM accumula crediti quando il relativo utilizzo è inferiore alla baseline. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline, usando fino al 100% della vCPU quando l'applicazione richiede prestazioni superiori per la CPU.

La serie B offre le sei dimensioni seguenti per le macchine virtuali:

| Dimensione             | vCPU  | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Prestazioni base della CPU della VM | Prestazioni massime della CPU della VM | Crediti di iniziali | Crediti accumulati/Ora | Crediti massimi accumulati | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps | Velocità effettiva massima del disco senza memorizzazione nella cache: IOPS/MBps | Schede di interfaccia di rete max |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls è supportato solo in Linux

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


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>D: La serie B supporta i dischi dati di archiviazione Premium?
**R**: Sì, tutte le dimensioni della serie B supportano dischi dati di archiviazione Premium.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>D: Il motivo per cui è viene impostato il credito rimanente su 0 dopo la distribuzione o un avvio/arresto?
**A** : Quando una macchina virtuale è "REDPLOYED" e la VM viene spostata in un altro nodo, il credito accumulato è perso. Se la VM viene arrestata o avviata, ma resta nello stesso nodo, mantiene il credito accumulato. Ogni volta che la VM viene avviata per la prima volta in un nodo, ottiene un credito iniziale, che per Standard_B8ms è di 240 minuti.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>D: Cosa accade se si distribuisce un'immagine del sistema operativo non supportata nel B1ls?
**A** : B1ls supporta solo le immagini Linux e se si distribuisce qualsiasi altra immagine del sistema operativo si potrebbe ottenere la migliore esperienza dei clienti.
    
### <a name="q-why-is-there-no-pricing-information-for-b1ls-windows"></a>D: Il motivo per cui non è disponibile alcuna informazioni sui prezzi per windows B1ls?
**A** : B1ls supporta solo le immagini Linux e se si distribuisce qualsiasi un'altra immagine del sistema operativo non è possibile ottenere la migliore esperienza cliente, ma ti verranno addebitate.


    

    
