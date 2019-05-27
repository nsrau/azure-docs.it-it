---
title: Gestire le prenotazioni di Azure | Microsoft Docs
description: Informazioni su come è possibile modificare l'ambito della sottoscrizione e gestire l'accesso per le prenotazioni di Azure.
ms.service: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 9a5b200ffb9441b90875c7764786004ff5f1e8a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127123"
---
# <a name="manage-reservations-for-azure-resources"></a>Gestire le prenotazioni per le risorse di Azure

Dopo l'acquisto di una prenotazione per Azure, potrebbe essere necessario applicare la prenotazione a una sottoscrizione diversa, modificare chi può gestire la prenotazione, o modificare l'ambito della prenotazione. È inoltre possibile dividere una prenotazione in due prenotazioni per applicare alcune delle istanze acquistate a un'altra sottoscrizione.

Se si acquistano le istanze di macchina virtuale riservate di Azure, è possibile modificare l'impostazione di ottimizzazione per la prenotazione. Lo sconto della prenotazione può essere applicato alle macchine virtuali nella stessa serie oppure è possibile riservare capacità del data center per una dimensione di macchina virtuale specifica.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Prenotazione e ordine di prenotazione

Quando si acquista una prenotazione, vengono creati due oggetti: **Ordine di prenotazione** e **prenotazione**.

Al momento dell'acquisto, un ordine di prenotazione dispone di una prenotazione sotto di esso. Le azioni, ad esempio split, merge, restituzione parziale o di exchange creano nuove prenotazioni sotto il **ordine di prenotazione**.

Per un ordine di prenotazione, vedere **prenotazioni** > selezionare la prenotazione e quindi scegliere il **ID ordine prenotazione**.

![Esempio di dettagli dell'ordine di prenotazione che mostra ID ordine prenotazione ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Una prenotazione eredita autorizzazioni dal relativo ordine di prenotazione.

## <a name="change-the-reservation-scope"></a>Modificare l'ambito della prenotazione

 Lo sconto si applica alle macchine virtuali, database SQL, Azure Cosmos DB o altre risorse che corrispondono alla prenotazione ed eseguite nell'ambito di prenotazione. Il contesto di fatturazione dipende dalla sottoscrizione usata per acquistare la prenotazione.

Per aggiornare l'ambito di una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare la prenotazione.
4. Selezionare **Impostazioni** > **Configurazione**.
5. Modificare l'ambito.

Se si passa da un ambito condiviso a un ambito singolo, è possibile selezionare solo le sottoscrizioni di cui l'utente è proprietario. È possibile selezionare solo le sottoscrizioni all'interno dello stesso contesto di fatturazione della prenotazione.

L'ambito è valido solo per le sottoscrizioni con pagamento in base al consumo MS-AZR-0003P o MS-AZR-0023P, MS-AZR-0017P o MS-AZR-0148P di tipo Enterprise o CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Aggiungere o modificare gli utenti che possono gestire una prenotazione

È possibile delegare la gestione di prenotazione mediante l'aggiunta di utenti ai ruoli nell'ordine di prenotazione o la prenotazione. Per impostazione predefinita, la persona che inserisce l'ordine di prenotazione e l'amministratore account dispone del ruolo di proprietario nell'ordine di prenotazione e la prenotazione.

È possibile gestire l'accesso agli ordini di prenotazione e le prenotazioni indipendentemente dalle sottoscrizioni di cui viene applicato lo sconto di prenotazione. L'assegnazione delle autorizzazioni per gestire un ordine di prenotazione o la prenotazione, ma non concede l'autorizzazione per gestire la sottoscrizione. Analogamente, se l'assegnazione dell'autorizzazione per gestire una sottoscrizione nell'ambito della prenotazione, ma non concede diritti per gestire l'ordine di prenotazione o la prenotazione.

Per eseguire un scambio o un rimborso, l'utente deve disporre di accesso per l'ordine di prenotazione. Quando si concedono a un utente le autorizzazioni, è consigliabile concedere le autorizzazioni per l'ordine di prenotazione, non la prenotazione.


Per delegare la gestione dell'accesso per una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco delle prenotazioni a cui è possibile accedere.
3. Selezionare la prenotazione per la quale si desidera delegare l'accesso ad altri utenti.
4. Selezionare **Controllo di accesso (IAM)**.
5. Selezionare **Aggiungi assegnazione di ruolo** > **Ruolo** > **Proprietario**. Oppure selezionare un ruolo diverso se si intende concedere un ruolo limitato.
6. Digitare l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario.
7. Selezionare l'utente e quindi selezionare **Salva**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividere una prenotazione singola in due prenotazioni

 Dopo aver acquistato più di un'istanza di risorsa all'interno di una prenotazione, è possibile assegnare le istanze all'interno di tale prenotazione a sottoscrizioni diverse. Per impostazione predefinita, tutte le istanze sono associate a un ambito (sottoscrizione singola o ambito condiviso). Si supponga, ad esempio, di aver acquistato 10 istanze di prenotazione e di avere impostato l'ambito sulla sottoscrizione A. Si vuole in seguito modificare l'ambito per 7 prenotazioni sulla sottoscrizione A, e per le restanti 3 sulla sottoscrizione B. La divisione di una prenotazione consente pertanto di distribuire le istanze per una gestione più granulare dell'ambito. È possibile semplificare il processo di allocazione alle sottoscrizioni scegliendo l'ambito condiviso. Tuttavia, per motivi di gestione di costi o budget dei costi è possibile allocare quantità a sottoscrizioni specifiche.

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

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

A seconda del tipo di prenotazione, potrebbe essere possibile annullare o scambiare una prenotazione. Per altre informazioni, vedere le sezioni relative all'annullamento e agli scambi negli argomenti seguenti:

- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Pagare in anticipo per i piani software SUSE dalle prenotazioni di Azure](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

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

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Quali sono le prenotazioni per Azure?](billing-save-compute-costs-reservations.md)

Acquista un piano di servizio:
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Pagare in anticipo le risorse di Azure Cosmos DB con capacità riservata di Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

Acquista un piano software:
- [Pagare in anticipo per i piani software Red Hat dalle prenotazioni di Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Pagare in anticipo per i piani software SUSE dalle prenotazioni di Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Comprendere lo sconto e utilizzo:
- [Informazioni su come viene applicato lo sconto sulla prenotazione di macchine virtuali](billing-understand-vm-reservation-charges.md)
- [Informazioni su come viene applicato lo sconto relativo piano software Red Hat Enterprise Linux](../billing/billing-understand-rhel-reservation-charges.md)
- [Informazioni su come viene applicato lo sconto relativo al piano software SUSE Linux Enterprise](../billing/billing-understand-suse-reservation-charges.md)
- [Informazioni su come vengono applicati gli altri sconti sulla prenotazione](billing-understand-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
