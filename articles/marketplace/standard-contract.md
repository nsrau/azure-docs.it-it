---
title: Il contratto standard | Azure
description: Contratto standard in Azure Marketplace e AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620349"
---
# <a name="standard-contract"></a>Contratto standard

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità legali per i fornitori di software, Microsoft offre un modello di contratto Standard per facilitare una transazione in marketplace. Piuttosto che alla creazione personalizzate termini e condizioni, gli editori di Marketplace di Azure è possono scegliere di offrire il proprio software nell'ambito del contratto Standard, che i clienti devono solo esaminare ed accettare una sola volta. Il contratto Standard è disponibile qui: [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178). 

Le condizioni per un'offerta sono definite nella scheda Marketplace durante la creazione di un'offerta nel portale Cloud Partner. L'opzione Standard contratto è abilitata modificando l'impostazione su Sì.

![Abilitazione dell'opzione Standard contratto](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Se si sceglie di utilizzare il contratto Standard, per separare i termini e condizioni sono ancora necessari per il [Cloud Solution Provider](./cloud-solution-providers.md) canale.

## <a name="standard-contract-amendments"></a>Modifiche del contratto standard

Modifiche del contratto standard consentono agli editori di selezionare il contratto standard per motivi di semplicità e con condizioni personalizzate per i propri prodotti o business.  I clienti devono solo controllare le modifiche al contratto, se si dispone già esaminato e accettato il contratto Standard Microsoft.

Sono disponibili due tipi di modifiche per gli editori di Marketplace di Azure:

* Modifiche universale: Tali modifiche vengono applicate universalmente per il contratto standard per tutti i clienti. Modifiche di universale vengono visualizzate per ogni cliente del prodotto nel flusso di acquisto.

![Modifiche di universale](media/marketplace-publishers-guide/universal-amendaments.png)

* Modifiche personalizzate: Azure Marketplace include anche il provisioning per le modifiche personalizzate ai tenant di destinazione. Sono speciali modifiche per il contratto standard che hanno come destinazione solo per alcuni clienti. Gli editori possono scegliere il tenant che desiderano di destinazione. I clienti dal tenant potrebbero acquistare il prodotto nel contratto di Standard e le modifiche di destinazione.

![Modifiche personalizzate](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Clienti di destinazione con modifiche personalizzate potranno inoltre ottenere il regolamento relativo alle universal le condizioni standard durante l'acquisto.

>[!Note]
>I seguenti tipi di offerta di supportano Standard le modifiche del contratto: Le applicazioni di Azure (modelli di soluzioni e le applicazioni gestite), le macchine virtuali, contenitori, le applicazioni del contenitore.

### <a name="customer-experience"></a>Esperienza dei clienti

Durante il processo di acquisto nel portale di Azure, i clienti saranno in grado di visualizzare le condizioni associate al prodotto come il contratto Standard Microsoft e le modifiche.

![L'esperienza dell'utente del portale di Azure.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

I clienti possono usare `Get-AzureRmMarketplaceTerms` per recuperare le condizioni per un'offerta e accettarla. Il contratto standard e le modifiche associate verranno restituite nell'output del cmdlet.

---
