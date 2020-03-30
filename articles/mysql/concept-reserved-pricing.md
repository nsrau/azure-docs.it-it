---
title: Pagamento anticipato per il calcolo con capacità riservata - Database di Azure per MySQL
description: Pagamento preliminare per il database di Azure per le risorse di calcolo MySQL con capacità riservata
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 78c8750de7189bad33e9bbc766a3d7543a646f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159354"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Pagamento preliminare per il database di Azure per le risorse di calcolo MySQL con capacità riservata

Database di Azure per MySQL ora consente di risparmiare denaro con il pagamento anticipato per le risorse di calcolo rispetto ai prezzi con pagamento in base al costo. Con il database di Azure per la capacità riservata MySQL, si assume un impegno iniziale nel server MySQL per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare il database di Azure per la capacità riservata MySQL, è necessario specificare l'area di Azure, il tipo di distribuzione, il livello di prestazioni e il termine. </br>

Non è necessario assegnare la prenotazione a un database di Azure specifico per i server MySQL.You do not need to assign the reservation to specific Azure Database for MySQL servers. Un database di Azure già in esecuzione per MySQL o quelli che sono appena distribuiti, otterrà automaticamente il vantaggio dei prezzi riservati. Tramite l'acquisto di una prenotazione, si pagano in anticipo i costi di calcolo per un periodo di uno o tre anni. Non appena si acquista una prenotazione, il database di Azure per gli addebiti di calcolo MySQL che corrispondono agli attributi di prenotazione non vengono più addebitati alle tariffe con pagamento in base al costo. Una prenotazione non copre i costi software, di rete o di archiviazione associati al server di database MySQL. Alla fine del periodo di prenotazione, il benefit di fatturazione scade e il database di Azure per MySQL viene fatturato al prezzo con pagamento in base all'acquisto. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere Il [Database di Azure per l'offerta di capacità riservata MySQL.](https://azure.microsoft.com/pricing/details/mysql/) </br>

È possibile acquistare Database di Azure per la capacità riservata MySQL nel portale di Azure.You can buy Azure Database for MySQL reserved capacity in the [Azure portal](https://portal.azure.com/). Per acquistare la capacità riservata:

* È necessario essere nel ruolo di proprietario per almeno un Enterprise o una singola sottoscrizione con tariffe con pagamento in base al costo.
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare Database di Azure per la capacità riservata MySQL.For Cloud Solution Provider (CSP) program, only the admin agents or sales agents can purchase Azure Database for MySQL reserved capacity. </br>

I dettagli su come vengono addebitati gli acquisti di prenotazioni per i clienti aziendali e i clienti con pagamento in base al consumo, vedere [Comprendere l'utilizzo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) della prenotazione di Azure per la registrazione Enterprise e l'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al [consumo.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-database-size-before-purchase"></a>Determinare le dimensioni corrette del database prima dell'acquisto

La dimensione della prenotazione deve essere basata sulla quantità totale di elaborazione utilizzata dal server esistente o da distribuire in fretta all'interno di un'area specifica e utilizzando lo stesso livello di prestazioni e generazione hardware.</br>

Si supponga, ad esempio, di eseguire uno scopo generale, Gen5 – 32 vCore database MySQL e due database mySQL ottimizzato per la memoria, Gen5 – 16 vCore MySQL. Inoltre, si suppone che si prevede di distribuire entro il mese successivo uno scopo generale aggiuntivo, Gen5 – 32 server di database vCore e una memoria ottimizzata, Gen5 – 16 server di database vCore. Supponiamo che tu sappia che queste risorse ti serviranno per almeno 1 anno. In questo caso, è necessario acquistare un 64 (2x32) vCores, 1 anno di prenotazione per singolo database generico - Gen5 e un 48 (2x16 - 16) vCore 1 prenotazione anno per la memoria del database singolo ottimizzato - Gen5


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Acquista database di Azure per la capacità riservata MySQL

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Selezionare Tutte le**prenotazioni** **dei servizi** > .
3. Selezionare Aggiungi e quindi nel riquadro Prenotazioni acquisti selezionare Database di Azure per MySQL per acquistare una nuova prenotazione per i database MySQL.Select **Add** and then in the Purchase reservations pane, select **Azure Database for MySQL** to purchase a new reservation for your MySQL databases.
4. Compilare i campi obbligatori. Database nuovi o esistenti che corrispondono agli attributi selezionati sono idonei per ottenere lo sconto sulla capacità riservata. Il numero effettivo del database di Azure per i server MySQL che ottengono lo sconto dipende dall'ambito e dalla quantità selezionati.


![Panoramica dei prezzi riservati](media/concepts-reserved-pricing/mysql-reserved-price.png)


Nella tabella seguente vengono descritti i campi obbligatori.

| Campo | Descrizione |
| :------------ | :------- |
| Subscription   | Sottoscrizione utilizzata per pagare il database di Azure per la prenotazione della capacità riservata MySQL.The subscription used to pay for the Azure Database for MySQL reserved capacity reservation. Al metodo di pagamento nella sottoscrizione vengono addebitati i costi iniziali per il database di Azure per la prenotazione della capacità riservata MySQL.The payment method on the subscription is charged the upfront costs for the Azure Database for MySQL reserved capacity reservation. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P) o un contratto individuale con prezzi con pagamento in base al consumo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per un abbonamento individuale con prezzi con pagamento in base al consumo, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura nell'abbonamento.
| Scope | L'ambito della prenotazione vCore può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: </br></br> **Condiviso**, lo sconto prenotazione vCore viene applicato al database di Azure per i server MySQL in esecuzione in qualsiasi sottoscrizione all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</br></br> **Sottoscrizione singola,** lo sconto prenotazione vCore viene applicato al database di Azure per i server MySQL in questa sottoscrizione. </br></br> **Singolo gruppo di risorse,** lo sconto sulla prenotazione viene applicato al database di Azure per i server MySQL nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione.
| Region | Area di Azure coperta dal database di Azure per la prenotazione della capacità riservata di MySQL.The Azure region that's covered by the Azure Database for MySQL reserved capacity reservation.
| Tipo di distribuzione | Il database di Azure per il tipo di risorsa MySQL per cui si vuole acquistare la prenotazione.
| Livello di prestazioni | Livello di servizio per il database di Azure per i server MySQL.The service tier for the Azure Database for MySQL servers.
| Termine | Un anno
| Quantità | Quantità di risorse di calcolo acquistate all'interno del database di Azure per la prenotazione della capacità riservata MySQL. La quantità è un numero di vCore nell'area di Azure e nel livello prestazioni selezionati che vengono prenotati e ottengono lo sconto sulla fatturazione. Ad esempio, se si esegue o si prevede di eseguire un database di Azure per i server MySQL con la capacità di calcolo totale di Gen5 16 vCore nell'area Stati Uniti orientali, è necessario specificare la quantità come 16 per ottimizzare il vantaggio per tutti i server.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Flessibilità delle dimensioni vCore

La flessibilità delle dimensioni vCore consente di aumentare o ridurre la quantità di risorse all'interno di un livello di prestazioni e di un'area, senza perdere il vantaggio della capacità riservata. 

## <a name="need-help--contact-us"></a>Hai bisogno di aiuto ? Contatti

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto .

## <a name="next-steps"></a>Passaggi successivi

Lo sconto sulla prenotazione vCore viene applicato automaticamente al numero di database di Azure per i server MySQL che corrispondono al database di Azure per gli attributi e l'ambito di prenotazione della capacità riservata MySQL.The vCore reservation discount is applied automatically to the number of Azure Database for MySQL servers that match the Azure Database for MySQL reserved capacity reservation scope and attributes. È possibile aggiornare l'ambito del database di Azure per la prenotazione della capacità riservata MySQL tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando o l'API. </br></br>
Per informazioni su come gestire il database di Azure per la capacità riservata MySQL, vedere Gestire il database di Azure per la capacità riservata MySQL.To learn how to manage the Azure Database for MySQL reserved capacity, see manage Azure Database for MySQL reserved capacity.

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Che cosa sono le prenotazioni](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)di Azure?
* [Gestire le prenotazioni di AzureManage Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Comprendere lo sconto sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

