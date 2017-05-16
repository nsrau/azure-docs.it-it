---
title: Operazioni da eseguire in caso di un&quot;interruzione del servizio Azure con impatto sui servizi cloud di Azure | Microsoft Docs
description: Informazioni sulle operazioni da eseguire in caso di un&quot;interruzione del servizio Azure con impatto sui servizi cloud di Azure.
services: cloud-services
documentationcenter: 
author: mmccrory
manager: drewm
editor: 
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: mmccrory
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: b20f846caa12866ce8815c7931a2c66346cd4085
ms.lasthandoff: 04/04/2017


---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sui servizi cloud di Azure
Microsoft si impegna costantemente per garantire agli utenti la disponibilità dei servizi in base alle esigenze. Eventi imprevisti possono, tuttavia, causare interruzioni non pianificate dei servizi.

La connettività e la disponibilità dei servizi Microsoft sono garantite da un contratto di servizio. I contratti di servizio relativi ai singoli servizi di Azure sono disponibili alla pagina [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

Azure offre già diverse funzionalità della piattaforma predefinite che supportano applicazioni a disponibilità elevata. Per altre informazioni su questi servizi, vedere [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Questo articolo illustra uno scenario reale di ripristino di emergenza, quando in un'intera area si verifica un'interruzione a causa di un grave disastro naturale o di un'interruzione diffusa del servizio. Si tratta di episodi rari, ma è necessario prepararsi alla possibilità che si verifichi un evento del genere. In caso di un'interruzione del servizio che interessa un'intera area, le copie ridondanti locali dei dati non saranno temporaneamente disponibili. Se è stata abilitata la replica geografica, esistono altre tre copie dei BLOB e delle tabelle di Archiviazione di Azure memorizzate in un'area diversa. Nel caso di un'interruzione a livello dell'intera area o di un'emergenza in cui l'area primaria non sia recuperabile, Azure esegue un nuovo mapping di tutte le voci DNS all'area con replica geografica.

> [!NOTE]
> Tenere presente che non è possibile controllare questo processo e che verrà eseguito solo in caso di interruzioni del servizio a livello di data center. Per questo motivo, è necessario affidarsi anche ad altre strategie di backup specifiche dell'applicazione per ottenere il massimo livello di disponibilità. Per altre informazioni, vedere [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Per poter influire sul failover è consigliabile prendere in considerazione l'uso dell' [archiviazione con ridondanza geografica e accesso in lettura](../storage/storage-redundancy.md#read-access-geo-redundant-storage)che consente di creare una copia di sola lettura dei dati in un'altra area.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opzione 1: usare una distribuzione di backup tramite Gestione traffico di Azure
La soluzione di ripristino di emergenza più efficace prevede la gestione di più distribuzioni dell'applicazione in aree diverse, quindi l'uso di [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) per indirizzare il traffico tra esse. Gestione traffico di Azure fornisce più [metodi di routing](../traffic-manager/traffic-manager-routing-methods.md), in modo da poter scegliere se gestire le distribuzioni usando un modello principale/di backup o dividere il traffico tra esse.

![Bilanciamento dei servizi cloud di Azure in diverse aree con Gestione traffico di Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Per la risposta più veloce alla perdita di un'area, è importante configurare il [monitoraggio degli endpoint](../traffic-manager/traffic-manager-monitoring.md) di Gestione traffico.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opzione 2: distribuire l'applicazione in una nuova area
La gestione di più distribuzioni attive come descritto nell'opzione precedente comporta costi continui. Se l'obiettivo del tempo di ripristino (RTO) è sufficientemente flessibile e si dispone del codice originale o del pacchetto di servizi Cloud compilato, è possibile creare una nuova istanza dell'applicazione in un'altra area e aggiornare i record DNS in modo che puntino alla nuova distribuzione.

Per altre informazioni sulla creazione e la distribuzione di un'applicazione di servizio cloud, vedere [Come creare e distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md).

In base alle origini dati dell'applicazione, può essere necessario controllare le procedure di ripristino per l'origine dati dell'applicazione.

* Per le origini dati di Archiviazione di Azure, vedere [Replica di Archiviazione di Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) per controllare le opzioni disponibili in base al modello di replica scelto per l'applicazione.
* Per le origini di database SQL, leggere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](../sql-database/sql-database-business-continuity.md) per controllare le opzioni disponibili in base al modello di replica scelto per l'applicazione.


## <a name="option-3-wait-for-recovery"></a>Opzione 3: attendere il ripristino
In questo caso, non è necessario alcun intervento da parte dell'utente, ma il servizio non sarà disponibile finché non viene ripristinata l'area. È possibile vedere lo stato corrente del servizio nel [Dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come implementare una strategia di disponibilità elevata e ripristino di emergenza, vedere [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Per sviluppare una conoscenza tecnica approfondita delle funzionalità della piattaforma cloud, vedere [Indicazioni tecniche sulla resilienza di Azure](../resiliency/resiliency-technical-guidance.md).
