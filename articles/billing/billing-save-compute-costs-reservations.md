---
title: Risparmiare sui costi grazie al pagamento anticipato delle macchine virtuali di Azure - Azure | Microsoft Docs
description: Informazioni sulle istanze di macchina virtuale riservate di Azure per ridurre i costi delle macchine virtuali.
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 96e9cf2fed0b22fd7aa7b9ffeab0e94738ce510d
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Risparmiare sui costi delle macchine virtuali tramite le istanze di macchina virtuale riservate di Azure 
Le istanze di macchina virtuale riservate consentono il pagamento anticipato della capacità di calcolo per un intervallo di tempo di uno o tre anni e pertanto di ottenere uno sconto sulle macchine virtuali in uso. Ciò consente di ridurre in modo significativo i costi, ovvero fino al 72% rispetto ai prezzi con pagamento in base al consumo, con un impegno anticipato di uno o tre anni. Le istanze di macchina virtuale riservate rappresentano uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle macchine virtuali.

È possibile acquistare un'istanza di macchina virtuale riservata nel [portale di Azure](https://aka.ms/reservations). Per altre informazioni, vedere [Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze di macchina virtuale riservate](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>Perché è consigliabile acquistare un'istanza di macchina virtuale riservata?
Se sono presenti macchine virtuali in esecuzione per lunghi periodi di tempo, l'acquisto di un'istanza di macchina virtuale riservata offre il prezzo effettivo migliore. Ad esempio, se si eseguono in modo continuo quattro istanze di macchine virtuali D2 standard nell'area Stati Uniti occidentali, senza istanze di macchina virtuale riservate verranno applicate le tariffe in base al consumo. Se invece si acquista un'istanza di macchina virtuale riservata per le quattro macchine virtuali, si otterrà un immediato vantaggio a livello di fatturazione. Infatti non verranno più addebitate tariffe in base al consumo. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>Quali sono le spese coperte da un'istanza di macchina virtuale riservata?
Un'istanza di macchina virtuale riservata copre solo i costi dell'infrastruttura di macchine virtuali Windows o Linux e non i costi aggiuntivi relativi a software, rete o archiviazione. Per le macchine virtuali Windows è possibile coprire i costi delle licenze Windows tramite le offerte descritte nella pagina [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>Chi è idoneo per l'acquisto di un'istanza di macchina virtuale riservata?
Possono acquistare un'istanza di macchina virtuale riservata i clienti di Azure con i tipi di sottoscrizione seguenti:
-   Tipo di offerta di sottoscrizione Contratto Enterprise (MS-AZR-0017P).
-   Tipo di offerta di sottoscrizione [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P).
Per poter acquistare un'istanza riservata, l'utente deve avere il ruolo "Proprietario" nella sottoscrizione. Per l'acquisto di istanze riservate con un contratto Enterprise, l'amministratore Enterprise deve abilitare l'acquisto di istanze riservate nel portale EA. Questa impostazione è abilitata per impostazione predefinita.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>Come viene fatturato l'acquisto delle istanze di macchina virtuale riservate?
L'acquisto delle istanze di macchina virtuale riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se tale saldo non copre il costo delle istanze riservate, l'eccedenza verrà fatturata.
Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo verrà addebitato immediatamente sulla carta di credito associata all'account. Se invece l'addebito avviene tramite fattura, il costo risulterà visibile sulla fattura successiva.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>Come viene applicato lo sconto relativo alle istanze di macchina virtuale riservate acquistate?
Lo sconto relativo alle istanze di macchina virtuale riservate viene applicato alle macchine virtuali corrispondenti agli attributi selezionati in fase di acquisto. Gli attributi includono l'ambito in cui vengono eseguite le macchine virtuali corrispondenti. Ad esempio, se si desidera avvalersi dello sconto per istanze di macchina virtuale riservate per quattro macchine virtuali D2 standard nell'area Stati Uniti occidentali, selezionare la sottoscrizione in cui le macchine virtuali sono in esecuzione. Se le macchine virtuali sono in esecuzione in diverse sottoscrizioni all'interno della sottoscrizione o dell'account corrente, selezionare l'ambito condiviso. L'ambito condiviso consente l'applicazione dello sconto relativo all'acquisto di istanze riservate tra sottoscrizioni.
Dopo l'acquisto di un'istanza di macchina virtuale riservata è possibile modificare l'ambito. Per modificare l'ambito, vedere la documentazione su come gestire l'acquisto di istanze riservate.

Lo sconto sull'acquisto di istanze riservate viene applicato solo alle macchine virtuali con sottoscrizioni di tipo Enterprise o con pagamento in base al consumo. Le macchine virtuali in esecuzione in una sottoscrizione con altri tipi di offerta non ricevono alcun tipo di sconto. Per le iscrizioni Enterprise, le sottoscrizioni di tipo Sviluppo/test Enterprise non sono idonee per i vantaggi validi per le istanze riservate.

L'influenza dell'acquisto di istanze riservate sulla fatturazione delle macchine virtuali è descritta nell'argomento [Introduzione all'applicazione dei vantaggi della fatturazione dell'acquisto di istanze riservate](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reservation-term-expires"></a>Cosa accade alla scadenza del periodo di prenotazione?
La scadenza del periodo di prenotazione comporta la scadenza anche dello sconto sulla fatturazione. L'infrastruttura di macchine virtuali verrà pertanto fatturata in base a tariffe con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per continuare a ottenere lo sconto sulla fatturazione, è necessario acquistare una nuova istanza di macchina virtuale riservata. 

## <a name="sizes-and-regional-availability"></a>Dimensioni e disponibilità internazionale
Le prenotazioni sono disponibili per la maggior parte delle dimensioni delle macchine virtuali con alcune eccezioni:
- Dimensioni di macchine virtuali di anteprima: qualsiasi dimensione non disponibile in anteprima per l'acquisto di istanze di macchina virtuale riservate.
- Cloud: le istanze di macchina virtuale riservate non sono disponibili per l'acquisto nelle aree Azure Governo degli Stati Uniti, Germania o Cina. 
- Quota insufficiente: un'istanza di macchina virtuale riservata con ambito riferito a una singola sottoscrizione deve avere una quota di CPU virtuali disponibile nell'istanza riservata. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota pari a 10 CPU virtuali per la famiglia di VM serie D, non sarà possibile acquistare un'istanza di macchina virtuale riservata per 11 istanze D1 standard. Il controllo della quota per le prenotazioni include le macchine virtuali già distribuite nella sottoscrizione. Ad esempio, se la sottoscrizione ha una quota di 10 CPU virtuali per la famiglia di VM serie D e include due istanze di VM D1 standard, sarà possibile acquistare un'istanza di macchina virtuale riservata per le 10 istanze D1 standard incluse nella sottoscrizione. 
- Restrizioni della capacità: in situazioni rare Azure limita l'acquisto di nuove istanze di macchina virtuale riservate per subset di dimensioni di macchine virtuali a causa della bassa capacità in un'area specifica.

## <a name="next-steps"></a>Passaggi successivi
È possibile iniziare subito a risparmiare sui costi delle macchine virtuali con l'acquisto di un'[istanza di macchina virtuale riservata](https://go.microsoft.com/fwlink/?linkid=861721). 

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
