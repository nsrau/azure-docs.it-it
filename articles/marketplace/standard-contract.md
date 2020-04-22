---
title: Contratto Standard Azure Marketplace
description: Contratto standard per Azure Marketplace e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681455"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per il mercato commerciale Microsoft

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità legale per i fornitori di software, Microsoft offre un contratto standard per il mercato commerciale Microsoft per facilitare le transazioni nel marketplace. Invece di creare termini e condizioni personalizzati, gli editori del mercato commerciale possono scegliere di offrire il proprio software ai sensi del Contratto Standard, che i clienti devono solo controllare e accettare una volta. Il Contratto Standard può [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)essere trovato qui: .

I termini e le condizioni per un'offerta vengono definiti durante la creazione dell'offerta nel Centro per i partner. È possibile scegliere di utilizzare il contratto standard per il marketplace commerciale Microsoft anziché fornire termini e condizioni personalizzati.

>[!Note]
>Una volta pubblicata un'offerta utilizzando il contratto Standard per il marketplace commerciale Microsoft, non è possibile utilizzare i termini e le condizioni personalizzati. Si tratta di uno scenario "o". Offrite la vostra soluzione in base al Contratto Standard *o* ai vostri termini e condizioni. Se si desidera modificare i termini del Contratto Standard, è possibile farlo attraverso le modifiche del contratto standard.

## <a name="standard-contract-amendments"></a>Modifiche del contratto standard

Le modifiche del contratto standard consentono agli editori di selezionare il contratto standard per semplicità e con termini personalizzati per il loro prodotto o azienda. I clienti devono rivedere le modifiche al contratto solo se hanno già esaminato e accettato il contratto standard Microsoft.

Sono disponibili due tipi di emendamenti per gli editori del mercato commerciale:

* Emendamenti universali: questi emendamenti sono applicati universalmente al contratto standard per tutti i clienti. Gli emendamenti universali vengono mostrati a tutti i clienti dell'offerta nel flusso di acquisto. I clienti devono accettare i termini del Contratto Standard e la modifica prima di poter utilizzare l'offerta.

* Modifiche personalizzate: queste modifiche sono modifiche speciali al contratto standard destinate a clienti specifici solo tramite ID tenant di Azure.Custom Amendments: These amendments are special amendments to the Standard Contract that are targeted to specific customers only via Azure tenant IDs. I publisher possono scegliere il tenant di destinazione. Solo ai clienti del tenant verranno presentati i termini di modifica personalizzati nel flusso di acquisto dell'offerta.  I clienti devono accettare i termini del Contratto Standard e gli emendamenti prima di poter utilizzare l'offerta.

>[!Note]
>Questi due tipi di emendamenti si sovrappongono l'uno sull'altro. I clienti mirati con modifiche personalizzate otterranno anche la modifica universale al contratto standard durante l'acquisto.

È possibile sfruttare il contratto standard per il marketplace commerciale Microsoft per i tipi di offerta seguenti: applicazioni di Azure (modelli di soluzione e applicazioni gestite), macchine virtuali, contenitori, applicazioni contenitore, moduli edge IoT e SaaS.You can leverage the Standard Contract for the Microsoft commercial marketplace for the following offer types: Azure Applications (Solution Templates and Managed Applications), Virtual Machines, Containers, Container Applications, IoT Edge Modules, and SaaS.

## <a name="customer-experience"></a>Esperienza cliente

Durante l'esperienza di individuazione nel marketplace di Azure o appSource, i clienti saranno in grado di visualizzare i termini associati all'offerta come contratto standard per il marketplace commerciale Microsoft e le eventuali modifiche universali.

![Esperienza di individuazione dei clienti del portale di Azure.The Azure portal customer discovery experience.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante il processo di acquisto nel portale di Azure, i clienti saranno in grado di visualizzare i termini associati all'offerta come contratto standard per il mercato commerciale Microsoft e qualsiasi modifica universale e/o specifica del tenant.

![Esperienza di acquisto del cliente del portale di Azure.The Azure portal customer purchase experience.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

I clienti possono usare Get-AzureRmMarketplaceTerms per recuperare i termini di un'offerta e accettarla. Il contratto standard e le modifiche associate verranno restituiti nell'output del cmdlet.
