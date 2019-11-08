---
title: Prerequisiti della macchina virtuale per Microsoft Azure | Azure Marketplace
description: Elenco dei prerequisiti necessari per pubblicare un'offerta di macchina virtuale in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a657ceaacf3680de54dc0d639a3f1a0aff6a6a03
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824428"
---
# <a name="virtual-machine-prerequisites"></a>Prerequisiti delle macchine virtuali

Questo articolo elenca i requisiti tecnici e aziendali che è necessario soddisfare prima di poter pubblicare un'offerta di macchina virtuale in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Se non è già stato fatto, vedere la [Guida alla pubblicazione dell'offerta di macchina virtuale](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Requisiti tecnici

I prerequisiti tecnici per la pubblicazione di una soluzione di macchina virtuale (VM, Virtual Machine) sono semplici:

- È necessario avere un account Azure attivo. Se non si ha un account di Azure, è possibile iscriversi presso il [sito Microsoft Azure](https://azure.microsoft.com).  
- È necessario che l'ambiente sia configurato per supportare lo sviluppo di macchine virtuali Windows o Linux.  Per altre informazioni, vedere il sito della documentazione appropriata a seconda del tipo di macchine virtuali:
    - [Documentazione delle macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentazione delle macchine virtuali Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Requisiti aziendali

I requisiti commerciali includono obblighi procedurali, legali e contrattuali: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- È necessario essere un editore di Marketplace cloud registrato.  Se non si è ancora effettuata la registrazione, seguire la procedura nell'articolo [Diventare un editore di Marketplace cloud](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Per l'accesso al [portale Cloud Partner](https://cloudpartner.azure.com) è consigliabile usare lo stesso account usato per la registrazione a Microsoft Developer Center.
    > È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. L'account non deve essere specifico di servizi o offerte singole.
    
- La società (o la sua filiale) deve trovarsi in un paese di origine/area geografica supportato da Azure Marketplace.  Per un elenco aggiornato di questi paesi/aree geografiche, vedere [criteri di partecipazione Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati da Azure Marketplace.  Per altre informazioni, vedere [Opzioni di fatturazione in Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- L'utente è responsabile di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite la community.
- Il partner ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.
- È necessario offrire contenuti che soddisfino i criteri perché l'offerta sia inserita in Azure Marketplace e nel portale di Azure. <!-- TD: Meaning/links? -->
- È necessario accettare le condizioni dei [Criteri di Partecipazione a Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) e del contratto per editori.
- È necessario rispettare le condizioni per l' [utilizzo del sito web Microsoft Azure](https://azure.microsoft.com/support/legal/website-terms-of-use/), l' [informativa sulla privacy Microsoft](https://privacy.microsoft.com/privacystatement)e il [contratto di Microsoft Azure Certified Program](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver soddisfatto questi prerequisiti, è possibile [creare l'offerta di VM](./cpp-create-offer.md).
