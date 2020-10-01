---
title: Determinare le dimensioni della subnet richieste & intervallo
titleSuffix: Azure SQL Managed Instance
description: Questo argomento descrive come calcolare le dimensioni della subnet in cui verrà distribuito il Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617639"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Determinare le dimensioni della subnet richieste & intervallo per Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita SQL di Azure deve essere distribuito in una [rete virtuale di Azure (VNet)](../../virtual-network/virtual-networks-overview.md).

Il numero di istanze gestite che possono essere distribuite nella subnet di un VNet dipende dalle dimensioni della subnet (intervallo di subnet).

Quando si crea un'istanza gestita, Azure alloca un numero di macchine virtuali a seconda del livello selezionato durante il provisioning. Poiché queste macchine virtuali sono associate alla subnet, richiedono indirizzi IP. Per assicurare una disponibilità elevata durante le normali operazioni e la manutenzione del servizio, Azure può allocare ulteriori macchine virtuali. Di conseguenza, il numero di indirizzi IP necessari in una subnet è maggiore del numero di istanze gestite in tale subnet.

Come da progettazione, un'istanza gestita necessita di un minimo di 32 indirizzi IP in una subnet. Di conseguenza, è possibile usare la subnet mask minima da /27 per definire gli intervalli IP della subnet. È consigliabile eseguire una pianificazione attenta delle dimensioni della subnet per le distribuzioni dell'istanza gestita. Gli input che devono essere presi in considerazione durante la pianificazione sono:

- Numero di istanze gestite, inclusi i parametri di istanza seguenti:
  - livello di servizio
  - generazione hardware
  - numero di vcore
- Piani per la scalabilità verticale o orizzontale o la modifica del livello di servizio

> [!IMPORTANT]
> Una dimensione della subnet con 16 indirizzi IP (subnet mask/28) consentirà la distribuzione dell'istanza gestita al suo interno, ma deve essere utilizzata solo per la distribuzione di un'istanza singola utilizzata per la valutazione o negli scenari di sviluppo/test, in cui le operazioni di ridimensionamento non verranno eseguite.

## <a name="determine-subnet-size"></a>Determinare le dimensioni della subnet

Ridimensionare la subnet in base alle esigenze future di distribuzione e scalabilità dell'istanza. I parametri seguenti possono risultare utili per la creazione di un calcolo:

- Cinque indirizzi IP nella subnet sono dedicati ad Azure
- Ogni cluster virtuale alloca un numero aggiuntivo di indirizzi 
- Ogni istanza gestita usa il numero di indirizzi che dipendono dal piano tariffario e dalla generazione hardware

> [!IMPORTANT]
> Non è possibile modificare l'intervallo di indirizzi della subnet se una risorsa è presente nella subnet. Non è inoltre possibile spostare le istanze gestite da una subnet a un'altra. Laddove possibile, provare a usare subnet più grandi anziché più piccole per evitare problemi in futuro.

GP = utilizzo generico; BC = Business Critical; VC = cluster virtuale

| **Generazione hardware** | **Piano tariffario** | **Utilizzo di Azure** | **Utilizzo VC** | **Utilizzo istanza** | **Totale*** |
| --- | --- | --- | --- | --- | --- |
| Quarta generazione | GP | 5 | 1 | 5 | 11 |
| Quarta generazione | BC | 5 | 1 | 5 | 11 |
| Quinta generazione | GP | 5 | 6 | 3 | 14 |
| Quinta generazione | BC | 5 | 6 | 5 | 16 |

  \* Totale colonne Visualizza il numero di indirizzi che verrebbero effettuati quando un'istanza viene distribuita nella subnet. Ogni istanza aggiuntiva nella subnet aggiunge il numero di indirizzi rappresentati con la colonna Usage dell'istanza. Gli indirizzi rappresentati con la colonna utilizzo di Azure vengono condivisi tra più cluster virtuali, mentre gli indirizzi rappresentati con la colonna utilizzo VC vengono condivisi tra le istanze inserite nel cluster virtuale.

L'operazione di aggiornamento richiede in genere il ridimensionamento del cluster virtuale. In alcuni casi, l'operazione di aggiornamento richiederà la creazione di un cluster virtuale. per altre informazioni, vedere l'articolo relativo alle [operazioni di gestione](sql-managed-instance-paas-overview.md#management-operations). In caso di creazione di un cluster virtuale, il numero di indirizzi aggiuntivi richiesti è uguale al numero di indirizzi rappresentati dalla colonna utilizzo VC sommata con gli indirizzi richiesti per le istanze posizionate nel cluster virtuale (colonna Utilizzo istanza).

**Esempio 1**: si prevede di avere un'istanza gestita per utilizzo generico (hardware Gen4) e un'istanza gestita di business critical (hardware quinta generazione). Ciò significa che è necessario un minimo di 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 indirizzi IP per poter eseguire la distribuzione. Poiché gli intervalli IP sono definiti nella potenza di 2, la subnet richiede un intervallo IP minimo di 32 (2 ^ 5) per questa distribuzione.<br><br>
Come indicato in precedenza, in alcuni casi, l'operazione di aggiornamento richiederà la creazione di un cluster virtuale. Ciò significa che, ad esempio, nel caso di un aggiornamento dell'istanza gestita di Quinta generazione business critical che richiede la creazione di un cluster virtuale, sarà necessario disporre di altri 6 + 5 = 11 indirizzi IP disponibili. Poiché si sta già usando 22 degli indirizzi 32, non sono disponibili indirizzi per questa operazione. Pertanto, è necessario riservare la subnet con subnet mask di/26 (64 indirizzi).

**Esempio 2**: si prevede di avere tre istanze di utilizzo generico (hardware quinta generazione) e due istanze gestite aziendali critiche (hardware quinta generazione) posizionate nella stessa subnet. Ciò significa che sono necessari 5 + 6 + 3 * 3 + 2 * 5 = 30 indirizzi IP. Pertanto, è necessario riservare la subnet con subnet mask di/26. Se si seleziona un subnet mask di/27, il numero rimanente di indirizzi sarà 2 (32 – 30). in questo modo si eviteranno le operazioni di aggiornamento per tutte le istanze, perché nella subnet sono necessari indirizzi aggiuntivi per l'esecuzione del ridimensionamento delle istanze.

**Esempio 3**: si prevede di avere un'istanza gestita per utilizzo generico (hardware quinta generazione) ed eseguire l'operazione di aggiornamento vcore di volta in volta. Ciò significa che sono necessari 5 + 6 + 1 * 3 + 3 = 17 indirizzi IP. Poiché gli intervalli IP sono definiti in potenza di 2, è necessario l'intervallo IP di 32 (2^5) indirizzi IP. Pertanto è necessario riservare la subnet con subnet mask /27.

### <a name="address-requirements-for-update-scenarios"></a>Requisiti degli indirizzi per gli scenari di aggiornamento

Durante la scalabilità, le istanze dell'operazione richiedono temporaneamente una capacità IP aggiuntiva che dipende dal piano tariffario e dalla generazione dell'hardware

| **Generazione hardware** | **Piano tariffario** | **Scenario** | **Indirizzi aggiuntivi*** |
| --- | --- | --- | --- |
| Quarta generazione | GP o BC | Ridimensionamento di vcore | 5 |
| Quarta generazione | GP o BC | Ridimensionamento dell'archiviazione | 5 |
| Quarta generazione | GP o BC | Passare da GP a BC o BC a GP | 5 |
| Quarta generazione | GP | Passare a Quinta generazione * | 9 |
| Quarta generazione | BC | Passare a Quinta generazione * | 11 |
| Quinta generazione | GP | Ridimensionamento di vcore | 3 |
| Quinta generazione | GP | Ridimensionamento dell'archiviazione | 0 |
| Quinta generazione | GP | Passare a BC | 5 |
| Quinta generazione | BC | Ridimensionamento di vcore | 5 |
| Quinta generazione | BC | Ridimensionamento dell'archiviazione | 5 |
| Quinta generazione | BC | Passare a GP | 3 |

  \* L'hardware Gen4 viene eliminato e non è più disponibile per le nuove distribuzioni. Aggiornare la generazione di hardware da Gen4 a Quinta generazione per sfruttare le funzionalità specifiche della generazione di hardware quinta generazione.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere informazioni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).
- Altre informazioni sull' [architettura di connettività per SQL istanza gestita](connectivity-architecture-overview.md).
- Vedere come [creare un VNet in cui si distribuirà SQL istanza gestita](virtual-network-subnet-create-arm-template.md).
- Per problemi relativi al DNS, vedere [configurare un DNS personalizzato](custom-dns-configure.md).
