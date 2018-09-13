---
title: Pagare in anticipo vCore del database SQL di Azure per risparmiare | Microsoft Docs
description: Informazioni su come acquistare capacità di riserva del database SQL di Azure per risparmiare sui costi per i servizi di calcolo.
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: fcb7f2c1bb3e1653b9f8112abc0effaddc45fa54
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306470"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Pagare in anticipo risorse di calcolo del database SQL con capacità di riserva del database SQL di Azure

Per usare il database SQL di Azure risparmiando, è possibile pagare in anticipo le risorse di calcolo del database SQL di Azure anziché pagare in base al consumo. La capacità di riserva del database SQL di Azure consente di sottoscrivere un impegno anticipato per il database SQL per un periodo di uno o tre anni, allo scopo di ottenere uno sconto significativo sui costi di calcolo. Per acquistare capacità di riserva del database SQL, è necessario specificare l'area di Azure, il tipo di distribuzione, il servizio e il termine. 

Non è necessario assegnare la prenotazione a istanze di database SQL specifiche. Il vantaggio verrà assegnato automaticamente alle istanze di database SQL corrispondenti, già in esecuzione o appena distribuite. Tramite l'acquisto di una prenotazione, si pagano in anticipo i costi di calcolo per le istanze di database SQL per un periodo di uno o tre anni. Non appena si acquista una prenotazione, i costi di calcolo del database SQL che corrispondono agli attributi della prenotazione stessa non vengono più addebitati in base alle tariffe relative al pagamento in base al consumo. Una prenotazione non copre i costi di software, rete o archiviazione associati all'istanza di database SQL. La scadenza del periodo di prenotazione comporta anche la scadenza del vantaggio sulla fatturazione. Dopo la scadenza, i database SQL vengono fatturati in base alla tariffa relativa al pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere l'[offerta di capacità di riserva del database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

È possibile acquistare capacità di riserva del database SQL di Azure nel [portale di Azure](https://portal.azure.com). Per acquistare capacità di riserva del database SQL:
- È necessario avere il ruolo di Proprietario per almeno una sottoscrizione Enterprise o con pagamento in base al consumo.
- Per le sottoscrizioni Enterprise, gli acquisti di prenotazioni di Azure devono essere abilitati nel [portale EA](https://ea.azure.com).
-  Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o di vendita possono acquistare capacità di riserva del database SQL.

I dettagli sul modo in cui gli acquisti di prenotazioni vengono addebitati ai clienti aziendali e ai clienti con pagamento in base al consumo è descritto in [Domande frequenti](#frequently-asked-questions).

## <a name="determine-the-right-sql-size-before-purchase"></a>Determinare la dimensione corretta del database SQL prima dell'acquisto

La dimensione della prenotazione deve basarsi sulla quantità totale di calcolo usata dai singoli database SQL e/o dai pool elastici esistenti o di cui è prevista la distribuzione a breve all'interno di un'area specifica e deve essere calcolata usando lo stesso livello di prestazioni e hardware della stessa generazione. 

Si supponga, ad esempio, che siano in esecuzione un pool elastico di quinta generazione a 16 vCore a utilizzo generico e due database singoli di quinta generazione a 4 vCore critici. Si supponga anche che entro un mese sia prevista la distribuzione di un altro pool elastico di quinta generazione a 16 vCore a utilizzo generico e di un pool elastico di quinta generazione a 32 vCore critico. Si supponga poi di sapere che queste risorse saranno necessarie per almeno 1 anno. In questo caso è necessario acquistare una prenotazione di 1 anno per 32 vCore (2x16) per database SQL singolo/pool elastico di quinta generazione per utilizzo generico di calcolo e una prenotazione di 1 anno per 40 vCore (4x2+32) per database singolo SQL/pool elastico di quinta generazione di calcolo critico.

## <a name="buy-sql-database-reserved-capacity"></a>Acquistare capacità di riserva del database SQL

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Per acquistare una nuova prenotazione per il database SQL, selezionare **Aggiungi** e quindi nel riquadro Selezionare il tipo di prodotto selezionare **Database SQL**.
4. Compilare i campi obbligatori. Sono qualificati per lo sconto correlato alla capacità di riserva i database singoli e i pool elastici nuovi o esistenti che corrispondano agli attributi selezionati. Il numero di istanze di database SQL a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

   ![Screenshot subito prima dell'acquisto della capacità di riserva del database SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Campo      | DESCRIZIONE|
    |:------------|:--------------|
    |NOME        |Il nome della prenotazione.| 
    |Sottoscrizione|Sottoscrizione usata per pagare la prenotazione della capacità di riserva del database SQL. L'acquisto della prenotazione di capacità di riserva del database SQL viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta: MS-AZR-0017P) o con pagamento in base al consumo (numero offerta: MS-AZR-0003P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.|    
    |Scope       |L'ambito della prenotazione di vCore può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>Sottoscrizione singola: lo sconto relativo sulla prenotazione di vCore viene applicato alle istanze di database SQL in questa sottoscrizione. </li><li>Condivisa: lo sconto sulla prenotazione di vCore viene applicato alle istanze di database SQL in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni (eccetto le sottoscrizioni di sviluppo/test) all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</li></ul>|
    |Region      |Area di Azure coperta dalla prenotazione di capacità di riserva del database SQL.|    
    |Tipo di distribuzione|Tipo di distribuzione di SQL per cui si vuole acquistare la prenotazione.|
    |Livello di servizio|Livello di servizio per le istanze di database SQL.
    |Termine        |Un anno o tre anni.|
    |Quantità    |Numero di istanze in corso di acquisto all'interno della prenotazione di capacità di riserva del database SQL. La quantità è il numero di istanze di database SQL in esecuzione che possono ottenere lo sconto sulla fatturazione. Se ad esempio si eseguono 10 istanze di database SQL negli Stati Uniti orientali, si specificherà 10 come quantità per ottimizzare lo sconto per tutti i computer in esecuzione. |

5. Rivedere il costo della prenotazione di capacità di riserva del database SQL nella sezione **Costi**.
6. Selezionare **Acquisto**.
7. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

## <a name="next-steps"></a>Passaggi successivi 
Lo sconto sulla prenotazione di vCore si applica automaticamente al numero di istanze di database SQL corrispondenti all'ambito di prenotazione e agli attributi della capacità di riserva del database SQL. È possibile aggiornare l'ambito della prenotazione di capacità di riserva del database SQL nel [portale di Azure](https://portal.azure.com), in PowerShell, nell'interfaccia della riga di comando o tramite l'API. 

Per informazioni su come gestire la prenotazione di capacità di riserva del database SQL, vedere [Gestire la capacità di riserva del database SQL](../billing/billing-manage-reserved-vm-instance.md).

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md)
- [Gestire le prenotazioni di Azure](../billing/billing-manage-reserved-vm-instance.md)
- [Comprendere lo sconto sulle prenotazioni di Azure](../billing/billing-understand-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../billing/billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

