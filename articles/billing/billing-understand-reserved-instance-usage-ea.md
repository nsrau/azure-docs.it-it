---
title: Comprendere l'utilizzo di prenotazioni di Azure per i contratti Enterprise
description: Informazioni su come leggere l'utilizzo per comprendere come viene applicata la prenotazione di Azure per l'iscrizione Enterprise.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: b2452580eaecc0ab922f8e7db48676f70831a8ca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66126852"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Ottenere informazioni sull'utilizzo e costi di prenotazione contratto Enterprise Agreement

I costi di prenotazione e i dati di utilizzo sono disponibili per i clienti con contratto Enterprise nel portale di Azure e le API REST. Questo articolo consente di:

- Ottenere i dati di acquisto di prenotazioni
- Ottenere i dati di utilizzo in difetto di prenotazione
- Ammortizzare i costi di prenotazione
- Chargeback per l'utilizzo della prenotazione
- Calcolare i risparmi di prenotazione

Gli addebiti di Marketplace sono consolidati in dati di utilizzo. Visualizzare gli addebiti per primo utilizzo di terze parti, marketplace e acquisti da una singola origine dati.

## <a name="reservation-charges-in-azure-usage-data"></a>Costi di prenotazione nei dati di utilizzo di Azure

I dati sono suddivisa in due set di dati separati: _Costo effettivo_ e _Amortized Cost_. Differenze tra questi due set di dati:

**Costo effettivo** -fornisce i dati per risolvere le differenze con la fattura mensile. Questi dati sono prenotazione i costi di acquisto. Ha zero EffectivePrice per l'utilizzo che ha ricevuto lo sconto di prenotazione.

**Costo ammortizzato** -EffectiveCost che ottiene lo sconto di prenotazione della risorsa è il costo ripartito dell'istanza riservata. Il set di dati include anche i costi di prenotazione inutilizzati. La somma dei costi di prenotazione e prenotazioni inutilizzate fornisce il costo giornaliero ammortizzato della prenotazione.

Confronto tra due set di dati:

| Dati | Set di dati di costo effettivo | Set di dati di costo ammortizzato |
| --- | --- | --- |
| Acquisto di istanze riservate | Disponibili in questa vista.<br><br>  Per ottenere questo filtro dei dati su ChargeType = &quot;acquisto&quot;. <br><br> Fare riferimento a ReservationID o ReservationName sapere quali prenotazione l'addebito è relativo.  | Non applicabile a questa visualizzazione. <br><br> I costi di acquisto non sono incluse nei dati ammortizzati. |
| EffectivePrice | Il valore è zero per l'utilizzo che consente di ottenere lo sconto della prenotazione. | Il valore è applicata la ripartizione proporzionale per ogni ora costo della prenotazione per l'utilizzo con lo sconto di prenotazione. |
| Prenotazioni inutilizzate (indica il numero di ore che della prenotazione non è stata utilizzata in un giorno e il valore monetario dei rifiuti) | Non applicabile in questa visualizzazione. | Disponibili in questa vista.<br><br> Per ottenere questi dati, filtrare ChargeType = &quot;UnusedReservation&quot;.<br><br>  Fare riferimento a ReservationID o ReservationName sapere quali prenotazione è stata utilizzata in modo inadeguata. Si tratta di quantità della prenotazione è stato sprecato nella relativa al giorno.  |
| Prezzo unitario (prezzo della risorsa dall'elenco prezzi) | Disponibile | Disponibile |

Altre informazioni disponibili nei dati di utilizzo di Azure sono stata modificata:

- Informazioni su prodotti e contatore - Azure non sostituito il contatore utilizzato originariamente ReservationId e ReservationName, come accadeva in precedenza.
- ReservationId e ReservationName - si tratta di propri campi nei dati. Usato in precedenza, a essere disponibili solo con informazioni aggiuntive.
- ProductOrderId - l'ID dell'ordine di prenotazione, aggiunto come il proprio campo.
- ProductOrderName - nome del prodotto la prenotazione acquistata.
- Termine - 36 mesi o 12 mesi.
- RINormalizationRatio - disponibile con informazioni aggiuntive. Questo è il rapporto in cui viene applicata la prenotazione per il record di utilizzo. Se flessibilità le dimensioni dell'istanza è attivata per la prenotazione, è possibile applicare a altre dimensioni. Il valore Mostra il rapporto che la prenotazione è stata applicata a per il record di utilizzo.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Ottengano dati di utilizzo di prenotazione e consumo di Azure usando l'API

È possibile ottenere i dati usando l'API o scaricarlo dal portale di Azure.

Si chiama il [API per dettagli sull'uso](/rest/api/consumption/usagedetails/list) con la versione API &quot;2019-04-01-preview&quot; per ottenere i nuovi dati. Per informazioni dettagliate sulla terminologia, vedere [condizioni di utilizzo](billing-understand-your-usage.md). Il chiamante deve essere un amministratore dell'organizzazione per il contratto enterprise tramite il [portale EA](https://ea.azure.com). Gli amministratori dell'organizzazione di sola lettura è anche possibile ottenere i dati.

I dati non sono disponibili nel [API di creazione di report per clienti Enterprise - dettagli di utilizzo](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Ecco un esempio di chiamata all'API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Per altre informazioni su {enrollmentId} e {billingPeriodId}, vedere la [dettagli di utilizzo – elenco](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) articolo API.

Le informazioni sulle metriche e filtro nella tabella seguente consentono di risolvere i problemi comuni di prenotazione.

| **Tipo di dati dell'API** | Azione chiamata API |
| --- | --- |
| **Tutti gli addebiti (utilizzo e gli acquisti)** | Sostituire {metric} con ActualCost |
| **Utilizzo è stata ricevuta lo sconto della prenotazione** | Sostituire {metric} con ActualCost<br><br>Sostituire {filtro} con: properties/reservationId%20ne%20 |
| **Utilizzo che non hanno ottenuto lo sconto della prenotazione** | Sostituire {metric} con ActualCost<br><br>Sostituire {filtro} con: properties/reservationId%20eq%20 |
| **Costi ammortizzati (utilizzo e gli acquisti)** | Sostituire {metric} con AmortizedCost |
| **Report di prenotazioni inutilizzate** | Sostituire {metric} con AmortizedCost<br><br>Sostituire {filtro} con: properties/ChargeType%20eq%20'UnusedReservation' |
| **Acquisto di istanze riservate** | Sostituire {metric} con ActualCost<br><br>Sostituire {filtro} con: properties/ChargeType%20eq%20'Purchase'  |
| **Rimborsi** | Sostituire {metric} con ActualCost<br><br>Sostituire {filtro} con: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Scaricare il file CSV di utilizzo con i nuovi dati

Se sei un amministratore con contratto Enterprise Agreement, è possibile scaricare il file CSV che contiene i nuovi dati di utilizzo dal portale di Azure. Questi dati non sono disponibili presso il [portale EA](https://ea.azure.com).

Nel portale di Azure, passare a [gestione costi + fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selezionare l'account di fatturazione.
2. Fare clic su **utilizzo e costi**.
3. Fare clic su **Download**.  
![Esempio che illustra come scaricare il file di dati CSV sull'utilizzo nel portale di Azure](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Nelle **Scarica utilizzo e costi** , in **versione 2 per i dettagli dell'utilizzo** , selezionare **tutti gli addebiti (utilizzo e gli acquisti)** e quindi fare clic su Scarica. Ripetere la procedura per **ammortizzato addebiti (utilizzo e gli acquisti)**.

I file CSV scaricato contengono i costi effettivi e i costi ammortizzati.

## <a name="common-cost-and-usage-tasks"></a>Attività comuni di utilizzo e costi

Le sezioni seguenti sono indicate operazioni comuni che usano la maggior parte delle persone per visualizzare i dati di utilizzo e costi di prenotazione.

### <a name="get-reservation-purchase-costs"></a>Ottenere una prenotazione dei costi di acquisto

I costi di acquisto di prenotazioni sono disponibili nei dati di costo effettivo. Filtrare _ChargeType = acquisto_. Fare riferimento a ProductOrderID per determinare quale ordine di prenotazione l'acquisto è relativo.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Ottenere una quantità di prenotazione sottoutilizzati e i costi

Ottenere i dati di costo ammortizzato e filtrare _ChargeType_ _= UnusedReservation_. Si ottiene la quantità di prenotazione inutilizzati giornaliero e il costo. È possibile filtrare i dati per una prenotazione o ordine di prenotazione mediante _ReservationId_ e _ProductOrderId_ campi, rispettivamente. Se una prenotazione è stata usata di 100%, il record contiene una quantità pari a 0.

### <a name="amortize-reservation-costs"></a>Ammortizzare i costi di prenotazione

Ottenere i dati di costo ammortizzato e filtro per un ordine di prenotazione mediante _ProductOrderID_ per ottenere i costi ammortizzati giornalieri di una prenotazione.

### <a name="chargeback-for-a-reservation"></a>Chargeback per una prenotazione

È possibile usare la prenotazione chargeback ad altre organizzazioni dalla sottoscrizione, gruppi di risorse o i tag. I dati di costo ammortizzato offre valore monetario di utilizzo della prenotazione a tipi di dati seguenti:

- Risorse (ad esempio una macchina virtuale)
- Gruppo di risorse
- `Tags`
- Sottoscrizione

### <a name="get-the-blended-rate-for-chargeback"></a>Ottenere la frequenza combinata per il chargeback

Per determinare la frequenza combinata, ottenere i dati dei costi ammortizzati e il costo totale di aggregazione. Per le macchine virtuali, è possibile usare le informazioni di nome del contatore o ServiceType dai dati AdditionalInfo JSON. Divide il totale dei costi in base alla quantità utilizzata per ottenere la frequenza combinata.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Prenotazione ottimale di controllo, ad esempio Usa la flessibilità di dimensioni

Più la quantità con le _RINormalizationRatio_, da informazioni aggiuntive. I risultati indicano il numero di ore di utilizzo di prenotazione è stata applicata al record di utilizzo.

### <a name="determine-reservation-savings"></a>Determinare il risparmio di prenotazione

Ottenere i dati dei costi Amortized e filtrare i dati per un'istanza riservata. Quindi:

1. Ottieni costi stimati con pagamento a consumo. Moltiplicare il _UnitPrice_ valore con _Quantity_ per ottenere i valori stimati i costi con pagamento a consumo, se non è stato applicato lo sconto di prenotazione per l'utilizzo.
2. Ottenere i costi di prenotazione. Somma i _costo_ valori da ottenere il valore monetario di ciò che hai pagato per l'istanza riservata. Include i costi utilizzati e inutilizzati della prenotazione.
3. Esegue la sottrazione dei costi di prenotazione dai costi stimati con pagamento a consumo per ottenere i risparmi stimati.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>Gli acquisti di prenotazioni e ripartirli in analisi dei costi di Azure

Costo delle istanze riservate sono disponibile nel [modalità di anteprima di analisi dei costi Azure](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis). Per impostazione predefinita, la visualizzazione di dati di costo è per il costo effettivo. È possibile passare a costo ammortizzato. Di seguito è riportato un esempio.

![Esempio che illustra la selezione di costo ammortizzato nell'analisi dei costi](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Applicare filtri per visualizzare gli addebiti per un tipo di prenotazione o costi. Raggruppa in base a nome prenotazione per visualizzare i costi per le prenotazioni.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto sulla prenotazione](billing-understand-vm-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
