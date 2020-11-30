---
title: Risparmiare sui costi con l'istanza riservata della soluzione VMware di Azure
description: Informazioni su come acquistare un'istanza riservata per la soluzione VMware di Azure.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 80780e9718001893b6960220b5004c1c29b2fec5
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327398"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Salva i costi con la soluzione VMware di Azure

Quando si esegue il commit in un'istanza riservata della [soluzione VMware di Azure](introduction.md), si risparmia denaro.  Lo sconto relativo alla prenotazione viene applicato automaticamente agli host della soluzione VMware di Azure in esecuzione che corrispondono agli attributi e all'ambito di prenotazione. Un acquisto di istanze riservate copre solo la parte di calcolo dell'utilizzo e include i costi di licenza software. 

## <a name="purchase-restriction-considerations"></a>Considerazioni sulla restrizione degli acquisti

Le istanze riservate sono disponibili con alcune eccezioni.

-   **Cloud** : le prenotazioni sono disponibili solo nelle aree elencate nella pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Quota insufficiente** : nella sottoscrizione per la nuova istanza riservata è necessario che nella sottoscrizione sia disponibile una quota di host per una sola sottoscrizione condivisa. Per risolvere il problema, è possibile [creare una richiesta di aumento della quota](enable-azure-vmware-solution.md) .

-   **Idoneità** per l'offerta: è necessario un [Enterprise Agreement di Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) con Microsoft.

-   **Restrizioni di capacità** : in rari casi, Azure limita l'acquisto di nuove prenotazioni per gli SKU dell'host della soluzione VMware di Azure a causa di una capacità ridotta in un'area.

## <a name="buy-a-reservation"></a>Acquistare una prenotazione

È possibile acquistare un'istanza riservata di un'istanza dell'host della soluzione VMware di Azure nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

È possibile pagare per la prenotazione [prima o con pagamenti mensili](../cost-management-billing/reservations/prepare-buy-reservation.md).

Questi requisiti si applicano all'acquisto di un'istanza host dedicata riservata:

-   È necessario avere un ruolo *proprietario* per almeno una sottoscrizione EA o una sottoscrizione con pagamento in base al consumo.

-   Per le sottoscrizioni EA, è necessario abilitare l'opzione **Aggiungi istanze riservate** nel [portale EA](https://ea.azure.com/). Se disabilitato, è necessario essere un amministratore EA per la sottoscrizione per abilitarlo.

-   Per la sottoscrizione in un piano di Azure Cloud Solution Provider (CSP), il partner deve acquistare le istanze riservate nel portale di Azure del cliente. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Acquistare istanze riservate per una sottoscrizione EA

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Tutti i servizi** > **Prenotazioni**.

3. Selezionare **Acquista adesso** , quindi selezionare **soluzione VMware di Azure**.

4. Immettere i campi obbligatori. Gli attributi selezionati che corrispondono all'esecuzione degli host della soluzione VMware di Azure sono idonei per lo sconto della prenotazione.  Gli attributi includono lo SKU, le aree (se applicabile) e l'ambito. L'ambito di una prenotazione determina le risorse a cui verrà applicato il risparmio corrispondente.

   Se si dispone di un contratto Enterprise, è possibile utilizzare l' **opzione Aggiungi ulteriore** per aggiungere istanze in modo rapido. L'opzione non è disponibile per altri tipi di sottoscrizione.

   | Campo        |  Descrizione |
   | ------------ | ------------ |
   | Subscription | La sottoscrizione usata per pagare la prenotazione. I costi per la prenotazione vengono addebitati al metodo di pagamento specificato nella sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P), un contratto per i clienti Microsoft o una sottoscrizione singola con tariffe a consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Gli addebiti vengono dedotti dal saldo dell'impegno monetario, se disponibile, o vengono addebitati come eccedenza. Per una sottoscrizione con tariffe con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito della sottoscrizione o su un metodo di pagamento con fattura. |
   | Scope        | L'ambito della prenotazione può coprire una sottoscrizione o più sottoscrizioni (ambito condiviso). Se si seleziona:<br><ul><li><b>Ambito del gruppo di risorse singolo</b> : applica lo sconto della prenotazione alle risorse corrispondenti solo nel gruppo di risorse selezionato.</li><li><b>Ambito singola sottoscrizione</b> : applica lo sconto di prenotazione alle risorse corrispondenti nella sottoscrizione selezionata.</li><li><b>Ambito condiviso</b> : applica lo sconto relativo alla prenotazione per le risorse corrispondenti nelle sottoscrizioni idonee presenti nel contesto di fatturazione. Per i clienti con contratto Enterprise, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.</li></ul>       |
   | Region       | Area di Azure coperta dalla prenotazione.   |
   | Dimensioni host    | AV36    |
   | Termine         | Un anno o tre anni.  |
   | Quantità     | Numero di istanze da acquistare nella prenotazione. La quantità è il numero di host della soluzione VMware di Azure in esecuzione che possono ottenere lo sconto per la fatturazione.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Acquistare istanze riservate per una sottoscrizione CSP

I CSP che vogliono acquistare istanze riservate per i clienti devono usare la procedura **amministrazione per conto di** (Aobo) dalla [documentazione di centro](/partner-center/azure-plan-manage)per i partner. Per ulteriori informazioni, vedere il video [di amministrazione per conto del (Aobo)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) .

1. Accedere al [Centro per i partner](https://partner.microsoft.com).

2. Selezionare **CSP** per accedere all'area **Customers** .

3. Espandere Dettagli cliente e selezionare **portale di gestione di Microsoft Azure**. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Area clienti del centro per i partner Microsoft" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. Nella portale di Azure selezionare **tutte le prenotazioni dei servizi**  >  **Reservations**.

5. Selezionare **Acquista adesso** , quindi selezionare **soluzione VMware di Azure**.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Prenotazioni portale di Microsoft Azure" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Immettere i campi obbligatori. Gli attributi selezionati che corrispondono all'esecuzione degli host della soluzione VMware di Azure sono idonei per lo sconto della prenotazione.  Gli attributi includono lo SKU, le aree (se applicabile) e l'ambito. L'ambito di una prenotazione determina le risorse a cui verrà applicato il risparmio corrispondente.

   | Campo        |  Descrizione |
   | ------------ | ------------ |
   | Subscription | La sottoscrizione usata per pagare la prenotazione. I costi per la prenotazione vengono addebitati al metodo di pagamento specificato nella sottoscrizione. Il tipo di sottoscrizione deve essere quello idoneo, che in questo caso è una sottoscrizione CSP|
   | Scope        | L'ambito della prenotazione può coprire una sottoscrizione o più sottoscrizioni (ambito condiviso). Se si seleziona:<br><ul><li><b>Ambito del gruppo di risorse singolo</b> : applica lo sconto della prenotazione alle risorse corrispondenti solo nel gruppo di risorse selezionato.</li><li><b>Ambito singola sottoscrizione</b> : applica lo sconto di prenotazione alle risorse corrispondenti nella sottoscrizione selezionata.</li><li><b>Ambito condiviso</b> : applica lo sconto relativo alla prenotazione per le risorse corrispondenti nelle sottoscrizioni idonee presenti nel contesto di fatturazione. Per i clienti con contratto Enterprise, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.</li></ul>       |
   | Region       | Area di Azure coperta dalla prenotazione.   |
   | Dimensioni host    | AV36    |
   | Termine         | Un anno o tre anni.  |
   | Quantità     | Numero di istanze da acquistare nella prenotazione. La quantità è il numero di host della soluzione VMware di Azure in esecuzione che possono ottenere lo sconto per la fatturazione.     |

Per altre informazioni su come visualizzare le prenotazioni acquistate per il cliente, vedere l'articolo [visualizzare le prenotazioni di Azure come provider di soluzioni cloud (CSP)](../cost-management-billing/reservations/how-to-view-csp-reservations.md) .

## <a name="usage-data-and-reservation-usage"></a>Utilizzo prenotazione e dati di utilizzo

L'utilizzo che ottiene uno sconto per la prenotazione ha un prezzo effettivo pari a zero. È possibile visualizzare l'istanza della soluzione VMware di Azure che ha ricevuto lo sconto di prenotazione per ogni prenotazione.

Per ulteriori informazioni sul modo in cui vengono visualizzati gli sconti di prenotazione nei dati di utilizzo:

- Per i clienti con contratto [Enterprise, vedere informazioni sull'utilizzo della prenotazione di Azure per la registrazione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Per le singole sottoscrizioni, vedere [informazioni sull'utilizzo delle prenotazioni di Azure per la sottoscrizione con pagamento in base al consumo](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>Modificare una prenotazione dopo l'acquisto

È possibile apportare queste modifiche a una prenotazione dopo l'acquisto:

-   Aggiornare l'ambito della prenotazione

-   Flessibilità delle dimensioni dell'istanza (se applicabile)

-   Proprietario

È inoltre possibile suddividere una prenotazione in blocchi più piccoli o in prenotazioni di tipo merge. Nessuna delle modifiche genera una nuova transazione commerciale o modifica la data di fine della prenotazione.

Per informazioni dettagliate sulle prenotazioni gestite da CSP, vedere [Sell Microsoft Azure prenotazioni per i clienti che usano il centro per i partner, le portale di Azure o le API](/partner-center/azure-reservations).



>[!NOTE]
>Una volta acquistata la prenotazione, non sarà possibile apportare direttamente questi tipi di modifiche:
>
> - Area della prenotazione esistente
> - SKU
> - Quantità
> - Duration
>
>Tuttavia, se si desidera apportare modifiche, è possibile *scambiare* una prenotazione.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

I CSP possono annullare, scambiare o rimborsare le prenotazioni, con determinate limitazioni, acquistate per i clienti. Per ulteriori informazioni, vedere [gestione, annullamento, scambio o rimborso Microsoft Azure prenotazioni per i clienti](/partner-center/azure-reservations-manage).