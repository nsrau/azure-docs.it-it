---
title: Gestire le prenotazioni di Azure | Microsoft Docs
description: Informazioni su come è possibile modificare l'ambito della sottoscrizione e gestire l'accesso per le prenotazioni di Azure.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashesvi
ms.openlocfilehash: d47c85d4197f45db50f1974b6faea270e6761237
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628573"
---
# <a name="manage-reservations-for-resources-in-azure"></a>Gestire le prenotazioni per le risorse in Azure

Dopo aver acquistato una prenotazione di Azure, potrebbe essere necessario applicare la prenotazione a una sottoscrizione diversa rispetto a quella specificata durante l'acquisto. In alternativa, se le macchine virtuali, i database SQL o altre risorse corrispondenti sono in esecuzione in più sottoscrizioni, potrebbe essere necessario modificare l'ambito della prenotazione impostandola sulla prenotazione condivisa. Per ottimizzare lo sconto associato alla prenotazione, assicurarsi che il numero di istanze acquistate corrisponda agli attributi e al numero di risorse in esecuzione. Per altre informazioni, vedere la documentazione sulle [prenotazioni di Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Modificare l'ambito di una prenotazione

 Lo sconto si applica alle macchine virtuali, ai database SQL o ad altre risorse che corrispondono alla prenotazione e vengono eseguiti all'interno dell'ambito di prenotazione. L'ambito di una prenotazione può fare riferimento a una singola sottoscrizione o a tutte le sottoscrizioni nel contesto di fatturazione. Se si imposta l'ambito su una singola sottoscrizione, la prenotazione corrisponde alle risorse in esecuzione nella sottoscrizione selezionata. Se si imposta l'ambito su un ambito condiviso, Azure individua la prenotazione corrispondente relativa alle risorse in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Il contesto di fatturazione dipende dalla sottoscrizione usata per acquistare la prenotazione.

Per aggiornare l'ambito di una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare la prenotazione.
4. Selezionare **Impostazioni** > **Configurazione**.
5. Modificare l'ambito. Se si passa da un ambito condiviso a un ambito singolo, è possibile selezionare solo le sottoscrizioni di cui l'utente è proprietario. È possibile selezionare solo le sottoscrizioni all'interno dello stesso contesto di fatturazione della prenotazione. Il contesto di fatturazione è determinato dalla sottoscrizione selezionata quando viene acquistata la prenotazione. L'ambito è valido solo per le sottoscrizioni con pagamento in base al consumo MS-AZR-0003P e le sottoscrizioni di tipo Enterprise MS-AZR-0017P. Per i contratti Enterprise le sottoscrizioni di sviluppo/test non sono idonee per ottenere lo sconto associato alla prenotazione.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Aggiungere o modificare gli utenti che possono gestire una prenotazione

È possibile delegare la gestione di una prenotazione mediante l'aggiunta di utenti ai ruoli nella prenotazione. Per impostazione predefinita, la persona che ha acquistato la prenotazione e l'amministratore dell'account dispongono del ruolo di proprietario per la prenotazione.

È possibile gestire l'accesso alle prenotazioni indipendentemente dalle sottoscrizioni idonee per lo sconto associato alla prenotazione. L'assegnazione dell'autorizzazione per la gestione di una prenotazione non comporta l'autorizzazione alla gestione della sottoscrizione. In modo analogo, l'assegnazione dell'autorizzazione per la gestione di una sottoscrizione all'interno dell'ambito di una prenotazione non comporta l'autorizzazione alla gestione della prenotazione.

Per delegare la gestione dell'accesso per una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco delle prenotazioni a cui è possibile accedere.
3. Selezionare la prenotazione per la quale si desidera delegare l'accesso ad altri utenti.
4. Selezionare **Controllo di accesso (IAM)** nel menu.
5. Selezionare **Aggiungi** > **Ruolo** > **Proprietario** oppure un ruolo diverso se si desidera concedere un ruolo limitato.
6. Digitare l'indirizzo di posta elettronica dell'utente che si vuole aggiungere come proprietario. 
7. Selezionare l'utente e quindi selezionare **Salva**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Ottimizzare l'istanza di macchina virtuale riservata per la priorità di capacità o la flessibilità delle dimensioni della macchina virtuale

 La flessibilità dell'istanza di macchina virtuale applicherà lo sconto sulla prenotazione ad altre macchine virtuali dello stesso [gruppo di macchine virtuali](https://aka.ms/RIVMGroups). Per impostazione predefinita, quando l'ambito della prenotazione è condiviso, la flessibilità delle dimensioni istanza è attiva e la capacità del data center non viene classificata in ordine di priorità per le distribuzioni di macchine virtuali. Per le prenotazioni con ambito singolo, è possibile ottimizzare la prenotazione per la priorità di capacità anziché per la flessibilità delle dimensioni istanza di macchina virtuale. La priorità di capacità prenota la capacità del data center per le distribuzioni, per garantire la possibilità di avviare le istanze di macchina virtuale quando servono.

Per aggiornare l'ambito di una prenotazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare la prenotazione.
4. Selezionare **Impostazioni** > **Configurazione**.
5. Modificare l'impostazione Ottimizza per.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividere una prenotazione singola in due prenotazioni

 Dopo aver acquistato più di un'istanza, è possibile assegnare le istanze all'interno di una prenotazione a sottoscrizioni diverse. Per impostazione predefinita, tutte le istanze (quantità specificata durante l'acquisto) sono associate a un ambito (sottoscrizione singola o ambito condiviso). Si supponga, ad esempio, di aver acquistato 10 macchine virtuali Standard D2 e di avere impostato l'ambito sulla sottoscrizione A. Si desidera in seguito modificare l'ambito per sette prenotazioni sulla sottoscrizione A, mentre per le restanti 3 macchine virtuali l'ambito viene impostato sulla sottoscrizione B. La divisione di una prenotazione consente pertanto di distribuire le istanze per una gestione più granulare dell'ambito. È possibile semplificare il processo di allocazione alle sottoscrizioni scegliendo l'ambito condiviso. Tuttavia, per motivi di gestione di costi o budget dei costi è possibile allocare quantità a sottoscrizioni specifiche.

 È possibile dividere una prenotazione in due prenotazioni tramite PowerShell, l'interfaccia della riga di comando oppure l'API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividere una prenotazione tramite PowerShell

1. Ottenere l'ID dell'ordine di prenotazione eseguendo il comando seguente:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Ottenere i dettagli di una prenotazione:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Dividere la prenotazione in due e distribuire le istanze:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. È possibile aggiornare l'ambito con il comando seguente:
    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Cosa sono le prenotazioni di Azure?](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Informazioni su come viene applicato lo sconto sulla prenotazione](billing-understand-vm-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
