---
title: Scenari di ripristino di emergenza per macchine virtuali di Azure | Documentazione Microsoft
description: Informazioni su cosa fare in caso di un'interruzione di servizio di Azure che influisce sulle macchine virtuali di Azure.
services: virtual-machines
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: fb986a41e33501ee71c93a48457ac4114e33c671
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Cosa fare in caso di un'interruzione di servizio di Azure che influisce sulle macchine virtuali di Azure
Microsoft si impegna costantemente per garantire agli utenti la disponibilità dei servizi in base alle esigenze. Eventi imprevisti possono, tuttavia, causare interruzioni non pianificate dei servizi.

La connettività e la disponibilità dei servizi Microsoft sono garantite da un contratto di servizio. I contratti di servizio relativi ai singoli servizi di Azure sono disponibili alla pagina [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

Azure offre già diverse funzionalità della piattaforma predefinite che supportano applicazioni a disponibilità elevata. Per altre informazioni su questi servizi, vedere [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Questo articolo illustra uno scenario reale di ripristino di emergenza, quando in un'intera area si verifica un'interruzione a causa di un grave disastro naturale o di un'interruzione diffusa del servizio. Si tratta di episodi rari, ma è necessario prepararsi alla possibilità che si verifichi un evento del genere. In caso di un'interruzione del servizio che interessa un'intera area, le copie ridondanti locali dei dati non saranno temporaneamente disponibili. Se è stata abilitata la replica geografica, esistono altre tre copie dei BLOB e delle tabelle di Archiviazione di Azure memorizzate in un'area diversa. Nel caso di un'interruzione a livello dell'intera area o di un'emergenza in cui l'area primaria non sia recuperabile, Azure esegue un nuovo mapping di tutte le voci DNS all'area con replica geografica.

Per semplificare la gestione di questi rari avvenimenti, di seguito vengono fornite indicazioni per le macchine virtuali di Azure in caso di interruzione del servizio nell'intera area in cui è distribuita la macchina virtuale di Azure.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opzione 1: avviare un failover con Azure Site Recovery
È possibile configurare Azure Site Recovery per le macchine virtuali in modo che sia possibile recuperare l'applicazione con un solo clic in pochi minuti. È possibile eseguire la replica in un'area di Azure di propria scelta che non sia limitata alle aree associate. Iniziare eseguendo [la replica delle macchine virtuali](https://aka.ms/a2a-getting-started). È possibile [creare un piano di ripristino](../site-recovery/site-recovery-create-recovery-plans.md) in modo da poter automatizzare tutto il processo di failover per l'applicazione. È possibile prima fare il [test dei failover](../site-recovery/site-recovery-test-failover-to-azure.md) senza alcun impatto sull'applicazione di produzione o sulla replica in corso. In caso di interruzione di un'area primaria basta [avviare un failover](../site-recovery/site-recovery-failover.md) e portare l'applicazione nell'area di destinazione.


## <a name="option-2-wait-for-recovery"></a>Opzione 2: attendere il ripristino
In tal caso, non è necessaria alcuna azione da parte dell'utente. Si sta lavorando per ripristinare la disponibilità dei servizi. È possibile vedere lo stato corrente del servizio nel [dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/).

Questa è l'opzione migliore se non sono stati configurati Azure Site Recovery, l'archiviazione con ridondanza geografica e accesso in lettura o l'archiviazione con ridondanza geografica precedente all'interruzione . Se è stata impostata l'archiviazione con ridondanza geografica o con ridondanza geografica e accesso in lettura per l'account di archiviazione in cui sono archiviati i dischi rigidi virtuali della VM, è possibile cercare di ripristinare il VHD immagine di base e usarlo per provare a effettuare il provisioning di una nuova VM. Questa non è un'opzione consigliata perché non offre alcuna garanzia di sincronizzazione dei dati. Di conseguenza, non è certo che questa opzione possa funzionare.


> [!NOTE]
> Tenere presente che non è possibile controllare questo processo e che verrà eseguito solo in caso di interruzioni del servizio a livello di area. Per questo motivo, è necessario affidarsi anche ad altre strategie di backup specifiche dell'applicazione per ottenere il massimo livello di disponibilità. Per altre informazioni, vedere la sezione [Strategie dei dati per il ripristino di emergenza](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Passaggi successivi

- Avviare [la protezione delle applicazioni in esecuzione sulle macchine virtuali di Azure](https://aka.ms/a2a-getting-started) usando Azure Site Recovery

- Per altre informazioni su come implementare una strategia di disponibilità elevata e ripristino di emergenza, vedere [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Per sviluppare una conoscenza tecnica approfondita delle funzionalità della piattaforma cloud, vedere [Indicazioni tecniche sulla resilienza di Azure](../resiliency/resiliency-technical-guidance.md).


- Per chiarimenti sulle istruzioni o per chiedere a Microsoft di eseguire le operazioni per proprio conto, contattare il [supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

