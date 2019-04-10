---
title: Pagare in anticipo vCore del database SQL di Azure per risparmiare | Microsoft Docs
description: Informazioni su come acquistare capacità di riserva del database SQL di Azure per risparmiare sui costi per i servizi di calcolo.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: ec9bd3ee106571484c513c2d005a374a90c1d17e
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359736"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Pagare in anticipo risorse di calcolo del database SQL con capacità di riserva del database SQL di Azure

Per usare il database SQL di Azure risparmiando, è possibile pagare in anticipo le risorse di calcolo anziché pagare in base al consumo. La capacità di riserva del database SQL di Azure consente di sottoscrivere un impegno anticipato per il database SQL per un periodo di uno o tre anni, allo scopo di ottenere uno sconto significativo sui costi di calcolo. Per acquistare capacità di riserva del database SQL, è necessario specificare l'area di Azure, il tipo di distribuzione, il livello di prestazioni e il periodo.

Non è necessario assegnare la prenotazione a istanze del database SQL specifiche (database singoli, pool elastici o istanze gestite). Il vantaggio verrà assegnato automaticamente alle istanze di database SQL corrispondenti, già in esecuzione o appena distribuite. Tramite l'acquisto di una prenotazione, si pagano in anticipo i costi di calcolo per un periodo di uno o tre anni. Non appena si acquista una prenotazione, i costi di calcolo del database SQL che corrispondono agli attributi della prenotazione stessa non vengono più addebitati in base alle tariffe relative al pagamento in base al consumo. Una prenotazione non copre i costi di software, rete o archiviazione associati all'istanza di database SQL. La scadenza del periodo di prenotazione comporta anche la scadenza del vantaggio sulla fatturazione. Dopo la scadenza, i database SQL vengono fatturati in base alla tariffa relativa al pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere l'[offerta di capacità di riserva del database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

È possibile acquistare capacità di riserva del database SQL di Azure nel [portale di Azure](https://portal.azure.com). Per acquistare capacità di riserva del database SQL:

- È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o con pagamento in base al consumo.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o di vendita possono acquistare capacità di riserva del database SQL.

Per informazioni dettagliate sul modo in cui gli acquisti di prenotazioni vengono addebitati ai clienti Enterprise e ai clienti con pagamento in base al consumo, vedere [Informazioni sull'utilizzo della prenotazione di Azure per l'iscrizione Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md) e [Informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Determinare la dimensione corretta del database SQL prima dell'acquisto

La dimensione della prenotazione deve basarsi sulla quantità totale di calcolo usata dai database singoli, dai pool elastici o dalle istanze gestite esistenti o di cui è prevista la distribuzione a breve all'interno di un'area specifica e deve essere calcolata usando lo stesso livello di prestazioni e hardware della stessa generazione.

Si supponga, ad esempio, che siano in esecuzione un pool elastico di quinta generazione a 16 vCore a utilizzo generico e due database singoli di quinta generazione a 4 vCore critici. Si supponga anche che entro un mese sia prevista la distribuzione di un altro pool elastico di quinta generazione a 16 vCore a utilizzo generico e di un pool elastico di quinta generazione a 32 vCore critico. Si supponga poi di sapere che queste risorse saranno necessarie per almeno 1 anno. In questo caso è necessario acquistare una prenotazione di 1 anno per 32 vCore (2x16) per database singolo/pool elastico di quinta generazione per utilizzo generico di calcolo e una prenotazione di 1 anno per 40 vCore (4x2+32) per database singolo SQL/pool elastico di quinta generazione Business Critical.

## <a name="buy-sql-database-reserved-capacity"></a>Acquistare capacità di riserva del database SQL

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Per acquistare una nuova prenotazione per il database SQL, selezionare **Aggiungi** e quindi nel riquadro Selezionare il tipo di prodotto selezionare **Database SQL**.
4. Compilare i campi obbligatori. Possono usufruire dello sconto per la capacità di riserva i database singoli, le istanze gestite o i pool elastici nuovi o esistenti che soddisfano gli attributi selezionati. Il numero di istanze di database SQL a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

   ![Screenshot subito prima dell'acquisto della capacità di riserva del database SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Campo      | DESCRIZIONE|
    |:------------|:--------------|
    |NOME        |Il nome della prenotazione.|
    |Sottoscrizione|Sottoscrizione usata per pagare la prenotazione della capacità di riserva del database SQL. L'acquisto della prenotazione di capacità di riserva del database SQL viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un Contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (numeri offerta: MS-AZR-0003P o MS-AZR-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.|
    |Scope       |L'ambito della prenotazione di vCore può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <br/><br/>**Sottoscrizione singola**: lo sconto per la prenotazione di vCore viene applicato alle istanze di database SQL in questa sottoscrizione. <br/><br/>**Sottoscrizione condivisa**: lo sconto sulla prenotazione di vCore viene applicato alle istanze di database SQL in esecuzione in tutte le sottoscrizioni nel contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.|
    |Region      |Area di Azure coperta dalla prenotazione di capacità di riserva del database SQL.|
    |Tipo di distribuzione|Tipo di risorsa di SQL per cui si vuole acquistare la prenotazione.|
    |Livello di prestazioni|Livello di servizio per le istanze di database SQL.
    |Termine        |Un anno o tre anni.|
    |Quantità    |Numero di istanze in corso di acquisto all'interno della prenotazione di capacità di riserva del database SQL. La quantità è il numero di istanze di database SQL in esecuzione che possono ottenere lo sconto sulla fatturazione. Se ad esempio si eseguono 10 istanze di database SQL negli Stati Uniti orientali, si specificherà 10 come quantità per ottimizzare lo sconto per tutti i computer in esecuzione. |
    |||

5. Rivedere il costo della prenotazione di capacità di riserva del database SQL nella sezione **Costi**.
6. Selezionare **Acquisto**.
7. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Se è necessario annullare la prenotazione della capacità di riserva del database SQL, potrebbe venire applicato un corrispettivo del 12% per l'interruzione anticipata. I rimborsi si basano sul prezzo più basso tra il prezzo di acquisto o il prezzo corrente della prenotazione. I rimborsi sono limitati a $50.000 dollari all'anno. Il rimborso ricevuto corrisponde al saldo ripartito rimanente meno la quota del 12% per l'interruzione anticipata. Per richiedere un annullamento, andare alla prenotazione nel portale di Azure e selezionare **Rimborso** per creare una richiesta di supporto.

Se è necessario modificare la prenotazione della capacità di riserva del database SQL scegliendo un'area, un tipo di distribuzione, un livello di prestazioni o un periodo diverso, è possibile effettuare uno scambio con un'altra prenotazione di valore uguale o superiore. Come data di inizio del periodo della nuova prenotazione non viene conservata quella della prenotazione scambiata. Il periodo di uno o tre anni inizia dal momento della creazione della nuova prenotazione. Per richiedere uno scambio, passare alla prenotazione nel portale di Azure e selezionare **Scambio** per creare una richiesta di supporto.

Per altre informazioni su come le prenotazioni di exchange o un rimborso, vedere [scambi di prenotazione e rimborsi](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="vcore-size-flexibility"></a>Flessibilità delle dimensioni vCore

La flessibilità delle dimensioni vCore consente di aumentare o ridurre la quantità di risorse all'interno di un livello di prestazioni e di un'area, senza perdere il vantaggio della capacità riservata. La capacità riservata del database SQL offre anche la possibilità di spostare temporaneamente i database ad accesso frequente tra pool e database singoli come parte delle normali operazioni (entro la stessa area e lo stesso livello di prestazioni) senza perdere il vantaggio della capacità riservata. Mantenendo un buffer di risorse non applicate nella prenotazione, è possibile gestire in modo efficace i picchi di prestazioni senza superare il budget.

## <a name="next-steps"></a>Passaggi successivi

Lo sconto sulla prenotazione di vCore si applica automaticamente al numero di istanze di database SQL corrispondenti all'ambito di prenotazione e agli attributi della capacità di riserva del database SQL. È possibile aggiornare l'ambito della prenotazione di capacità di riserva del database SQL nel [portale di Azure](https://portal.azure.com), in PowerShell, nell'interfaccia della riga di comando o tramite l'API.

Per informazioni su come gestire la prenotazione di capacità di riserva del database SQL, vedere [Gestire la capacità di riserva del database SQL](../billing/billing-manage-reserved-vm-instance.md).

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md)
- [Gestire le prenotazioni di Azure](../billing/billing-manage-reserved-vm-instance.md)
- [Comprendere che le prenotazioni di Azure di sconto](../billing/billing-understand-reservation-charges.md)
- [Comprendere l'utilizzo della prenotazione per la sottoscrizione con pagamento a consumo](../billing/billing-understand-reserved-instance-usage.md)
- [Comprendere l'utilizzo della prenotazione per l'iscrizione Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) di Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
