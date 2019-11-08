---
title: Prerequisiti dell'offerta applicazione Azure | Azure Marketplace
description: Prerequisiti per la pubblicazione di un'offerta di applicazione Azure in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 261af94e233bdb6189d7819f8f28c7e23b5dd112
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826150"
---
# <a name="azure-application-prerequisites"></a>Prerequisiti per un'applicazione Azure

Questo articolo descrive i prerequisiti tecnici e aziendali per la pubblicazione di un'offerta di un'applicazione gestita in Azure Marketplace.  Se non è già stato fatto, rivedere le seguenti fonti di informazioni:
- A seconda del tipo di SKU, [applicazioni Azure:](../../marketplace-solution-templates.md) Guida alla pubblicazione dell'offerta del modello di soluzione o [applicazioni Azure: Guida alla pubblicazione dell'offerta](../../marketplace-managed-apps.md) di applicazioni gestite
- [Creazione di modelli di soluzioni e applicazioni gestite per il video su Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)


## <a name="technical-requirements"></a>Requisiti tecnici

I requisiti tecnici includono gli elementi seguenti:

*   Modelli di Azure Resource Manager. Per altre informazioni, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Questo articolo descrive la struttura di un modello di Azure Resource Manager. Presenta le diverse sezioni di un modello e le proprietà disponibili in queste sezioni. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. 
* Modelli di avvio rapido di Azure.<br> Per altre informazioni, vedere:

  * [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). Distribuire le risorse di Azure attraverso Azure Resource Manager con i modelli offerti dalla community per incrementare la produttività. Gestione risorse di Azure consente di effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Si usa lo stesso modello per distribuire più volte l'applicazione durante ogni fase del ciclo di vita dell'applicazione.
  * [GitHub: Azure Resource Manager modelli di avvio rapido](https://github.com/azure/azure-quickstart-templates). Questo repository contiene tutti i modelli di Azure Resource Manager attualmente disponibili offerti dalla community. Un indice dei modelli in cui è possibile eseguire ricerche è disponibile all'indirizzo https://azure.microsoft.com/documentation/templates/.
* Creare la definizione dell'interfaccia utente<br>
Per altre informazioni, vedere [Creare l'interfaccia utente del portale di Azure per l'applicazione gestita](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Questo articolo illustra i concetti di base del file createUiDefinition.json. Il portale di Azure usa questo file per generare l'interfaccia utente per la creazione di un'applicazione gestita.


## <a name="business-requirements"></a>Requisiti aziendali

I requisiti aziendali includono gli obblighi procedurali, legali e contrattuali riportati di seguito:

* È necessario essere un editore di Marketplace cloud registrato. Se non si è registrati, seguire la procedura descritta nell'articolo [diventare un editore del Marketplace cloud](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Per l'accesso al portale Cloud Partner è consigliabile usare lo stesso account usato per la registrazione a Microsoft Developer Center. È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. Questo account non deve essere specifico per singoli servizi o offerte.

* La società (o la sua filiale) deve trovarsi in un paese di origine/area geografica supportato da Azure Marketplace. Per un elenco aggiornato di questi paesi/aree geografiche, vedere [criteri di partecipazione Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati da Azure Marketplace. Per altre informazioni, vedere le [opzioni di fatturazione](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) in Azure Marketplace.
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
  * Un'immagine del logo (in formato PNG) e nelle dimensioni di immagine fisse: 40 x 40 pixel, 90 x 90 pixel, 115 x 115 pixel e 255 x 115 pixel.
* Condizioni per l' *utilizzo* e documenti *informativi sulla privacy*
* Documentazione dell'applicazione
* Contatti del supporto tecnico


## <a name="next-steps"></a>Passaggi successivi

Dopo aver soddisfatto tutti i requisiti, sarà possibile iniziare a [creare un'offerta di applicazione di Azure](./cpp-create-offer.md). 
 
