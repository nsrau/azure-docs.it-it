---
title: Contratto standard | Azure
description: Contratto standard per Azure Marketplace e AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/19/2019
ms.author: v-chjen
ms.openlocfilehash: dc8edf2b6e4a1204e7edd515da9323896049eb13
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228233"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per Microsoft Commercial Marketplace

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard per Microsoft Commercial Marketplace allo scopo di semplificare le transazioni nel Marketplace. Anziché creare termini e condizioni personalizzati, gli editori del Marketplace commerciale possono scegliere di offrire il proprio software al di sotto del contratto standard, che i clienti devono solo controllare e accettare una sola volta. Il contratto standard è disponibile qui: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

I termini e le condizioni per un'offerta vengono definiti durante la creazione dell'offerta nel centro per i partner o nell'portale Cloud Partner. È possibile scegliere di utilizzare il contratto standard per Microsoft Commercial Marketplace anziché fornire termini e condizioni personalizzati.

>[!Note]
>Dopo la pubblicazione di un'offerta con il contratto standard per Microsoft Commercial Marketplace, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione in base al contratto standard *o* a termini e condizioni. Se si desidera modificare le condizioni del contratto standard, è possibile utilizzare le modifiche del contratto standard.

## <a name="standard-contract-amendments"></a>Modifiche contrattuali standard

Gli emendamenti contrattuali standard consentono agli editori di selezionare il contratto standard per semplicità e con termini personalizzati per il prodotto o l'azienda. I clienti devono solo esaminare le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori di Marketplace commerciali sono disponibili due tipi di modifiche:

* Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Gli emendamenti universali vengono visualizzati a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e la relativa rettifica prima di poter usare l'offerta.

* Modifiche personalizzate: queste modifiche sono modifiche speciali al contratto standard destinate a specifici clienti solo tramite ID tenant di Azure. Gli editori possono scegliere il tenant di destinazione. Solo i clienti del tenant verranno presentati con le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta.  Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

>[!Note]
>Questi due tipi di modifiche si impilano tra loro. I clienti destinati a modifiche personalizzate riceveranno anche la modifica universale del contratto standard durante l'acquisto.

È possibile utilizzare il contratto standard per Microsoft Commercial Marketplace per i tipi di offerta seguenti: applicazioni Azure (modelli di soluzioni e applicazioni gestite), macchine virtuali, contenitori, applicazioni contenitore, moduli IoT Edge e SaaS .

## <a name="customer-experience"></a>Esperienza del cliente

Durante l'esperienza di individuazione in Azure Marketplace o AppSource, i clienti saranno in grado di visualizzare i termini associati all'offerta come contratto standard per il Marketplace commerciale Microsoft e gli eventuali emendamenti universali.

![Esperienza di individuazione del cliente portale di Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante il processo di acquisto nel portale di Azure, i clienti potranno vedere le condizioni associate all'offerta come contratto standard per il Marketplace commerciale Microsoft e qualsiasi modifica universale e/o specifica del tenant.

![Esperienza di acquisto del cliente portale di Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

I clienti possono usare Get-AzureRmMarketplaceTerms per recuperare le condizioni di un'offerta e accettarle. Il contratto standard e le modifiche associate verranno restituiti nell'output del cmdlet.
