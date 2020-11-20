---
title: SKU per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: SKU per SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKU, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd979f553ff03d8373948d73c416a96bb140b544
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964763"
---
# <a name="available-skus-for-hana-large-instances"></a>SKU disponibili per le istanze large di HANA

SAP HANA in Azure (istanze large) in base ai soli timbri di revisione 3, è disponibile in diverse configurazioni nelle aree di Azure di:

- Australia orientale
- Australia sud-orientale
- Giappone orientale
- Giappone occidentale

Il servizio SAP HANA in Azure (istanze large) basato su indicatori di revisione 4 è disponibile in diverse configurazioni nelle aree di Azure di:

- West US 2
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-meridionali
- Europa occidentale
- Europa settentrionale



L'elenco delle istanze di grandi dimensioni di Azure disponibili sono elencate come quelle riportate di seguito.

> [!IMPORTANT]
> Tenere presente la prima colonna che rappresenta lo stato della certificazione HANA per ogni tipo di istanza di grandi dimensioni nell'elenco. La colonna deve essere correlata alla [directory hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) per gli SKU di Azure che iniziano con la lettera **S**



| SAP HANA Certified | Modellare | Memoria totale | Memoria DRAM | Memoria Optane | Archiviazione | Disponibilità |
| --- | --- | --- | --- | --- | --- | --- |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA in Azure S96<br /> -2 x processore Intel® Xeon® E7-8890 V4 <br /> 48 core CPU e 96 thread CPU |  768 GB | 768 GB | --- | 3 TB | Disponibile |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA in Azure S224<br /> – 4 x processore Intel® Xeon® Platinum 8276 <br /> 112 core CPU e 224 thread CPU |  3 TB | 3 TB | --- | 6,3 TB | Disponibile |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA in Azure S224m<br /> – 4 x processore Intel® Xeon® Platinum 8276 <br /> 112 core CPU e 224 thread CPU |  6 TB | 6 TB | --- | 10,5 TB | Disponibile |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA in Azure S224om<br /> – 4 x processore Intel® Xeon® Platinum 8276 <br /> 112 core CPU e 224 thread CPU | 6 TB |  3 TB |  3 TB | 10,5 TB | Disponibile |
| NO | SAP HANA in Azure S224oo<br /> – 4 x processore Intel® Xeon® Platinum 8276 <br /> 112 core CPU e 224 thread CPU | 4,5 TB |  1,5 TB |  3 TB | 8,4 TB | Disponibile |
| NO | SAP HANA in Azure S224ooo<br /> – 4 x processore Intel® Xeon® Platinum 8276 <br /> 112 core CPU e 224 thread CPU | 7,5 TB |  1,5 TB |  6 TB | 12,7 TB | Disponibile |
| NO | SAP HANA in Azure S224oom<br /> – 4 x processore Intel® Xeon® Platinum 8276 <br /> 112 core CPU e 224 thread CPU | 9,0 TB |  3 TB |  6 TB | 14,8 TB | Disponibile |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA in Azure S384<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  4 TB | 4 TB | --- | 16 TB | Disponibile |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA in Azure S384m<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  6 TB | 6 TB | --- | 18 TB |  Disponibile  |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA in Azure S384xm<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  8 TB | 8 TB | --- | 22 TB | Disponibile |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA in Azure S448<br /> – 8 x processore Intel® Xeon® Platinum 8276 <br /> 224 core CPU e 448 thread CPU | 6 TB |  6 TB |  --- | 10,5 TB | Disponibile (solo Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA in Azure S448m<br /> – 8 x processore Intel® Xeon® Platinum 8276 <br /> 224 core CPU e 448 thread CPU | 12 TB |  12 TB |  --- | 18,9 TB | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S448oo<br /> – 8 x processore Intel® Xeon® Platinum 8276 <br /> 224 core CPU e 448 thread CPU | 9,0 TB |  3 TB |  6 TB | 14,8 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S448om<br /> – 8 x processore Intel® Xeon® Platinum 8276 <br /> 224 core CPU e 448 thread CPU | 12 TB |  6 TB |  6 TB | 18,9 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S448ooo<br /> – 8 x processore Intel® Xeon® Platinum 8276 <br /> 224 core CPU e 448 thread CPU | 15,0 TB |  3 TB |  12 TB | 23,2 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S448oom<br /> – 8 x processore Intel® Xeon® Platinum 8276 <br /> 224 core CPU e 448 thread CPU | 18,0 TB |  6 TB |  12 TB | 27,4 TB  | Disponibile (solo Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA in Azure S576m<br /> – 12 x processore Intel® Xeon® E7-8890 v4<br /> 288 core CPU e 576 thread CPU |  12 TB | 12 TB | --- | 28 TB | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S576xm<br /> – 12 x processore Intel® Xeon® E7-8890 v4<br /> 288 core CPU e 576 thread CPU |  18,0 TB | 18.0 | --- |  41 TB | Disponibile |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA in Azure S672<br /> – 12 x processore Intel® Xeon® Platinum 8276 <br /> 336 core CPU e 672 thread CPU | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Disponibile (solo Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA in Azure S672m<br /> – 12 x processore Intel® Xeon® Platinum 8276 <br /> 336 core CPU e 672 thread CPU | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S672oo<br /> – 12 x processore Intel® Xeon® Platinum 8276 <br /> 336 core CPU e 672 thread CPU | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S672om<br /> – 12 x processore Intel® Xeon® Platinum 8276 <br /> 336 core CPU e 672 thread CPU | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S672ooo<br /> – 12 x processore Intel® Xeon® Platinum 8276 <br /> 336 core CPU e 672 thread CPU | 22.5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S672oom<br /> – 12 x processore Intel® Xeon® Platinum 8276 <br /> 336 core CPU e 672 thread CPU | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Disponibile (solo Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA in Azure S768m<br /> – 16 x processore Intel® Xeon® E7-8890 v4<br /> 384 core CPU e 768 thread CPU |  16 TB | 16 TB | -- | 36 TB | Disponibile |
| NO | SAP HANA in Azure S768xm<br /> – 16 x processore Intel® Xeon® E7-8890 v4<br /> 384 core CPU e 768 thread CPU |  24,0 TB | 24,0 TB | --- | 56 TB | Disponibile |
|  YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA in Azure S896<br /> -16 x processore Intel® Xeon® Platinum 8276 <br /> 448 core CPU e 896 thread CPU | 12 TB |  12 TB |  --- | 18,9 TB | Disponibile (solo Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA in Azure S896m<br /> -16 x processore Intel® Xeon® Platinum 8276 <br /> 448 core CPU e 896 thread CPU | 24,0 TB | 24,0 TB | -- | 35,8 TB | Disponibile |
| NO | SAP HANA in Azure S896oo<br /> -16 x processore Intel® Xeon® Platinum 8276 <br /> 448 core CPU e 896 thread CPU | 18,0 TB |  6 TB |  12 TB | 27,4 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S896om<br /> -16 x processore Intel® Xeon® Platinum 8276 <br /> 448 core CPU e 896 thread CPU | 24,0 TB |  12 TB |  12 TB | 35,8 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S896ooo<br /> -16 x processore Intel® Xeon® Platinum 8276 <br /> 448 core CPU e 896 thread CPU | 30,0 TB |  6 TB |  24,0 TB | 44,3 TB  | Disponibile (solo Rev 4) |
| NO | SAP HANA in Azure S896oom<br /> -16 x processore Intel® Xeon® Platinum 8276 <br /> 448 core CPU e 896 thread CPU | 36,0 TB |  12 TB |  24,0 TB | 52,7 TB  | Disponibile (solo Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA in Azure S960m<br /> – 20 x processore Intel® Xeon® E7-8890 v4<br /> 480 core CPU e 960 thread CPU |  20 TB | 20 TB | -- | 46 TB | Disponibile (solo Rev 4) |


- Core CPU: somma di core CPU senza hyperthreading nella somma dei processori dell'unità server.
- Thread CPU: somma di thread di calcolo forniti da core CPU con hyperthreading nella somma dei processori dell'unità server. Per impostazione predefinita, la maggior parte delle unità sono configurate per l'uso di Hyper-Threading Technology.
- Conformemente ai consigli del fornitore, S768m, S768xm e S960m non sono configurate per l'uso di Hyper-Threading quando eseguono SAP HANA.


> [!IMPORTANT]
> Non è più possibile acquistare gli SKU seguenti, anche se ancora supportati: S72, S72m, S144, S144m, S192 e S192m 

Le configurazioni specifiche scelte dipendono dal carico di lavoro, dalle risorse della CPU e dalla memoria desiderata. È possibile che il carico di lavoro OLTP sfrutti gli SKU ottimizzati per il carico di lavoro OLAP. 

Gli SKU sono suddivisi in due diverse classi di hardware:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 e S224m, S224oo, S224om, S224ooo, S224oom sono denominate "classe di tipo I" degli SKU.
- Tutti gli altri SKU sono detti "classe di tipo II" degli SKU.
- Se si è interessati a SKU non ancora elencati nella directory hardware SAP, contattare il team di account Microsoft per ottenere altre informazioni. 


Un intero modulo per istanze Large di HANA non viene allocato esclusivamente per l'uso di un singolo cliente. Ciò vale anche per i rack delle risorse di calcolo e archiviazione connesse tramite un'infrastruttura di rete distribuita in Azure. Analogamente ad Azure, l'infrastruttura di istanze Large di HANA distribuisce diversi &quot;tenant&quot; dei clienti, isolati l'uno dall'altro, nei tre livelli seguenti:

- **Rete**: isolamento tramite reti virtuali all'interno del timbro di istanze large di Hana.
- **Archiviazione**: isolamento tramite macchine virtuali di archiviazione a cui sono assegnati volumi di archiviazione e isolamento dei volumi di archiviazione tra i tenant.
- **Calcolo**: assegnazione dedicata di unità server a un singolo tenant. Nessun partizionamento hardware o software delle unità server. Nessuna condivisione di una singola unità server o host tra i tenant. 

Le distribuzioni di unità di istanze Large di HANA tra tenant diversi non risultano visibili l'una all'altra. Le unità di istanze Large di HANA distribuite in tenant diversi non possono comunicare direttamente tra loro a livello del modulo per istanze Large di HANA. Questa comunicazione è possibile solo per le unità di istanze Large di HANA all'interno di un solo tenant.

A un tenant distribuito nel modulo per istanze Large viene assegnata una sola sottoscrizione di Azure ai fini della fatturazione. A livello di rete, tuttavia, il tenant è accessibile dalle reti virtuali di altre sottoscrizioni di Azure nell'ambito della stessa iscrizione ad Azure. Se si esegue la distribuzione con un'altra sottoscrizione di Azure nella stessa area di Azure, si può anche scegliere di richiedere un tenant per istanze Large di HANA separato.

Vi sono differenze significative tra l'esecuzione di SAP HANA nelle istanze Large di HANA e quella nelle VM distribuite in Azure:

- Non esiste alcun livello di virtualizzazione per SAP HANA in Azure (istanze di grandi dimensioni). Si ottengono le prestazioni dell'hardware bare metal sottostante.
- A differenza di Azure, il server di SAP HANA in Azure (istanze di grandi dimensioni) è dedicato a un cliente specifico. Un'unità server, o host, non può essere in alcun modo partizionata a livello di hardware o software. Di conseguenza, un'unità di istanze Large di HANA viene usata come se fosse assegnata per intero a un tenant e con il tenant assegnato al singolo cliente. Un riavvio o un arresto del server non comporta automaticamente la distribuzione del sistema operativo e di SAP HANA in un altro server. Per gli SKU della classe di tipo I, l'unica eccezione è data dal caso in cui vengono riscontrati problemi in un server ed è necessario eseguire la ridistribuzione in un altro server.
- A differenza di Azure, in cui i tipi di processore host vengono selezionati per il miglior rapporto prezzo/prestazioni, per SAP HANA in Azure (istanze Large) vengono scelti i tipi di processore con le prestazioni più elevate della linea di processori Intel E7v3 ed E7v4.

**Passaggi successivi**
- Vedere [ridimensionamento HLI ](hana-sizing.md)
