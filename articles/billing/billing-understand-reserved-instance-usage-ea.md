---
title: Informazioni sull'utilizzo delle prenotazioni di Azure per i contratti Enterprise
description: Informazioni su come leggere l'utilizzo per comprendere come viene applicata la prenotazione di Azure per l'iscrizione Enterprise.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598090"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Ottenere Enterprise Agreement costi di prenotazione e utilizzo

I costi di prenotazione e i dati di utilizzo sono disponibili per i clienti Enterprise Agreement nel portale di Azure e nelle API REST. Questo articolo consente di:

- Ottenere i dati di acquisto della prenotazione
- Conosce la sottoscrizione, il gruppo di risorse o la risorsa che ha usato la prenotazione
- Chargeback per l'utilizzo della prenotazione
- Calcolo risparmi prenotazione
- Ottenere i dati di prenotazione in sottoutilizzo
- Ammortizzare i costi di prenotazione

I prezzi del Marketplace sono consolidati nei dati di utilizzo. È possibile visualizzare gli addebiti per l'utilizzo, l'utilizzo del Marketplace e gli acquisti da parte di una singola origine dati.

## <a name="reservation-charges-in-azure-usage-data"></a>Addebiti di prenotazione nei dati di utilizzo di Azure

I dati sono divisi in due set di dati separati: _Costo effettivo_ e _costo ammortizzato_. Differenze tra i due set di impostazioni:

**Costo effettivo** : fornisce i dati da riconciliare con la fattura mensile. Questi dati hanno i costi di acquisto della prenotazione e i dettagli dell'applicazione di prenotazione. Con questi dati, è possibile individuare la sottoscrizione o il gruppo di risorse o la risorsa che ha ricevuto lo sconto di prenotazione in un determinato giorno. Il valore di EffectivePrice per l'utilizzo che riceve lo sconto di prenotazione è zero.

**Costo ammortizzato** : questo set di dati è simile al set di dati dei costi effettivi, tranne per il fatto che il valore di EffectivePrice per l'utilizzo che ottiene lo sconto della prenotazione è il costo riordinato della prenotazione (anziché zero). Questo consente di individuare il valore monetario del consumo di prenotazione da parte di una sottoscrizione, di un gruppo di risorse o di una risorsa e di ricaricare internamente l'utilizzo della prenotazione. Il set di dati dispone anche di ore di prenotazione inutilizzate. Il set di dati non contiene record di acquisto della prenotazione. 

Confronto tra due set di dati:

| Data | Set di dati dei costi effettivi | Set di dati dei costi ammortizzati |
| --- | --- | --- |
| Acquisti prenotazione | Disponibile in questa visualizzazione.<br><br>  Per ottenere questo filtro dei dati in ChargeType &quot;=&quot;purchase. <br><br> Per informazioni sulla prenotazione, vedere ReservationID o Reservationname.  | Non applicabile a questa visualizzazione. <br><br> I costi di acquisto non vengono forniti nei dati ammortizzati. |
| EffectivePrice | Il valore è zero per l'utilizzo che ottiene lo sconto della prenotazione. | Il valore è il costo rivalutato per ora della prenotazione per l'utilizzo con lo sconto per la prenotazione. |
| Prenotazione inutilizzata (indica il numero di ore in cui la prenotazione non è stata usata in un giorno e il valore monetario degli sprechi) | Non applicabile in questa visualizzazione. | Disponibile in questa visualizzazione.<br><br> Per ottenere questi dati, filtrare in ChargeType = &quot;UnusedReservation&quot;.<br><br>  Per informazioni sulla prenotazione sottoutilizzata, vedere ReservationID o Reservationname. Questa è la quantità di prenotazione sprecata per la giornata.  |
| PrezzoUnitario (prezzo della risorsa dall'elenco prezzi) | Disponibile | Disponibile |

Sono state modificate altre informazioni disponibili nei dati di utilizzo di Azure:

- Informazioni sul prodotto e sul contatore: Azure non sostituisce il contatore utilizzato originariamente con ReservationId e Reservationname, come in precedenza.
- ReservationId e Reservationname: si tratta dei propri campi nei dati. In precedenza, era disponibile solo in AdditionalInfo.
- ProductOrderId: ID dell'ordine di prenotazione, aggiunto come campo.
- ProductOrderName: nome del prodotto della prenotazione acquistata.
- Termine: 12 mesi o 36 mesi.
- RINormalizationRatio-disponibile in AdditionalInfo. Questo è il rapporto in cui viene applicata la prenotazione al record di utilizzo. Se la flessibilità delle dimensioni dell'istanza è abilitata per la prenotazione, può essere applicata ad altre dimensioni. Il valore Mostra il rapporto a cui è stata applicata la prenotazione per il record di utilizzo.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Ottenere il consumo di Azure e i dati di utilizzo delle prenotazioni usando l'API

È possibile ottenere i dati usando l'API o scaricarli dalla portale di Azure.

Per ottenere i nuovi dati, è necessario chiamare [l'API Details Usage](/rest/api/consumption/usagedetails/list) con la versione API &quot;2019-04-01-preview&quot;. Per informazioni dettagliate sulla terminologia, vedere [condizioni di utilizzo](billing-understand-your-usage.md). Il chiamante deve essere un amministratore dell'organizzazione per il contratto Enterprise Agreement usando il [portale EA](https://ea.azure.com). Gli amministratori dell'organizzazione di sola lettura possono anche ottenere i dati.

I dati non sono disponibili nelle [API per la creazione di report per i clienti Enterprise-dettagli sull'utilizzo](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Di seguito è riportato un esempio di chiamata all'API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Per altre informazioni su {enrollmentId} e {billingPeriodId}, vedere l'articolo relativo ai [Dettagli sull'utilizzo-API List](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) .

Le informazioni riportate nella tabella seguente sulla metrica e sul filtro consentono di risolvere i problemi comuni di prenotazione.

| **Tipo di dati API** | Azione chiamata API |
| --- | --- |
| **Tutti gli addebiti (utilizzo e acquisti)** | Sostituisci {Metric} con ActualCost |
| **Utilizzo che ha ottenuto lo sconto sulla prenotazione** | Sostituisci {Metric} con ActualCost<br><br>Sostituire {Filter} con: Properties/reservationId% 20NE% 20 |
| **Utilizzo che non ha ottenuto lo sconto sulla prenotazione** | Sostituisci {Metric} con ActualCost<br><br>Sostituire {Filter} con: Properties/reservationId% 20eq% 20 |
| **Addebiti ammortizzati (utilizzo e acquisti)** | Sostituisci {Metric} con AmortizedCost |
| **Report prenotazione non usato** | Sostituisci {Metric} con AmortizedCost<br><br>Sostituire {Filter} con: Properties/ChargeType% 20eq% 20' UnusedReservation ' |
| **Acquisti prenotazione** | Sostituisci {Metric} con ActualCost<br><br>Sostituire {Filter} con: Properties/ChargeType% 20eq% 20' Purchase '  |
| **Rimborsi** | Sostituisci {Metric} con ActualCost<br><br>Sostituire {Filter} con: Properties/ChargeType% 20eq% 20' restituzione ' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Scaricare il file CSV di utilizzo con nuovi dati

Se si è un amministratore EA, è possibile scaricare il file CSV contenente i nuovi dati di utilizzo da portale di Azure. Questi dati non sono disponibili dal [portale EA](https://ea.azure.com).

Nella portale di Azure passare a [Gestione costi e fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selezionare l'account di fatturazione.
2. Fare clic su **utilizzo**e addebiti.
3. Scegliere **Download**.  
![Esempio che mostra dove scaricare il file di dati di utilizzo CSV nel portale di Azure](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. In **Scarica utilizzo** e addebiti in **Dettagli utilizzo versione 2** Selezionare **tutti gli addebiti (utilizzo e acquisti)** , quindi fare clic su download. Si ripete per gli addebiti **ammortizzati (utilizzo e acquisti)** .

I file CSV scaricati contengono costi effettivi e costi ammortizzati.

## <a name="common-cost-and-usage-tasks"></a>Attività comuni relative a costi e utilizzo

Le sezioni seguenti sono attività comuni utilizzate dalla maggior parte degli utenti per visualizzare i dati di utilizzo e i costi di prenotazione.

### <a name="get-reservation-purchase-costs"></a>Ottenere i costi di acquisto della prenotazione

I costi di acquisto della prenotazione sono disponibili nei dati di costo effettivo. Filtro per _ChargeType = Purchase_. Per determinare l'ordine di prenotazione per l'acquisto, vedere ProductOrderID.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Ottenere la quantità di prenotazione e i costi sottoutilizzati

Ottenere i dati dei costi ammortizzati e filtrare per _ChargeType_ _= UnusedReservation_. Si ottiene la quantità di prenotazione giornaliera non utilizzata e il costo. È possibile filtrare i dati per un ordine di prenotazione o prenotazione usando rispettivamente i campi _ReservationId_ e _ProductOrderId_ . Se è stata utilizzata una prenotazione pari al 100%, il record ha una quantità di 0.

### <a name="amortize-reservation-costs"></a>Ammortizzare i costi di prenotazione

Ottenere i dati dei costi ammortizzati e filtrare per un ordine di prenotazione usando _ProductOrderID_ per ottenere i costi giornalieri ammortizzati per una prenotazione.

### <a name="chargeback-for-a-reservation"></a>Chargeback per una prenotazione

È possibile usare la prenotazione di chargeback per le altre organizzazioni per sottoscrizione, gruppi di risorse o tag. I dati relativi ai costi ammortizzati forniscono il valore monetario dell'utilizzo di una prenotazione con i tipi di dati seguenti:

- Risorse (ad esempio una macchina virtuale)
- Gruppo di risorse
- Tag
- Sottoscrizione

### <a name="get-the-blended-rate-for-chargeback"></a>Ottenere la velocità di fusione per il chargeback

Per determinare la frequenza di Blend, ottenere i dati dei costi ammortizzati e aggregare il costo totale. Per le macchine virtuali, è possibile usare le informazioni Metername o ServiceType dei dati JSON di AdditionalInfo. Dividere il costo totale per la quantità usata per ottenere la frequenza di Blend.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Controllare l'uso della prenotazione ottimale per la flessibilità delle dimensioni dell'istanza

Più la quantità con _RINormalizationRatio_, da AdditionalInfo. I risultati indicano il numero di ore di utilizzo della prenotazione applicato al record di utilizzo.

### <a name="determine-reservation-savings"></a>Determinare il risparmio di prenotazione

Ottenere i dati dei costi ammortizzati e filtrare i dati per un'istanza riservata. Quindi:

1. Ottieni costi stimati con pagamento in base al consumo. Moltiplicare il valore _PrezzoUnitario_ con i valori di _quantità_ per ottenere i costi stimati con pagamento in base al consumo, se lo sconto per la prenotazione non è stato applicato.
2. Ottenere i costi di prenotazione. Sommare i valori di _costo_ per ottenere il valore monetario di quello pagato per l'istanza riservata. Include i costi utilizzati e inutilizzati della prenotazione.
3. Sottrarre i costi di prenotazione dai costi stimati con pagamento in base al consumo per ottenere il risparmio stimato.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Acquisti di prenotazione e ammortamento nell'analisi dei costi

I costi di prenotazione sono disponibili nell' [analisi dei costi](https://aka.ms/costanalysis). Per impostazione predefinita, l'analisidei costi Mostra i costi effettivi, ovvero il modo in cui i costi verranno visualizzati nella fattura. Per visualizzare gli acquisti di prenotazione suddivisi e associati alle risorse che hanno usato il vantaggio, passare al **costo ammortizzato**:

![Esempio che mostra dove selezionare il costo ammortizzato nell'analisi dei costi](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Raggruppa per tipo di addebito per visualizzare un'interruzione dell'utilizzo, degli acquisti e dei rimborsi; o tramite prenotazione per una ripartizione della prenotazione e dei costi su richiesta. Tenere presente che gli unici costi di prenotazione che verranno visualizzati quando si esaminano i costi effettivi sono gli acquisti, ma i costi verranno allocati alle singole risorse che hanno usato il benfit quando si esaminano i costi ammortizzati. Quando si esamina il costo ammortizzato, viene visualizzato anche un nuovo tipo di addebito **UnusedReservation** .

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
