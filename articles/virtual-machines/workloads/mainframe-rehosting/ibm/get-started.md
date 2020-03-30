---
title: Carichi di lavoro IBM in Azure Documenti Microsoft
description: Usa un emulatore mainframe e altri servizi dei partner Microsoft per ospitare nuovamente i carichi di lavoro IBM z/OS usando Microsoft Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834593"
---
# <a name="ibm-workloads-on-azure"></a>Carichi di lavoro IBM in Azure

Molti carichi di lavoro mainframe IBM basati su z/OS possono essere replicati in Azure senza alcuna perdita di funzionalità e senza che gli utenti notino le modifiche nei sistemi sottostanti. Il rehosting delle applicazioni in Azure offre le funzionalità simili a mainframe di cui hai bisogno, oltre all'elasticità, alla disponibilità e ai potenziali risparmi sui costi del cloud.

Azure supporta l'integrazione con gli ambienti mainframe IBM esistenti, consentendo di eseguire la migrazione delle soluzioni ibride per le esigenze, di eseguire soluzioni ibride e di eseguire la migrazione nel tempo. Sebbene sia possibile riscrivere completamente i programmi basati su mainframe esistenti per Azure, è più comune ospitarli nuovamente. La riscrittura aggiunge costi, complessità e tempo ai progetti di migrazione. Con il rehosting, è possibile:

- Spostare le applicazioni in un emulatore basato su cloud.

- Eseguire la migrazione del database in un database basato su cloud.

- Sostituire i moduli e il codice usando i motori di trasformazione del codice.

Inoltre, il software IBM, inclusi WebSphere e MQ, è ora presente in Azure Marketplace. Con una licenza per il software IBM, è possibile sfruttare la scalabilità dell'infrastruttura su richiesta fornita da Azure per avviare rapidamente una macchina virtuale.

È disponibile un ampio ecosistema di partner che consente di eseguire la migrazione dei sistemi mainframe IBM in Azure.An extensive partner ecosystem is available to help you migrate IBM mainframe systems to Azure. La maggior parte segue un approccio pragmatico di riutilizzo, ove possibile, prima di intraprendere una distribuzione graduale della riscrittura o della sostituzione delle applicazioni. Altre indicazioni e assistenza dai partner nel Centro migrazione mainframe di [Azure.](https://azure.microsoft.com/migration/mainframe/)

**Passaggi successivi**

- [Migrazione del mainframe: miti e fatti](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Installare IBM zD&ambiente di sviluppo/test T in AzureInstall IBM zD&T dev/test environment on Azure](./install-ibm-z-environment.md)
- [Impostare una distribuzione controllata da sviluppatori di applicazioni (ADCD) in IBM zD&T v1](./demo.md)
- [IBM DB2 pureScale su Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
