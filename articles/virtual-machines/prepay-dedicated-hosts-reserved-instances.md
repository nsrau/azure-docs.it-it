---
title: Pagamento anticipato per gli host dedicati di Azure per risparmiare denaro
description: Informazioni su come acquistare istanze riservate degli host dedicati di Azure per risparmiare sui costi di calcolo.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207745"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Risparmiare i costi con un'istanza riservata di host dedicati di AzureSave costs with a Reserved Instance of Azure Dedicated Hosts

Quando si esegue il commit in un'istanza riservata di host dedicati di Azure, è possibile risparmiare denaro. Lo sconto prenotazione viene applicato automaticamente al numero di host dedicati in esecuzione che corrispondono all'ambito e agli attributi della prenotazione. Non è necessario assegnare una prenotazione a un host dedicato per ottenere gli sconti. L'acquisto di un'istanza riservata copre solo la parte di calcolo dell'utilizzo e include i costi di licenza del software. Vedere [Panoramica degli host dedicati di Azure per](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)le macchine virtuali .

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Determinare lo SKU host dedicato corretto prima di acquistare


Prima di acquistare una prenotazione, è necessario determinare quale host dedicato è necessario. Uno SKU viene definito per un host dedicato che rappresenta la serie e il tipo di macchina virtuale. 

Iniziare esaminando le dimensioni supportate per la [macchina virtuale Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) o [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per identificare la serie di macchine virtuali.

Verificare quindi se è supportato in host dedicati di Azure.Next, check whether it is supported on Azure Dedicated Hosts. La pagina [dei prezzi degli host dedicati](https://aka.ms/ADHPricing) di Azure include l'elenco completo degli SKU degli host dedicati, le informazioni sulla CPU e varie opzioni di determinazione dei prezzi (incluse le istanze riservate).

È possibile trovare diversi SKU che supportano serie di macchine virtuali (con tipi diversi). Identificare lo SKU migliore confrontando la capacità dell'host (numero di vCPU). Si noti che sarà possibile applicare la prenotazione a più SKU host dedicati che supportano la stessa serie di macchine virtuali (ad esempio DSv3_Type1 e DSv3_Type2) ma non a serie di macchine virtuali diverse (ad esempio DSv3 ed ESv3).



## <a name="purchase-restriction-considerations"></a>Considerazioni sulle restrizioni all'acquisto

Le istanze riservate sono disponibili per la maggior parte delle dimensioni host dedicate, con alcune eccezioni.

Gli sconti di prenotazione non si applicano ai seguenti elementi:

- **Cloud:** le prenotazioni non sono disponibili per l'acquisto in Germania o in Cina. 

- **Quota insufficiente:** una prenotazione con ambito a una singola sottoscrizione deve disporre della quota vCPU disponibile nella sottoscrizione per la nuova istanza riservata. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota di 10 vCPU per la serie DSv3, non è possibile acquistare una prenotazione host dedicati che supportano questa serie. Il controllo delle quote per le prenotazioni include le macchine virtuali e gli host dedicati già distribuiti nella sottoscrizione. È possibile creare una richiesta di [aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)per risolvere il problema.

- **Restrizioni** di capacità: in rari casi, Azure limita l'acquisto di nuove prenotazioni per subset di SKU host dedicati, a causa della bassa capacità in un'area.

## <a name="buy-a-reservation"></a>Acquistare una prenotazione

È possibile acquistare un'istanza riservata di un'istanza di Host dedicato di Azure nel portale di [Azure.](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)

Pagare per la prenotazione [in anticipo o con pagamenti mensili](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations). Questi requisiti si applicano all'acquisto di un'istanza di Host dedicato riservata:These requirements apply to buying a reserved Dedicated Host instance:

- È necessario essere in un ruolo proprietario per almeno una sottoscrizione EA o una sottoscrizione con una tariffa con pagamento in base al quale è possibile.

- Per le sottoscrizioni EA, l'opzione **Aggiungi istanze** riservate deve essere abilitata nel [portale EA.](https://ea.azure.com/) Se tale impostazione è disabilitata, è necessario essere un amministratore del contratto EA della sottoscrizione.

- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

Per acquistare un'istanza:

1. Accedere al  [portale di Azure](https://portal.azure.com/).

2. Selezionare **Tutte le** \> **prenotazioni**dei servizi .

3. Selezionare **Aggiungi** per acquistare una nuova prenotazione, quindi fare clic su **Host dedicati**.

4. Compilare i campi obbligatori. Esecuzione di istanze di host dedicati che corrispondono agli attributi selezionati sono idonei per ottenere lo sconto sulla prenotazione. Il numero effettivo delle istanze dell'host dedicato che ottengono lo sconto dipende dall'ambito e dalla quantità selezionati.

Se si dispone di un accordo EA, è possibile utilizzare l'opzione Aggiungi **altro**per aggiungere rapidamente altre istanze. L'opzione non è disponibile per altri tipi di sottoscrizione.

| **Campo**           | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription        | La sottoscrizione usata per pagare la prenotazione. Al metodo di pagamento della sottoscrizione vengono addebitati i costi per la prenotazione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P) o contratto microsoft con contratto cliente o una singola sottoscrizione con tariffe con pagamento in base al costo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P). Gli addebiti vengono detratti dal saldo dell'impegno monetario, se disponibile, o addebitati come eccessivamente. Per un abbonamento con tariffe con pagamento in base al pagamento, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura. |
| Scope               | L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | L'area di Azure coperta dalla prenotazione.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dimensioni host dedicate | Dimensioni delle istanze dell'host dedicato.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Termine                | Un anno o tre anni.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Quantità            | Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze dell'host dedicato in esecuzione che possono ottenere lo sconto sulla fatturazione.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Ambito singolo gruppo** di risorse: applica lo sconto di prenotazione solo alle risorse corrispondenti nel gruppo di risorse selezionato.

- **Ambito della sottoscrizione** singola: applica lo sconto di prenotazione alle risorse corrispondenti nella sottoscrizione selezionata.

- **Ambito condiviso:** applica lo sconto sulla prenotazione alle risorse corrispondenti nelle sottoscrizioni idonee presenti nel contesto di fatturazione. Per i clienti EA, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.

## <a name="usage-data-and-reservation-utilization"></a>Dati di utilizzo e utilizzo delle prenotazioni

I dati di utilizzo hanno un prezzo effettivo pari a zero per l'utilizzo che ottiene uno sconto sulla prenotazione. È possibile vedere quale istanza della macchina virtuale ha ricevuto lo sconto di prenotazione per ogni prenotazione.

Per altre informazioni su come vengono visualizzati gli sconti di prenotazione nei dati di utilizzo, vedere [Informazioni sull'utilizzo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) della prenotazione di Azure per la registrazione Enterprise se si è un cliente EA. Se si ha una singola sottoscrizione, vedere [Informazioni sull'utilizzo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo.

## <a name="change-a-reservation-after-purchase"></a>Modificare una prenotazione dopo l'acquisto

Dopo l'acquisto, a una prenotazione è possibile apportare i tipi di modifiche seguenti:

- Aggiornare l'ambito della prenotazione

- Flessibilità delle dimensioni dell'istanza (se applicabile)Instance size flexibility (if applicable)

- Proprietario

È inoltre possibile dividere una prenotazione in blocchi più piccoli e unire le prenotazioni già suddivise. Nessuna delle modifiche causa una nuova transazione commerciale o modifica la data di fine della prenotazione.

Non è possibile apportare i seguenti tipi di modifiche direttamente dopo l'acquisto:

- Regione di una prenotazione esistente

- SKU

- Quantità

- Duration

Tuttavia, è possibile *scambiare* una prenotazione se si desidera apportare modifiche.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi in modalità self-service per le prenotazioni](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)di Azure.For more information, see Self-service exchanges and refunds for Azure Reservations .

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto .

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire una prenotazione, vedere [Gestire prenotazioni](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)di Azure.

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Uso degli host dedicati di AzureUsing Azure Dedicated Hosts](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Prezzi degli host dedicati](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gestire le prenotazioni in AzureManage Reservations in Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Informazioni su come viene applicato lo sconto sulla prenotazione](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Costi del software Windows non inclusi nelle prenotazioni](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)


