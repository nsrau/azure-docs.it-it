---
title: Prezzi di calcolo riservati-database di Azure per PostgreSQL-server singolo
description: Pagamento anticipato per le risorse di calcolo per database di Azure per PostgreSQL con capacità riservata
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 634ab645ea49c29bb5624afd82e549dfbc8d0fdf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769898"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>Pagamento anticipato per le risorse di calcolo per database di Azure per PostgreSQL con capacità riservata

Database di Azure per PostgreSQL consente ora di risparmiare denaro prepagando le risorse di calcolo rispetto ai prezzi con pagamento in base al consumo. Con la capacità riservata di database di Azure per PostgreSQL, si crea un impegno iniziale sul server PostgreSQL per un periodo di un anno per ottenere uno sconto significativo sui costi di calcolo. Per acquistare la capacità riservata di database di Azure per PostgreSQL, è necessario specificare l'area di Azure, il tipo di distribuzione, il livello di prestazioni e il termine. </br>

Non è necessario assegnare la prenotazione a specifici server di database di Azure per PostgreSQL. Un database di Azure già in esecuzione per PostgreSQL o uno appena distribuito, otterrà automaticamente il vantaggio dei prezzi riservati. Acquistando una prenotazione, si paga il pagamento anticipato dei costi di calcolo per un periodo di un anno. Non appena si acquista una prenotazione, i costi di calcolo per database di Azure per PostgreSQL che corrispondono agli attributi di prenotazione non vengono più addebitati in base alle tariffe a consumo. Una prenotazione non copre il software, la rete o gli addebiti di archiviazione associati ai server del database PostgreSQL. Al termine del periodo di prenotazione, il vantaggio di fatturazione scadrà e il database di Azure per PostgreSQL verrà fatturato in base al prezzo con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere l' [offerta di capacità riservata per database di Azure per PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> I prezzi per la capacità riservata sono disponibili solo per la distribuzione a [server singolo](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) di database di Azure per PostgreSQL e non per la distribuzione di [CITUS con iperscalabilità](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) .

È possibile acquistare la capacità riservata di database di Azure per PostgreSQL nel [portale di Azure](https://portal.azure.com/). Per acquistare la capacità riservata:

* È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare la capacità riservata di database di Azure per PostgreSQL. </br>

Per informazioni dettagliate sul modo in cui gli acquisti di prenotazioni vengono addebitati ai clienti Enterprise e ai clienti con pagamento in base al consumo, vedere [Informazioni sull'utilizzo della prenotazione di Azure per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e [Informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-server-size-before-purchase"></a>Determinare le dimensioni del server corrette prima dell'acquisto

Le dimensioni della prenotazione devono essere basate sulla quantità totale di risorse di calcolo usate dai server esistenti o presto distribuiti in un'area specifica e usando lo stesso livello di prestazioni e la stessa generazione di hardware.</br>

Si supponga, ad esempio, di eseguire un database PostgreSQL di uso generico, quinta generazione – 32 vCore e due database PostgreSQL con ottimizzazione per la memoria, quinta generazione-16 vCore. Si supponga, inoltre, di pianificare la distribuzione entro il prossimo mese con un server di database vCore, quinta generazione – 32 e un server di database con ottimizzazione per la memoria, quinta generazione – 16 vCore. Supponiamo che tu sappia che ti serviranno queste risorse per almeno un anno. In questo caso, è necessario acquistare una prenotazione di 64 (2x32) Vcore, 1 anno per utilizzo generico per database singolo-quinta generazione e una prenotazione 48 (2x16 + 16) vCore 1 anno per l'ottimizzazione della memoria per database singolo-quinta generazione


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Acquistare la capacità riservata di database di Azure per PostgreSQL

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** e quindi nel riquadro prenotazioni acquisti selezionare database di **Azure per PostgreSQL** per acquistare una nuova prenotazione per i database PostgreSQL.
4. Compilare i campi obbligatori. I database nuovi o esistenti che corrispondono agli attributi selezionati sono idonei per ottenere lo sconto per la capacità riservata. Il numero effettivo dei server di database di Azure per PostgreSQL che ottengono lo sconto dipende dall'ambito e dalla quantità selezionati.


![Panoramica dei prezzi riservati](media/concepts-reserved-pricing/postgresql-reserved-price.png)


Nella tabella seguente vengono descritti i campi obbligatori.

| Campo | Description |
| :------------ | :------- |
| Sottoscrizione   | Sottoscrizione usata per pagare la prenotazione di capacità riservata per database di Azure per PostgreSQL. Il metodo di pagamento per la sottoscrizione viene addebitato ai costi iniziali per la prenotazione della capacità riservata del database di Azure per PostgreSQL. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto singolo con prezzi con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per una singola sottoscrizione con prezzi con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.
| Scope | L'ambito della prenotazione di vCore può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: </br></br> **Condiviso**, lo sconto di prenotazione vCore viene applicato al database di Azure per i server PostgreSQL in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</br></br> **Sottoscrizione singola**, lo sconto di prenotazione vCore viene applicato al database di Azure per i server PostgreSQL in questa sottoscrizione. </br></br> **Gruppo di risorse singolo**, lo sconto relativo alla prenotazione viene applicato ai server del database di Azure per PostgreSQL nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione.
| Area geografica | Area di Azure coperta dalla prenotazione di capacità riservata del database di Azure per PostgreSQL.
| Tipo di distribuzione | Il tipo di risorsa database di Azure per PostgreSQL per cui si vuole acquistare la prenotazione.
| Livello di prestazioni | Livello di servizio per il database di Azure per i server PostgreSQL.
| Durata | Un anno
| Quantità | Quantità di risorse di calcolo acquistate nella riserva di capacità riservata del database di Azure per PostgreSQL. La quantità è un numero di Vcore nell'area di Azure selezionata e il livello di prestazioni che vengono riservati e otterranno lo sconto per la fatturazione. Ad esempio, se si esegue o si prevede di eseguire un database di Azure per i server PostgreSQL con la capacità di calcolo totale di Quinta generazione 16 Vcore nell'area Stati Uniti orientali, è necessario specificare Quantity come 16 per ottimizzare il vantaggio per tutti i server.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Cambi self-service e rimborsi per le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Flessibilità delle dimensioni vCore

La flessibilità delle dimensioni vCore consente di aumentare o ridurre la quantità di risorse all'interno di un livello di prestazioni e di un'area, senza perdere il vantaggio della capacità riservata. 

## <a name="need-help-contact-us"></a>Opzioni per Contattaci

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Lo sconto per la prenotazione di vCore viene applicato automaticamente al numero di server di database di Azure per PostgreSQL che corrispondono agli attributi e all'ambito di prenotazione della capacità riservata del database di Azure per PostgreSQL. È possibile aggiornare l'ambito della prenotazione di capacità riservata del database di Azure per PostgreSQL tramite portale di Azure, PowerShell, l'interfaccia della riga di comando o tramite l'API. </br></br>
Per informazioni su come gestire la capacità riservata del database di Azure per PostgreSQL, vedere gestire la capacità riservata di database di Azure per PostgreSQL.

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Che cosa sono le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Gestire le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Comprendere lo sconto sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)
