---
title: Prerequisiti dell'offerta di applicazioni Azure | Microsoft Docs
description: Prerequisiti per la pubblicazione di un'offerta di applicazione Azure in Azure Marketplace.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1fe847bd0cdceec7eccab8218ccf787d8f4366ba
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195821"
---
# <a name="azure-application-prerequisites"></a>Prerequisiti per un'applicazione Azure

Questo articolo descrive i prerequisiti tecnici e aziendali per la pubblicazione di un'offerta di un'applicazione gestita in Azure Marketplace.

## <a name="technical-requirements"></a>Requisiti tecnici

I requisiti tecnici includono gli elementi seguenti:

*   Modelli di Azure Resource Manager. Per altre informazioni, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Questo articolo descrive la struttura di un modello di Azure Resource Manager. Presenta le diverse sezioni di un modello e le proprietà disponibili in queste sezioni. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. 
* Modelli di avvio rapido di Azure.<br> Per altre informazioni, vedere:

  * [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). Distribuire le risorse di Azure attraverso Azure Resource Manager con i modelli offerti dalla community per incrementare la produttività. Gestione risorse di Azure consente di effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Si usa lo stesso modello per distribuire più volte l'applicazione durante ogni fase del ciclo di vita dell'applicazione.
  * [GitHub: Modelli di avvio rapido di Azure Resource Manager](https://github.com/azure/azure-quickstart-templates). Questo repository contiene tutti i modelli di Azure Resource Manager attualmente disponibili offerti dalla community. Un indice dei modelli in cui è possibile eseguire ricerche è disponibile all'indirizzo https://azure.microsoft.com/en-us/documentation/templates/.
* Creare la definizione dell'interfaccia utente<br>
Per altre informazioni, vedere [Creare l'interfaccia utente del portale di Azure per l'applicazione gestita](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Questo articolo illustra i concetti di base del file createUiDefinition.json. Il portale di Azure usa questo file per generare l'interfaccia utente per la creazione di un'applicazione gestita.

## <a name="business-requirements"></a>Requisiti aziendali

I requisiti aziendali includono gli obblighi procedurali, legali e contrattuali riportati di seguito:

* È necessario essere un editore di Marketplace cloud registrato. Se non è stata effettuata la registrazione, seguire la procedura nell'articolo Diventare un editore di Marketplace cloud.

>[!NOTE]
>Per l'accesso al portale Cloud Partner è consigliabile usare lo stesso account usato per la registrazione a Microsoft Developer Center. È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. L’account non deve essere specifico di servizi o offerte singole.

* La società (o l'affiliata) deve trovarsi in uno dei paesi di origine della vendita supportati da Azure Marketplace. Per un elenco aggiornato di questi paesi, vedere [Criteri di Partecipazione a Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati da Azure Marketplace. Per altre informazioni, vedere le [opzioni di fatturazione](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) in Azure Marketplace.
* L'utente ha la responsabilità di mettere a disposizione dei clienti il team di supporto in modo ragionevole da un punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite la community.
* L'utente ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.
* È necessario offrire contenuti che soddisfino i criteri perché l'offerta sia inserita in Azure Marketplace e nel portale di Azure.
* È necessario accettare le condizioni dei Criteri di partecipazione a Microsoft Azure Marketplace e del contratto per editori.
* È necessario rispettare le Condizioni per l'utilizzo del sito Web di Microsoft Azure, l'Informativa sulla privacy di Microsoft e il Contratto del Programma Microsoft Azure Certified.

## <a name="publishing-requirements"></a>Requisiti per la pubblicazione

Per pubblicare una nuova offerta di applicazione Azure, è necessario soddisfare i prerequisiti seguenti:

* Disporre dei metadati pronti per l'uso. L'elenco seguente (non completo) mostra un esempio di questi metadati:
  * Un titolo
  * Una descrizione (in formato HTML)
  * Un'immagine del logo (in formato PNG), con queste dimensioni fisse dell'immagine: 40 x 40 pixel, 90 x 90 pixel, 115 x 115 pixel e 255 x 115 pixel.
* Condizioni per l'utilizzo e Informativa sulla privacy
* Documentazione
* Contatti del supporto tecnico

## <a name="next-steps"></a>Passaggi successivi

[Creare un'offerta di applicazione Azure](./cpp-create-offer.md) 
