---
title: Informazioni sulle prenotazioni di Azure
description: Informazioni sulle prenotazioni di Azure e sui prezzi che consentono di risparmiare sui costi di istanze riservate per macchine virtuali, database SQL, Azure Cosmos DB e altre risorse.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: overview
ms.date: 10/01/2020
ms.author: banders
ms.openlocfilehash: 5aeb9046b8901580e4de37b1735a2d2dddd42fce
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147460"
---
# <a name="what-are-azure-reservations"></a>Informazioni sulle prenotazioni di Azure

Le prenotazioni di Azure consentono di risparmiare denaro a fronte di un impegno di acquisto di un piano di uno o tre anni per più prodotti. L'impegno consente di ottenere uno sconto sulle risorse che si usano. Le prenotazioni consentono di ridurre sensibilmente i costi delle risorse, fino al 72% rispetto ai prezzi con pagamento in base al consumo. Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle risorse. Dopo l'acquisto, lo sconto per la prenotazione si applica automaticamente alle risorse corrispondenti.

Il pagamento per le prenotazioni può essere anticipato o mensile. Il costo totale delle prenotazioni con pagamento anticipato e mensile è lo stesso e non vengono addebitate spese aggiuntive quando si sceglie di pagare mensilmente. Il pagamento mensile è disponibile per le prenotazioni di Azure, non per prodotti di terze parti.

È possibile acquistare una prenotazione nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Perché acquistare una prenotazione?

Se si registra un utilizzo coerente delle risorse che supporta le prenotazioni, l'acquisto di una prenotazione consente di ridurre i costi. Se ad esempio si eseguono costantemente istanze di un servizio senza una prenotazione, verranno addebitati i costi in base al modello di pagamento in base al consumo. Se invece si acquista una prenotazione, si ottiene immediatamente lo sconto per la prenotazione. Per le risorse non verranno più addebitate tariffe in base al consumo.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Dopo l'acquisto, lo sconto per la prenotazione viene applicato automaticamente all'utilizzo delle risorse corrispondenti agli attributi selezionati in fase di acquisto. Gli attributi includono lo SKU, le aree (se applicabile) e l'ambito. L'ambito di una prenotazione determina le risorse a cui verrà applicato il risparmio corrispondente.

Per altre informazioni su come viene applicato lo sconto, vedere [Applicazione dello sconto alle istanze riservate](reservation-discount-application.md).

Per altre informazioni sul funzionamento dell'ambito di prenotazione, vedere [Definire l'ambito delle prenotazioni](prepare-buy-reservation.md#scope-reservations).

## <a name="determine-what-to-purchase"></a>Decidere cosa acquistare 

Tutte le prenotazioni, ad eccezione di Azure Databricks, vengono applicate su base oraria. Valutare gli acquisti delle prenotazioni in base all'utilizzo di base coerente. È possibile determinare la prenotazione da acquistare analizzando i dati di utilizzo o usando le raccomandazioni per le prenotazioni. Le raccomandazioni sono disponibili in:

- Azure Advisor (solo VM)
- Esperienza di acquisto della prenotazione nel portale di Azure
- App Power BI Gestione costi di Azure
- API 

Per altre informazioni, vedere  [Determinare la prenotazione da acquistare](determine-reservation-purchase.md) 

## <a name="buying-a-reservation"></a>Acquisto di una prenotazione 

È possibile acquistare prenotazioni tramite il portale di Azure, le API, PowerShell e l'interfaccia della riga di comando. 

Passare al [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs) per effettuare un acquisto.

Per altre informazioni, vedere  [Acquistare una prenotazione](prepare-buy-reservation.md).

## <a name="how-is-a-reservation-billed"></a>Come viene fatturata una prenotazione? 

La prenotazione viene addebitata in base al metodo di pagamento associato alla sottoscrizione. Il costo della prenotazione viene sottratto dal saldo dell'impegno monetario, se disponibile. Se tale saldo non copre il costo della prenotazione, l'eccedenza verrà fatturata. Se si ha una sottoscrizione di un singolo piano con pagamento in base al consumo, sulla carta di credito associata all'account verranno immediatamente addebitati gli acquisti anticipati. I pagamenti mensili vengono visualizzati nella fattura e addebitati mensilmente sulla carta di credito. Se l'addebito avviene tramite fattura, il costo risulterà visibile sulla fattura successiva. 

## <a name="permissions-to-view-and-manage-reservations"></a>Assegnare autorizzazioni per la visualizzazione e la gestione delle prenotazioni 

L'utente che acquista una prenotazione e l'amministratore dell'account della sottoscrizione usata per la fatturazione della prenotazione ottengono il ruolo di proprietario per l'ordine della prenotazione e per la prenotazione.

È possibile delegare la gestione di una prenotazione mediante l'aggiunta di utenti all'ordine o alla prenotazione. I ruoli vengono assegnati nel portale di Azure o tramite le API e PowerShell. 

Per altre informazioni vedere  [Aggiungere o modificare gli utenti che possono gestire una prenotazione](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation) 

## <a name="get-reservation-details-and-utilization-after-purchase"></a>Ottenere i dettagli e l'utilizzo della prenotazione dopo l'acquisto

Se si dispone dell'autorizzazione per visualizzare la prenotazione, è possibile visualizzare la prenotazione e il relativo utilizzo nel portale di Azure. È anche possibile ottenere i dati tramite le API. 

Per altre informazioni su come visualizzare le prenotazioni nel portale di Azure, vedere [Visualizzare le prenotazioni nel portale di Azure](view-reservations.md) 

## <a name="manage-reservations-after-purchase"></a>Gestire le prenotazioni dopo l'acquisto 

Dopo aver acquistato una prenotazione di Azure, può essere necessario aggiornare l'ambito per applicarla a una sottoscrizione diversa, cambiare la persona che gestisce la prenotazione, dividere la prenotazioni in parti più piccole o modificare la flessibilità delle dimensioni istanza. 

Per altre informazioni, vedere  [Gestire le prenotazioni per le risorse di Azure](manage-reserved-vm-instance.md) 

## <a name="flexibility-with-azure-reservations"></a>Flessibilità offerta dalle prenotazioni di Azure

Le prenotazioni di Azure offrono la flessibilità necessaria a soddisfare le esigenze in continua evoluzione. È possibile scambiare una prenotazione per un'altra dello stesso tipo. È anche possibile rimborsare una prenotazione, fino a 50.000 USD nel periodo di 12 mesi, se questa non è più necessaria. Il limite massimo del rimborso si applica a tutte le prenotazioni nell'ambito del contratto con Microsoft.

Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>Addebiti coperti dalla prenotazione

- **Istanza di macchina virtuale riservata**: una prenotazione copre solo i costi di calcolo della macchina virtuale e dei servizi cloud. Non copre i costi aggiuntivi relativi a software, Windows, rete o archiviazione.
- **Capacità riservata di Archiviazione di Azure**: una prenotazione copre la capacità di archiviazione per gli account di archiviazione standard per l'archiviazione BLOB o Azure Data Lake Gen2. La prenotazione non copre la velocità della larghezza di banda o delle transazioni.
- **Capacità riservata di Azure Cosmos DB**: una prenotazione copre le unità di elaborazione di cui è stato effettuato il provisioning per le risorse. Non copre i costi di archiviazione e rete.
- **vCore riservato del database SQL**: in una prenotazione sono inclusi solo i costi di calcolo. I costi della licenza SQL vengono fatturati separatamente.
- **Azure Synapse Analytics**: una prenotazione riguarda l'uso di unità Data Warehouse a elevato utilizzo di calcolo. Non copre gli addebiti per le risorse di archiviazione o di rete associate all'utilizzo di Azure Synapse Analytics.
- **Azure Databricks**: una prenotazione copre solo l'utilizzo di unità Databricks. Gli altri addebiti, ad esempio per il calcolo, l'archiviazione e la rete, vengono applicati separatamente.
- **Tariffa stamp del servizio app**: una prenotazione copre l'utilizzo di stamp. Non si applica ai ruoli di lavoro, quindi qualsiasi altra risorsa associata allo stamp viene addebitata separatamente.
- **Database di Azure per MySQL**: in una prenotazione sono inclusi solo i costi di calcolo. Non sono inclusi gli addebiti per il software, la rete o l'archiviazione associati al server di database MySQL.
- **Database di Azure per PostgreSQL**: in una prenotazione sono inclusi solo i costi di calcolo. Non sono inclusi gli addebiti per il software, la rete o l'archiviazione associati al server di database PostgreSQL.
- **Database di Azure per MariaDB**: in una prenotazione sono inclusi solo i costi di calcolo. Non sono inclusi gli addebiti per il software, la rete o l'archiviazione associati al server di database MariaDB.
- **Esplora dati di Azure**: in una prenotazione sono inclusi gli addebiti di ricarico. Non sono inclusi gli addebiti per il calcolo, la rete o l'archiviazione associati ai cluster.
- **Azure Cache for Redis**: in una prenotazione sono inclusi solo i costi delle risorse di calcolo. Non sono inclusi gli addebiti per le funzionalità di rete o archiviazione associate alle istanze della cache Redis.
- **Host dedicato di Azure**: solo i costi di calcolo sono inclusi nell'host dedicato.
- **Prenotazioni di archiviazione su disco di Azure**: una prenotazione copre solo unità SSD Premium di dimensioni P30 o superiori. Non sono coperti altri tipi di disco o dimensioni inferiori a P30.

Piani software:

- **SUSE Linux**: una prenotazione copre i costi del piano software. Gli sconti si applicano solo ai contatori SUSE e non all'utilizzo della macchina virtuale.
- **Piani Red Hat**: una prenotazione copre i costi del piano software. Gli sconti si applicano solo ai contatori RedHat e non all'utilizzo della macchina virtuale.
- **Soluzione Azure VMware di CloudSimple**: una prenotazione copre i costi dei nodi di VMware CloudSimple. Vengono comunque applicati costi aggiuntivi per il software.
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
    - [Risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../../azure-sql/database/reserved-capacity-overview.md)
    - [Risorse di Cache di Azure per Redis con capacità riservata di Cache di Azure per Redis](../../azure-cache-for-redis/cache-reserved-pricing.md) Altre informazioni sulle prenotazioni per i piani software:
    - [Piani software Red Hat da prenotazioni di Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
    - [Piani software SUSE da prenotazioni di Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)