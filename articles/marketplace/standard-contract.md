---
title: Contratto standard per il marketplace commerciale Microsoft
description: Contratto standard per Azure Marketplace e AppSource nel Centro per i partner
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: dsindona
ms.openlocfilehash: 53a22140fe62ce0db18c1d02d816eb31b12a381b
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714152"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Contratto standard per il marketplace commerciale Microsoft

Microsoft offre un contratto standard per il marketplace commerciale Microsoft che semplifica il processo di acquisto per i clienti, riduce la complessità giuridica per i fornitori di software e agevola le transazioni nel marketplace. Invece di creare termini e condizioni personalizzati, in qualità di editore del marketplace commerciale è possibile scegliere di offrire il proprio software con il [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178), che i clienti devono solo esaminare e accettare una sola volta.

I termini e le condizioni di un'offerta vengono definiti durante la creazione dell'offerta nel Centro per i partner. È possibile scegliere di usare il contratto standard per il marketplace commerciale Microsoft anziché fornire termini e condizioni personalizzati.

>[!Note]
>Dopo la pubblicazione di un'offerta con il contratto standard per il marketplace commerciale Microsoft, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione con il contratto standard *o* con i propri termini e condizioni. Se si vogliono modificare le condizioni del contratto standard, è possibile usare le modifiche del contratto standard stesso.

## <a name="standard-contract-amendments"></a>Modifiche del contratto standard

Le modifiche del contratto standard consentono agli editori di selezionare il contratto standard per semplicità e di aggiungere termini personalizzati per il prodotto o l'azienda. I clienti devono solo verificare le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori del marketplace commerciale sono disponibili due tipi di modifiche:

* Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Le modifiche universali vengono visualizzate a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e la relativa modifica prima di poter usare l'offerta.

* Modifiche personalizzate: si tratta di modifiche speciali apportate al contratto standard destinate a specifici clienti solo tramite ID tenant di Azure. Gli editori possono scegliere il tenant di destinazione. Solo ai clienti del tenant verranno presentate le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta.  Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

>[!Note]
>Questi due tipi di modifiche si integrano tra loro. I clienti destinatari delle modifiche personalizzate riceveranno anche la modifica universale al contratto standard durante l'acquisto.

È possibile usare il contratto standard per il marketplace commerciale Microsoft per i tipi di offerta seguenti:  Applicazioni Azure (modelli di soluzioni e applicazioni gestite), macchine virtuali e SaaS.

## <a name="customer-experience"></a>Esperienza dei clienti

Durante l'esperienza di esplorazione in Azure Marketplace o AppSource, i clienti potranno visualizzare i termini associati all'offerta come contratto standard per il marketplace commerciale Microsoft e le eventuali modifiche universali.

![Esperienza di esplorazione del cliente nel portale di Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante il processo di acquisto nel portale di Azure, i clienti potranno vedere le condizioni associate all'offerta come contratto standard per il marketplace commerciale Microsoft e qualsiasi modifica universale e/o specifica per il tenant.

![Esperienza di acquisto del cliente nel portale di Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

I clienti possono usare Get-AzureRmMarketplaceTerms per recuperare le condizioni di un'offerta e accettarle. Il contratto standard e le modifiche associate verranno restituiti nell'output del cmdlet.
