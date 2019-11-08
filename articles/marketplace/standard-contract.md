---
title: Contratto standard | Azure
description: Contratto standard in Azure Marketplace e AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819502"
---
# <a name="standard-contract"></a>Contratto standard

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un modello di contratto standard per facilitare una transazione nel Marketplace. Anziché creare termini e condizioni personalizzati, gli editori di Azure Marketplace possono scegliere di offrire il proprio software al di sotto del contratto standard, che i clienti devono solo controllare e accettare una sola volta. Il contratto standard è disponibile qui: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

I termini e le condizioni per un'offerta vengono definiti nella scheda Marketplace quando si crea un'offerta nel portale Cloud Partner. L'opzione del contratto standard è abilitata modificando l'impostazione su Sì.

![Abilitazione dell'opzione del contratto standard](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Se si sceglie di utilizzare il contratto standard, i termini e le condizioni separate sono ancora necessari per il canale [Cloud Solution Provider](./cloud-solution-providers.md) .

## <a name="standard-contract-amendments"></a>Modifiche contrattuali standard

Gli emendamenti contrattuali standard consentono agli editori di selezionare il contratto standard per semplicità e con termini personalizzati per il prodotto o l'azienda.  I clienti devono solo esaminare le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori di Azure Marketplace sono disponibili due tipi di modifiche:

* Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Gli emendamenti universali vengono visualizzati a ogni cliente del prodotto nel flusso di acquisto.

![Modifiche universali](media/marketplace-publishers-guide/universal-amendaments.png)

* Modifiche personalizzate: Azure Marketplace dispone anche di un provisioning degli emendamenti personalizzati assegnati ai tenant. Sono modifiche speciali al contratto standard destinate solo a determinati clienti. Gli editori possono scegliere il tenant di destinazione. I clienti di tale tenant acquisteranno il prodotto nel contratto standard e gli emendamenti di destinazione.

![Modifiche personalizzate](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>I clienti destinati a modifiche personalizzate riceveranno anche la modifica universale delle condizioni standard durante l'acquisto.

>[!Note]
>I tipi di offerta seguenti supportano le modifiche contrattuali standard: applicazioni Azure (modelli di soluzioni e applicazioni gestite), macchine virtuali, contenitori, applicazioni contenitore.

### <a name="customer-experience"></a>Esperienza del cliente

Durante il processo di acquisto nel portale di Azure, i clienti potranno vedere le condizioni associate al prodotto come contratto standard Microsoft e le modifiche.

![Esperienza del cliente portale di Azure.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

I clienti possono utilizzare `Get-AzureRmMarketplaceTerms` per recuperare le condizioni di un'offerta e accettarle. Il contratto standard e le modifiche associate verranno restituiti nell'output del cmdlet.

---
