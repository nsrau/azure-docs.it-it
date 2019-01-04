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
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195946"
---
# <a name="azure-application-offer"></a>Offerta di un'applicazione Azure

Questa sezione illustra come pubblicare una nuova offerta per un'applicazione Azure per Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Ogni applicazione Azure contiene un modello di Azure Resource Manager che definisce tutte le risorse tecniche usate dall'applicazione, in cui sono in genere inclusi una o più macchine virtuali e altri servizi basati sul Web o che supportano Azure. | ![Icona delle app di Azure](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>Vantaggi

I vantaggi della presenza delle applicazioni in un marketplace di Microsoft includono la possibilità di:

* Raggiungere 100 milioni di utenti di Azure Active Directory tramite Office 365 e Dynamics 365.

* Espandere il team di vendita: raggiungere utenti aziendali in tutto il mondo e ottenere un canale di vendita che coinvolge gli utenti finali, aiuta a generare clienti potenziali e avvia conversazioni con nuovi clienti di diversi settori.

* Ottenere informazioni dettagliate di utilità pratica: verranno condivise informazioni sulle prestazioni dell'app in AppSource, cosa funziona e come migliorare ulteriormente le procedure di vendita.

## <a name="types-of-azure-applications"></a>Tipi di applicazioni Azure

Esistono due tipi di applicazioni Azure: un'applicazione gestita e un modello di soluzione. Anche se sono simili, esistono alcune differenze significative.

### <a name="solution-template"></a>Modello di soluzione

I modelli di soluzione sono uno dei modi principali per pubblicare una soluzione nel Marketplace. Questo tipo di offerta viene usato quando una soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una singola macchina virtuale. È possibile garantire l'automazione fornendo più di una macchina virtuale tramite un modello di soluzione. È incluso il provisioning delle risorse di rete e archiviazione per offrire soluzioni IaaS complesse. Per una panoramica dei requisiti del modello di soluzione e il modello di fatturazione, vedere [Applicazioni di Azure: modelli di soluzione](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

### <a name="managed-application"></a>Applicazione gestita

Un'applicazione gestita è simile a un modello di soluzione del Marketplace, con una differenza essenziale. In un'applicazione gestita le risorse vengono distribuite in un gruppo di risorse gestito dall'entità di pubblicazione dell'app. Il gruppo di risorse è presente nella sottoscrizione del consumer, ma un'identità nel tenant dell'entità di pubblicazione dell'app ha accesso al gruppo di risorse. In qualità di entità di pubblicazione dell'app, è possibile specificare il costo del supporto continuativo per la soluzione. Usare le applicazioni gestite di Azure per creare e distribuire con facilità applicazioni chiavi in mano completamente gestite ai clienti.

Oltre che nel Marketplace, è possibile offrire applicazioni gestite anche in un catalogo di servizi. Il catalogo di servizi è un catalogo interno di soluzioni approvate per gli utenti in un'organizzazione. Il catalogo può essere usato per garantire la conformità agli standard aziendali e offrire al contempo soluzioni ottimali per i gruppi all'interno dell'organizzazione. I dipendenti lo usano per trovare facilmente le applicazioni consigliate e approvate dai reparti IT

Per altre informazioni sui vantaggi e sui tipi di applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="publishing-overview"></a>Panoramica della pubblicazione

Il video seguente, [Building Solution Templates, and Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) (Creazione di modelli di soluzioni e applicazioni gestite per Azure Marketplace), fornisce una panoramica su come creare un modello di Azure Resource Manager per definire una soluzione di applicazione Azure e su come pubblicare successivamente l'offerta dell'app in Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>Flusso di lavoro del processo di pubblicazione

Il diagramma seguente illustra il processo generale per la pubblicazione di un'offerta per un'applicazione Azure.

![Flusso di lavoro per la pubblicazione dell'offerta](./media/new-offer-process.png)

## <a name="offer-components"></a>Componenti dell'offerta

Questa sezione, che descrive gli elementi della pubblicazione di un'offerta per un'applicazione gestita, è da intendersi come guida per chi effettua la pubblicazione in Azure Marketplace. La pubblicazione è suddivisa nelle parti principali seguenti: 

* [Prerequisiti](./cpp-prerequisites.md): elenca i requisiti tecnici e commerciali necessari per la creazione o la pubblicazione di un'offerta per un'applicazione gestita. 
* [Creare l'offerta](./cpp-create-offer.md): fornisce i passaggi necessari per creare la voce dell'offerta per un'applicazione gestita usando il portale Cloud Partner. 
* [Pubblicare l'offerta](./cpp-publish-offer.md): descrive come inviare l'offerta per la pubblicazione in Azure Marketplace.

## <a name="steps-in-the-publishing-process"></a>Passaggi del processo di pubblicazione

I passaggi generali per la pubblicazione di un'offerta per un'applicazione Azure sono:

1. Creazione dell'offerta: specificare le informazioni dettagliate sull'offerta. Queste informazioni includono la descrizione dell'offerta, il materiale di marketing, le informazioni di supporto e le specifiche degli asset.

2. Creazione delle risorse commerciali e tecniche: creare le risorse aziendali (documenti legali e materiali di marketing) e tecniche per la soluzione associata.

3. Creazione dello SKU: creare gli SKU associati all'offerta. È necessario uno SKU per ogni immagine che si intende pubblicare.

4. Certificazione e pubblicazione dell'offerta: dopo aver completato l'offerta e le risorse tecniche, è possibile procedere all'invio dell'offerta. L'invio dell'offerta avvia il processo di pubblicazione. Durante questo processo la soluzione viene sottoposta a test, viene convalidata e certificata e quindi viene "aggiunta" in Azure Marketplace.

## <a name="next-steps"></a>Passaggi successivi

Prima di prendere in considerazione questi passaggi, è necessario soddisfare i [requisiti tecnici e commerciali](./cpp-prerequisites.md) per la pubblicazione di un'applicazione gestita in Microsoft Azure Marketplace.