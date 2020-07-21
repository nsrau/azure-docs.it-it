---
title: Prezzi di calcolo riservati-database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: PrePay per database di Azure per PostgreSQL-risorse di calcolo con iperscalabilità (CITUS) con capacità riservata.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: a5ce99927ce4cd2b04b5dd5cb865299b4be84ecb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519797"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>PrePay per database di Azure per PostgreSQL-risorse di calcolo con iperscalabilità (CITUS) con capacità riservata

Database di Azure per PostgreSQL – iperscalabilità (CITUS) consente ora di risparmiare denaro prepagando le risorse di calcolo rispetto ai prezzi con pagamento in base al consumo. Con la capacità riservata iperscalabile (CITUS), si crea un impegno iniziale per il gruppo di server CITUS (overscale) per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare la capacità riservata CITUS (iperscale), è necessario specificare l'area di Azure, il periodo di prenotazione e la frequenza di fatturazione.

> [!IMPORTANT]
> Questo articolo descrive la capacità riservata per database di Azure per PostgreSQL – iperscalabilità (CITUS). Per informazioni sulla capacità riservata per database di Azure per PostgreSQL-server singolo, vedere [PrePay per database di Azure per PostgreSQL: risorse di calcolo a server singolo con capacità riservata](/azure/postgresql/concept-reserved-pricing).

Non è necessario assegnare la prenotazione a gruppi di server CITUS (overscale) specifici. Un gruppo di server con iperscalabilità (CITUS) già in esecuzione che viene appena distribuito automaticamente ottiene il vantaggio dei prezzi riservati. Acquistando una prenotazione, i costi di calcolo sono prepagati per un anno o tre anni. Non appena si acquista una prenotazione, i costi di calcolo con iperscalabilità (CITUS) corrispondenti agli attributi di prenotazione non vengono più addebitati in base alle tariffe a consumo. 

Una prenotazione non copre i prezzi di software, di rete o di archiviazione associati ai gruppi di server di iperscala (CITUS). Al termine del periodo di prenotazione, il vantaggio per la fatturazione scadrà e i gruppi di server di iperscala (CITUS) vengono fatturati in base al prezzo con pagamento in base al consumo. Le prenotazioni non vengono rinnovate. Per informazioni sui prezzi, vedere l' [offerta di capacità riservata database di Azure per PostgreSQL – iperscalabile (CITUS)](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

È possibile acquistare capacità riservata di iperscala (CITUS) nel [portale di Azure](https://portal.azure.com/). Usare [pagamenti anticipati o mensili](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations) per acquistare la prenotazione. Per acquistare la capacità riservata:

* È necessario avere il ruolo di proprietario per almeno un Enterprise Agreement (EA) o una sottoscrizione singola con tariffe con pagamento in base al consumo.
* Per Enterprise Agreement sottoscrizioni, è necessario abilitare l' **aggiunta di istanze riservate** nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore Enterprise Agreement nella sottoscrizione.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare la capacità riservata di iperscala (CITUS).

Per informazioni su come Enterprise Agreement clienti e clienti con pagamento in base al consumo vengono addebitati sugli acquisti di prenotazione, vedere:
- [Informazioni sull'utilizzo della prenotazione di Azure per la registrazione Enterprise Agreement](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
- [Informazioni sull'utilizzo delle prenotazioni di Azure per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Determinare le dimensioni corrette del gruppo di server prima dell'acquisto

Le dimensioni della prenotazione si basano sulla quantità totale di risorse di calcolo usate dal coordinatore esistente o presto da distribuire e dai nodi del ruolo di lavoro nei gruppi di server iperscalari (CITUS) all'interno di un'area specifica.

Si supponga, ad esempio, di eseguire un gruppo di server con iperscalabilità (CITUS) con 16 coordinatore vCore e tre nodi di lavoro da 8 vCore. Si supponga inoltre di pianificare la distribuzione nel prossimo mese di un gruppo di server CITUS (overscale) aggiuntivo con un coordinatore vCore di 32 e due nodi del ruolo di lavoro di 4 vCore. Supponiamo anche che siano necessarie per almeno un anno.

In questo caso, acquistare una prenotazione di un anno per:

* Totale 16 Vcore + 32 Vcore = 48 Vcore per i nodi coordinatore
* Totale 3 nodi x 8 Vcore + 2 nodi x 4 Vcore = 24 + 8 = 32 Vcore per i nodi del ruolo di lavoro

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Acquistare la capacità riservata di database di Azure per PostgreSQL

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Tutti i servizi** > **Prenotazioni**.
1. Selezionare **Aggiungi**. Nel riquadro **prenotazioni acquisti** selezionare database di **Azure per PostgreSQL** per acquistare una nuova prenotazione per i database PostgreSQL.
1. Selezionare il tipo di **calcolo iperscalabile (CITUS)** da acquistare e fare clic su **Seleziona**.
1. Esaminare la quantità per il tipo di calcolo selezionato nella scheda **prodotti** .
1. Passare alla scheda **Acquista + verifica** per completare l'acquisto.

Nella tabella seguente vengono descritti i campi obbligatori.

| Campo        | Descrizione                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription | Sottoscrizione usata per pagare la prenotazione di capacità riservata per database di Azure per PostgreSQL. Il metodo di pagamento per la sottoscrizione viene addebitato ai costi iniziali per la prenotazione della capacità riservata del database di Azure per PostgreSQL. Il tipo di sottoscrizione deve essere un Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto singolo con prezzi con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Per una sottoscrizione di Enterprise Agreement, gli addebiti vengono dedotti dal saldo dell'impegno monetario della registrazione o addebitati come eccedenze. Per una singola sottoscrizione con prezzi con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.                                                                                  |
| Scope        | L'ambito della prenotazione vCore può coprire una sottoscrizione o più sottoscrizioni (ambito condiviso). Se si seleziona **condiviso**, lo sconto per la prenotazione di vCore viene applicato ai gruppi di server di iperscalabilità (CITUS) in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per Enterprise Agreement clienti, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso è costituito da tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore account. Se si seleziona **sottoscrizione singola**, lo sconto per la prenotazione di vCore viene applicato ai gruppi di server di iperscala (CITUS) in questa sottoscrizione. Se si seleziona **gruppo di risorse singolo**, lo sconto per la prenotazione viene applicato ai gruppi di server di iperscala (CITUS) nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione. |
| Region       | Area di Azure coperta dalla prenotazione di capacità riservata per database di Azure per PostgreSQL – iperscala (CITUS).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Termine         | Un anno o tre anni.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Quantità     | Quantità di risorse di calcolo acquistate entro la prenotazione di capacità riservata iperscalabile (CITUS). In particolare, il numero di Coordinator o del nodo di lavoro Vcore nell'area di Azure selezionata che viene riservata e che otterrà lo sconto per la fatturazione. Se, ad esempio, si esegue (o si prevede di eseguire) gruppi di server iperscalare (CITUS) con la capacità di calcolo totale del nodo coordinatore 64 e del nodo del ruolo di lavoro 32 Vcore nell'area Stati Uniti orientali, specificare rispettivamente la quantità come 64 e 32 per i nodi Coordinator e Worker per ottimizzare i vantaggi di tutti i server.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Flessibilità delle dimensioni vCore

la flessibilità delle dimensioni vCore consente di aumentare o ridurre i nodi del coordinatore e del ruolo di lavoro all'interno di un'area, senza perdere il vantaggio della capacità riservata.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Lo sconto di prenotazione vCore viene applicato automaticamente al numero di gruppi di server di iperscala (CITUS) che corrispondono agli attributi e all'ambito di prenotazione della capacità riservata del database di Azure per PostgreSQL. È possibile aggiornare l'ambito della prenotazione di capacità riservata di database di Azure per PostgreSQL (CITUS) tramite la portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API.

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Che cosa sono le prenotazioni di Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Gestire le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Informazioni sullo sconto per la prenotazione di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Informazioni sull'utilizzo della prenotazione per la registrazione Enterprise Agreement](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Prenotazioni di Azure nel programma Cloud Solution Provider per partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
