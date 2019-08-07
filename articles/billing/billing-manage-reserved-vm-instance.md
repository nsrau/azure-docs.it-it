---
title: Gestisci prenotazioni di Azure
description: Informazioni su come gestire le prenotazioni di Azure.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840012"
---
# <a name="manage-reservations-for-azure-resources"></a>Gestire le prenotazioni per le risorse di Azure

Dopo aver acquistato una prenotazione di Azure, potrebbe essere necessario applicare la prenotazione a una sottoscrizione diversa, modificare gli utenti che possono gestire la prenotazione o modificare l'ambito della prenotazione. È inoltre possibile dividere una prenotazione in due prenotazioni per applicare alcune delle istanze acquistate a un'altra sottoscrizione.

Se sono state acquistate istanze di macchine virtuali riservate di Azure, è possibile modificare l'impostazione di ottimizzazione per la prenotazione. Lo sconto della prenotazione può essere applicato alle macchine virtuali nella stessa serie oppure è possibile riservare capacità del data center per una dimensione di macchina virtuale specifica. È quindi consigliabile provare a ottimizzare le prenotazioni in modo che siano completamente utilizzate.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Ordine di prenotazione e prenotazione

Quando si acquista una prenotazione, vengono creati due oggetti: **Ordine di prenotazione** e **prenotazione**.

Al momento dell'acquisto, un ordine di prenotazione include una prenotazione al suo interno. Azioni come Split, merge, rimborso parziale o Exchange creano nuove prenotazioni nell' **ordine di prenotazione**.

Per visualizzare un ordine di prenotazione, passare a **prenotazioni** > selezionare la prenotazione, quindi fare clic sull' **ID dell'ordine di prenotazione**.

![Esempio di dettagli dell'ordine di prenotazione che mostra l'ID dell'ordine di prenotazione ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Una prenotazione eredita le autorizzazioni dal relativo ordine di prenotazione.

## <a name="change-the-reservation-scope"></a>Modificare l'ambito della prenotazione

 Lo sconto per la prenotazione si applica alle macchine virtuali, ai database SQL, Azure Cosmos DB o ad altre risorse che corrispondono alla prenotazione ed eseguite nell'ambito della prenotazione. Il contesto di fatturazione dipende dalla sottoscrizione usata per acquistare la prenotazione.

Per aggiornare l'ambito di una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare la prenotazione.
4. Selezionare **Impostazioni** > **Configurazione**.
5. Modificare l'ambito.

Se si passa dall'ambito condiviso a quello singolo, è possibile selezionare solo le sottoscrizioni di cui si è proprietari. È possibile selezionare solo le sottoscrizioni all'interno dello stesso contesto di fatturazione della prenotazione.

L'ambito è applicabile solo a singole sottoscrizioni con tariffe con pagamento in base al consumo (offerte MS-AZR-0003P o MS-AZR-0023P), Enterprise offer MS-AZR-0017P o MS-AZR-0148P oppure i tipi di sottoscrizione CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Aggiungere o modificare gli utenti che possono gestire una prenotazione

È possibile delegare la gestione delle prenotazioni aggiungendo persone ai ruoli in base all'ordine di prenotazione o alla prenotazione. Per impostazione predefinita, la persona che inserisce l'ordine di prenotazione e l'amministratore dell'account hanno il ruolo di proprietario per l'ordine di prenotazione e la prenotazione.

È possibile gestire l'accesso agli ordini di prenotazione e alle prenotazioni in modo indipendente dalle sottoscrizioni che ricevono lo sconto per la prenotazione. Quando si concede a un utente le autorizzazioni per gestire un ordine di prenotazione o la prenotazione, non concede loro l'autorizzazione per gestire la sottoscrizione. Analogamente, se si concede a un utente le autorizzazioni per gestire una sottoscrizione nell'ambito della prenotazione, non concede loro i diritti per gestire l'ordine di prenotazione o la prenotazione.

Per eseguire uno scambio o un rimborso, l'utente deve avere accesso all'ordine di prenotazione. Quando si concedono le autorizzazioni a un utente, è preferibile concedere le autorizzazioni per l'ordine di prenotazione, non per la prenotazione.


Per delegare la gestione dell'accesso per una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco delle prenotazioni a cui è possibile accedere.
3. Selezionare la prenotazione per la quale si desidera delegare l'accesso ad altri utenti.
4. Selezionare **Controllo di accesso (IAM)** .
5. Selezionare **Aggiungi assegnazione di ruolo** > **Ruolo** > **Proprietario**. Oppure selezionare un ruolo diverso se si intende concedere un ruolo limitato.
6. Digitare l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario.
7. Selezionare l'utente e quindi selezionare **Salva**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividere una prenotazione singola in due prenotazioni

 Dopo aver acquistato più di un'istanza di risorsa all'interno di una prenotazione, è possibile assegnare le istanze all'interno di tale prenotazione a sottoscrizioni diverse. Per impostazione predefinita, tutte le istanze sono associate a un ambito (sottoscrizione singola o ambito condiviso). Ad esempio, sono state acquistate 10 istanze di prenotazione e l'ambito è stato specificato come sottoscrizione A. È ora possibile modificare l'ambito per sette prenotazioni per la sottoscrizione a e i restanti tre alla sottoscrizione B. la suddivisione di una prenotazione consente di distribuire le istanze per la gestione dell'ambito granulare. È possibile semplificare il processo di allocazione alle sottoscrizioni scegliendo l'ambito condiviso. Tuttavia, per motivi di gestione di costi o budget dei costi è possibile allocare quantità a sottoscrizioni specifiche.

 È possibile dividere una prenotazione in due prenotazioni tramite PowerShell, l'interfaccia della riga di comando oppure l'API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividere una prenotazione tramite PowerShell

1. Ottenere l'ID dell'ordine di prenotazione eseguendo il comando seguente:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Ottenere i dettagli di una prenotazione:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Dividere la prenotazione in due e distribuire le istanze:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. È possibile aggiornare l'ambito con il comando seguente:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Prenotazioni per annullamento, scambio o rimborso

È possibile annullare, scambiare o rimborsare le prenotazioni con determinate limitazioni. Per altre informazioni, vedere [scambi e rimborsi self-service per le prenotazioni di Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Modifica l'impostazione di ottimizzazione per le istanze di macchina virtuale riservate

 Quando si acquista un'istanza di macchina virtuale riservata, si sceglie la flessibilità delle dimensioni istanza o la priorità di capacità. La flessibilità delle dimensioni istanza applicherà lo sconto di prenotazione ad altre macchine virtuali dello stesso [gruppo di macchine virtuali](https://aka.ms/RIVMGroups). La priorità di capacità assegna la capacità del data center dando priorità alle distribuzioni. Questa opzione aumenta la certezza di avere la possibilità di avviare le istanze di macchina virtuale quando servono.

Per impostazione predefinita, quando l'ambito della prenotazione è condiviso, la flessibilità delle dimensioni istanza è attiva. La capacità del data center non viene classificata per le distribuzioni di macchine virtuali.

Per le prenotazioni con ambito singolo, è possibile ottimizzare la prenotazione per la priorità di capacità anziché per la flessibilità delle dimensioni istanza di macchina virtuale.

Per aggiornare l'impostazione di ottimizzazione per la prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare la prenotazione.
4. Selezionare **Impostazioni** > **Configurazione**.
5. Modificare l'impostazione **Ottimizza per**.

## <a name="optimize-reservation-use"></a>Ottimizza utilizzo prenotazione

Il risparmio di prenotazione di Azure deriva solo dall'uso di risorse prolungate. Quando si effettua l'acquisto di una prenotazione, si paga un costo iniziale per l'utilizzo delle risorse essenzialmente pari al 100% per un periodo di uno o tre anni. Provare a massimizzare la prenotazione per sfruttare al meglio l'utilizzo e i risparmi possibili. Le sezioni seguenti illustrano come monitorare una prenotazione e ottimizzarne l'uso.

### <a name="view-reservation-use-in-the-azure-portal"></a>Visualizza l'utilizzo della prenotazione nella portale di Azure

Un modo per visualizzare l'utilizzo della prenotazione è nell'portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare tutte le[**prenotazioni**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) **dei servizi** > e prendere nota dell' **utilizzo (%)** per una prenotazione.  
  ![Immagine che mostra l'elenco delle prenotazioni](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. Selezionare una prenotazione.
4. Esaminare la tendenza di utilizzo della prenotazione nel tempo.  
  ![Immagine che mostra l'uso della prenotazione ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Visualizzare l'uso della prenotazione con l'API

Se si è un cliente Enterprise Agreement (EA), è possibile visualizzare a livello di codice la modalità di utilizzo delle prenotazioni nell'organizzazione. Si ottiene una prenotazione inutilizzata tramite i dati di utilizzo. Quando si esaminano i costi di prenotazione, tenere presente che i dati sono divisi tra i costi effettivi e quelli ammortizzati. Il costo effettivo fornisce dati per riconciliare la fattura mensile. Dispone inoltre dei dettagli dell'applicazione di prenotazione e dei costi di acquisto. Il costo ammortizzato è simile al costo effettivo, tranne per il fatto che il prezzo effettivo per l'utilizzo della prenotazione viene rivalutato. Le ore di prenotazione inutilizzate vengono visualizzate in dati di costo ammortizzati. Per ulteriori informazioni sui dati di utilizzo per i clienti con contratto Enterprise, vedere [ottenere Enterprise Agreement costi e utilizzo della prenotazione](billing-understand-reserved-instance-usage-ea.md).

Per altri tipi di sottoscrizione, usare i riepiloghi delle [prenotazioni API: elenco in base all'ordine di prenotazione e alla prenotazione](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Ottimizzare la prenotazione

Se si ritiene che le prenotazioni dell'organizzazione siano sottoutilizzate:

- Assicurarsi che le macchine virtuali create dall'organizzazione corrispondano alle dimensioni della macchina virtuale per la prenotazione.
- Assicurarsi che la flessibilità delle dimensioni istanza sia attiva. Per altre informazioni, vedere [Gestire le prenotazioni - Modificare l'impostazione di ottimizzazione per le istanze di macchina virtuale riservate](#change-optimize-setting-for-reserved-vm-instances).
- Modificare l'ambito della prenotazione in _Shared_ in modo che venga applicato in modo più ampio. Per altre informazioni, vedere [modificare l'ambito di una prenotazione](#change-the-reservation-scope).
- Si consiglia di scambiare la quantità inutilizzata. Per altre informazioni, vedere [annullamenti ed Exchange](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Che cosa sono le prenotazioni per Azure?](billing-save-compute-costs-reservations.md)

Acquistare un piano di servizio:
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Pagare in anticipo le risorse di Azure Cosmos DB con capacità riservata di Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

Acquistare un piano software:
- [Pagamento anticipato per i piani software Red Hat dalle prenotazioni di Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Pagare in anticipo per i piani software SUSE dalle prenotazioni di Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Informazioni sugli sconti e sull'utilizzo:
- [Informazioni su come viene applicato lo sconto sulla prenotazione di macchine virtuali](billing-understand-vm-reservation-charges.md)
- [Informazioni sul modo in cui viene applicato lo sconto del piano software Red Hat Enterprise Linux](../billing/billing-understand-rhel-reservation-charges.md)
- [Informazioni su come viene applicato lo sconto relativo al piano software SUSE Linux Enterprise](../billing/billing-understand-suse-reservation-charges.md)
- [Informazioni su come vengono applicati gli altri sconti sulla prenotazione](billing-understand-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
