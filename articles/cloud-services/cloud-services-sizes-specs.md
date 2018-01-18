---
title: Dimensioni delle macchine virtuali per i servizi cloud di Azure | Documentazione Microsoft
description: Elenco delle diverse dimensioni e ID delle macchine virtuali per i ruoli Web e di lavoro del servizio cloud di Azure.
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 7bef7643a989caee846f8235e024deb482f4b0a0
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="sizes-for-cloud-services"></a>Dimensioni dei servizi cloud
Questo argomento descrive le dimensioni e le opzioni disponibili per le istanze del ruolo di servizio cloud, come ruoli Web e di lavoro. Fornisce anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse. Ogni dimensione dispone di un ID da inserire nel [file di definizione del servizio](cloud-services-model-and-package.md#csdef). I prezzi per ogni dimensione sono disponibili nella pagina relativa ai [prezzi dei servizi cloud](https://azure.microsoft.com/pricing/details/cloud-services/).

> [!NOTE]
> Per i limiti di Azure correlati, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Dimensioni delle istanze del ruolo Web e di lavoro
In Azure sono disponibili più dimensioni standard. Si tengano presenti le considerazioni seguenti per alcune di queste dimensioni:

* Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo. Per ulteriori informazioni, vedere l'annuncio sul blog di Azure, [Nuove dimensioni delle macchine virtuali serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* Le serie Dv3 e Dv2, versioni successive della serie D originale, sono dotate di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswell) e grazie alla tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,1 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.
* Le VM serie G offrono la maggiore quantità di memoria e vengono eseguite su host con processori della famiglia Intel Xeon E5 V3.
* Le VM della serie A possono essere distribuite su vari tipi di hardware e processori. La dimensione è limitata, in base all'hardware, per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale.
* La sottoscrizione della dimensione A0 è eccessiva nell'hardware fisico. Solo per questa dimensione specifica, altre distribuzioni dei clienti possono compromettere le prestazioni del carico di lavoro in esecuzione. Le prestazioni relative sono indicate di seguito come linea di base prevista, con variabilità approssimativa del 15%.

La dimensione della macchina virtuale influisce sul prezzo. Influisce, inoltre, sull'elaborazione, sulla memoria e sulla capacità di archiviazione della macchina virtuale. I costi di archiviazione vengono calcolati separatamente in base alle pagine usate nell'account di archiviazione. Per informazioni dettagliate, vedere i [dettagli sui prezzi dei servizi cloud](https://azure.microsoft.com/pricing/details/cloud-services/) e [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Le considerazioni seguenti potrebbero essere utili all'utente per scegliere una dimensione:

* Le dimensioni delle serie A8-A11 e H sono note anche come *istanze a elevato uso di calcolo*. L'hardware che esegue queste dimensioni è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, come applicazioni cluster HPC, modellazione e simulazioni. La serie A8-A11 usa Intel Xeon E5-2670 a 2,6 GHZ, mentre la serie H usa Intel Xeon E5-2667 v3 a 3,2 GHz. Per informazioni e considerazioni dettagliate sull'uso di queste dimensioni, vedere [Dimensioni delle VM High Performance Computing (HPC)](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Le serie Dv3, Dv2, D e G sono ideali per applicazioni che richiedono CPU più veloci, prestazioni migliori dei dischi locali o una quantità più elevata di memoria. Offrono una potente combinazione per molte applicazioni di livello aziendale.
* Alcuni degli host fisici nei data center di Azure potrebbero non supportare macchine virtuali di dimensioni superiori, ad esempio da A5 ad A11. Di conseguenza, potrebbe essere visualizzato il messaggio di errore **Impossibile configurare la macchina virtuale {nome macchina virtuale}** o **Impossibile creare la macchina virtuale {nome macchina virtuale}** quando si configura una macchina virtuale esistente in base a una nuova dimensione, si crea una nuova macchina virtuale in una rete virtuale creata prima del 16 aprile 2013 o si aggiunge una nuova macchina a un servizio cloud esistente. Vedere [Errore: "Impossibile configurare la macchina virtuale"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) nel forum di supporto per le soluzioni alternative per ogni scenario di distribuzione.
* La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni . Per aumentare la quota, contattare il supporto tecnico di Azure.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni
È stato creato il concetto di unità di calcolo di Azure (ACU, Azure Compute Unit) per mettere a punto un modo per confrontare le prestazioni di calcolo (CPU) degli SKU di Azure e identificare lo SKU in grado di soddisfare con più probabilità le esigenze di prestazioni.  L'unità ACU adotta come standard una macchina virtuale Small (Standard_A1), a cui attribuisce il valore 100. Per tutte le altre SKU sarà quindi possibile valutare la maggiore velocità di elaborazione con cui sono in grado di eseguire un benchmark standard.

> [!IMPORTANT]
> L'unità ACU costituisce solo un'indicazione di riferimento. I risultati possono variare in base al carico di lavoro.
>
>

<br>

| Famiglia SKU | ACU/Core |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Small-ExtraLarge](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [A v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |160 - 190* |
| [D v3](#dv3-series) |160 - 190* |
| [E v3](#ev3-series) |160 - 190* |
| [G](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

Le unità ACU contrassegnate con * usano la tecnologia Intel® Turbo per aumentare la frequenza della CPU e offrire un miglioramento delle prestazioni. L'entità dell'aumento di prestazioni può variare in base alle dimensioni della macchina virtuale, al carico di lavoro e agli altri carichi di lavoro in esecuzione sullo stesso host.

## <a name="size-tables"></a>Tabelle delle dimensioni
Le tabelle seguenti illustrano le dimensioni e le capacità offerte da ogni dimensione.

* La capacità di archiviazione viene visualizzata in unità di GiB o 1.024^3 byte. Quando si confrontano dischi misurati in GB (1.000^3 byte) con dischi misurati in GiB (1.024^3), tenere presente che i valori di capacità specificati in GiB potrebbero apparire inferiori. Ad esempio, 1.023 GiB = 1.098,4 GB
* La velocità effettiva del disco viene misurata in operazioni di input/output al secondo (IOPS) e MBps, dove il valore di MBps corrisponde a 10^6 byte al secondo.
* I dischi dati possono operare in modalità memorizzata nella cache o non memorizzata nella cache. Per il funzionamento dei dischi dati memorizzati nella cache, la modalità di cache host è impostata su **ReadOnly** o su **ReadWrite**. Per il funzionamento dei dischi dati non memorizzati nella cache, la modalità di cache host è impostata su **None**.
* La larghezza di banda della rete massima è la larghezza di banda aggregata massima allocata e assegnata a ogni tipo di VM. La larghezza di banda massima fornisce indicazioni per selezionare il tipo di VM corretto per garantire la disponibilità della capacità di rete adeguata. Quando si passa da bassa, moderata, alta e molto alta, la velocità effettiva aumenta di conseguenza. Le prestazioni di rete effettive dipenderanno da molti fattori, tra cui carichi di rete e dell'applicazione e le impostazioni di rete dell'applicazione.

## <a name="a-series"></a>Serie A
| Dimensione            | Core CPU | Memoria: GiB  | Unità HDD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Molto piccola      | 1         | 0,768        | 20                   | 1/bassa |
| Piccolo           | 1         | 1,75         | 225                  | 1/moderata |
| Medio          | 2         | 3,5 GB       | 490                  | 1/moderata |
| Grande           | 4         | 7            | 1000                 | 2/alta |
| Molto grande      | 8         | 14           | 2040                 | 4/alta |
| A5              | 2         | 14           | 490                  | 1/moderata |
| A6              | 4         | 28           | 1000                 | 2/alta |
| A7              | 8         | 56           | 2040                 | 4/alta |

## <a name="a-series---compute-intensive-instances"></a>Serie A - Istanze a elevato utilizzo di calcolo
Per informazioni e considerazioni sull'uso di queste dimensioni, vedere [Dimensioni delle VM High Performance Computing (HPC)](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Dimensione            | Core CPU | Memoria: GiB  | Unità HDD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2/alta |
| A9*             |16         | 112          | 1817                 | 4/molto alta |
| A10             |8          | 56           | 1817                 | 2/alta |
| A11             |16         | 112          | 1817                 | 4/molto alta |

\*Con supporto di RDMA

## <a name="av2-series"></a>Serie Av2

| Dimensione            | Core CPU | Memoria: GiB  | Unità SSD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1/moderata                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/moderata                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4/alta                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8/alta                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/moderata                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4/alta                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8/alta                     |


## <a name="d-series"></a>Serie D
| Dimensione            | Core CPU | Memoria: GiB  | Unità SSD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3,5          | 50                   | 1/moderata |
| Standard_D2     | 2         | 7            | 100                  | 2/alta |
| Standard_D3     | 4         | 14           | 200                  | 4/alta |
| Standard_D4     | 8         | 28           | 400                  | 8/alta |
| Standard_D11    | 2         | 14           | 100                  | 2/alta |
| Standard_D12    | 4         | 28           | 200                  | 4/alta |
| Standard_D13    | 8         | 56           | 400                  | 8/alta |
| Standard_D14    | 16        | 112          | 800                  | 8/molto alta |

## <a name="dv2-series"></a>Serie Dv2
| Dimensione            | Core CPU | Memoria: GiB  | Unità SSD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3,5          | 50                   | 1/moderata |
| Standard_D2_v2  | 2         | 7            | 100                  | 2/alta |
| Standard_D3_v2  | 4         | 14           | 200                  | 4/alta |
| Standard_D4_v2  | 8         | 28           | 400                  | 8/alta |
| Standard_D5_v2  | 16        | 56           | 800                  | 8/estremamente alta |
| Standard_D11_v2 | 2         | 14           | 100                  | 2/alta |
| Standard_D12_v2 | 4         | 28           | 200                  | 4/alta |
| Standard_D13_v2 | 8         | 56           | 400                  | 8/alta |
| Standard_D14_v2 | 16        | 112          | 800                  | 8/estremamente alta |
| Standard_D15_v2 | 20        | 140          | 1.000                | 8/estremamente alta |

## <a name="dv3-series"></a>Serie Dv3

| Dimensione            | Core CPU | Memoria: GiB   | Unità SSD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 16                   | 2/moderata |
| Standard_D4_v3  | 4         | 16            | 32                   | 2/alta |
| Standard_D8_v3  | 8         | 32            | 64                   | 4/alta |
| Standard_D16_v3 | 16        | 64            | 128                  | 8/estremamente alta |
| Standard_D32_v3 | 32        | 128           | 256                  | 8/estremamente alta |
| Standard_D64_v3 | 64        | 256           | 512                  | 8/estremamente alta |

## <a name="ev3-series"></a>Serie Ev3

| Dimensione            | Core CPU | Memoria: GiB   | Unità SSD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 32                   | 2/moderata |
| Standard_E4_v3  | 4         | 32            | 64                   | 2/alta |
| Standard_E8_v3  | 8         | 64            | 128                  | 4/alta |
| Standard_E16_v3 | 16        | 128           | 256                  | 8/estremamente alta |
| Standard_E32_v3 | 32        | 256           | 512                  | 8/estremamente alta |
| Standard_E64_v3 | 64        | 432           | 864                  | 8/estremamente alta |


## <a name="g-series"></a>Serie G
| Dimensione            | Core CPU | Memoria: GiB  | Unità SSD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/alta |
| Standard_G2     | 4         | 56           | 768                  |2/alta |
| Standard_G3     | 8         | 112          | 1.536                |4/molto alta |
| Standard_G4     | 16        | 224          | 3.072                |8/estremamente alta |
| Standard_G5     | 32        | 448          | 6.144                |8/estremamente alta |

## <a name="h-series"></a>Serie H
Le macchine virtuali serie H di Azure sono le VM high performance computing di prossima generazione che puntano a risolvere esigenze di calcolo di fascia alta, come modellazione molecolare e fluidodinamica computazionale. Queste VM a 8 e 16 core sono basate sulla tecnologia del processore Intel Haswell E5-2667 V3 con memoria DDR4 e archiviazione basata su SSD locale.

Oltre alla sostanziale potenza della CPU, la serie H offre diverse opzioni per rete RDMA a bassa latenza con FDR InfiniBand e diverse configurazioni di memoria a supporto di requisiti di calcolo a elevato uso di memoria.

| Dimensione            | Core CPU | Memoria: GiB  | Unità SSD locale: GiB       | Larghezza di banda della rete/scheda NIC max |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8/alta |
| Standard_H16    | 16        | 112          | 2000                 | 8/molto alta |
| Standard_H8m    | 8         | 112          | 1000                 | 8/alta |
| Standard_H16m   | 16        | 224          | 2000                 | 8/molto alta |
| Standard_H16r*  | 16        | 112          | 2000                 | 8/molto alta |
| Standard_H16mr* | 16        | 224          | 2000                 | 8/molto alta |

\*Con supporto di RDMA

## <a name="configure-sizes-for-cloud-services"></a>Configurare le dimensioni per i servizi Cloud
È possibile specificare le dimensioni di un'istanza del ruolo delle macchine virtuali nell'ambito del modello del servizio descritto dal [file di definizione del servizio](cloud-services-model-and-package.md#csdef). Le dimensioni di un ruolo determinano il numero di memorie centrali CPU, la capacità di memoria e la dimensione del file system locale allocati a una istanza in esecuzione. Scegliere le dimensioni del ruolo in base ai requisiti delle risorse dell'applicazione.

Di seguito è riportato un esempio per impostare la dimensione di ruolo [Standard_D2](#general-purpose-d) per un'istanza del ruolo Web:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Modifica delle dimensioni di un ruolo esistente

Man mano che la natura del carico di lavoro cambia o nuove dimensioni della VM diventano disponibili, è possibile che si desideri modificare la dimensione del ruolo. A tale scopo, è necessario modificare le dimensioni della macchina virtuale nel file di definizione del servizio, come illustrato in precedenza, ricreare il pacchetto del servizio cloud e distribuirlo. Non è possibile modificare le dimensioni delle macchine virtuali direttamente dal portale o da PowerShell.

>[!TIP]
> È possibile che si desideri usare dimensioni della VM differenti per il ruolo in ambienti diversi (ad esempio, test e produzione). A tal fine, è possibile creare più file di definizione del servizio (.csdef) nel progetto e successivamente creare pacchetti del servizio cloud differenti per ogni ambiente durante la compilazione automatica usano lo strumento CSPack. Per ulteriori informazioni sugli elementi di un pacchetto di servizi cloud e su come crearli, vedere [Cos'è il modello di servizi cloud e come è possibile crearne il pacchetto?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Ottenere un elenco di dimensioni
È possibile usare PowerShell o l'API REST per ottenere un elenco di dimensioni. L'API REST è documentata [qui](https://msdn.microsoft.com/library/azure/dn469422.aspx). Il codice seguente è un comando di PowerShell che elenca tutte le dimensioni disponibili per Servizi cloud. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
* Altre informazioni [sulle Dimensioni delle VM High Performance Computing (HPC)](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per carichi di lavoro HPC.
