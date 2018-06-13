---
title: Gestire le istanze riservate di Azure - Fatturazione di Azure | Microsoft Docs
description: Informazioni su come è possibile modificare l'ambito della sottoscrizione e gestire l'accesso per le istanze di macchina virtuale riservate di Azure.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: fc473906be9c572e6d6549c85f9faa8fe7566b86
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303063"
---
# <a name="manage-reserved-instances"></a>Gestire le istanze riservate

Dopo avere acquistato un'istanza di macchina virtuale riservata di Azure potrebbe essere necessario applicare l'istanza riservata a una sottoscrizione diversa rispetto a quella specificata durante l'acquisto. In alternativa, se le macchine virtuali corrispondenti sono in esecuzione in più sottoscrizioni, potrebbe essere necessario modificare l'ambito dell'istanza riservata impostandola sull'ambito condiviso. Per ottimizzare lo sconto associato all'istanza riservata, assicurarsi che il numero di istanze acquistate corrisponda agli attributi e al numero di macchine virtuali in esecuzione. Per altre informazioni sulle istanze riservate di Azure, vedere [Risparmiare sui costi grazie al pagamento anticipato delle macchine virtuali di Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Modificare l'ambito per un'istanza riservata
 Lo sconto per l'istanza riservata si applica alle macchine virtuali che corrispondono all'istanza riservata e che vengono eseguite all'interno dell'ambito dell'istanza riservata. L'ambito di un'istanza riservata può essere una singola sottoscrizione o tutte le sottoscrizioni nel contesto di fatturazione. Se si imposta l'ambito su una singola sottoscrizione, l'istanza riservata corrisponde alle macchine virtuali in esecuzione nella sottoscrizione selezionata. Se si imposta l'ambito condiviso, Azure individua l'istanza riservata corrispondente relativa alle macchine virtuali in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Il contesto di fatturazione dipende dalla sottoscrizione usata per acquistare l'istanza riservata. Per altre informazioni, vedere [Pagare in anticipo le macchine virtuali tramite le istanze riservate](https://go.microsoft.com/fwlink/?linkid=861721).

Per aggiornare l'ambito di un'istanza riservata: 
1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare l'istanza riservata.
4. Selezionare **Impostazioni** > **Configurazione**.
5. Modificare l'ambito. Se si passa da un ambito condiviso a un ambito singolo, è possibile selezionare solo le sottoscrizioni di cui l'utente è proprietario. È possibile selezionare solo le sottoscrizioni all'interno dello stesso contesto di fatturazione dell'istanza riservata. Il contesto di fatturazione è determinato dalla sottoscrizione selezionata quando è stata acquistata l'istanza riservata. L'ambito è valido solo per le sottoscrizioni con pagamento in base al consumo MS-AZR-0003P e le sottoscrizioni di tipo Enterprise MS-AZR-0017P. Per i contratti Enterprise le sottoscrizioni di sviluppo/test non sono idonee per ottenere lo sconto associato all'istanza riservata.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Dividere una singola istanza riservata in due istanze riservate
 Dopo avere acquistato più di un'istanza, è possibile assegnare le istanze all'interno di un'istanza riservata a sottoscrizioni diverse. Per impostazione predefinita, tutte le istanze (quantità specificata durante l'acquisto) sono associate a un ambito (sottoscrizione singola o ambito condiviso). Si supponga, ad esempio, di avere acquistato 10 macchine virtuali D2 standard e di avere impostato l'ambito sulla sottoscrizione A. Si desidera in seguito modificare l'ambito per sette istanze riservate impostandolo sulla sottoscrizione A, mentre per le restanti 3 istanze riservate l'ambito viene impostato sulla sottoscrizione B. La divisione di un'istanza riservata consente quindi di distribuire le istanze per una gestione più granulare dell'ambito. È possibile semplificare il processo di allocazione alle sottoscrizioni scegliendo l'ambito condiviso. Tuttavia, per motivi di gestione di costi o budget dei costi è possibile allocare quantità a sottoscrizioni specifiche.

 È possibile dividere un'istanza riservata in due istanze riservate tramite PowerShell, l'interfaccia della riga di comando oppure l'API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Dividere un'istanza riservata tramite PowerShell
1. Ottenere l'ID ordine dell'istanza riservata eseguendo il comando seguente:

    ```powershell
    # Get the Reserved Instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Ottenere i dettagli di un'istanza riservata:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Dividere l'istanza riservata in due e distribuire le istanze:

    ```powershell
    # Split the Reserved Instance. The sum of the Reserved Instances, the quantity, must equal the total number of instances in the Reserved Instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. È possibile aggiornare l'ambito con il comando seguente:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Aggiungere o modificare gli utenti che possono gestire un'istanza riservata
È possibile delegare la gestione di un'istanza riservata mediante l'aggiunta di utenti ai ruoli per l'istanza riservata. Per impostazione predefinita, la persona che ha acquistato l'istanza riservata e l'amministratore dell'account dispongono del ruolo di proprietario per l'istanza riservata. 

È possibile gestire l'accesso alle istanze riservate indipendentemente dalle sottoscrizioni idonee per lo sconto associato all'istanza riservata. L'assegnazione dell'autorizzazione per la gestione di un'istanza riservata non comporta l'autorizzazione alla gestione della sottoscrizione. In modo analogo, l'assegnazione dell'autorizzazione per la gestione di una sottoscrizione all'interno dell'ambito di un'istanza riservata non comporta l'autorizzazione alla gestione dell'istanza riservata.
 
Per delegare la gestione dell'accesso per un'istanza riservata: 
1.  Accedere al [Portale di Azure](https://portal.azure.com).
2.  Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco di istanze riservate a cui è possibile accedere.
3.  Selezionare l'istanza riservata per la quale si vuole delegare l'accesso ad altri utenti.
4.  Selezionare **Controllo di accesso (IAM)** nel menu.
5.  Selezionare **Aggiungi** > **Ruolo** > **Proprietario** oppure un ruolo diverso se si desidera concedere un ruolo limitato. 
6. Digitare l'indirizzo di posta elettronica dell'utente che si vuole aggiungere come proprietario. 
7. Selezionare l'utente e quindi selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle istanze riservate di Azure, vedere gli articoli seguenti:

- [Risparmiare sui costi delle macchine virtuali tramite le istanze riservate di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze riservate](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Informazioni su come viene applicato lo sconto relativo alle istanze riservate](billing-understand-vm-reservation-charges.md)
- [Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle istanze riservate](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
