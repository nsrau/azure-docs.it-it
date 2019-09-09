---
title: Informazioni sulle prenotazioni di Azure
description: Informazioni sulle prenotazioni e i prezzi di Azure per il salvataggio in macchine virtuali, database SQL, Azure Cosmos DB e altri costi delle risorse.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: bb90a9dec161746356b8c13df448718c53626684
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806355"
---
# <a name="what-are-azure-reservations"></a>Informazioni sulle prenotazioni di Azure

Le prenotazioni di Azure ti aiutano a risparmiare denaro grazie al commit di un anno o di tre anni di macchine virtuali, alla capacità di calcolo del database SQL, alla velocità effettiva Azure Cosmos DB o ad altre risorse di Azure. Il commit consente di ottenere uno sconto sulle risorse utilizzate. Le prenotazioni possono ridurre notevolmente i costi di macchina virtuale, calcolo dei database SQL, Azure Cosmos DB o altre risorse fino al 72% sui prezzi con pagamento a consumo. Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle risorse.

È possibile pagare per una prenotazione in anticipo o mensili. Il costo totale di prenotazioni anticipate e mensili è lo stesso e non vengono addebitate spese aggiuntive quando si sceglie di pagare mensilmente. Il pagamento mensile è disponibile per le prenotazioni di Azure, non per i prodotti di terze parti.

È possibile acquistare una prenotazione nel [portale di Azure](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Perché acquistare una prenotazione?

Se si dispone di macchine virtuali, Azure Cosmos DB o database SQL in esecuzione per lunghi periodi di tempo, l'acquisto di una prenotazione offre un'opzione più conveniente. Se ad esempio si eseguono in modo continuo quattro istanze di un servizio senza prenotazione, verranno addebitate le tariffe con pagamento in base al consumo. Quando si acquista una prenotazione per tali risorse, si ottiene immediatamente lo sconto relativo alla prenotazione. Per le risorse non verranno più addebitate tariffe in base al consumo.

## <a name="charges-covered-by-reservation"></a>Addebiti coperti dalla prenotazione

Piani di servizio:

- **Istanza di macchina virtuale riservata** : una prenotazione copre solo i costi di calcolo della macchina virtuale. e non i costi aggiuntivi relativi a software, rete o archiviazione.
- **Azure Cosmos DB capacità riservata** : una prenotazione copre la velocità effettiva di cui è stato effettuato il provisioning per le risorse. Non copre i costi di archiviazione e rete.
- **VCore riservato database SQL** : solo i costi di calcolo sono inclusi in una prenotazione. I costi della licenza vengono fatturati separatamente.
- **SQL data warehouse** : una prenotazione riguarda l'uso di DWU. Non copre i prezzi di archiviazione o di rete associati all'utilizzo del SQL Data Warehouse.
- **Tariffa di timbro del servizio app** : una prenotazione riguarda l'utilizzo del timbro. Non si applica ai thread di lavoro, quindi qualsiasi altra risorsa associata al timbro viene addebitata separatamente.

Per le macchine virtuali Windows e il database SQL, i costi delle licenze possono essere coperti con le offerte di [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Chi è idoneo all'acquisto di una prenotazione?

Per acquistare un piano, è necessario avere un ruolo di proprietario della sottoscrizione in una sottoscrizione Enterprise (MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (MS-AZR-0003P o MS-AZR-0023P). I provider di soluzioni cloud possono usare il portale di Azure o il [centro](/partner-center/azure-reservations) per i partner per acquistare prenotazioni di Azure.

I clienti Enterprise Agreement (EA) possono limitare gli acquisti agli amministratori EA disabilitando l'opzione **Aggiungi istanze riservate** nel portale EA. Gli amministratori EA devono essere un proprietario della sottoscrizione per almeno una sottoscrizione EA per l'acquisto di una prenotazione. Questa opzione è utile per le aziende che desiderano che un team centralizzato acquisti prenotazioni per diversi centri di costo. Dopo l'acquisto, i team centralizzati possono aggiungere i proprietari del centro di costo alle prenotazioni. I proprietari possono quindi definire l'ambito della prenotazione per le sottoscrizioni. Il team centrale non deve avere l'accesso al proprietario della sottoscrizione in cui viene acquistata la prenotazione.

Uno sconto sulla prenotazione si applica solo alle risorse associate alle sottoscrizioni acquistate tramite Enterprise, Cloud Solution Provider (CSP) e singoli piani con tariffe con pagamento in base al consumo.

## <a name="scope-reservations"></a>Prenotazioni ambito

È possibile definire l'ambito di una prenotazione a una sottoscrizione o a gruppi di risorse. Se si imposta l'ambito di una prenotazione, viene selezionato il punto di risparmio di prenotazione. Quando si crea l'ambito della prenotazione per un gruppo di risorse, gli sconti per la prenotazione si applicano solo al gruppo di risorse, non all'intera sottoscrizione.

### <a name="reservation-scoping-options"></a>Opzioni di ambito prenotazione

Con l'ambito del gruppo di risorse sono disponibili tre opzioni per definire l'ambito di una prenotazione, a seconda delle esigenze:

- **Ambito del gruppo di risorse singolo** : applica lo sconto di prenotazione alle risorse corrispondenti solo nel gruppo di risorse selezionato.
- **Singolo ambito di sottoscrizione** : applica lo sconto di prenotazione alle risorse corrispondenti nella sottoscrizione selezionata.
- **Ambito condiviso** : applica lo sconto di prenotazione alle risorse corrispondenti nelle sottoscrizioni idonee presenti nel contesto di fatturazione. Per Enterprise Agreement clienti, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni con tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.

Quando si applicano gli sconti di prenotazione sull'utilizzo, Azure elabora la prenotazione nell'ordine seguente:

1. Prenotazioni con ambito di un gruppo di risorse
2. Prenotazioni a ambito singolo
3. Prenotazioni per ambito condiviso

Un singolo gruppo di risorse può ottenere sconti di prenotazione da più prenotazioni, a seconda della modalità di definizione dell'ambito delle prenotazioni.

### <a name="scope-a-reservation-to-a-resource-group"></a>Definire l'ambito di una prenotazione per un gruppo di risorse

È possibile definire l'ambito della prenotazione per un gruppo di risorse quando si acquista la prenotazione oppure si imposta l'ambito dopo l'acquisto. Per definire l'ambito della prenotazione per un gruppo di risorse, è necessario essere un proprietario della sottoscrizione.

Per impostare l'ambito, andare alla pagina di [prenotazione acquisti](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) nel portale di Azure. Selezionare il tipo di prenotazione che si desidera acquistare. Nel modulo **selezionare il prodotto di cui si vuole acquistare** la selezione, modificare il valore dell'ambito in un singolo gruppo di risorse. Quindi, selezionare un gruppo di risorse.

![Esempio che mostra la selezione di acquisto della prenotazione VM](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Vengono visualizzate le raccomandazioni di acquisto per il gruppo di risorse nella prenotazione della macchina virtuale. Le raccomandazioni vengono calcolate analizzando l'utilizzo negli ultimi 30 giorni. Viene fornita una raccomandazione di acquisto se il costo di esecuzione delle risorse con istanze riservate è più economico rispetto al costo di esecuzione delle risorse con tariffe con pagamento in base al consumo. Per ulteriori informazioni sulle raccomandazioni per l'acquisto di prenotazioni, vedere [ottenere consigli per l'acquisto di istanze riservate in base al modello di utilizzo](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

È sempre possibile aggiornare l'ambito dopo l'acquisto di una prenotazione. A tale scopo, passare alla prenotazione, fare clic su **configurazione**e ridefinire l'ambito della prenotazione. La ridefinizione dell'ambito di una prenotazione non è una transazione commerciale. Il periodo di prenotazione non è stato modificato. Per ulteriori informazioni sull'aggiornamento dell'ambito, vedere [la pagina relativa all'aggiornamento dell'ambito dopo l'acquisto di una prenotazione](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Esempio che mostra una modifica dell'ambito della prenotazione](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorare e ottimizzare l'utilizzo della prenotazione

È possibile monitorare l'utilizzo della prenotazione in diversi modi: tramite portale di Azure, tramite le API o tramite i dati di utilizzo. Per visualizzare tutte le prenotazioni a cui si ha accesso, passare a **prenotazioni** nella portale di Azure. La griglia prenotazioni Mostra l'ultima percentuale di utilizzo registrata per la prenotazione. Fare clic sulla prenotazione per visualizzare l'utilizzo a lungo termine della prenotazione.

È anche possibile ottenere l'utilizzo delle prenotazioni usando le [API](billing-reservation-apis.md#see-reservation-usage) e i [dati di utilizzo](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) se si è un cliente con contratto Enterprise Agreement.

Se si nota che l'utilizzo della prenotazione con ambito gruppo di risorse è basso, è possibile aggiornare l'ambito della prenotazione a una singola sottoscrizione o condividerlo nel contesto di fatturazione. È anche possibile dividere la prenotazione e applicare le prenotazioni risultanti a gruppi di risorse diversi.

### <a name="other-considerations"></a>Altre considerazioni

Se non si hanno risorse corrispondenti in un gruppo di risorse, la prenotazione sarà sottoutilizzata. La prenotazione non viene applicata automaticamente a un gruppo di risorse o a una sottoscrizione diversa in cui è presente un utilizzo ridotto.

Un ambito di prenotazione non viene aggiornato automaticamente se si sposta il gruppo di risorse da una sottoscrizione a un'altra. L'ambito non viene aggiornato se si elimina il gruppo di risorse. Sarà necessario ridefinire [l'ambito della prenotazione](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). In caso contrario, la prenotazione sarà sottoutilizzata.

## <a name="discounted-subscription-and-offer-types"></a>Sottoscrizione scontata e tipi di offerta

Gli sconti Prenotazione si applicano alle sottoscrizioni idonee e ai tipi di offerte seguenti.

- Contratto Enterprise (numeri offerte: MS-AZR-0017P o MS-AZR-0148P)
- Piani individuali con tariffe con pagamento in base al consumo (numeri offerte: MS-AZR-0003P o MS-AZR-0023P)
- Sottoscrizioni provider di servizi cloud

Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

## <a name="how-is-a-reservation-billed"></a>Come viene fatturata una prenotazione?

La prenotazione viene addebitata in base al metodo di pagamento associato alla sottoscrizione. Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Quando il saldo dell'impegno monetario non copre il costo della prenotazione, l'eccedenza viene addebitata. Se si dispone di una sottoscrizione di un singolo piano con tariffe con pagamento in base al consumo, la carta di credito per l'account viene addebitata immediatamente per gli acquisti iniziali. I pagamenti mensili vengono visualizzati nella fattura e la carta di credito viene addebitata mensilmente. Quando la fatturazione viene addebitata in base alla fattura, verranno visualizzati gli addebiti per la fattura successiva.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto della prenotazione

Lo sconto relativo alla prenotazione si applica all'utilizzo delle risorse che corrisponde agli attributi selezionati quando si acquista la prenotazione. Gli attributi includono l'ambito in cui vengono eseguite le VM corrispondenti, i database SQL, Azure Cosmos DB o altre risorse. Ad esempio, se si vuole uno sconto di prenotazione per quattro macchine virtuali D2 standard nell'area Stati Uniti occidentali, selezionare la sottoscrizione in cui sono in esecuzione le macchine virtuali.

Uno sconto sulla prenotazione è "*use-it-or-lose-it*". Se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile portare in avanti ore riservate inutilizzate.

Quando si arresta una risorsa, lo sconto della prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non vengono trovate risorse corrispondenti nell'ambito specificato, le ore riservate vengono *perse*.

Ad esempio, è possibile creare una risorsa in un secondo momento e disporre di una prenotazione corrispondente sottoutilizzata. Lo sconto della prenotazione si applica automaticamente alla nuova risorsa corrispondente.

Se le macchine virtuali sono in esecuzione in diverse sottoscrizioni all'interno della sottoscrizione o dell'account corrente, selezionare l'ambito condiviso. L'ambito condiviso consente l'applicazione dello sconto relativo all'acquisto di istanze riservate tra sottoscrizioni. Dopo aver acquistato una prenotazione è possibile modificare l'ambito. Per altre informazioni, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).

Uno sconto sulla prenotazione si applica solo a risorse associate a Enterprise, CSP o sottoscrizioni con tariffe con pagamento in base al consumo. Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

## <a name="when-the-reservation-term-expires"></a>Alla scadenza del periodo di prenotazione

Alla fine del periodo di prenotazione, lo sconto per la fatturazione scade. Per la macchina virtuale, il database SQL, Azure Cosmos DB o un'altra risorsa viene addebitato il prezzo con pagamento in base al consumo. Le prenotazioni di Azure non vengono rinnovate automaticamente. Per continuare a usufruire dello sconto sulla fatturazione è necessario acquistare una nuova prenotazione per i servizi e il software idonei.

## <a name="discount-applies-to-different-sizes"></a>Lo sconto si applica a dimensioni diverse

Quando si acquista una prenotazione, è possibile applicare lo sconto ad altre istanze con gli attributi compresi nello stesso gruppo di dimensioni. Questa funzionalità è nota come flessibilità delle dimensioni dell'istanza. La flessibilità della copertura degli sconti dipende dal tipo di prenotazione e dagli attributi scelti all'acquisto della prenotazione.

Piani di servizio:

- Istanze di macchina virtuale riservate: Quando si acquista la prenotazione e si seleziona **ottimizzato per la flessibilità delle dimensioni dell'istanza**, il code coverage degli sconti dipende dalle dimensioni della VM selezionate. La prenotazione può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Per altre informazioni vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacità riservata del database SQL: la copertura degli sconti dipende dal livello di prestazioni selezionato. Per altre informazioni, vedere [Informazioni su come viene applicato ai database SQL lo sconto sulla prenotazione](billing-understand-reservation-charges.md).
- Capacità riservata di Azure Cosmos DB: la copertura degli sconti dipende dalla velocità effettiva di cui viene effettuato il provisioning. Per altre informazioni, vedere [Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Notifiche di prenotazione

A seconda di come si paga la sottoscrizione di Azure, le notifiche di prenotazione vengono inviate tramite posta elettronica agli utenti seguenti dell'organizzazione. Le notifiche vengono inviate per vari eventi, tra cui:

- Acquista
- Scadenza prenotazione imminente
- Expiry
- Rinnovo
- Annullamento
- Modifica ambito

Per i clienti con sottoscrizioni EA:
- Viene inviata una notifica di acquisto all'acquirente e ai contatti per le notifiche EA.
- Le altre notifiche del ciclo di vita della prenotazione vengono inviate solo ai contatti delle notifiche EA.
- Gli utenti aggiunti a una prenotazione tramite l'autorizzazione RBAC (IAM) non ricevono notifiche tramite posta elettronica.

Per i clienti con sottoscrizioni individuali:
- L'acquirente riceve una notifica di acquisto.
- Al momento dell'acquisto, il proprietario dell'account di fatturazione della sottoscrizione riceve una notifica di acquisto.
- Il proprietario dell'account riceve tutte le altre notifiche.


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con tariffe con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](/partner-center/azure-reservations)

- Altre informazioni sulle prenotazioni per i piani di servizio:
    - [Macchine virtuali con istanze di VM riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB risorse con Azure Cosmos DB capacità riservata](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md) Altre informazioni sulle prenotazioni per i piani software:
    - [Piani software Red Hat di Azure prenotazione](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Piani software SUSE da prenotazioni di Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
