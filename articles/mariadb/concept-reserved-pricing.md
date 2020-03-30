---
title: Pagamento anticipato per il calcolo con capacità riservata - Database di Azure per MariaDB
description: Pagamento anticipato per il database di Azure per le risorse di calcolo MariaDB con capacità riservata
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a6bd3cab41c69075f5170a8a3aad4f059d970e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159048"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Pagamento anticipato per il database di Azure per le risorse di calcolo MariaDB con capacità riservata

Database di Azure per MariaDB ora consente di risparmiare denaro con il pagamento anticipato per le risorse di calcolo rispetto ai prezzi con pagamento in base al costo. Con il database di Azure per la capacità riservata di MariaDB, si assume un impegno iniziale nei server MariaDB per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare il database di Azure per la capacità riservata Di MariaDB, è necessario specificare l'area di Azure, il tipo di distribuzione, il livello di prestazioni e il termine. </br>

Non è necessario assegnare la prenotazione a un database di Azure specifico per i server MariaDB.You do not need to assign the reservation to specific Azure Database for MariaDB servers. Un database di Azure già in esecuzione per MariaDB o quelli che sono appena distribuiti, otterrà automaticamente il vantaggio dei prezzi riservati. Tramite l'acquisto di una prenotazione, si pagano in anticipo i costi di calcolo per un periodo di uno o tre anni. Non appena si acquista una prenotazione, le spese di calcolo del database di Azure per MariaDB che corrispondono agli attributi di prenotazione non vengono più addebitate alle tariffe con pagamento in base al costo. Una prenotazione non copre i costi software, di rete o di archiviazione associati al server di database MariaDB. Alla fine del periodo di prenotazione, il benefit di fatturazione scade e il database di Azure per MariaDB viene fatturato al prezzo con pagamento in base all'acquisto. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere Il [Database di Azure per MariaDB offerta di capacità riservata](https://azure.microsoft.com/pricing/details/mariadb/). </br>

È possibile acquistare la capacità riservata di Database di Azure per MariaDB nel portale di [Azure.](https://portal.azure.com/) Per acquistare la capacità riservata:

* È necessario essere nel ruolo di proprietario per almeno un Enterprise o una singola sottoscrizione con tariffe con pagamento in base al costo.
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare il database di Azure per la capacità riservata di MariaDB.For Cloud Solution Provider (CSP) program, only the admin agents or sales agents can purchase Azure Database for MariaDB reserved capacity. </br>

I dettagli su come vengono addebitati gli acquisti di prenotazioni per i clienti aziendali e i clienti con pagamento in base al consumo, vedere [Comprendere l'utilizzo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) della prenotazione di Azure per la registrazione Enterprise e l'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al [consumo.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-server-size-before-purchase"></a>Determinare la dimensione corretta del server prima dell'acquisto

La dimensione della prenotazione deve essere basata sulla quantità totale di calcolo utilizzata dall'istanza di database esistente o di prossima distribuzione all'interno di un'area specifica e utilizzando lo stesso livello di prestazioni e generazione hardware.</br>

Si supponga, ad esempio, di eseguire uno scopo generale, Gen5 – 32 vCore MariaDB database e due memoria ottimizzata, Gen5 – 16 vCore MariaDB database. Inoltre, si suppone che si prevede di distribuire entro il mese successivo uno scopo generale aggiuntivo, Gen5 – 32 server di database vCore e una memoria ottimizzata, Gen5 – 16 server di database vCore. Supponiamo che tu sappia che queste risorse ti serviranno per almeno 1 anno. In questo caso, è necessario acquistare un 64 (2x32) vCores, 1 anno di prenotazione per singolo database generico - Gen5 e un 48 (2x16 - 16) vCore 1 prenotazione anno per la memoria del database singolo ottimizzato - Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Acquistare il database di Azure per la capacità riservata di MariaDBBuy Azure Database for MariaDB reserved capacity

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Selezionare Tutte le**prenotazioni** **dei servizi** > .
3.  Selezionare Aggiungi e quindi nel riquadro Prenotazioni acquisti selezionare Database di Azure per MariaDB per acquistare una nuova prenotazione per i database MariaDB.Select **Add** and then in the Purchase reservations pane, select **Azure Database for MariaDB** to purchase a new reservation for your MariaDB databases.
4.  Compilare i campi obbligatori. Database nuovi o esistenti che corrispondono agli attributi selezionati sono idonei per ottenere lo sconto sulla capacità riservata. Il numero effettivo del database di Azure per i server MariaDB che ottengono lo sconto dipende dall'ambito e dalla quantità selezionati.


![Panoramica dei prezzi riservati](media/concepts-reserved-pricing/mariadb-reserved-price.png)


Nella tabella seguente vengono descritti i campi obbligatori.

| Campo | Descrizione |
| :------------ | :------- |
| Subscription   | Sottoscrizione utilizzata per pagare il database di Azure per MariaDB riservato la prenotazione della capacità. Al metodo di pagamento nella sottoscrizione vengono addebitati i costi iniziali per il database di Azure per MariaDB prenotazione capacità riservata. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P) o un contratto individuale con prezzi con pagamento in base al consumo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per un abbonamento individuale con prezzi con pagamento in base al consumo, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura nell'abbonamento.
| Scope | L'ambito della prenotazione vCore può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: </br></br> **Condiviso**, lo sconto prenotazione vCore viene applicato al database di Azure per i server MariaDB in esecuzione in qualsiasi sottoscrizione all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</br></br> **Sottoscrizione singola,** lo sconto prenotazione vCore viene applicato al database di Azure per i server MariaDB in questa sottoscrizione. </br></br> Gruppo di **risorse singolo,** lo sconto sulla prenotazione viene applicato al database di Azure per i server MariaDB nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione.
| Region | Area di Azure coperta dal database di Azure per MariaDB riservato la prenotazione della capacità.
| Tipo di distribuzione | Tipo di risorsa Database di Azure per MariaDB per cui si vuole acquistare la prenotazione.
| Livello di prestazioni | Livello di servizio per il database di Azure per i server MariaDB.The service tier for the Azure Database for MariaDB servers.
| Termine | Un anno
| Quantità | Quantità di risorse di calcolo acquistate all'interno del database di Azure per MariaDB impegnata nella prenotazione della capacità. La quantità è un numero di vCore nell'area di Azure e nel livello prestazioni selezionati che vengono prenotati e ottengono lo sconto sulla fatturazione. Ad esempio, se si esegue o si prevede di eseguire un database di Azure per i server MariaDB con la capacità di calcolo totale di Gen5 16 vCore nell'area Stati Uniti orientali, è necessario specificare la quantità come 16 per ottimizzare il vantaggio per tutti i server.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Flessibilità delle dimensioni vCore

La flessibilità delle dimensioni vCore consente di aumentare o ridurre la quantità di risorse all'interno di un livello di prestazioni e di un'area, senza perdere il vantaggio della capacità riservata. 

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto .

## <a name="next-steps"></a>Passaggi successivi

Lo sconto sulla prenotazione vCore viene applicato automaticamente al numero di database di Azure per i server MariaDB che corrispondono al database di Azure per gli attributi e l'ambito di prenotazione della capacità riservata MariaDB. È possibile aggiornare l'ambito del database di Azure per la prenotazione della capacità riservata di MariaDB tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando o l'API. </br></br>
Per informazioni su come gestire la capacità riservata del database di Azure per MariaDB, vedere Gestire il database di Azure per la capacità riservata MariaDB.To learn how to manage the Azure Database for MariaDB reserved capacity, see manage Azure Database for MariaDB reserved capacity.

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Che cosa sono le prenotazioni](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)di Azure?
* [Gestire le prenotazioni di AzureManage Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Comprendere lo sconto sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)
