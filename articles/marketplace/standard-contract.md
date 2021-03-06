---
title: Contratto standard per il marketplace commerciale Microsoft
description: Contratto standard per Azure Marketplace e AppSource nel Centro per i partner
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/20/2020
ms.openlocfilehash: 20a257bde6022249fd7b2ab875b94f356234b490
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488877"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Contratto standard per il marketplace commerciale Microsoft

Microsoft offre un contratto standard per il marketplace commerciale Microsoft che semplifica il processo di acquisto per i clienti, riduce la complessità giuridica per i fornitori di software e agevola le transazioni nel marketplace. Invece di creare termini e condizioni personalizzati, in qualità di editore del marketplace commerciale è possibile scegliere di offrire il proprio software con il [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178), che i clienti devono solo esaminare e accettare una sola volta.

I termini e le condizioni di un'offerta vengono definiti durante la creazione dell'offerta nel Centro per i partner. È possibile scegliere di usare il contratto standard per il marketplace commerciale Microsoft anziché fornire termini e condizioni personalizzati.

>[!Note]
>Dopo la pubblicazione di un'offerta con il contratto standard per Microsoft Commercial Marketplace, non sarà possibile usare i termini e le condizioni personalizzati. È possibile offrire la propria soluzione ai sensi del contratto standard *o* di termini e condizioni personalizzati. I termini e le condizioni personalizzati sono definiti a livello di offerta e si applicano a tutti i piani; scrivere i termini e le condizioni personalizzati nella pagina delle **Proprietà** dell'offerta nel centro per i partner. Se si desidera modificare le condizioni del contratto standard, è possibile eseguire questa operazione tramite le modifiche del contratto standard.

## <a name="standard-contract-amendments"></a>Modifiche del contratto standard

Le modifiche del contratto standard consentono agli editori di selezionare il contratto standard per semplicità e di aggiungere termini personalizzati per il prodotto o l'azienda. I clienti devono solo verificare le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori del marketplace commerciale sono disponibili due tipi di modifiche:

* Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Le modifiche universali vengono visualizzate a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e la relativa modifica prima di poter usare l'offerta.

* Modifiche personalizzate: si tratta di modifiche speciali apportate al contratto standard destinate a specifici clienti solo tramite ID tenant di Azure. Gli editori possono scegliere il tenant di destinazione. Solo ai clienti del tenant verranno presentate le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta.  Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

>[!Note]
>Questi due tipi di modifiche si integrano tra loro. I clienti destinatari delle modifiche personalizzate riceveranno anche la modifica universale al contratto standard durante l'acquisto. Gli emendamenti sono limitati a 4000 caratteri, inclusi gli spazi.

È possibile usare il contratto standard per il marketplace commerciale Microsoft per i tipi di offerta seguenti:  Applicazioni Azure (modelli di soluzioni e applicazioni gestite), macchine virtuali e SaaS.

## <a name="customer-experience"></a>Esperienza dei clienti

Durante l'esperienza di esplorazione in Azure Marketplace o AppSource, i clienti potranno visualizzare i termini associati all'offerta come contratto standard per il marketplace commerciale Microsoft e le eventuali modifiche universali.

![Esperienza di esplorazione del cliente nel portale di Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante il processo di acquisto nel portale di Azure, i clienti potranno vedere le condizioni associate all'offerta come contratto standard per il marketplace commerciale Microsoft e qualsiasi modifica universale e/o specifica per il tenant.

![Esperienza di acquisto del cliente nel portale di Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

I clienti possono usare Get-AzureRmMarketplaceTerms per recuperare le condizioni di un'offerta e accettarle. Il contratto standard e le modifiche associate verranno restituiti nell'output del cmdlet.
