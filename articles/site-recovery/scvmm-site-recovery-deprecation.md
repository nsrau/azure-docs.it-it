---
title: Imminente deprecazione del ripristino di emergenza tra i siti di proprietà del cliente che usano Hyper-V e tra siti gestiti da SCVMM in Azure | Microsoft Docs
description: Informazioni dettagliate sulla deprecazione imminente del ripristino di emergenza tra i siti di proprietà del cliente che usano Hyper-V e tra siti gestiti da SCVMM in Azure e opzioni alternative
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 68881d57f251d2f26996b2a837780106326ec1d3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492291"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Imminente deprecazione del ripristino di emergenza tra siti di proprietà del cliente che usano Hyper-V e tra siti gestiti da SCVMM in Azure

Questo articolo descrive il piano di deprecazione imminente, le implicazioni corrispondenti e le opzioni alternative disponibili per i clienti per gli scenari seguenti supportati da Azure Site Recovery. 

- RIPRISTINO di emergenza tra macchine virtuali Hyper-V gestite da SCVMM tra siti di proprietà del cliente 
- RIPRISTINO di emergenza di macchine virtuali Hyper-V gestite da SCVMM in Azure

> [!IMPORTANT]
> Questi scenari sono attualmente contrassegnati per la deprecazione e i clienti si aspettano di eseguire i passaggi correttivi prima di evitare eventuali problemi nell'ambiente. 
 

## <a name="what-changes-should-you-expect"></a>Quali modifiche si aspettano?

- A partire dal 2019 novembre, per questi scenari non saranno consentiti nuovi utenti in bacheca. **Le operazioni di replica e di gestione esistenti** , tra cui il failover, il failover di test, il monitoraggio e così via, **non saranno interessate**.

- Una volta che gli scenari sono stati deprecati, si verificano le implicazioni seguenti, a meno che il cliente non segua la procedura consigliata.

    - RIPRISTINO di emergenza tra macchine virtuali Hyper-V gestite da SCVMM tra siti di proprietà del cliente: le repliche continueranno a funzionare, perché la funzionalità sottostante della replica Hyper-V continuerà a funzionare, ma i clienti non saranno in grado di visualizzare, gestire o eseguire operazioni relative al ripristino di emergenza. tramite l'esperienza di ripristino di Azure Sire nel portale di Azure. 
    - RIPRISTINO di emergenza di macchine virtuali Hyper-V gestite da SCVMM in Azure: le repliche esistenti verranno interrotte e i clienti non saranno in grado di visualizzare, gestire o eseguire operazioni relative al ripristino di emergenza tramite il Azure Site Recovery


## <a name="recommended-actions-to-be-taken"></a>Azioni consigliate da intraprendere

Di seguito sono riportate le opzioni che il cliente deve verificare che la strategia di ripristino di emergenza non abbia alcun effetto quando lo scenario è deprecato. 

- Scegliere di [iniziare a usare Azure come destinazione di ripristino di emergenza per le macchine virtuali in host Hyper-V](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Si noti che l'ambiente locale può ancora avere SCVMM, ma sarà necessario configurare ASR con i riferimenti solo agli host Hyper-V.

- Scegliere di continuare con la replica da sito a sito ma usando la [soluzione di replica Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)sottostante, ma non sarà in grado di gestire le configurazioni di ripristino di emergenza usando Azure Site Recovery nel portale di Azure. 


## <a name="next-steps"></a>Passaggi successivi
Pianificare la deprecazione e scegliere un'opzione alternativa più adatta per l'infrastruttura e l'azienda. Nel caso in cui siano presenti query relative a questo, contattare supporto tecnico Microsoft

