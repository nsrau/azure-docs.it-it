---
title: Acquistare una prenotazione di Azure
description: Informazioni sui punti importanti prima di acquistare una prenotazione di Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: f22a8f085938f5a3cc3e7a748391a9048129296c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888369"
---
# <a name="buy-a-reservation"></a>Acquistare una prenotazione

Le prenotazioni di Azure consentono di risparmiare denaro a fronte di un impegno di acquisto di un piano di uno o tre anni per numerose risorse di Azure. Prima di sottoscrivere un impegno di acquisto per una prenotazione, esaminare le sezioni seguenti per prepararsi all'acquisto.

## <a name="who-can-buy-a-reservation"></a>Chi può acquistare una prenotazione

Per acquistare una prenotazione, è necessario avere il ruolo di proprietario o di acquirente di prenotazioni in una sottoscrizione di Azure di tipo Enterprise (MS-AZR-0017P o MS-AZR-0148P), con pagamento in base al consumo (MS-AZR-0003P o MS-AZR-0023P) o di tipo Contratto del cliente Microsoft. Per acquistare prenotazioni di Azure, i partner CSP (Cloud Solution Provider) possono usare il portale di Azure o il  [Centro per i partner](/partner-center/azure-reservations) .

I clienti con contratto Enterprise Agreement (EA) possono limitare gli acquisti agli amministratori EA disabilitando l'opzione **Aggiungi istanze riservate** in EA Portal. Per acquistare una prenotazione, gli amministratori del contratto Enterprise devono avere accesso come proprietario o acquirente di prenotazioni in almeno una sottoscrizione EA. Questa opzione è utile per le organizzazioni che vogliono che le prenotazioni vengano acquistate da un team centralizzato.

Lo sconto per la prenotazione si applica solo alle risorse associate alle sottoscrizioni acquistate tramite piani Enterprise, Cloud Solution Provider (CSP), Contratto del cliente Microsoft e singoli piani con pagamento in base al consumo.

## <a name="scope-reservations"></a>Definire l'ambito delle prenotazioni

Come ambito di una prenotazione è possibile definire una sottoscrizione o gruppi di risorse. Quando si imposta l'ambito di una prenotazione, si selezionano le risorse a cui verrà applicato il risparmio corrispondente. Se come ambito della prenotazione si sceglie un gruppo di risorse, gli sconti della prenotazione si applicano solo al gruppo di risorse, non all'intera sottoscrizione.

### <a name="reservation-scoping-options"></a>Opzioni dell'ambito delle prenotazioni

Sono disponibili tre opzioni per definire l'ambito di una prenotazione, a seconda delle esigenze:

- **Ambito gruppo di risorse singolo**: lo sconto per la prenotazione si applica alle risorse corrispondenti incluse solo nel gruppo di risorse selezionato.
- **Ambito sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.
- **Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione.
    - Per i clienti con contratto Enterprise Agreement, il contesto di fatturazione è la registrazione. L'ambito condiviso della prenotazione include più tenant di Active Directory in una registrazione.
    - Per i clienti con Contratto del cliente Microsoft, l'ambito di fatturazione corrisponde al profilo di fatturazione.
    - Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.

Per l'applicazione degli sconti sull'utilizzo di risorse, Azure elabora la prenotazione nell'ordine seguente:

1. Prenotazioni con ambito costituito da un gruppo di risorse
2. Prenotazioni con ambito singolo
3. Prenotazioni con ambito condiviso

Dopo aver acquistato una prenotazione, è sempre possibile aggiornare l'ambito. A tale scopo, passare alla prenotazione, fare clic su **Configurazione** e ridefinire l'ambito della prenotazione. La ridefinizione dell'ambito di una prenotazione non è una transazione commerciale. Il periodo di prenotazione non cambia. Per altre informazioni sull'aggiornamento dell'ambito, vedere [Aggiornare l'ambito dopo l'acquisto di una prenotazione](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Esempio di modifica dell'ambito di una prenotazione](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>Sottoscrizione scontata e tipi di offerte

Gli sconti Prenotazione si applicano alle sottoscrizioni idonee e ai tipi di offerte seguenti.

- Contratto Enterprise (numeri offerte: MS-AZR-0017P o MS-AZR-0148P)
- Sottoscrizioni Contratto del cliente Microsoft.
- Piani individuali con tariffe con pagamento in base al consumo (numeri offerte: MS-AZR-0003P o MS-AZR-0023P)
- Sottoscrizioni provider di servizi cloud

Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

## <a name="purchase-reservations"></a>Acquistare prenotazioni

È possibile acquistare prenotazioni tramite il portale di Azure, le API, PowerShell e l'interfaccia della riga di comando. Quando si è pronti per acquistare una prenotazione, leggere gli articoli seguenti in base alle esigenze:

- [Servizio app](prepay-app-service-isolated-stamp.md)
- [Cache Redis di Azure](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Esplora dati](/azure/data-explorer/pricing-reserved-capacity)
- [Archiviazione su disco](../../virtual-machines/disks-reserved-capacity.md)
- [Host dedicato](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Piani software](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Database SQL](../../azure-sql/database/reserved-capacity-overview.md)
- [Database di Azure per PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Database di Azure per MySQL](../../mysql/concept-reserved-pricing.md)
- [Database di Azure per MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [Macchine virtuali](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Acquistare prenotazioni con pagamenti mensili

Ora è possibile pagare le prenotazioni con pagamenti mensili. A differenza di un acquisto anticipato, in cui si paga l'importo completo, l'opzione di pagamento mensile divide il costo totale della prenotazione in modo uniforme per ogni mese del periodo. Il costo totale delle prenotazioni con pagamento anticipato e mensile è lo stesso e non vengono addebitate spese aggiuntive quando si sceglie di pagare mensilmente.

Se la prenotazione è stata acquistata tramite un Contratto del cliente Microsoft, l'importo del pagamento mensile potrebbe essere soggetto a variazioni in base al tasso di cambio del mese corrente per la valuta locale.

I pagamenti mensili non sono disponibili per: Databricks, prenotazioni SUSE Linux, piani Red Hat e licenze Azure Red Hat OpenShift.

### <a name="view-payments-made"></a>Visualizzare i pagamenti effettuati

È possibile visualizzare i pagamenti effettuati usando le API, i dati di utilizzo e l'analisi dei costi. Per le prenotazioni pagate mensilmente, il valore della frequenza viene visualizzato come **ricorrente** nei dati di utilizzo e nell'API relativa agli addebiti della prenotazione. Per le prenotazioni pagate in anticipo, il valore viene visualizzato come **una tantum**.

L'analisi dei costi mostra gli acquisti mensili nella visualizzazione predefinita. Applicare il filtro **acquisto** per **Tipo di addebito** e **ricorrente** per **Frequenza** per visualizzare tutti gli acquisti. Per visualizzare solo le prenotazioni, applicare un filtro per **Prenotazione**.

![Esempio che mostra i costi di acquisto della prenotazione nell'analisi dei costi](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Cambio e rimborsi

Analogamente ad altre prenotazioni, è possibile scambiare le prenotazioni acquistate con la fatturazione mensile o ottenere un rimborso. 

Quando si scambia una prenotazione con pagamento mensile, il costo di durata totale del nuovo acquisto deve essere superiore ai pagamenti rimanenti annullati per la prenotazione restituita. Non sono previsti altri limiti o tariffe per gli scambi. È possibile scambiare una prenotazione pagata in anticipo per acquistare una nuova prenotazione fatturata mensilmente. Il valore di durata della nuova prenotazione, tuttavia, deve essere maggiore del valore ripartito in modo proporzionale della prenotazione da restituire.

Se si annulla una prenotazione con pagamento mensile, i pagamenti futuri annullati vengono cumulati fino al limite di rimborso di $ 50.000 USD.

Per altre informazioni su scambi e rimborsi, vedere [Scambi e rimborsi in modalità self-service per le prenotazioni di Azure](exchange-and-refund-azure-reservations.md).

## <a name="reservation-notifications"></a>Notifiche per le prenotazioni

A seconda del metodo di pagamento scelto per la sottoscrizione di Azure, gli utenti dell'organizzazione indicati di seguito riceveranno notifiche sulle prenotazioni tramite posta elettronica. Le notifiche vengono inviate per vari eventi, tra cui: 

- Purchase
- Scadenza imminente della prenotazione
- Expiry
- Rinnovo
- Annullamento
- Cambio di ambito

Per i clienti con sottoscrizioni EA:

- Le altre notifiche vengono inviate solo ai contatti per le notifiche del contratto Enterprise.
- Gli utenti aggiunti a una prenotazione tramite l'autorizzazione Controllo degli accessi in base al ruolo di Azure (IAM) non ricevono notifiche tramite posta elettronica.

Per i clienti con sottoscrizioni singole:

- L'acquirente riceve una notifica di acquisto.
- Al momento dell'acquisto, il proprietario dell'account di fatturazione della sottoscrizione riceve una notifica di acquisto.
- Il proprietario dell'account riceve tutte le altre notifiche.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le prenotazioni per le risorse di Azure](manage-reserved-vm-instance.md)
