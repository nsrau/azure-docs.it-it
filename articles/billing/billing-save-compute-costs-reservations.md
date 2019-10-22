---
title: Informazioni sulle prenotazioni di Azure
description: Informazioni sulle prenotazioni e sui prezzi di Azure per risparmiare sui costi di macchine virtuali, database SQL, Azure Cosmos DB e altre risorse.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: b2c3fd9b59b371330e37dceb52b2e89b3db6c48e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390150"
---
# <a name="what-are-azure-reservations"></a>Informazioni sulle prenotazioni di Azure

Le prenotazioni di Azure consentono di risparmiare aderendo a un impegno di due o tre anni per macchine virtuali, capacità di calcolo di database SQL, unità di elaborazione di Azure Cosmos DB e altre risorse di Azure. L'impegno consente di ottenere uno sconto sulle risorse che si usano. Le prenotazioni possono ridurre notevolmente i costi di macchina virtuale, calcolo dei database SQL, Azure Cosmos DB o altre risorse fino al 72% sui prezzi con pagamento a consumo. Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle risorse.

Il pagamento per le prenotazioni può essere anticipato o mensile. Il costo totale delle prenotazioni con pagamento anticipato e mensile è lo stesso e non vengono addebitate spese aggiuntive quando si sceglie di pagare mensilmente. Il pagamento mensile è disponibile per le prenotazioni di Azure, non per prodotti di terze parti.

È possibile acquistare una prenotazione nel [portale di Azure](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Perché acquistare una prenotazione?

Se si hanno macchine virtuali, Azure Cosmos DB o database SQL in esecuzione per lunghi periodi di tempo, l'acquisto di una prenotazione rappresenta l'opzione più conveniente. Se ad esempio si eseguono costantemente quattro istanze di un servizio senza una prenotazione, verranno addebitati i costi in base al consumo. Se invece si acquista una prenotazione per tali risorse, si otterrà immediatamente lo sconto applicabile. Per le risorse non verranno più addebitate tariffe in base al consumo.

## <a name="charges-covered-by-reservation"></a>Addebiti coperti dalla prenotazione

Piani di servizio:

- **Istanza di macchina virtuale riservata**: una prenotazione copre solo i costi di calcolo della macchina virtuale. e non i costi aggiuntivi relativi a software, rete o archiviazione.
- **Capacità riservata di Azure Cosmos DB**: una prenotazione copre le unità di elaborazione di cui è stato effettuato il provisioning per le risorse. Non copre i costi di archiviazione e rete.
- **vCore riservato del database SQL**: in una prenotazione sono inclusi solo i costi di calcolo. I costi della licenza vengono fatturati separatamente.
- **SQL data warehouse**: una prenotazione copre la capacità di DWU a elevato utilizzo di calcolo. Non copre invece i costi delle risorse di archiviazione o di rete associate all'utilizzo di SQL Data Warehouse.
- **Tariffa stamp del servizio app**: una prenotazione copre l'utilizzo di stamp. Non si applica ai ruoli di lavoro, quindi qualsiasi altra risorsa associata allo stamp viene addebitata separatamente.

Per le macchine virtuali Windows e il database SQL, i costi delle licenze possono essere coperti con le offerte di [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Chi è idoneo all'acquisto di una prenotazione?

Per acquistare un piano, è necessario avere un ruolo di proprietario di una sottoscrizione Enterprise (MS-AZR-0017P o MS-AZR-0148P), di una sottoscrizione con pagamento in base al consumo (MS-AZR-0003P o MS-AZR-0023P) o di una sottoscrizione Contratto del cliente Microsoft. Per acquistare prenotazioni di Azure, i partner CSP (Cloud Solution Provider) possono usare il portale di Azure o il  [Centro per i partner](/partner-center/azure-reservations) .

I clienti con contratto Enterprise Agreement (EA) possono limitare gli acquisti agli amministratori EA disabilitando l'opzione **Aggiungi istanze riservate** in EA Portal. Gli amministratori EA devono essere proprietari di almeno una sottoscrizione EA per acquistare una prenotazione. Questa opzione è utile per le organizzazioni che vogliono che le prenotazioni per diversi centri di costo vengano acquistate da un team centralizzato. Dopo l'acquisto, i team centralizzati possono aggiungere proprietari del centro di costo alle prenotazioni. I proprietari possono quindi definire l'ambito della prenotazione per le sottoscrizioni. Il team centralizzato non deve necessariamente avere accesso come proprietario della sottoscrizione in cui viene acquistata la prenotazione.

Lo sconto per la prenotazione si applica solo alle risorse associate alle sottoscrizioni acquistate tramite piani Enterprise, Cloud Solution Provider (CSP), Contratto del cliente Microsoft e singoli piani con pagamento in base al consumo.

## <a name="scope-reservations"></a>Definire l'ambito delle prenotazioni

Come ambito di una prenotazione è possibile definire una sottoscrizione o gruppi di risorse. Quando si imposta l'ambito di una prenotazione, si selezionano le risorse a cui verrà applicato il risparmio corrispondente. Se come ambito della prenotazione si sceglie un gruppo di risorse, gli sconti della prenotazione si applicano solo al gruppo di risorse, non all'intera sottoscrizione.

### <a name="reservation-scoping-options"></a>Opzioni dell'ambito delle prenotazioni

Sono disponibili tre opzioni per definire l'ambito di una prenotazione, a seconda delle esigenze:

- **Gruppo di risorse singolo**: lo sconto per la prenotazione si applica solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.
- **Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.
- **Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise Agreement, il contesto di fatturazione è la registrazione. Per i clienti con Contratto del cliente Microsoft, l'ambito di fatturazione corrisponde al profilo di fatturazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.

Per l'applicazione degli sconti sull'utilizzo di risorse, Azure elabora la prenotazione nell'ordine seguente:

1. Prenotazioni con ambito costituito da un gruppo di risorse
2. Prenotazioni con ambito singolo
3. Prenotazioni con ambito condiviso

Un singolo gruppo di risorse può ottenere sconti da più prenotazioni, a seconda dell'ambito definito.

### <a name="scope-a-reservation-to-a-resource-group"></a>Definire un gruppo di risorse come ambito di una prenotazione

È possibile definire un gruppo di risorse come ambito di una prenotazione al momento dell'acquisto o in seguito. Per definire un gruppo di risorse come ambito della prenotazione, è necessario essere un proprietario della sottoscrizione.

Per impostare l'ambito, passare alla pagina [Acquista prenotazioni](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) del portale di Azure. Selezionare il tipo di prenotazione da acquistare. Nel modulo **Selezionare il prodotto da acquistare** impostare il valore di Ambito su Gruppo di risorse singolo. Quindi, selezionare un gruppo di risorse.

![Esempio di selezione dell'acquisto di prenotazioni di VM](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Vengono visualizzate le raccomandazioni per l'acquisto per il gruppo di risorse nella prenotazione di macchine virtuali. Le raccomandazioni vengono calcolate analizzando l'utilizzo negli ultimi 30 giorni. Vengono fornite se il costo di esecuzione delle risorse con istanze riservate è meno elevato rispetto al costo con pagamento in base al consumo. Per altre informazioni sulle raccomandazioni per l'acquisto di prenotazioni, vedere [Ottenere raccomandazioni per l'acquisto di istanze riservate in base al modello di utilizzo](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Dopo aver acquistato una prenotazione, è sempre possibile aggiornare l'ambito. A tale scopo, passare alla prenotazione, fare clic su **Configurazione** e ridefinire l'ambito della prenotazione. La ridefinizione dell'ambito di una prenotazione non è una transazione commerciale. Il periodo di prenotazione non cambia. Per altre informazioni sull'aggiornamento dell'ambito, vedere [Aggiornare l'ambito dopo l'acquisto di una prenotazione](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Esempio di modifica dell'ambito di una prenotazione](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorare e ottimizzare l'utilizzo delle prenotazioni

È possibile monitorare l'utilizzo delle prenotazioni in diversi modi: tramite il portale di Azure, le API o i dati di utilizzo. Per visualizzare tutte le prenotazioni a cui si ha accesso, passare a **Prenotazioni** nel portale di Azure. La griglia mostra l'ultima percentuale di utilizzo registrata per la prenotazione. Fare clic sulla prenotazione per visualizzarne l'utilizzo a lungo termine.

I clienti con contratto Enterprise o con Contratto del cliente Microsoft possono ottenere i dati sull'utilizzo delle prenotazioni anche tramite le [API](billing-reservation-apis.md#see-reservation-usage) e i [dati di utilizzo](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks).

Se si nota che l'utilizzo della prenotazione è basso con il gruppo di risorse come ambito, è possibile aggiornare l'ambito impostandolo su una sottoscrizione singola oppure condividerlo nel contesto di fatturazione. È anche possibile suddividere la prenotazione e applicare le prenotazioni risultanti a gruppi di risorse diversi.

### <a name="other-considerations"></a>Altre considerazioni

Se un gruppo di risorse non include risorse corrispondenti, la prenotazione risulterà sottoutilizzata. In caso di basso utilizzo, la prenotazione non viene applicata automaticamente a un gruppo di risorse o a una sottoscrizione diversa.

L'ambito di una prenotazione non viene aggiornato automaticamente se il gruppo di risorse viene spostato da una sottoscrizione a un'altra. L'ambito non viene aggiornato se si elimina il gruppo di risorse. Sarà necessario [ridefinire l'ambito della prenotazione](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). In caso contrario, la prenotazione sarà sottoutilizzata.

## <a name="discounted-subscription-and-offer-types"></a>Sottoscrizione scontata e tipi di offerte

Gli sconti Prenotazione si applicano alle sottoscrizioni idonee e ai tipi di offerte seguenti.

- Contratto Enterprise (numeri offerte: MS-AZR-0017P o MS-AZR-0148P)
- Sottoscrizioni Contratto del cliente Microsoft.
- Piani individuali con tariffe con pagamento in base al consumo (numeri offerte: MS-AZR-0003P o MS-AZR-0023P)
- Sottoscrizioni provider di servizi cloud

Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

## <a name="how-is-a-reservation-billed"></a>Come viene fatturata una prenotazione?

La prenotazione viene addebitata in base al metodo di pagamento associato alla sottoscrizione. Il costo della prenotazione viene sottratto dal saldo dell'impegno monetario, se disponibile. Se tale saldo non copre il costo della prenotazione, l'eccedenza verrà fatturata. Se si ha una sottoscrizione di un singolo piano con pagamento in base al consumo, sulla carta di credito associata all'account verranno immediatamente addebitati gli acquisti anticipati. I pagamenti mensili vengono visualizzati nella fattura e addebitati mensilmente sulla carta di credito. Se l'addebito avviene tramite fattura, il costo risulterà visibile sulla fattura successiva.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si applica all'utilizzo delle risorse in base agli attributi selezionati al momento dell'acquisto. Gli attributi includono l'ambito in cui vengono eseguiti i database SQL, Azure Cosmos DB, le macchine virtuali o altre risorse corrispondenti. Se ad esempio si vuole usufruire di uno sconto per la prenotazione di quattro macchine virtuali Standard D2 nell'area Stati Uniti occidentali, selezionare la sottoscrizione in cui sono in esecuzione le VM.

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

Ad esempio, è possibile creare una risorsa in un secondo momento e avere una prenotazione corrispondente sottoutilizzata. Lo sconto per la prenotazione si applica automaticamente alla nuova risorsa corrispondente.

Se le macchine virtuali sono in esecuzione in diverse sottoscrizioni all'interno della sottoscrizione o dell'account corrente, selezionare l'ambito condiviso. L'ambito condiviso consente l'applicazione dello sconto relativo all'acquisto di istanze riservate tra sottoscrizioni. Dopo aver acquistato una prenotazione è possibile modificare l'ambito. Per altre informazioni, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).

Lo sconto per la prenotazione si applica solo alle risorse associate a sottoscrizioni Enterprise, Contratto del cliente Microsoft, CSP o con pagamento in base al consumo. Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

## <a name="when-the-reservation-term-expires"></a>Quando il periodo di prenotazione termina

Al termine del periodo di prenotazione, lo sconto per la fatturazione scade. Le macchine virtuali, i database SQL, Azure Cosmos DB o altre risorse verranno fatturate al prezzo con pagamento in base al consumo. Le prenotazioni di Azure non vengono rinnovate automaticamente. Per continuare a usufruire dello sconto sulla fatturazione è necessario acquistare una nuova prenotazione per i servizi e il software idonei.

## <a name="discount-applies-to-different-sizes"></a>Lo sconto si applica a dimensioni diverse

Quando si acquista una prenotazione, è possibile applicare lo sconto ad altre istanze con gli attributi compresi nello stesso gruppo di dimensioni. Questa funzionalità è nota come flessibilità delle dimensioni istanza. La flessibilità della copertura degli sconti dipende dal tipo di prenotazione e dagli attributi scelti all'acquisto della prenotazione.

Piani di servizio:

- Istanze di macchina virtuale riservate: quando si acquista la prenotazione, se si seleziona **Ottimizzato per: Flessibilità delle dimensioni istanza**, la copertura dello sconto dipende dalle dimensioni delle VM selezionate. La prenotazione può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Per altre informazioni vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacità riservata del database SQL: la copertura degli sconti dipende dal livello di prestazioni selezionato. Per altre informazioni, vedere [Informazioni su come viene applicato ai database SQL lo sconto sulla prenotazione](billing-understand-reservation-charges.md).
- Capacità riservata di Azure Cosmos DB: la copertura degli sconti dipende dalla velocità effettiva di cui viene effettuato il provisioning. Per altre informazioni, vedere [Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Notifiche per le prenotazioni

A seconda del metodo di pagamento scelto per la sottoscrizione di Azure, gli utenti dell'organizzazione indicati di seguito riceveranno notifiche sulle prenotazioni tramite posta elettronica. Le notifiche vengono inviate per vari eventi, tra cui:

- Purchase
- Scadenza imminente della prenotazione
- Expiry
- Rinnovo
- Annullamento
- Cambio di ambito

Per i clienti con sottoscrizioni EA:
- Viene inviata una notifica di acquisto all'acquirente e ai contatti per le notifiche EA.
- Le altre notifiche del ciclo di vita della prenotazione vengono inviate solo ai contatti delle notifiche EA.
- Gli utenti aggiunti a una prenotazione tramite l'autorizzazione Controllo degli accessi in base al ruolo (IAM) non ricevono notifiche tramite posta elettronica.

Per i clienti con sottoscrizioni singole:
- L'acquirente riceve una notifica di acquisto.
- Al momento dell'acquisto, il proprietario dell'account di fatturazione della sottoscrizione riceve una notifica di acquisto.
- Il proprietario dell'account riceve tutte le altre notifiche.


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](/partner-center/azure-reservations)

- Altre informazioni sulle prenotazioni per i piani di servizio:
    - [Macchine virtuali con istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Risorse di Azure Cosmos DB con capacità riservata di Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md) Altre informazioni sulle prenotazioni per i piani software:
    - [Piani software Red Hat da prenotazioni di Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Piani software SUSE da prenotazioni di Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
