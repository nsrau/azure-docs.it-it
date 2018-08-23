---
title: Consigli di Azure Advisor sulla disponibilità elevata | Microsoft Docs
description: Usare Azure Advisor per aumentare la disponibilità delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: 7bd0737e7fb26af95eed63696d1ac07c88a9dec4
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "42141121"
---
# <a name="advisor-high-availability-recommendations"></a>Consigli di Advisor sulla disponibilità elevata

Azure Advisor aiuta a garantire e migliorare la continuità delle applicazioni aziendali critiche. È possibile ottenere consigli da Advisor sulla disponibilità elevata nella scheda **Disponibilità elevata** del dashboard di Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Garantire la tolleranza di errore delle macchine virtuali

Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Advisor identifica le macchine virtuali che non fanno parte di un set di disponibilità e consiglia di trasferirle in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare un set di disponibilità per la macchina virtuale oppure aggiungere la macchina virtuale a un set di disponibilità esistente.

> [!NOTE]
> Se si sceglie di creare un set di disponibilità, è necessario aggiungere almeno un'altra macchina virtuale. È consigliabile raggruppare due o più macchine virtuali in un set di disponibilità per garantire la disponibilità di almeno una macchina durante un'interruzione.

## <a name="ensure-availability-set-fault-tolerance"></a>Garantire la tolleranza di errore del set di disponibilità 

Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Advisor identifica i set di disponibilità che contengono una singola macchina virtuale e consiglia di aggiungere una o più macchine virtuali. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare una macchina virtuale o di aggiungerne una esistente al set di disponibilità.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantire la tolleranza di errore del gateway applicazione
Per garantire la continuità aziendale delle applicazioni cruciali gestite dai gateway applicazione, Advisor identifica le istanze dei gateway applicazione non configurate per la tolleranza di errore e suggerisce le azioni correttive da intraprendere. Advisor identifica i gateway applicazione di medie o grandi dimensioni a istanza singola e consiglia di aggiungere almeno un'altra istanza. Identifica inoltre i gateway applicazione di piccole dimensioni a una o più istanze e consiglia di eseguire la migrazione a SKU di medie o grandi dimensioni. Advisor consiglia queste azioni per garantire che le istanze dei gateway applicazione siano configurate in modo da soddisfare gli attuali requisiti del contratto di servizio per tali risorse.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Migliorare le prestazioni e l'affidabilità dei dischi delle macchine virtuali

Advisor identifica le macchine virtuali con dischi Standard e consiglia di aggiornarle con dischi Premium.
 
Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con attività di I/O intensive. I dischi delle macchine virtuali che usano account di Archiviazione Premium memorizzano i dati in unità SSD. Per ottenere prestazioni ottimali per l'applicazione, è consigliabile eseguire la migrazione in Archiviazione Premium di tutti i dischi di macchine virtuali che richiedono un numero elevato di operazioni di I/O al secondo. 

Se i dischi non richiedono un numero elevato di operazioni di I/O al secondo, è possibile limitare i costi mantenendoli nel servizio di archiviazione Standard. L'archiviazione Standard memorizza i dati dei dischi delle macchine virtuali nelle unità disco rigido anziché nelle unità SSD. È possibile scegliere di eseguire la migrazione dei dischi delle macchine virtuali a dischi Premium. I dischi Premium sono supportati nella maggior parte degli SKU delle macchine virtuali. Tuttavia, in alcuni casi, per usare i dischi Premium potrebbe essere necessario aggiornare anche lo SKU della macchina virtuale.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteggere i dati delle macchine virtuali da eliminazioni accidentali

L'impostazione del backup nelle macchine virtuali garantisce la disponibilità dei dati aziendali critici e la protezione contro le eliminazioni o i danni accidentali.  Advisor identifica le macchine virtuali in cui il backup non è abilitato e consiglia di abilitare il backup. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Verificare di poter consultare gli esperti di cloud di Azure quando necessario

Quando si esegue un carico di lavoro aziendale critico, è importante avere accesso al supporto tecnico se necessario. Advisor identifica potenziali sottoscrizioni aziendali critiche che non hanno il supporto tecnico incluso nel piano di supporto e consiglia di eseguire l'aggiornamento a un'opzione che includa il supporto tecnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Creare avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi in Azure

Si consiglia di impostare gli avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi di servizio in Azure. [Integrità dei servizi di Azure](https://azure.microsoft.com/features/service-health/) è un servizio gratuito che fornisce supporto e indicazioni personalizzati quando si verifica un problema di servizio in Azure. Advisor identifica le sottoscrizioni che non dispongono degli avvisi configurati e ne consiglia la creazione.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurazione degli endpoint di Gestione traffico per la resilienza

I profili di Gestione traffico con più di un endpoint dispongono di maggiore disponibilità in caso di anomalie di uno di essi. La collocazione degli endpoint in diverse aree migliora ulteriormente l'affidabilità del servizio. Advisor identifica i profili di Gestione traffico in cui è presente un solo endpoint e consiglia di aggiungerne almeno uno in un'altra regione.

Se tutti gli endpoint in un profilo di Gestione traffico configurato per il routing di prossimità si trovano nella stessa area, gli utenti di altre aree potrebbero subire ritardi di connessione. L'aggiunta o lo spostamento di un endpoint in un'altra area migliorerà le prestazioni generali e fornirà una migliore disponibilità se tutti gli endpoint in una regione presenta anomalie. Advisor identifica i profili di Gestione traffico configurati per il routing di prossimità in cui tutti gli endpoint si trovano nella stessa area e consiglia di aggiungere o spostare un endpoint in un'altra area Azure.

Se un profilo di Gestione traffico è configurato per il routing geografico, il traffico viene instradato verso gli endpoint in base ad aree definite. Se un'area presenta anomalie, non vi sono failover predefiniti. Avere un endpoint in cui il raggruppamento di area è configurato su "Tutto (mondo)" eviterà l’interruzione del traffico e migliorerà la disponibilità del servizio. Advisor identifica i profili di Gestione traffico configurati per il routing geografico dove non vi sono endpoint configurati per avere il raggruppamento di area come "Tutto (mondo)" e raccomanda di apportare le modifiche necessarie alla configurazione.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Come accedere ai consigli sulla disponibilità elevata in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Disponibilità elevata**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)

