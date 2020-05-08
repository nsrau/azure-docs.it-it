---
title: Contratto standard per Microsoft Commercial Marketplace
description: Contratto standard per Azure Marketplace e AppSource nel centro per i partner
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 3886b29a47edcfb36114cedf8b8edb3799e0c345
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871427"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Contratto standard per Microsoft Commercial Marketplace

Microsoft offre un contratto standard per Microsoft Commercial Marketplace. Ciò consente di semplificare il processo di approvvigionamento per i clienti, ridurre la complessità giuridica per i fornitori di software e facilitare le transazioni nel Marketplace. Anziché creare termini e condizioni personalizzati, in qualità di editore del Marketplace commerciale, è possibile scegliere di offrire il proprio software al di sotto del [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178), che i clienti devono solo controllare e accettare una sola volta.

I termini e le condizioni per un'offerta vengono definiti durante la creazione dell'offerta nel centro per i partner. È possibile scegliere di utilizzare il contratto standard per Microsoft Commercial Marketplace anziché fornire termini e condizioni personalizzati.

>[!Note]
>Dopo la pubblicazione di un'offerta con il contratto standard per Microsoft Commercial Marketplace, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione in base al contratto standard *o* a termini e condizioni. Se si desidera modificare le condizioni del contratto standard, è possibile utilizzare le modifiche del contratto standard.

## <a name="standard-contract-amendments"></a>Modifiche contrattuali standard

Gli emendamenti contrattuali standard consentono agli editori di selezionare il contratto standard per semplicità e con termini personalizzati per il prodotto o l'azienda. I clienti devono solo esaminare le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori di Marketplace commerciali sono disponibili due tipi di modifiche:

* Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Gli emendamenti universali vengono visualizzati a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e la relativa rettifica prima di poter usare l'offerta.

* Modifiche personalizzate: queste modifiche sono modifiche speciali al contratto standard destinate a specifici clienti solo tramite ID tenant di Azure. Gli editori possono scegliere il tenant di destinazione. Solo i clienti del tenant verranno presentati con le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta.  Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

>[!Note]
>Questi due tipi di modifiche si impilano tra loro. I clienti destinati a modifiche personalizzate riceveranno anche la modifica universale del contratto standard durante l'acquisto.

È possibile utilizzare il contratto standard per Microsoft Commercial Marketplace per i tipi di offerta seguenti: applicazioni Azure (modelli di soluzioni e applicazioni gestite), macchine virtuali, contenitori, applicazioni contenitore, moduli IoT Edge e SaaS.

## <a name="customer-experience"></a>Esperienza cliente

Durante l'esperienza di individuazione in Azure Marketplace o AppSource, i clienti saranno in grado di visualizzare i termini associati all'offerta come contratto standard per il Marketplace commerciale Microsoft e gli eventuali emendamenti universali.

![Esperienza di individuazione del cliente portale di Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante il processo di acquisto nel portale di Azure, i clienti potranno vedere le condizioni associate all'offerta come contratto standard per il Marketplace commerciale Microsoft e qualsiasi modifica universale e/o specifica del tenant.

![Esperienza di acquisto del cliente portale di Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

I clienti possono usare Get-AzureRmMarketplaceTerms per recuperare le condizioni di un'offerta e accettarle. Il contratto standard e le modifiche associate verranno restituiti nell'output del cmdlet.
