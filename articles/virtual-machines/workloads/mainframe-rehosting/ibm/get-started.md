---
title: Carichi di lavoro IBM in Azure | Microsoft Docs
description: Usare un emulatore mainframe e altri servizi dei partner Microsoft per riospitare i carichi di lavoro IBM z/OS con Microsoft Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834593"
---
# <a name="ibm-workloads-on-azure"></a>Carichi di lavoro IBM in Azure

Molti carichi di lavoro IBM mainframe basati su z/OS possono essere replicati in Azure senza alcuna perdita di funzionalità e senza che gli utenti abbiano notato modifiche nei sistemi sottostanti. La riallocazione di applicazioni in Azure ti offre le funzionalità di tipo mainframe che ti servono e l'elasticità, la disponibilità e il risparmio di costi potenziali del cloud.

Azure supporta l'integrazione con gli ambienti mainframe IBM esistenti, consentendo di eseguire la migrazione di applicates che hanno senso, di eseguire soluzioni ibride laddove necessario e di eseguire la migrazione nel tempo. Sebbene sia possibile riscrivere completamente programmi basati su mainframe esistenti per Azure, è più comune riallocarli. La riscrittura aggiunge i costi, la complessità e il tempo per i progetti di migrazione. Con la riallocazione è possibile:

- Spostare le applicazioni in un emulatore basato sul cloud.

- Eseguire la migrazione del database in un database basato su cloud.

- Sostituire i moduli e il codice usando i motori di trasformazione del codice.

Inoltre, il software IBM, incluso WebSphere e MQ, è ora disponibile in Azure Marketplace. Con una licenza per il software IBM, puoi usufruire della scalabilità dell'infrastruttura on demand offerta da Azure per avviare rapidamente una macchina virtuale.

È disponibile un ampio ecosistema di partner per facilitare la migrazione dei sistemi mainframe IBM ad Azure. Per la maggior parte, seguire un approccio pragmatico al riutilizzo, laddove possibile, prima di intraprendere una distribuzione graduale di riscrittura o sostituzione di applicazioni. Ottieni altre indicazioni e guida dai partner presso il [centro migrazione del mainframe di Azure](https://azure.microsoft.com/migration/mainframe/).

**Passaggi successivi**

- [Migrazione del mainframe: miti e fact](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Installare l'ambiente di sviluppo/test IBM zD & T in Azure](./install-ibm-z-environment.md)
- [Configurare una distribuzione controllata per gli sviluppatori di applicazioni (ADCD) in IBM zD & T V1](./demo.md)
- [IBM DB2 pureScale in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
