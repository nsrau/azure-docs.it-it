---
title: Offrendo servizi di Azure Stack | Microsoft Docs
description: Come operatore di cloud, è possibile offrire servizi per gli utenti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 4deb72eae7dffac6eabb34b18a9e879ac1fd8113
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179961"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Panoramica dell'offerta di servizi in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

[Microsoft Azure Stack](azure-stack-poc.md) è una piattaforma di cloud ibrido che ti permette di offrire servizi dal Data Center. Come provider di servizi, è possibile offrire servizi ai tenant. All'interno di un'azienda o l'ente governativo, è possibile offrire servizi locali per i dipendenti. 

È possibile offrire [infrastruttura come servizio](https://azure.microsoft.com/overview/what-is-iaas/) servizi (IaaS) che consentono agli utenti di compilare un'infrastruttura di calcolo on demand, il provisioning e gestite dal portale per gli utenti Azure Stack.

È anche possibile distribuire [piattaforma distribuita come servizio](https://azure.microsoft.com/overview/what-is-paas/) servizi (PaaS) per Azure Stack da Microsoft e altri provider di terze parti 3rd. I servizi che è possibile recapitare includono, ma non sono limitati a:

- [Aggiungere un provider di risorse del servizio app ad Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview)

- [Aggiungere un provider di risorse di SQL Server in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy)

- [Aggiungere un provider di risorse MySQL Server in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy)


È anche possibile combinare servizi per l'integrazione e la creazione di soluzioni complesse per utenti diversi.

Per distribuire questi servizi per gli utenti, è necessario creare innanzitutto [piani, offerte e quote](azure-stack-plan-offer-quota-overview.md). Gli utenti possono quindi sottoscrivono le offerte di utilizzare i servizi.

## <a name="plan-your-service-offers"></a>Pianificare le offerte di servizio

Quando si prevede le offerte, tenere presente quanto riportato di seguito:

**Offerte di prova**: È possibile utilizzare le offerte di prova per attrarre nuovi utenti, che possono quindi eseguire l'aggiornamento a servizi aggiuntivi. Per creare un'offerta di valutazione, creare un piccolo [piano base](azure-stack-plan-offer-quota-overview.md#base-plan) con un piano aggiuntivo più grande facoltativo.

**Pianificazione della capacità**: È possibile evitare che gli utenti che rilevano grandi quantità di risorse e bloccano il sistema per tutti gli utenti. Per migliorare le prestazioni, è possibile [configurare i piani con quote](azure-stack-plan-offer-quota-overview.md#plans) all'utilizzo di criteri di autorizzazione connessioni.

**Delega provider**: È possibile concedere ad altri utenti la possibilità di creare offerte nell'ambiente in uso. Ad esempio, se si ha un provider di servizi, è possibile [delegare](azure-stack-delegated-provider.md) questa possibilità per i rivenditori. In alternativa, se si ha un'organizzazione, è possibile delegare a altre divisioni/controllate.

## <a name="next-steps"></a>Passaggi successivi

[Creare un'offerta in Azure Stack](azure-stack-create-offer.md)
