---
title: Migliorare la disponibilità dell'applicazione con Azure Advisor | Microsoft Docs
description: Usare Azure Advisor per aumentare la disponibilità delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: d78d8a689b860162a742e85c155205f072a3667c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446782"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Migliorare la disponibilità dell'applicazione con Azure Advisor

Azure Advisor aiuta a garantire e migliorare la continuità delle applicazioni aziendali critiche. È possibile ottenere consigli da Advisor sulla disponibilità elevata nella scheda **Disponibilità elevata** del dashboard di Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Garantire la tolleranza di errore delle macchine virtuali

Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Advisor identifica le macchine virtuali che non fanno parte di un set di disponibilità e consiglia di trasferirle in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare un set di disponibilità per la macchina virtuale oppure aggiungere la macchina virtuale a un set di disponibilità esistente.

> [!NOTE]
> Se si sceglie di creare un set di disponibilità, è necessario aggiungere almeno un'altra macchina virtuale. È consigliabile raggruppare due o più macchine virtuali in un set di disponibilità per garantire la disponibilità di almeno una macchina durante un'interruzione.

## <a name="ensure-availability-set-fault-tolerance"></a>Garantire la tolleranza di errore del set di disponibilità

Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Advisor identifica i set di disponibilità che contengono una singola macchina virtuale e consiglia di aggiungere una o più macchine virtuali. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare una macchina virtuale o di aggiungerne una esistente al set di disponibilità.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Per migliorare l'affidabilità dei dati, usare il servizio Managed Disks

Le macchine virtuali contenute in un set di disponibilità con dischi che condividono account di archiviazione o unità di scalabilità di archiviazione, non sono resilienti agli errori di archiviazione di un'unità di scalabilità singola durante le interruzioni. Advisor identificherà questi set di disponibilità e consiglierà di eseguire la migrazione ad Azure Managed Disks. Assicura che i dischi di macchine virtuali diverse in un set di disponibilità siano sufficientemente isolati tra loro per evitare un singolo punto di guasto. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantire la tolleranza di errore del gateway applicazione

Seguendo questo consiglio si ha la sicurezza della continuità aziendale delle applicazioni cruciali gestite dai gateway applicazione. Advisor identifica le istanze dei gateway applicazione non configurate per la tolleranza di errore e suggerisce le azioni correttive da intraprendere. Advisor identifica i gateway applicazione di medie o grandi dimensioni a istanza singola e consiglia di aggiungere almeno un'altra istanza. Identifica inoltre i gateway applicazione di piccole dimensioni a una o più istanze e consiglia di eseguire la migrazione a SKU di medie o grandi dimensioni. Advisor consiglia queste azioni per garantire che le istanze dei gateway applicazione siano configurate in modo da soddisfare gli attuali requisiti del contratto di servizio per tali risorse.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteggere i dati delle macchine virtuali da eliminazioni accidentali

L'impostazione del backup nelle macchine virtuali garantisce la disponibilità dei dati aziendali critici e la protezione contro le eliminazioni o i danni accidentali. Advisor identifica le macchine virtuali in cui il backup non è abilitato e consiglia di abilitare il backup. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Verificare di poter consultare gli esperti di cloud di Azure quando necessario

Quando si esegue un carico di lavoro aziendale critico, è importante avere accesso al supporto tecnico se necessario. Advisor identifica potenziali sottoscrizioni aziendali critiche che non hanno il supporto tecnico incluso nel piano di supporto e consiglia di eseguire l'aggiornamento a un'opzione che includa il supporto tecnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Creare avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi in Azure

Si consiglia di impostare gli avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi di servizio in Azure. [Integrità dei servizi di Azure](https://azure.microsoft.com/features/service-health/) è un servizio gratuito che fornisce supporto e indicazioni personalizzati quando si verifica un problema di servizio in Azure. Advisor identifica le sottoscrizioni che non dispongono degli avvisi configurati e ne consiglia la creazione.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurazione degli endpoint di Gestione traffico per la resilienza

I profili di Gestione traffico con più di un endpoint dispongono di maggiore disponibilità in caso di anomalie di uno di essi. La collocazione degli endpoint in diverse aree migliora ulteriormente l'affidabilità del servizio. Advisor identifica i profili di Gestione traffico in cui è presente un solo endpoint e consiglia di aggiungerne almeno uno in un'altra regione.

Se tutti gli endpoint in un profilo di Gestione traffico configurato per il routing di prossimità si trovano nella stessa area, gli utenti di altre aree potrebbero subire ritardi di connessione. L'aggiunta o lo spostamento di un endpoint in un'altra area migliorerà le prestazioni generali e fornirà una migliore disponibilità se tutti gli endpoint in una regione presenta anomalie. Advisor identifica i profili di Gestione traffico configurati per il routing di prossimità in cui tutti gli endpoint si trovano nella stessa area. Consiglia quindi di aggiungere o spostare un endpoint in un'altra area di Azure.

Se un profilo di Gestione traffico è configurato per il routing geografico, il traffico viene instradato verso gli endpoint in base ad aree definite. Se un'area presenta anomalie, non vi sono failover predefiniti. Avere un endpoint in cui il raggruppamento di area è configurato su "Tutto (mondo)" eviterà l’interruzione del traffico e migliorerà la disponibilità del servizio. Advisor identifica i profili di Gestione traffico configurati per il routing geografico dove non sono presenti endpoint configurati per il raggruppamento di aree come "Tutte (mondo)". Consiglia di modificare la configurazione in modo che un endpoint sia impostato come "Tutte (mondo)".

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Usare l'eliminazione temporanea nell'account di Archiviazione di Azure per salvare e ripristinare i dati dopo la sovrascrittura o l'eliminazione accidentale.

Abilitare l'[eliminazione temporanea](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) nell'account di archiviazione per fare in modo che i BLOB eliminati passino in uno stato di eliminazione temporanea anziché essere definitivamente eliminati. Quando i dati vengono sovrascritti, viene generato uno snapshot eliminato temporaneamente in modo da salvare lo stato dei dati sovrascritti. Usando l'eliminazione temporanea, è possibile ripristinare i dati in caso di sovrascrittura o eliminazione accidentale. Advisor identifica gli account di Archiviazione di Azure per i quali non è abilitata l'eliminazione temporanea e suggerisce di abilitarla.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configurare il gateway VPN in modalità attiva-attiva per garantire la resilienza di connessione

Nella configurazione attivo / attivo, entrambe le istanze di un gateway VPN stabiliranno tunnel VPN S2S con il dispositivo VPN locale. Quando in un'istanza del gateway si verifica un evento di manutenzione pianificata o non pianificata, il traffico viene trasferito automaticamente sull'altro tunnel IPSec attivo. Azure Advisor identificherà i gateway VPN non configurati come attivo-attivo e consiglierà di configurarli per la disponibilità elevata.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Usare i gateway VPN di produzione per eseguire i carichi di lavoro di produzione

Azure Advisor controlla tutti i gateway VPN sono uno SKU Basic e consigliabile usare invece un SKU di produzione. Lo SKU Basic è progettato per scopi di sviluppo e test. Gli SKU di produzione offrono un numero maggiore di tunnel, supporto BGP, attivo-attivo opzioni di configurazione, personalizzato criteri Ipsec/IKE e maggiore stabilità e disponibilità.

## <a name="repair-invalid-log-alert-rules"></a>Correggere le regole di avviso di log non è valido

Azure Advisor rileverà le regole di avviso con query non valida specificata nella sezione condizione. Le regole di avviso del log vengono create in Monitoraggio di Azure e vengono usate per eseguire le query di analisi a intervalli specificati. I risultati della query determinano se è necessario attivare un avviso. Le query di analisi doverebbero diventare non valide nel corso del tempo a causa di modifiche nelle risorse, nelle tabelle o nei comandi di riferimento. Advisor consiglia di correggere la query nella regola di avviso per impedire che il recupero automatico-disabilitato e garantire una copertura monitoraggio delle risorse in Azure. [Altre informazioni sulla risoluzione dei problemi delle regole di avviso](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configurare la modalità di indicizzazione coerente per la raccolta di Cosmos DB

Contenitori di Azure Cosmos DB configurati con modalità di indicizzazione differita potrebbero influire negativamente la validità dei risultati della query. Advisor rileverà i contenitori configurati in questo modo e consigliabile passare alla modalità coerente. [Altre informazioni sull'indicizzazione criteri in Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurare i contenitori di Azure Cosmos DB con una chiave di partizione

Azure Advisor identifica le raccolte non partizionata Azure Cosmos DB che stanno per raggiungere la quota di archiviazione con provisioning. Consiglia di eseguire la migrazione di queste raccolte per le nuove raccolte con definizione di una chiave di partizione in modo che vengono automaticamente può essere scalate dal servizio. [Altre informazioni sulla scelta di una chiave di partizione](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Eseguire l'aggiornamento di Azure Cosmos DB .NET SDK alla versione più recente da Nuget

Azure Advisor identifica gli account Azure Cosmos DB che usano le versioni precedenti di .NET SDK e consigliabile eseguire l'aggiornamento alla versione più recente da Nuget per le correzioni più recenti miglioramenti delle prestazioni e nuove funzionalità. [Altre informazioni su Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Eseguire l'aggiornamento di Azure Cosmos DB Java SDK alla versione più recente da Maven

Azure Advisor identifica gli account Azure Cosmos DB che usano le versioni precedenti di Java SDK e consigliabile eseguire l'aggiornamento alla versione più recente da Maven per le correzioni più recenti miglioramenti delle prestazioni e nuove funzionalità. [Altre informazioni su Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Eseguire l'aggiornamento del connettore Spark di Azure Cosmos DB alla versione più recente da Maven

Azure Advisor identifica gli account Azure Cosmos DB che usano le versioni precedenti del connettore Spark per Cosmos DB e consigliabile eseguire l'aggiornamento alla versione più recente da Maven per le correzioni più recenti miglioramenti delle prestazioni e nuove funzionalità. [Altre informazioni sul connettore Spark per Cosmos DB](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Abilitare la replica delle macchine virtuali
Le macchine virtuali che non è abilitata in un'altra area la replica non sono resiliente alle interruzioni a livello di area. Replica di macchine virtuali riduce effetti negativi sulle attività aziendali durante il periodo di un'interruzione di area di Azure. Advisor rileverà le macchine virtuali che non dispongono della replica abilitato e consiglia l'abilitazione della replica in modo che in caso di interruzione, possono introdurre rapidamente backup delle macchine virtuali in un'area di Azure remota. [Altre informazioni sulla replica di macchine virtuali](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Come accedere ai consigli sulla disponibilità elevata in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Disponibilità elevata**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)

