---
title: Disponibilità di SAP HANA tra aree di Azure | Microsoft Docs
description: Fornisce una panoramica delle considerazioni relative alla disponibilità durante l'esecuzione di SPA HANA in macchine virtuali di Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilità di SAP HANA tra aree di Azure
In questo articolo vengono descritti ed esaminati scenari relativi alla disponibilità di SAP HANA in diverse aree di Azure. Poiché aree distinte di Azure si trovano a distanze più elevate le une dalle altre, in questo articolo sono elencate alcune considerazioni speciali.

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>Motivazione per la distribuzione in più aree di Azure
Diverse aree di Azure sono separate da una distanza maggiore. In base all'area geopolitica, potrebbe trattarsi di centinaia di chilometri o addirittura alcune migliaia di chilometri, ad esempio negli Stati Uniti. A causa della distanza tra le diverse aree di Azure, è possibile che il traffico di rete tra asset distribuiti in due aree diverse di Azure presenti una latenza significativa a livello di round trip di rete. La latenza è sufficientemente elevata da escludere lo scambio sincrono di dati tra due istanze di SAP HANA in un carico di lavoro SAP tipico. È tuttavia possibile che siano previsti requisiti specifici relativi alla distanza tra il data center primario e il data center secondario, in modo da assicurare la disponibilità in caso di disastro naturale in un'area più ampia, ad esempio gli uragani che hanno colpito i Caraibi e l'area della Florida nei mesi di settembre e ottobre 2017. È possibile che sia previsto almeno un requisito relativo alla distanza minima. Nella maggior parte dei casi relativi ai clienti, la definizione della distanza minima richiede la progettazione per la disponibilità nelle [aree di Azure](https://azure.microsoft.com/regions/). Poiché la distanza è troppo elevata tra due aree di Azure per l'uso della modalità di replica sincrona di HANA, è possibile che i requisiti di RTO e RPO impongano la distribuzione di configurazioni di disponibilità in un'area e quindi l'aggiunta di altre distribuzioni in una seconda area.

In queste configurazioni è necessario prendere in considerazione anche il failover e il reindirizzamento del client. Si presuppone che il failover tra le istanze di SAP HANA in due aree di Azure diverse sia sempre un failover manuale. Poiché la modalità di replica della replica di sistema di SAP HANA è impostata su asincrona, è possibile che i dati di cui è stato eseguito il commit nell'istanza primaria di HANA non siano ancora disponibili nell'istanza secondaria di HANA. Non è quindi possibile accettare il failover automatico per le configurazioni in cui la replica è asincrona. Anche con il failover controllato manualmente, ad esempio in un esercizio relativo al failover, è necessario assicurarsi che tutti i dati di cui è stato eseguito il commit sul lato primario siano disponibili nell'istanza secondaria prima di passare manualmente all'altra area di Azure.
 
Poiché si usa un diverso intervallo di indirizzi IP nelle reti virtuali di Azure, distribuite nella seconda area di Azure, i client di SAP HANA devono essere modificati a livello di configurazione oppure è necessario prevedere passaggi per la modifica della risoluzione dei nomi, in modo che i client vengano quindi reindirizzati al nuovo indirizzo IP del server dell'area secondaria. Per altri dettagli, vedere l'articolo [Client Connection Recovery after Takeover](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) (Ripristino della connessione client dopo l'acquisizione) di SAP.   

## <a name="simple-availability-between-two-azure-regions"></a>Semplice disponibilità tra due aree di Azure
In questo scenario è stato deciso di non definire alcuna configurazione di disponibilità in una singola area. È tuttavia necessario che il carico di lavoro sia disponibile in caso di emergenza. Sistemi di questo tipo sono in genere i sistemi non di produzione. Benché l'inattività del sistema per mezza giornata o addirittura per una giornata intera sia sostenibile, non è possibile consentire che il sistema non sia disponibile per 48 ore e oltre. Per ridurre i costi dell'installazione, eseguire un altro sistema ancora meno importante nella macchina virtuale che funge da destinazione oppure specificare dimensioni minori per la macchina virtuale nell'area secondaria e scegliere di non precaricare i dati. Poiché il failover sarà manuale e prevede un numero molto più elevato di passaggi per il failover anche dello stack completo dell'applicazione, il tempo aggiuntivo necessario per arrestare la macchina virtuale, ridimensionarla e riavviarla è accettabile.

> [!NOTE]
> Anche senza precaricamento dei dati nella destinazione della replica di sistema HANA, sono necessari almeno 64 GB di memoria, oltre a una quantità di memoria sufficiente per mantenere i dati rowstore nella memoria dell'istanza di destinazione.

![Due macchine virtuali in due aree](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> In questa configurazione non è possibile specificare un RPO=0, poiché la modalità di replica di sistema di HANA è asincrona. Se è necessario specificare un RPO=0, questa configurazione non è consigliabile.

Una piccola modifica alla configurazione può consistere nella configurazione del precaricamento dei dati. Considerati tuttavia la natura manuale del failover e il fatto che i livelli dell'applicazione devono passare anche alla seconda area, è possibile che il precaricamento dei dati non sia consigliabile. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combinare la disponibilità in un'area e tra diverse aree 
Una combinazione di disponibilità entro un'area e tra diverse aree può essere determinata dai fattori seguenti:

- Requisito di RPO=0 entro un'area di Azure.
- Impossibilità di accettare che le operazioni globali dell'azienda siano interessate da una grave catastrofe naturale che colpisce un'area più ampia, come ad esempio alcuni uragani che hanno colpito i Caraibi negli ultimi anni.
- Normative che richiedono distanze tra sito primario e secondario evidentemente superiori rispetto a quanto offerto dalle zone di disponibilità di Azure.

 
In questi casi è consigliabile configurare quella che SAP definisce una [configurazione di replica di sistema multilivello di SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) con la replica di sistema HANA. L'architettura avrà un aspetto analogo al seguente:

![Tre macchine virtuali in due aree](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Questa configurazione fornisce un RPO=0 con tempi di RTO ridotti entro l'area primaria e fornisce anche RPO decrescente in caso di spostamento nella seconda area. I tempi di RTO nella seconda area dipendono dal fatto che si configuri o meno i precaricamento dei dati. In molti casi relativi ai clienti la macchina virtuale nell'area secondaria viene usata per eseguire un sistema di test. Come risultato di tale utilizzo non sarà possibile precaricare i dati.

> [!NOTE]
> Poiché si usa la modalità operativa logreplay per la replica di sistema HANA per il passaggio dal livello 1 al livello 2 (replica sincrona nell'area primaria), la replica tra il livello 2 e il livello 3 (replica nel sito secondario) non può essere eseguita in modalità operativa delta_datashipping. Per dettagli delle modalità operative e alcune restrizioni documentate da SAP, vedere [Operation Modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) (Modalità operative per la replica di sistema di SAP HANA). 

## <a name="next-steps"></a>Passaggi successivi
Se sono necessarie indicazioni dettagliate su come impostare una configurazione di questo tipo in Azure, leggere gli articoli:

- [Configurare la replica di sistema SAP HANA nelle macchine virtuali di Azure](sap-hana-high-availability.md)
- [Your SAP on Azure – Part 4 – High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (SAP in Azure – Parte 4 – Disponibilità elevata per SAP HANA tramite la replica di sistema)

 



 
  
