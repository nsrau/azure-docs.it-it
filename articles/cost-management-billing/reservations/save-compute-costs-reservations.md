---
title: Informazioni sulle prenotazioni di Azure
description: Informazioni sulle prenotazioni e sui prezzi di Azure per risparmiare sui costi di macchine virtuali, database SQL, Azure Cosmos DB e altre risorse.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 6277a7e7dc5891a3bc67c298a31344284c92e31d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235640"
---
# <a name="what-are-azure-reservations"></a>Informazioni sulle prenotazioni di Azure

Le prenotazioni di Azure consentono di risparmiare denaro a fronte di un impegno di acquisto di un piano di uno o tre anni per più prodotti. L'impegno consente di ottenere uno sconto sulle risorse che si usano. Le prenotazioni possono ridurre in modo significativo i costi delle risorse fino al 72% sui prezzi con pagamento a consumo. Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle risorse. Dopo l'acquisto, lo sconto per la prenotazione si applica automaticamente alle risorse corrispondenti.

Il pagamento per le prenotazioni può essere anticipato o mensile. Il costo totale delle prenotazioni con pagamento anticipato e mensile è lo stesso e non vengono addebitate spese aggiuntive quando si sceglie di pagare mensilmente. Il pagamento mensile è disponibile per le prenotazioni di Azure, non per prodotti di terze parti.

È possibile acquistare una prenotazione nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Perché acquistare una prenotazione?

Se si registra un utilizzo coerente delle risorse che supporta le prenotazioni, l'acquisto di una prenotazione consente di ridurre i costi. Se ad esempio si eseguono costantemente istanze di un servizio senza una prenotazione, verranno addebitati i costi in base al modello di pagamento in base al consumo. Se invece si acquista una prenotazione, si ottiene immediatamente lo sconto per la prenotazione. Per le risorse non verranno più addebitate tariffe in base al consumo.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Dopo l'acquisto, lo sconto per la prenotazione viene applicato automaticamente all'utilizzo delle risorse corrispondenti agli attributi selezionati in fase di acquisto. Gli attributi includono lo SKU, le aree (se applicabile) e l'ambito. L'ambito di una prenotazione determina le risorse a cui verrà applicato il risparmio corrispondente.

Per altre informazioni su come viene applicato lo sconto, vedere [Applicazione dello sconto alle istanze riservate](reservation-discount-application.md).

Per altre informazioni sul funzionamento dell'ambito di prenotazione, vedere [Definire l'ambito delle prenotazioni](prepare-buy-reservation.md#scope-reservations).


## <a name="flexibility-with-azure-reservations"></a>Flessibilità offerta dalle prenotazioni di Azure

Le prenotazioni di Azure offrono la flessibilità necessaria a soddisfare le esigenze in continua evoluzione. È possibile scambiare una prenotazione per un'altra dello stesso tipo. È anche possibile rimborsare una prenotazione, fino a 50.000 USD nel periodo di 12 mesi, se questa non è più necessaria. Il limite massimo del rimborso si applica a tutte le prenotazioni nell'ambito del contratto con Microsoft.

Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>Addebiti coperti dalla prenotazione

- **Istanza di macchina virtuale riservata**: una prenotazione copre solo i costi di calcolo della macchina virtuale. Non copre i costi aggiuntivi relativi a software, Windows, rete o archiviazione.
- **Capacità riservata di Archiviazione di Azure**: una prenotazione copre la capacità di archiviazione per gli account di archiviazione standard per l'archiviazione BLOB o Azure Data Lake Gen2. La prenotazione non copre la velocità della larghezza di banda o delle transazioni.
- **Capacità riservata di Azure Cosmos DB**: una prenotazione copre le unità di elaborazione di cui è stato effettuato il provisioning per le risorse. Non copre i costi di archiviazione e rete.
- **vCore riservato del database SQL**: in una prenotazione sono inclusi solo i costi di calcolo. I costi della licenza SQL vengono fatturati separatamente.
- **SQL data warehouse**: una prenotazione copre la capacità di DWU a elevato utilizzo di calcolo. Non copre invece i costi delle risorse di archiviazione o di rete associate all'utilizzo di SQL Data Warehouse.
- **Azure Databricks**: una prenotazione copre solo l'utilizzo di unità Databricks. Gli altri addebiti, ad esempio per il calcolo, l'archiviazione e la rete, vengono applicati separatamente.
- **Tariffa stamp del servizio app**: una prenotazione copre l'utilizzo di stamp. Non si applica ai ruoli di lavoro, quindi qualsiasi altra risorsa associata allo stamp viene addebitata separatamente.
- **Database di Azure per MySQL**: in una prenotazione sono inclusi solo i costi di calcolo. Non sono inclusi gli addebiti per il software, la rete o l'archiviazione associati al server di database MySQL.
- **Database di Azure per PostgreSQL**: in una prenotazione sono inclusi solo i costi di calcolo. Non sono inclusi gli addebiti per il software, la rete o l'archiviazione associati al server di database PostgreSQL.
- **Database di Azure per MariaDB**: in una prenotazione sono inclusi solo i costi di calcolo. Non sono inclusi gli addebiti per il software, la rete o l'archiviazione associati al server di database MariaDB.
- **Esplora dati di Azure**: in una prenotazione sono inclusi gli addebiti di ricarico. Non sono inclusi gli addebiti per il software, la rete o l'archiviazione associati ai cluster.
- **Azure Cache for Redis**: in una prenotazione sono inclusi solo i costi delle risorse di calcolo. Non sono inclusi gli addebiti per le funzionalità di rete o archiviazione associate alle istanze della cache Redis.
- **Host dedicato di Azure**: solo i costi di calcolo sono inclusi nell'host dedicato.
- **Prenotazioni di archiviazione su disco di Azure**: una prenotazione copre solo unità SSD Premium di dimensioni P30 o superiori. Non sono coperti altri tipi di disco o dimensioni inferiori a P30.

Piani software:

- **SUSE Linux**: una prenotazione copre i costi del piano software. Gli sconti si applicano solo ai contatori SUSE e non all'utilizzo della macchina virtuale.
- **Piani Red Hat**: una prenotazione copre i costi del piano software. Gli sconti si applicano solo ai contatori RedHat e non all'utilizzo della macchina virtuale.
- **Soluzione VMware di Azure di CloudSimple**: una prenotazione copre i nodi CloudSimple VMWare. Vengono comunque applicati costi aggiuntivi per il software.
- **Azure Red Hat OpenShift**: una prenotazione si applica ai costi di OpenShift e non a quelli dell'infrastruttura di Azure.

Per le macchine virtuali Windows e il database SQL, lo sconto per la prenotazione non si applica ai costi del software. È possibile coprire i costi di licenza con il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](/partner-center/azure-reservations)

- Altre informazioni sulle prenotazioni per i piani di servizio:
    - [Macchine virtuali con istanze di macchina virtuale riservate di Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Risorse di Azure Cosmos DB con capacità riservata di Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../../sql-database/sql-database-reserved-capacity.md) Altre informazioni sulle prenotazioni per i piani software:
    - [Piani software Red Hat da prenotazioni di Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Piani software SUSE da prenotazioni di Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
