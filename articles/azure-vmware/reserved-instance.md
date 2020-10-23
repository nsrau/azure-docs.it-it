---
title: Risparmiare sui costi con l'istanza riservata della soluzione VMware di Azure
description: Informazioni su come acquistare un'istanza riservata per la soluzione VMware di Azure.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: bac2497c637a301c7ce8cbc44fc6945c3ef43b06
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370679"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Salva i costi con la soluzione VMware di Azure

Quando si esegue il commit in un'istanza riservata della [soluzione VMware di Azure](introduction.md), si risparmia denaro. Lo sconto relativo alla prenotazione viene applicato automaticamente agli host della soluzione VMware di Azure in esecuzione che corrispondono agli attributi e all'ambito di prenotazione. Non è necessario assegnare una prenotazione a un host dedicato per ottenere gli sconti. Un acquisto di istanze riservate copre solo la parte di calcolo dell'utilizzo e include i costi di licenza software. 


## <a name="purchase-restriction-considerations"></a>Considerazioni sulla restrizione degli acquisti

Le istanze riservate sono disponibili con alcune eccezioni.

-   **Cloud** : le prenotazioni sono disponibili solo nelle aree elencate nella pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Quota insufficiente** : nella sottoscrizione per la nuova istanza riservata è necessario che nella sottoscrizione sia disponibile una quota di host per una sola sottoscrizione condivisa. Per risolvere il problema, è possibile [creare una richiesta di aumento della quota](enable-azure-vmware-solution.md) .

-   **Idoneità**per l'offerta: è necessario un [Enterprise Agreement di Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) con Microsoft.

-   **Restrizioni di capacità** : in rari casi, Azure limita l'acquisto di nuove prenotazioni per gli SKU dell'host della soluzione VMware di Azure a causa di una capacità ridotta in un'area.

## <a name="buy-a-reservation"></a>Acquistare una prenotazione

È possibile acquistare un'istanza riservata di un'istanza dell'host della soluzione VMware di Azure nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Usare [pagamenti anticipati o mensili](../cost-management-billing/reservations/prepare-buy-reservation.md) per acquistare la prenotazione.

Questi requisiti si applicano all'acquisto di un'istanza host dedicata riservata:

-   È necessario avere un ruolo proprietario per almeno una sottoscrizione EA o una sottoscrizione con pagamento in base al consumo.

-   Per le sottoscrizioni EA, l'opzione **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). Se tale impostazione è disabilitata, è necessario essere un amministratore del contratto EA della sottoscrizione.

Per acquistare un'istanza:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Tutti i servizi** > **Prenotazioni**.

3. Selezionare **Aggiungi** per acquistare una nuova prenotazione e quindi selezionare **soluzione VMware di Azure**.

4. Immettere i campi obbligatori. Eseguendo gli host della soluzione VMware di Azure che corrispondono agli attributi, selezionare idoneo per ottenere lo sconto per la prenotazione. Il numero effettivo degli host della soluzione VMware di Azure che ottengono lo sconto a seconda dell'ambito e della quantità selezionati.

   Se si dispone di un contratto Enterprise, è possibile utilizzare l' **opzione Aggiungi** ulteriore per aggiungere più istanze in modo rapido. L'opzione non è disponibile per altri tipi di sottoscrizione.

   | Campo        |  Descrizione |
   | ------------ | ------------ |
   | Subscription | La sottoscrizione usata per pagare la prenotazione. I costi per la prenotazione vengono addebitati al metodo di pagamento specificato nella sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto di servizio Microsoft o una sottoscrizione singola con tariffe a consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Gli addebiti vengono dedotti dal saldo dell'impegno monetario, se disponibile, o vengono addebitati come eccedenza. Per una sottoscrizione con tariffe con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione. |
   | Ambito        | L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona:<br><ul><li><b>Ambito del gruppo di risorse singolo: applica lo sconto di prenotazione alle risorse corrispondenti solo nel gruppo di risorse selezionato.</li><li><b>Singolo ambito di sottoscrizione: applica lo sconto di prenotazione alle risorse corrispondenti nella sottoscrizione selezionata.</li><li><b>Ambito condiviso: applica lo sconto di prenotazione alle risorse corrispondenti nelle sottoscrizioni idonee presenti nel contesto di fatturazione. Per i clienti con contratto Enterprise, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.</li></ul>       |
   | Area       | L'area di Azure coperta dalla prenotazione.   |
   | Dimensioni host    | AV36    |
   | Termine         | Un anno o tre anni.  |
   | Quantità     | Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di host della soluzione VMware di Azure in esecuzione che possono ottenere lo sconto per la fatturazione.    |

## <a name="usage-data-and-reservation-usage"></a>Utilizzo prenotazione e dati di utilizzo

L'utilizzo che ottiene uno sconto per la prenotazione ha un prezzo effettivo pari a zero. È possibile visualizzare l'istanza della soluzione VMware di Azure che ha ricevuto lo sconto di prenotazione per ogni prenotazione.

Per ulteriori informazioni sul modo in cui vengono visualizzati gli sconti di prenotazione nei dati di utilizzo:

- Per i clienti con contratto [Enterprise, vedere informazioni sull'utilizzo della prenotazione di Azure per la registrazione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Per le singole sottoscrizioni, vedere [informazioni sull'utilizzo delle prenotazioni di Azure per la sottoscrizione con pagamento in base al consumo](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>Modificare una prenotazione dopo l'acquisto

Dopo l'acquisto, a una prenotazione è possibile apportare i tipi di modifiche seguenti:

-   Aggiornare l'ambito della prenotazione

-   Flessibilità delle dimensioni dell'istanza (se applicabile)

-   Proprietario

È inoltre possibile suddividere una prenotazione in blocchi più piccoli o in prenotazioni di tipo merge. Nessuna delle modifiche genera una nuova transazione commerciale o modifica la data di fine della prenotazione.

>[!NOTE]
>Una volta acquistata la prenotazione, non sarà possibile apportare direttamente i tipi di modifiche seguenti:
>
> - Area della prenotazione esistente
> - SKU
> - Quantità
> - Durata
>
>Tuttavia, se si desidera apportare modifiche, è possibile *scambiare* una prenotazione.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).