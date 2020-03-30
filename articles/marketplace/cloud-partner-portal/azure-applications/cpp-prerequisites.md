---
title: Prerequisiti per l'offerta di applicazioni di Azure Azure Marketplace
description: Prerequisiti per la pubblicazione di un'offerta di applicazione Azure in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b927404758dc59419fb3c5e638ac32758d534681
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281749"
---
# <a name="azure-application-prerequisites"></a>Prerequisiti per un'applicazione Azure

Questo articolo descrive i prerequisiti tecnici e aziendali per la pubblicazione di un'offerta di un'applicazione gestita in Azure Marketplace.  Se non è già stato fatto, esaminare le seguenti fonti di informazioni:
- A seconda del tipo di SKU, [Azure Applications: Guida](../../marketplace-solution-templates.md) alla pubblicazione dell'offerta di modelli di soluzione o Azure Applications: Guida alla [pubblicazione dell'offerta di applicazioni gestite](../../marketplace-managed-apps.md)
- Creazione di modelli di soluzione e applicazioni gestite per il video di [Azure MarketplaceBuilding Solution Templates, and Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) video


## <a name="technical-requirements"></a>Requisiti tecnici

I requisiti tecnici includono gli elementi seguenti:

*   Modelli di Azure Resource Manager. Per altre informazioni, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Questo articolo descrive la struttura di un modello di Azure Resource Manager. Presenta le diverse sezioni di un modello e le proprietà disponibili in queste sezioni. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. 
* Modelli di avvio rapido di Azure.<br> Per altre informazioni, vedere:

  * [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). Distribuire le risorse di Azure attraverso Azure Resource Manager con i modelli offerti dalla community per incrementare la produttività. Gestione risorse di Azure consente di effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Si usa lo stesso modello per distribuire più volte l'applicazione durante ogni fase del ciclo di vita dell'applicazione.
  * [GitHub: Modelli di guida introduttiva](https://github.com/azure/azure-quickstart-templates)di Azure Resource Manager . Questo repository contiene tutti i modelli di Azure Resource Manager attualmente disponibili offerti dalla community. Un indice dei modelli in cui è possibile eseguire ricerche è disponibile all'indirizzo https://azure.microsoft.com/documentation/templates/.
* Creare la definizione dell'interfaccia utente<br>
Per altre informazioni, vedere [Creare l'interfaccia utente del portale di Azure per l'applicazione gestita](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Questo articolo illustra i concetti di base del file createUiDefinition.json. Il portale di Azure usa questo file per generare l'interfaccia utente per la creazione di un'applicazione gestita.


## <a name="business-requirements"></a>Requisiti aziendali

I requisiti aziendali includono gli obblighi procedurali, legali e contrattuali riportati di seguito:

* È necessario essere un editore di Marketplace cloud registrato. Se non sei registrato, segui la procedura descritta nell'articolo [Diventare un editore](https://docs.microsoft.com/azure/marketplace/become-publisher
)di Cloud Marketplace.

>[!NOTE]
>Per l'accesso al portale Cloud Partner è consigliabile usare lo stesso account usato per la registrazione a Microsoft Developer Center. È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. Questo account non deve essere specifico per singoli servizi o offerte.

* La società (o la relativa filiale) deve trovarsi in un paese/area geografica di vendita supportata da Azure Marketplace. Per un elenco aggiornato di questi paesi/aree geografiche, vedere Criteri di [partecipazione](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)di Microsoft Azure Marketplace .
* Il prodotto deve essere concesso in licenza in modo compatibile con i modelli di fatturazione supportati da Azure Marketplace. Per altre informazioni, vedere le [opzioni di fatturazione](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) in Azure Marketplace.
* Il partner è responsabile di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite la community.
* Il partner ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.
* È necessario offrire contenuti che soddisfino i criteri perché l'offerta sia inserita in Azure Marketplace e nel portale di Azure.
* È necessario accettare le condizioni dei Criteri di partecipazione a Microsoft Azure Marketplace e del contratto per editori.
* È necessario rispettare le Condizioni per l'utilizzo del sito Web di Microsoft Azure, l'Informativa sulla privacy di Microsoft e il Contratto del Programma Microsoft Azure Certified.


## <a name="publishing-requirements"></a>Requisiti per la pubblicazione

Per pubblicare una nuova offerta di applicazione Azure, è necessario soddisfare i prerequisiti seguenti:

* Disporre dei metadati pronti per l'uso. L'elenco seguente (non completo) mostra un esempio di questi metadati:
  * Un titolo
  * Una descrizione (in formato HTML)
  * Un'immagine del logo (in formato PNG) e in queste dimensioni fisse dell'immagine: 40 x 40 pixel, 90 x 90 pixel, 115 x 115 pixel e 255 x 115 pixel.
* Una *Delle Condizioni per l'utilizzo* e i documenti dell'Informativa *sulla privacy*
* Documentazione dell'applicazione
* Contatti del supporto tecnico


## <a name="next-steps"></a>Passaggi successivi

Dopo aver soddisfatto tutti i requisiti, sarà possibile iniziare a [creare un'offerta di applicazione di Azure](./cpp-create-offer.md). 
 
