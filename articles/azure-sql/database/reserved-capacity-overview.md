---
title: Salva i costi di calcolo con la capacità riservata
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Scopri come acquistare il database SQL di Azure e la capacità riservata di SQL Istanza gestita per risparmiare sui costi di calcolo.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 81e3547dbd86c840baed8e044a84afd3b63f5be5
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86075774"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Risparmiare sui costi per le risorse con capacità riservata: database SQL di Azure & SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Risparmia denaro con il database SQL di Azure e con SQL Istanza gestita eseguendo il commit di una prenotazione per le risorse di calcolo rispetto ai prezzi con pagamento in base al consumo. Con la capacità riservata, si impegna per il database SQL e/o SQL Istanza gestita usare per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare la capacità riservata, è necessario specificare l'area di Azure, il tipo di distribuzione, il livello di prestazioni e il termine.

Non è necessario assegnare la prenotazione a un database specifico o a un'istanza gestita. Le distribuzioni esistenti che sono già in esecuzione o quelle appena distribuite vengono ricavate automaticamente. Con l'acquisto di una prenotazione, viene eseguito il commit dell'utilizzo per i costi di calcolo per un periodo di uno o tre anni. Non appena si acquista una prenotazione, i costi di calcolo che corrispondono agli attributi di prenotazione non vengono più addebitati in base alle tariffe con pagamento in base al consumo. Una prenotazione non copre il software, la rete o gli addebiti di archiviazione associati al servizio. Al termine del periodo di prenotazione, il vantaggio di fatturazione scadrà e il database o l'istanza gestita verrà fatturato in base al prezzo con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere l' [offerta di capacità riservata](https://azure.microsoft.com/pricing/details/sql-database/managed/).

È possibile acquistare capacità riservata nell' [portale di Azure](https://portal.azure.com). Usare [pagamenti anticipati o mensili](../../cost-management-billing/reservations/prepare-buy-reservation.md) per acquistare la prenotazione. Per acquistare capacità riservata:

- È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise. Capacità riservata.

Per altre informazioni su come i clienti aziendali e i clienti con pagamento in base al consumo vengono addebitati per gli acquisti di prenotazione, vedere [informazioni sull'utilizzo della prenotazione di Azure per la registrazione Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-correct-size-before-purchase"></a>Determinare le dimensioni corrette prima dell'acquisto

Le dimensioni della prenotazione devono essere basate sulla quantità totale di risorse di calcolo usate dal database esistente o presto distribuito o da un'istanza gestita in un'area specifica e usando lo stesso livello di prestazioni e la stessa generazione dell'hardware.

Si supponga, ad esempio, di eseguire un pool elastico per utilizzo generico, quinta generazione – 16 vCore e due database singoli quinta generazione – 4 vCore business critical. Si supponga, inoltre, di pianificare la distribuzione nel prossimo mese per un pool elastico quinta generazione – 16 vCore aggiuntivo per utilizzo generico e un pool elastico quinta generazione-32 vCore critico per il business. Si supponga poi di sapere che queste risorse saranno necessarie per almeno 1 anno. In questo caso, è necessario acquistare una prenotazione 32 (2x16) vcore di 1 anno per il database singolo o il pool elastico per utilizzo generico-quinta generazione e una prenotazione 40 (4 + 32) vCore di 1 anno per un singolo database o un pool elastico critico per il business-quinta generazione.

## <a name="buy-reserved-capacity"></a>Acquistare la capacità riservata

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** e quindi nel riquadro **prenotazioni acquisti** Selezionare **database SQL** per acquistare una nuova prenotazione per il database SQL.
4. Compilare i campi obbligatori. I database esistenti nel database SQL e Istanza gestita SQL che corrispondono agli attributi selezionati sono idonei per ottenere lo sconto per la capacità riservata. Il numero effettivo di database o di istanze gestite che ottengono lo sconto dipende dall'ambito e dalla quantità selezionati.

    ![Screenshot prima di inviare l'acquisto di capacità riservata](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    Nella tabella seguente vengono descritti i campi obbligatori.
    
    | Campo      | Descrizione|
    |------------|--------------|
    |Subscription|Sottoscrizione usata per pagare la prenotazione della capacità. L'acquisto delle istanze di macchina virtuale riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta MS-AZR-0017P o MS-AZR-0148P) o un contratto singolo con prezzi con pagamento in base al consumo (numero offerta MS-AZR-0003P o MS-AZR-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per una singola sottoscrizione con prezzi con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.|
    |Scope       |L'ambito della prenotazione vCore può coprire una sottoscrizione o più sottoscrizioni (ambito condiviso). Se si seleziona <br/><br/>**Condiviso**, lo sconto di prenotazione vCore viene applicato al database o all'istanza gestita in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.<br/><br/>**Sottoscrizione singola**, lo sconto di prenotazione vCore viene applicato ai database o alle istanze gestite in questa sottoscrizione. <br/><br/>**Gruppo di risorse singolo**, lo sconto relativo alla prenotazione viene applicato alle istanze di database o istanze gestite nella sottoscrizione selezionata e nel gruppo di risorse selezionato all'interno di tale sottoscrizione.|
    |Region      |Area di Azure coperta dalla prenotazione della capacità.|
    |Tipo di distribuzione|Tipo di risorsa di SQL per cui si vuole acquistare la prenotazione.|
    |Livello di prestazioni|Livello di servizio per i database o le istanze gestite. |
    |Termine        |Un anno o tre anni.|
    |Quantità    |Quantità di risorse di calcolo acquistate entro la prenotazione della capacità. La quantità è un numero di Vcore nell'area di Azure selezionata e il livello di prestazioni che vengono riservati e otterranno lo sconto per la fatturazione. Ad esempio, se si esegue o si prevede di eseguire più database con la capacità di calcolo totale di Quinta generazione 16 Vcore nell'area Stati Uniti orientali, è necessario specificare la quantità come 16 per ottimizzare il vantaggio per tutti i database. |

1. Esaminare il costo della prenotazione di capacità nella sezione **costi** .
1. Selezionare **Acquisto**.
1. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flessibilità delle dimensioni vCore

La flessibilità delle dimensioni vCore consente di aumentare o ridurre la quantità di risorse all'interno di un livello di prestazioni e di un'area, senza perdere il vantaggio della capacità riservata. La capacità riservata offre anche la flessibilità necessaria per spostare temporaneamente i database attivi all'interno e all'esterno di pool elastici (all'interno della stessa area e livello di prestazioni) come parte delle normali operazioni senza perdere il vantaggio della capacità riservata. Mantenendo un buffer non applicato nella prenotazione, è possibile gestire efficacemente i picchi di prestazioni senza superare il budget.

## <a name="limitation"></a>Limitazione

Non è possibile riservare i database basati su DTU (Basic, standard o Premium) nel database SQL.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Lo sconto per la prenotazione di vCore viene applicato automaticamente al numero di database o di istanze gestite che corrispondono all'ambito di prenotazione della capacità e agli attributi. È possibile aggiornare l'ambito della prenotazione di capacità tramite il [portale di Azure](https://portal.azure.com), PowerShell, l'interfaccia della riga di comando di Azure o l'API.

Per informazioni su come gestire la prenotazione della capacità, vedere [gestire la capacità riservata](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gestire le prenotazioni di Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Comprendere lo sconto sulle prenotazioni di Azure](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)
