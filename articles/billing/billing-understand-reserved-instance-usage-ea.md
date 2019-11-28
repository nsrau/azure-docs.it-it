---
title: Informazioni sull'utilizzo delle prenotazioni di Azure per i contratti Enterprise Agreement
description: Informazioni su come leggere l'utilizzo per comprendere come viene applicata la prenotazione di Azure per l'iscrizione Enterprise.
author: bandersmsft
manager: yashar
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 07f8d897d55868923ecca03797cf18a5346d667c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225803"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Ottenere informazioni sui costi di prenotazione e l'utilizzo dei contratti Enterprise Agreement

I costi di prenotazione e i dati di utilizzo sono disponibili per gli utenti con contratto Enterprise Agreement nel portale di Azure e nelle API REST. Questo articolo offre informazioni utili per:

- Ottenere i dati sugli acquisti di prenotazioni
- Individuare la sottoscrizione, il gruppo di risorse o la risorsa che ha usato la prenotazione
- Effettuare il chargeback per l'utilizzo delle prenotazioni
- Calcolare il risparmio delle prenotazioni
- Ottenere i dati di sottoutilizzo delle prenotazioni
- Ammortizzare i costi di prenotazione

Gli addebiti per Marketplace sono consolidati nei dati di utilizzo. Vengono visualizzati gli addebiti per l'utilizzo proprietario, l'utilizzo del Marketplace e gli acquisti da una singola origine dati.

## <a name="reservation-charges-in-azure-usage-data"></a>Addebiti delle prenotazioni nei dati di utilizzo di Azure

I dati sono divisi in due set di dati separati: _Costo effettivo_ e _Costo ammortizzato_. Ecco le differenze tra i due set di dati:

**Costo effettivo**: fornisce i dati da riconciliare con la fattura mensile. Questi dati includono i costi di acquisto della prenotazione e i dettagli sull'applicazione della stessa. Grazie a questi dati è possibile individuare la sottoscrizione, il gruppo di risorse o la risorsa che ha ricevuto lo sconto sulla prenotazione in un determinato giorno. Il valore di EffectivePrice per l'utilizzo che riceve lo sconto sulla prenotazione è pari a zero.

**Costo ammortizzato**: questo set di dati è simile al set di dati dei costi effettivi, tranne per il fatto che il valore di EffectivePrice per l'utilizzo che ottiene lo sconto sulla prenotazione è il costo ripartito in modo proporzionale della prenotazione (anziché zero). Questo consente di conoscere il valore monetario del consumo della prenotazione da parte di una sottoscrizione, di un gruppo di risorse o di una risorsa e di stornare internamente l'addebito per l'utilizzo della prenotazione. Il set di dati dispone anche di ore di prenotazione inutilizzate. Non ha record di acquisto della prenotazione. 

Ecco un confronto tra i due set di dati:

| Dati | Set di dati Costo effettivo | Set di dati Costo ammortizzato |
| --- | --- | --- |
| Acquisti di prenotazioni | Disponibili in questa visualizzazione.<br><br>  Per ottenere questi dati, filtrare per ChargeType = &quot;Purchase&quot;. <br><br> Fare riferimento a ReservationID o ReservationName per sapere a quale prenotazione è relativo l'addebito.  | Non applicabili a questa visualizzazione. <br><br> I costi di acquisto non vengono forniti nei dati ammortizzati. |
| EffectivePrice | Il valore è zero per l'utilizzo che ottiene lo sconto sulla prenotazione. | Il valore è il costo orario ripartito in modo proporzionale della prenotazione per l'utilizzo con lo sconto per la prenotazione. |
| Prenotazione inutilizzata (indica il numero di ore in cui la prenotazione non è stata usata in un giorno e il valore monetario dello spreco) | Non applicabili in questa visualizzazione. | Disponibili in questa visualizzazione.<br><br> Per ottenere questi dati, filtrare per ChargeType = &quot;UnusedReservation&quot;.<br><br>  Fare riferimento a ReservationID o ReservationName per sapere quale prenotazione è stata sottoutilizzata. Corrisponde alla quantità di prenotazione sprecata per la giornata.  |
| UnitPrice (prezzo della risorsa dall'elenco prezzi) | Disponibile | Disponibile |

Sono state modificate altre informazioni disponibili nei dati di utilizzo di Azure:

- Informazioni sul prodotto e sul contatore: Azure non sostituisce il contatore utilizzato originariamente con ReservationId e ReservationName, come faceva in precedenza.
- ReservationId e ReservationName: sono i campi dell'ID e del nome della prenotazione nei dati. In precedenza erano disponibili solo in AdditionalInfo.
- ProductOrderId: ID dell'ordine di prenotazione, aggiunto come campo.
- ProductOrderName: nome del prodotto della prenotazione acquistata.
- Durata: 12 mesi o 36 mesi.
- RINormalizationRatio: disponibile in AdditionalInfo. È il rapporto in base a cui la prenotazione viene applicata al record di utilizzo. Se la flessibilità delle dimensioni dell'istanza è abilitata per la prenotazione, può essere applicata ad altre dimensioni. Il valore mostra il rapporto a cui è stata applicata la prenotazione per il record di utilizzo.

[Visualizzare la definizione del campo](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Ottenere i dati di consumo e di utilizzo delle prenotazioni di Azure tramite l'API

È possibile ottenere i dati usando l'API o scaricarli dal portale di Azure.

Per ottenere i nuovi dati, chiamare l'[API Dettagli utilizzo](/rest/api/consumption/usagedetails/list). Per informazioni dettagliate sulla terminologia, vedere [Comprendere i termini di utilizzo nel file su utilizzo e costi di Azure](billing-understand-your-usage.md). Il chiamante deve essere un amministratore dell'organizzazione per il contratto Enterprise Agreement che usa [EA Portal](https://ea.azure.com). Anche gli amministratori dell'organizzazione in sola lettura possono ottenere i dati.

I dati non sono disponibili in [API di creazione di report per i clienti Enterprise - Dettagli di utilizzo](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Ecco un esempio di chiamata all'API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Per altre informazioni su {enrollmentId} e {billingPeriodId}, vedere l'articolo [Dettagli utilizzo - Elenco](https://docs.microsoft.com/rest/api/consumption/usagedetails/list).

Le informazioni riportate nella tabella seguente sulla metrica e sul filtro possono essere utili per risolvere i problemi comuni delle prenotazioni.

| **Tipo di dati API** | Azione di chiamata API |
| --- | --- |
| **Tutti gli addebiti (utilizzo e acquisti)** | Sostituire {metric} con ActualCost |
| **Utilizzo che ha ottenuto lo sconto per la prenotazione** | Sostituire {metric} con ActualCost<br><br>Sostituire {filter} con: properties/reservationId%20ne%20 |
| **Utilizzo che non ha ottenuto lo sconto per la prenotazione** | Sostituire {metric} con ActualCost<br><br>Sostituire {filter} con: properties/reservationId%20eq%20 |
| **Addebiti ammortizzati (utilizzo e acquisti)** | Sostituire {metric} con AmortizedCost |
| **Report sulle prenotazioni non usate** | Sostituire {metric} con AmortizedCost<br><br>Sostituire {filter} con: properties/ChargeType%20eq%20'UnusedReservation' |
| **Acquisti di prenotazioni** | Sostituire {metric} con ActualCost<br><br>Sostituire {filter} con: properties/ChargeType%20eq%20'Purchase'  |
| **Rimborsi** | Sostituire {metric} con ActualCost<br><br>Sostituire {filter} con: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Scaricare il file CSV dei dati di utilizzo con i nuovi dati

Gli amministratori EA possono scaricare il file CSV contenente i nuovi dati di utilizzo dal portale di Azure. Questi dati n sono disponibili in [EA Portal](https://ea.azure.com).

Nel portale di Azure passare a [Gestione dei costi e fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selezionare l'account di fatturazione.
2. Fare clic su **Utilizzo e addebiti**.
3. Fare clic su **Download**.  
![Esempio che mostra dove scaricare il file CSV dei dati di utilizzo nel portale di Azure](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. In **Scarica l'utilizzo e gli addebiti** selezionare **Tutti gli addebiti (utilizzo e acquisti)** in **Dettagli utilizzo - Versione 2**, quindi fare clic su Scarica. Ripetere per **Addebiti ammortizzati (utilizzo e acquisti)** .

I file CSV scaricati contengono i costi effettivi e i costi ammortizzati.

## <a name="common-cost-and-usage-tasks"></a>Attività comuni relative a costi e utilizzo

Le sezioni seguenti descrivono attività comuni eseguite dalla maggior parte degli utenti per visualizzare i dati di utilizzo e i costi delle prenotazioni.

### <a name="get-reservation-purchase-costs"></a>Ottenere i costi di acquisto della prenotazione

I costi di acquisto della prenotazione sono disponibili nei dati di costo effettivo. Filtrare per _ChargeType = Purchase_. Fare riferimento a ProductOrderID per determinare l'ordine di prenotazione relativo all'acquisto.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Ottenere la quantità e i costi delle prenotazioni sottoutilizzate

Ottenere i dati di costo ammortizzato e filtrare per _ChargeType_ _= UnusedReservation_. Si ottiene la quantità e il costo giornalieri della prenotazione non utilizzata. È possibile filtrare i dati per una prenotazione o un ordine di prenotazione usando rispettivamente i campi _ReservationId_ e _ProductOrderId_. Se una prenotazione è stata utilizzata al 100%, il record ha una quantità pari a 0.

### <a name="amortize-reservation-costs"></a>Ammortizzare i costi di prenotazione

Ottenere i dati di costo ammortizzato e filtrare per un ordine di prenotazione usando _ProductOrderID_ per ottenere i costi ammortizzati giornalieri per una prenotazione.

### <a name="chargeback-for-a-reservation"></a>Effettuare il chargeback per una prenotazione

È possibile effettuare il chargeback dell'uso della prenotazione ad altre organizzazioni per sottoscrizione, gruppi di risorse o tag. I dati relativi ai costi ammortizzati forniscono il valore monetario dell'utilizzo di una prenotazione con i tipi di dati seguenti:

- Risorse (ad esempio una macchina virtuale)
- Resource group
- Tag
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>Ottenere il tasso misto per il chargeback

Per determinare il tasso misto, aggregare ai dati dei costi ammortizzati il costo totale. Per le macchine virtuali è possibile usare le informazioni MeterName o ServiceType dei dati JSON AdditionalInfo. Dividere il costo totale per la quantità usata per ottenere il tasso misto.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Controllare l'uso ottimale della prenotazione per la flessibilità delle dimensioni istanza

Moltiplicare la quantità per _RINormalizationRatio_ da AdditionalInfo. I risultati indicano il numero di ore di utilizzo della prenotazione applicato al record di utilizzo.

### <a name="determine-reservation-savings"></a>Determinare il risparmio delle prenotazioni

Ottenere i dati dei costi ammortizzati e filtrare i dati per un'istanza riservata. Quindi:

1. Ottenere i costi con pagamento in base al consumo stimati. Moltiplicare il valore _UnitPrice_ per i valori _Quantity_ per ottenere i costi con pagamento in base al consumo stimati, se lo sconto sulla prenotazione non è stato applicato all'utilizzo.
2. Ottenere i costi di prenotazione. Sommare i valori _Cost_ per ottenere il valore monetario dell'importo pagato per l'istanza riservata. Include i costi per la prenotazione utilizzata e inutilizzata.
3. Sottrarre i costi di prenotazione dai costi con pagamento in base al consumo stimati per ottenere il risparmio stimato.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Acquisti di prenotazioni e ammortamento nell'analisi dei costi

I costi di prenotazione sono disponibili nell'[analisi dei costi](https://aka.ms/costanalysis). Per impostazione predefinita, l'analisi dei costi mostra il **costo effettivo**, ossia i costi che verranno visualizzati nella fattura. Per visualizzare gli acquisti di prenotazioni suddivisi e associati alle risorse che hanno usato il vantaggio, passare al **costo ammortizzato**:

![Esempio che mostra dove selezionare il costo ammortizzato nell'analisi dei costi](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Raggruppare per tipo di addebito per visualizzare una ripartizione di utilizzo, acquisti e rimborsi. Raggruppare per prenotazione per una ripartizione dei costi della prenotazione e su richiesta. Tenere presente che gli unici costi di prenotazione che verranno visualizzati quando si esaminano i costi effettivi sono gli acquisti, mentre quando si esaminano i costi ammortizzati i costi verranno allocati alle singole risorse che hanno usato il vantaggio. Quando si esamina il costo ammortizzato, viene visualizzato anche un nuovo tipo di addebito **UnusedReservation**.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto sulla prenotazione](billing-understand-vm-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
