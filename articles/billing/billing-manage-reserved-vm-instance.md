---
title: Gestire le prenotazioni di Azure
description: Informazioni su come gestire le prenotazioni di Azure.
ms.service: cost-management-billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: f1a23f7217ccae843f2394fdea4c04b5842e6fd0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226207"
---
# <a name="manage-reservations-for-azure-resources"></a>Gestire le prenotazioni per le risorse di Azure

Dopo aver acquistato una prenotazione di Azure, può essere necessario applicarla a una sottoscrizione diversa, sostituire la persona che la gestisce oppure cambiarne l'ambito. È inoltre possibile dividere una prenotazione in due prenotazioni per applicare alcune delle istanze acquistate a un'altra sottoscrizione.

Se si acquistano le istanze di macchine virtuali riservate di Azure, è possibile cambiare l'impostazione di ottimizzazione per la prenotazione. Lo sconto della prenotazione può essere applicato alle macchine virtuali nella stessa serie oppure è possibile riservare capacità del data center per una dimensione di macchina virtuale specifica. È quindi consigliabile provare a ottimizzare le prenotazioni in modo che vengano pienamente utilizzate.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Ordine di prenotazione e prenotazione

Quando si acquista una prenotazione, vengono creati due oggetti: **ordine di prenotazione** e **prenotazione**.

Al momento dell'acquisto, un ordine di prenotazione include un'unica prenotazione. Operazioni come la suddivisione, l'unione, il rimborso parziale o lo scambio creano nuove prenotazioni nell'**ordine di prenotazione**.

Per visualizzare un ordine di prenotazione, passare a **Prenotazioni**, selezionare la prenotazione, quindi fare clic su **ID ordine di prenotazione**.

![Esempio di dettagli dell'ordine di prenotazione che mostra l'ID ordine di prenotazione ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Una prenotazione eredita le autorizzazioni dal relativo ordine di prenotazione.

## <a name="change-the-reservation-scope"></a>Cambiare l'ambito della prenotazione

 Lo sconto per la prenotazione si applica a macchine virtuali, database SQL, Azure Cosmos DB o altre risorse che corrispondono alla prenotazione e vengono eseguite all'interno del relativo ambito. Il contesto di fatturazione dipende dalla sottoscrizione usata per acquistare la prenotazione.

Per aggiornare l'ambito di una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare la prenotazione.
4. Selezionare **Impostazioni** > **Configurazione**.
5. Modificare l'ambito.

Se si passa da un ambito condiviso a un ambito singolo, è possibile selezionare solo le sottoscrizioni di cui si è proprietari. È possibile selezionare solo le sottoscrizioni all'interno dello stesso contesto di fatturazione della prenotazione.

L'ambito si applica solo a singole sottoscrizioni con pagamento in base al consumo (offerte MS-AZR-0003P o MS-AZR-0023P), all'offerta Enterprise MS-AZR-0017P o MS-AZR-0148P oppure ai tipi di sottoscrizione CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Aggiungere o modificare gli utenti che possono gestire una prenotazione

È possibile delegare la gestione di una prenotazione mediante l'aggiunta di utenti all'ordine o alla prenotazione. Per impostazione predefinita, la persona che ha acquistato la prenotazione e l'amministratore dell'account dispongono del ruolo di proprietario per l'ordine e per la prenotazione.

È possibile gestire l'accesso agli ordini di prenotazione e alle prenotazioni in modo indipendente rispetto alle sottoscrizioni che ricevono lo sconto associato. L'assegnazione delle autorizzazioni per la gestione di un ordine di prenotazione o di una prenotazione non implica l'autorizzazione per la gestione della sottoscrizione. Analogamente, l'assegnazione delle autorizzazioni per la gestione di una sottoscrizione nell'ambito della prenotazione non implica i diritti per la gestione dell'ordine di prenotazione o della prenotazione.

Per eseguire uno scambio o richiedere un rimborso, l'utente deve avere accesso all'ordine di prenotazione. Quando si concedono le autorizzazioni a qualcuno, è preferibile assegnarle per l'ordine di prenotazione, non per la prenotazione.


Per delegare la gestione dell'accesso per una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco delle prenotazioni a cui è possibile accedere.
3. Selezionare la prenotazione per la quale si desidera delegare l'accesso ad altri utenti.
4. Selezionare **Controllo di accesso (IAM)** .
5. Selezionare **Aggiungi assegnazione di ruolo** > **Ruolo** > **Proprietario**. Oppure selezionare un ruolo diverso se si intende concedere un ruolo limitato.
6. Digitare l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario.
7. Selezionare l'utente e quindi selezionare **Salva**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividere una prenotazione singola in due prenotazioni

 Dopo aver acquistato più di un'istanza di risorsa all'interno di una prenotazione, è possibile assegnare le istanze all'interno di tale prenotazione a sottoscrizioni diverse. Per impostazione predefinita, tutte le istanze sono associate a un ambito (sottoscrizione singola o ambito condiviso). Si supponga ad esempio di aver acquistato 10 istanze di prenotazione e di aver specificato la sottoscrizione A come ambito. Si vuole in seguito impostare l'ambito di sette prenotazioni sulla sottoscrizione A e quello delle restanti tre sulla sottoscrizione B. La suddivisione di una prenotazione consente di distribuire le istanze per una gestione più granulare dell'ambito. È possibile semplificare il processo di allocazione alle sottoscrizioni scegliendo l'ambito condiviso. Tuttavia, per motivi di gestione di costi o budget dei costi è possibile allocare quantità a sottoscrizioni specifiche.

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

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

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

## <a name="optimize-reservation-use"></a>Ottimizzare l'uso delle prenotazioni

Il risparmio sulle prenotazioni di Azure deriva solo dall'uso prolungato delle risorse. Quando si acquista una prenotazione, si paga un costo iniziale per un possibile uso delle risorse essenzialmente pari al 100% per un periodo di uno o tre anni. Provare a ottimizzare la prenotazione per usare tutte le risorse possibili e ottenere il massimo risparmio. Le sezioni seguenti illustrano come monitorare una prenotazione e ottimizzarne l'uso.

### <a name="view-reservation-use-in-the-azure-portal"></a>Visualizzare l'uso delle prenotazioni nel portale di Azure

Il portale di Azure è uno dei modi disponibili per visualizzare l'utilizzo delle prenotazioni.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Tutti i servizi** > [**Prenotazioni**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e notare il valore di **Utilizzo (%)** associato a una prenotazione.  
  ![Immagine che mostra l'elenco delle prenotazioni](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. Selezionare una prenotazione.
4. Esaminare la tendenza dell'uso della prenotazione nel corso del tempo.  
  ![Immagine che mostra l'uso della prenotazione ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Visualizzare l'uso delle prenotazioni con l'API

I clienti con contratto Enterprise Agreement (EA) possono visualizzare a livello di codice come vengono usate le prenotazioni all'interno dell'organizzazione. Una prenotazione inutilizzata si rileva tramite i dati di utilizzo. Quando si esaminano gli addebiti delle prenotazione, tenere presente che i dati sono divisi tra costi effettivi e costi ammortizzati. I costi effettivi forniscono i dati per riconciliare la fattura mensile. Includono inoltre il costo di acquisto della prenotazione e i dettagli sulla relativa applicazione. I costi ammortizzati sono simili a quelli effettivi, con la differenza che il prezzo valido per l'utilizzo della prenotazione è ripartito in modo proporzionale. Le ore di prenotazioni inutilizzate vengono visualizzate nei dati dei costi ammortizzati. Per altre informazioni sui dati di utilizzo per i clienti con contratto EA, vedere [Ottenere i costi e l'utilizzo delle prenotazioni con contratto Enterprise Agreement](billing-understand-reserved-instance-usage-ea.md).

Per altri tipi di sottoscrizione, usare l'API per i [riepiloghi delle prenotazioni](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation), con l'elenco per ordine di prenotazione e prenotazione.

### <a name="optimize-your-reservation"></a>Ottimizzare la prenotazione

Se si ritiene che le prenotazioni dell'organizzazione siano sottoutilizzate:

- Assicurarsi che le macchine virtuali create dall'organizzazione corrispondano alle dimensioni di VM riportate nella prenotazione.
- Assicurarsi che la flessibilità delle dimensioni istanza sia attiva. Per altre informazioni, vedere [Gestire le prenotazioni - Modificare l'impostazione di ottimizzazione per le istanze di macchina virtuale riservate](#change-optimize-setting-for-reserved-vm-instances).
- Cambiare l'ambito della prenotazione impostandolo su _condiviso_ in modo che venga applicato su scala più ampia. Per altre informazioni, vedere [Cambiare l'ambito di una prenotazione](#change-the-reservation-scope).
- Valutare se scambiare la quantità inutilizzata. Per altre informazioni, vedere [Annullamenti e scambi](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Cosa sono le prenotazioni per Azure?](billing-save-compute-costs-reservations.md)

Acquistare un piano di servizio:
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Pagare in anticipo le risorse di Azure Cosmos DB con capacità riservata di Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

Acquistare un piano software:
- [Pagare in anticipo i piani software Red Hat dalle prenotazioni di Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Pagare in anticipo per i piani software SUSE dalle prenotazioni di Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Informazioni su sconti e utilizzo:
- [Informazioni su come viene applicato lo sconto sulla prenotazione di macchine virtuali](billing-understand-vm-reservation-charges.md)
- [Informazioni su come viene applicato lo sconto relativo al piano software Red Hat Enterprise Linux](../billing/billing-understand-rhel-reservation-charges.md)
- [Informazioni su come viene applicato lo sconto relativo al piano software SUSE Linux Enterprise](../billing/billing-understand-suse-reservation-charges.md)
- [Informazioni su come vengono applicati gli altri sconti sulla prenotazione](billing-understand-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
