---
title: Guida alla pubblicazione per le offerte di macchine virtuali in Azure Marketplace
description: Questo articolo descrive i requisiti per la pubblicazione di una macchina virtuale e una versione di valutazione gratuita di software da distribuire da Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/15/2020
ms.openlocfilehash: dac67c6dae801d11866f7c4978a050a0cb2fa1dd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065650"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>Guida alla pubblicazione per le offerte di macchine virtuali

Le immagini della macchina virtuale (VM) di pubblicazione sono uno dei modi principali per pubblicare una soluzione in Azure Marketplace. Utilizzare questa guida per comprendere i requisiti per questo tipo di offerta. 

Le offerte di macchine virtuali sono offerte di transazioni distribuite e fatturate tramite Azure Marketplace. La chiamata all'azione visualizzata da un utente è *ora*disponibile.

## <a name="free-trial"></a>Versione di prova gratuita 

Per consentire agli utenti di testare l'offerta, accedere a licenze software a breve termine quando si usa il modello di fatturazione Bring your own License (BYOL). 

## <a name="test-drive"></a>Test drive

È possibile distribuire una o più macchine virtuali tramite app di infrastruttura distribuita come servizio (IaaS) o Software as a Service (SaaS). Un vantaggio dell'opzione di pubblicazione *test drive* è la configurazione automatica di una macchina virtuale o di un'intera soluzione condotta da una presentazione guidata ospitata da un partner. Una test drive consente ai clienti di valutare le macchine virtuali senza costi aggiuntivi. Non è necessario che il cliente sia un cliente di Azure esistente per partecipare all'esperienza di valutazione. 

Per iniziare, contattaci tramite posta elettronica all'indirizzo [amp-testdrive](mailto:amp-testdrive@microsoft.com). 

|Requisiti  |Dettagli |
|---------|---------|
| Si dispone di un'app di Azure Marketplace   |  Una o più macchine virtuali tramite IaaS o SaaS.      |

## <a name="interactive-demo"></a>Demo interattiva

Con questa offerta è possibile offrire ai clienti un'esperienza guidata della soluzione usando una dimostrazione interattiva. Il vantaggio di un'opzione di pubblicazione demo interattiva è che è possibile offrire un'esperienza di valutazione senza dover fornire un'installazione complessa della soluzione complessa. 

## <a name="virtual-machine-offer"></a>Offerta di una macchina virtuale

Usare il tipo di offerta della *macchina virtuale* quando si distribuisce un'appliance virtuale nella sottoscrizione associata al cliente. Le macchine virtuali sono completamente abilitate per il commercio, usando modelli di licenza con pagamento in base al consumo o bring your own License (BYOL). Microsoft gestisce la transazione commerciale e addebita il costo al cliente per conto dell'utente. Si ottiene il vantaggio di usare la relazione di pagamento preferita tra il cliente e Microsoft, compresi eventuali contratti Enterprise.

> [!NOTE]
> Al momento, gli impegni monetari associati a un Enterprise Agreement possono essere usati per l'utilizzo di Azure della VM, ma non per i costi di licenza software.  
> 
> [!NOTE]
> È possibile limitare l'individuazione e la distribuzione della macchina virtuale a un set specifico di clienti pubblicando l'immagine e i prezzi come offerta privata. Le offerte private offrono la possibilità di creare offerte esclusive per i clienti più vicini e offrire software e termini personalizzati. Tali condizioni personalizzate consentono di porre in evidenza numerosi scenari, con offerte contenenti condizioni e prezzi particolari, nonché l'accesso anticipato a versioni software limitate. Le offerte private consentono di fornire prezzi o prodotti specifici a un set limitato di clienti creando un nuovo piano con tali dettagli.  
>
> Per altre informazioni, vedere [offerte private in Azure Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Requisito | Dettagli |  
|:--- |:--- | 
| Fatturazione e misurazione | La macchina virtuale deve supportare una fatturazione mensile con pagamento in base al consumo o BYOL. |  
| Disco rigido virtuale (VHD) compatibile con Azure | Le macchine virtuali devono essere compilate in Windows o Linux. Per ulteriori informazioni sulla creazione di un disco rigido virtuale, vedere: <ul> <li>[Distribuzioni Linux approvate in Azure](../virtual-machines/linux/endorsed-distros.md) (per dischi rigidi virtuali Linux).</li> <li>[Creare un VHD compatibile con Azure](./partner-center-portal/azure-vm-create-offer.md) (per i dischi rigidi virtuali Windows).</li> </ul> |  

>[!Note]
>Il consenso esplicito del canale partner Cloud Solution Provider (CSP) è ora disponibile. Per ulteriori informazioni sul marketing dell'offerta tramite i canali del partner Microsoft CSP, vedere [Cloud Solution Provider](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, scoprire come [Aumentare il business sul cloud con Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Per eseguire la registrazione e iniziare a usare il Centro per i partner:

- [Accedere al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare l'offerta.
- Per ulteriori informazioni, vedere [creare un'offerta di macchina virtuale](./partner-center-portal/azure-vm-create-offer.md) .
