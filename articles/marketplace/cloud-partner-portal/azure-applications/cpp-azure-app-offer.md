---
title: Offerta di applicazione Azure | Microsoft Docs
description: Panoramica del processo per la pubblicazione di un'offerta per un'applicazione Azure in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pbutlerm
ms.openlocfilehash: 9faa38a23b2039902366e5b885ab73c68a2a3d80
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076021"
---
# <a name="azure-application-offer"></a>Offerta di un'applicazione Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Questa sezione illustra come pubblicare una nuova offerta di applicazione Azure in [Azure Marketplace](https://azuremarketplace.microsoft.com).  Ogni applicazione Azure contiene un modello di Azure Resource Manager che definisce tutte le risorse tecniche usate dall'applicazione, in cui sono in genere inclusi una o più macchine virtuali e altri servizi basati sul Web o che supportano Azure. Tutte le offerte di app Azure devono abilitare la sicurezza dell'accesso tramite [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Icona delle app di Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Panoramica della pubblicazione

Il video seguente, [Building Solution Templates, and Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) (Creazione di modelli di soluzione e applicazioni gestite per Azure Marketplace), offre un'introduzione generale presentando i tipi di offerta disponibili, gli asset tecnici necessari, la procedura per creare un modello di Azure Resource Manager, le attività di sviluppo e testing dell'interfaccia utente dell'app, la procedura per pubblicare l'offerta dell'app e il processo di revisione dell'app.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Tipi di applicazioni Azure

Esistono due tipi di applicazioni Azure, le applicazioni gestite e i modelli di soluzione. 

- I modelli di soluzione sono uno dei modi principali per pubblicare una soluzione nel Marketplace. Questo tipo di offerta viene usato quando una soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale. È possibile garantire l'automazione fornendo più di una macchina virtuale tramite un modello di soluzione. L'automazione include il provisioning delle risorse di rete e archiviazione per offrire soluzioni IaaS complesse. Per una panoramica dei requisiti del modello di soluzione e il modello di fatturazione, vedere [Applicazioni di Azure: modelli di soluzione](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Le applicazioni gestite sono simili ai modelli di soluzione, con una differenza fondamentale. In un'applicazione gestita le risorse vengono distribuite in un gruppo di risorse gestito dall'entità di pubblicazione dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. In qualità di entità di pubblicazione dell'app, è possibile specificare il costo del supporto continuativo per la soluzione. Usare le applicazioni gestite di Azure per creare e distribuire con facilità applicazioni chiavi in mano completamente gestite ai clienti.

Oltre che in Azure Marketplace, è possibile offrire applicazioni gestite anche in un catalogo di servizi. Il catalogo di servizi è un catalogo interno di soluzioni approvate per gli utenti in un'organizzazione. Il catalogo può essere usato per garantire la conformità agli standard aziendali e offrire al contempo soluzioni ottimali per i gruppi all'interno dell'organizzazione. I dipendenti lo usano per trovare facilmente le applicazioni consigliate e approvate dai reparti IT

>[!Note]
>Cloud Solution Provider (CSP) partner canale acconsentire esplicitamente a questo punto è disponibile.  Vedi [Cloud Solution Provider](../../cloud-solution-providers.md) per altre informazioni sul marketing dell'offerta tramite Microsoft CSP partner canali.

Per altre informazioni sui vantaggi e sui tipi di applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Flusso di lavoro del processo di pubblicazione

Il diagramma seguente illustra il processo generale per la pubblicazione di un'offerta per un'applicazione Azure.

![Flusso di lavoro per la pubblicazione dell'offerta](./media/new-offer-process.png)

I passaggi generali per la pubblicazione di un'offerta per un'applicazione Azure sono:

1. Verifica dei [prerequisiti](./cpp-prerequisites.md): (non illustrato) verificare che siano soddisfatti i requisiti aziendali e tecnici per la pubblicazione di un'app Azure in Azure Marketplace. 

1. [Creazione dell'offerta](./cpp-create-offer.md): specificare le informazioni dettagliate sull'offerta. Queste informazioni includono la descrizione dell'offerta, il materiale di marketing, le informazioni di supporto e le specifiche degli asset.

1. [Creazione o raccolta di asset aziendali e tecnici esistenti](./cpp-create-technical-assets.md): creare gli asset aziendali (documenti legali e materiale di marketing) e tecnici per la soluzione associata.

1. [Creazione dello SKU](./cpp-skus-tab.md): creare gli SKU associati all'offerta. È necessario uno SKU per ogni immagine che si intende pubblicare.

1. Certificazione e [pubblicazione dell'offerta](./cpp-publish-offer.md): dopo aver completato l'offerta e gli asset tecnici, è possibile procedere all'invio dell'offerta. L'invio dell'offerta avvia il processo di pubblicazione. Durante questo processo la soluzione viene sottoposta a test, viene convalidata e certificata e quindi viene "aggiunta" in Azure Marketplace.

## <a name="next-steps"></a>Passaggi successivi

Prima di prendere in considerazione questi passaggi, è necessario soddisfare i [requisiti tecnici e commerciali](./cpp-prerequisites.md) per la pubblicazione di un'applicazione gestita in Microsoft Azure Marketplace.
