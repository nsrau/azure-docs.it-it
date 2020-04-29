---
title: Guida alla pubblicazione dell'offerta per applicazioni gestite di Azure | Azure Marketplace
description: Questo articolo descrive i requisiti per la pubblicazione di un'applicazione gestita in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084873"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Applicazioni Azure: requisiti di pubblicazione dell'offerta di applicazioni gestite

Questo articolo illustra i requisiti per il tipo di offerta di applicazione gestita, un modo per pubblicare un'offerta di applicazione Azure in Azure Marketplace. Le applicazioni gestite sono offerte per transazioni distribuite e fatturate tramite Azure Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

Usare il tipo di offerta di applicazione gestita quando sono necessarie le condizioni seguenti:

- Si distribuisce una soluzione basata sulla sottoscrizione per il cliente usando una macchina virtuale o un'intera soluzione basata su IaaS.
- L'utente o il cliente richiedono che la soluzione venga gestita da un partner.

>[!NOTE]
>Ad esempio, un partner potrebbe essere un integratore di sistema o provider di servizi gestiti (MSP).  

## <a name="managed-application-offer"></a>Offerta applicazione gestita

|Requisiti |Dettagli  |
|---------|---------|
|Distribuito nella sottoscrizione di Azure di un cliente | Le app gestite devono essere distribuite nella sottoscrizione del cliente e possono essere gestite da terze parti. |
|Fatturazione e misurazione    |  Verrà effettuato il provisioning delle risorse nella sottoscrizione di Azure del cliente. Le macchine virtuali con pagamento in base al consumo (consumo) verranno sottoposte a transazione con il cliente tramite Microsoft, fatturate tramite la sottoscrizione di Azure del cliente (consumo). <br> Nel caso di Bring your own License, mentre Microsoft fattura i costi dell'infrastruttura sostenuti per la sottoscrizione del cliente, l'utente effettuerà la transazione delle tariffe di licenza software direttamente al cliente.        |
|Disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Per ulteriori informazioni sulla creazione di un disco rigido virtuale di Windows, vedere [creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il Marketplace. Se la comunicazione con il cliente rappresenta un problema, contattare i clienti interessati dopo aver attivato la condivisione dei clienti potenziali.  

>[!Note]
>Il consenso esplicito del canale partner Cloud Solution Provider (CSP) è ora disponibile. Per ulteriori informazioni sul marketing dell'offerta tramite i canali del partner Microsoft CSP, vedere [Cloud Solution Provider](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passaggi successivi

- Se non è già stato [fatto, vedere informazioni su](https://azuremarketplace.microsoft.com/sell) Azure Marketplace.
- [Accedere al centro](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per i partner per creare o completare l'offerta.
- Per ulteriori informazioni, [creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md) .
