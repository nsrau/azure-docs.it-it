---
title: Guida alla pubblicazione dell'offerta di applicazione gestita delle applicazioni di Azure
description: Questo articolo descrive i requisiti per pubblicare un'applicazione gestita nel Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: v-qiwe
ms.openlocfilehash: bdb2a54748c734d7b44dfa0b1f106d37e817f80e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822924"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Applicazioni di Azure: Guida alla pubblicazione dell'offerta di applicazione gestita

Un'applicazione gestita è uno dei modi principali per pubblicare una soluzione nel Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Sono offerte di transazioni che vengono distribuite e fatturate attraverso il Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

Usare l'app di Azure: tipo di offerta di app gestita quando sono necessarie le condizioni seguenti:
- Si distribuisce una soluzione basata su sottoscrizione per i clienti che usano una macchina virtuale o un'intera soluzione basata su IaaS.
- L'utente o il cliente richiedono che la soluzione venga gestita da un partner.

>[!NOTE]
>Ad esempio, un partner potrebbe essere un integratore di sistema o provider di servizi gestiti (MSP).  

## <a name="managed-application-offer"></a>Offerta applicazione gestita

|Requisiti |Dettagli  |
|---------|---------|
|Distribuito nella sottoscrizione di Azure di un cliente | Le app gestite devono essere distribuite nella sottoscrizione del cliente e possono essere gestite da terze parti. | 
|Fatturazione e misurazione    |  Verrà effettuato il provisioning delle risorse nella sottoscrizione di Azure del cliente. Le macchine virtuali con pagamento in base al consumo (consumo) verranno sottoposte a transazione con il cliente tramite Microsoft, fatturate tramite la sottoscrizione di Azure del cliente (consumo). <br> Nel caso di Bring your own License, mentre Microsoft fattura i costi dell'infrastruttura sostenuti per la sottoscrizione del cliente, l'utente effettuerà la transazione delle tariffe di licenza software direttamente al cliente.        |
|Disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, [vedere Creare un disco rigido virtuale compatibile con Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il Marketplace. Se la comunicazione con il cliente rappresenta un problema, contattare i clienti interessati dopo aver attivato la condivisione dei clienti potenziali.  

>[!Note]
>Il consenso esplicito del canale partner Cloud Solution Provider (CSP) è ora disponibile.  Per ulteriori informazioni sul marketing dell'offerta tramite i canali del partner Microsoft CSP, vedere [provider di soluzioni cloud](./cloud-solution-providers.md) .

## <a name="next-steps"></a>Passaggi successivi
Se non è già stato fatto, 

- [Registrarsi](https://azuremarketplace.microsoft.com/sell) in Marketplace.

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedere al portale cloud partner](https://cloudpartner.azure.com) per creare o completare l'offerta.
