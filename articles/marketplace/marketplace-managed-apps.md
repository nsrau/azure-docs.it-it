---
title: Guida alla pubblicazione dell'offerta di applicazioni gestite dalle applicazioni di Azure Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare un'applicazione gestita in Azure Marketplace.This article describes the requirements to publish a managed application in the Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084873"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Applicazioni di Azure: requisiti di pubblicazione dell'offerta di applicazioni gestiteAzure Applications: Managed application offer publishing requirements

Questo articolo illustra i requisiti per il tipo di offerta dell'applicazione gestita, che è un modo per pubblicare un'offerta di applicazioni di Azure in Azure Marketplace.This article explains the requirements for the managed application offer type, which is one way to publish an Azure application offer in the Azure Marketplace. Le applicazioni gestite sono offerte di transazioni distribuite e fatturate tramite Azure Marketplace.Managed applications are transact offers that are deployed and billed through the Azure Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

Utilizzare il tipo di offerta dell'applicazione gestita quando sono necessarie le condizioni seguenti:Use the managed application offer type when the following conditions are required:

- Si distribuisce una soluzione basata su sottoscrizione per il cliente usando una macchina virtuale o un'intera soluzione basata su IaaS.You deploy a subscription-based solution for your customer using either a VM or an all IaaS-based solution.
- L'utente o il cliente richiedono che la soluzione venga gestita da un partner.

>[!NOTE]
>Ad esempio, un partner potrebbe essere un integratore di sistema o provider di servizi gestiti (MSP).  

## <a name="managed-application-offer"></a>Offerta applicazione gestita

|Requisiti |Dettagli  |
|---------|---------|
|Distribuito nella sottoscrizione di Azure di un clienteDeployed to a customer's Azure subscription | Le app gestite devono essere distribuite nella sottoscrizione del cliente e possono essere gestite da terze parti. |
|Fatturazione e misurazione    |  Verrà eseguito il provisioning delle risorse nella sottoscrizione di Azure del cliente. Le macchine virtuali PAYGO (Pay-as-you-go) verranno effettuate transazioni con il cliente tramite Microsoft, fatturate tramite la sottoscrizione di Azure del cliente (PAYGO). <br> Nel caso di licenza bring-your-own, mentre Microsoft addebiterà i costi dell'infrastruttura sostenuti nell'abbonamento del cliente, si correranno le spese di licenza del software al cliente direttamente.        |
|Disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale di Windows, vedere [Creare un'offerta](./partner-center-portal/create-new-azure-apps-offer.md)di applicazione Azure.For more information about creating a Windows VHD, see create an Azure application offer.</li> </ul> |

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il Marketplace. Se la comunicazione con il cliente rappresenta un problema, contattare i clienti interessati dopo aver attivato la condivisione dei clienti potenziali.  

>[!Note]
>L'opt-in del canale per i partner Cloud Solution Provider (CSP) è ora disponibile. Per ulteriori informazioni sul marketing dell'offerta tramite i canali partner CSP Microsoft, vedere [Cloud Solution Providers](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passaggi successivi

- Se non è già stato fatto, vedere Azure Marketplace.If you haven't already done so, [learn](https://azuremarketplace.microsoft.com/sell) about the Azure Marketplace.
- [Accedi al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare la tua offerta.
- [Creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md) per altre informazioni.
