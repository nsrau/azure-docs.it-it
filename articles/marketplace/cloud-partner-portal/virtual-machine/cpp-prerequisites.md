---
title: Prerequisiti delle macchine virtuali di Microsoft Azure | Azure Marketplace
description: Elenco dei prerequisiti necessari per pubblicare un'offerta di macchina virtuale in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 258d21eae5af50b5dc0bed6887618e2999cae45a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257393"
---
# <a name="virtual-machine-prerequisites"></a>Prerequisiti delle macchine virtuali

Questo articolo vengono elencate entrambe le tecniche e requisiti aziendali che è necessario soddisfare prima di pubblicare un'offerta di macchina virtuale per il [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Se non è già stato fatto, consultare il [Guida alla pubblicazione dell'offerta macchina virtuali](../../marketplace-virtual-machines.md).


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
    
- La società (o la sua affiliata) deve trovarsi in un'origine della vendita-da-paese/area geografica supportato da Azure Marketplace.  Per un elenco aggiornato di questi paesi/aree geografiche, vedere [politiche di partecipazione di Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati da Azure Marketplace.  Per altre informazioni, vedere [Opzioni di fatturazione in Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- L'utente è responsabile di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite la community.
- Il partner ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.
- È necessario offrire contenuti che soddisfino i criteri perché l'offerta sia inserita in Azure Marketplace e nel portale di Azure. <!-- TD: Meaning/links? -->
- È necessario accettare le condizioni dei [Criteri di Partecipazione a Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) e del contratto per editori.
- È necessario rispettare le [Microsoft Azure condizioni di utilizzo del sito Web](https://azure.microsoft.com/support/legal/website-terms-of-use/), [informativa sulla Privacy Microsoft](https://privacy.microsoft.com/privacystatement), e [contratto di Microsoft Azure Certified programma](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver soddisfatto questi prerequisiti, è possibile [creare l'offerta VM](./cpp-create-offer.md).
