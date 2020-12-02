---
title: Che cos'è Collegamento privato di Azure?
description: Panoramica su funzionalità, architettura e implementazione di Collegamento privato di Azure. Informazioni sul funzionamento di Endpoint privati di Azure e del servizio Collegamento privato di Azure e su come usarli.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 09/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: f4803cc9871b71e65e4d94ed37d3f9771be1cae0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454964"
---
# <a name="what-is-azure-private-link"></a>Che cos'è Collegamento privato di Azure? 
Collegamento privato di Azure consente di accedere ai servizi PaaS di Azure, ad esempio Archiviazione di Azure e Database SQL, nonché ai servizi di proprietà di clienti/partner ospitati in Azure tramite un [endpoint privato](private-endpoint-overview.md) nella rete virtuale.

Il traffico tra la rete virtuale e il servizio attraversa la rete del backbone Microsoft. L'esposizione del servizio sulla rete Internet pubblica non è più necessaria. È possibile creare un [servizio Collegamento privato](private-link-service-overview.md) personale nella rete virtuale e distribuirlo ai clienti. Collegamento privato di Azure offre un'esperienza coerente di configurazione e utilizzo per i servizi PaaS di Azure, i servizi di proprietà dei clienti e quelli condivisi dei partner.

> [!IMPORTANT]
> Collegamento privato di Azure è ora disponibile a livello generale. L'endpoint privato e Collegamento privato (il servizio alla base di Load Balancer Standard) sono disponibili a livello generale. L'onboarding di soluzioni Azure PaaS diverse in Collegamento privato di Azure verrà eseguito in base a pianificazioni diverse. Per informazioni accurate sullo stato di Azure PaaS in Collegamento privato, vedere la sezione [Disponibilità](#availability) in questo articolo. Per informazioni sulle limitazioni note, vedere [Endpoint privato](private-endpoint-overview.md#limitations) e [Servizio Collegamento privato](private-link-service-overview.md#limitations). 

## <a name="key-benefits"></a>Vantaggi principali
Collegamento privato di Azure offre i vantaggi descritti di seguito.  
- **Accesso privato ai servizi nella piattaforma Azure**: connettere la rete virtuale ai servizi in Azure senza un indirizzo IP pubblico all'origine o alla destinazione. I provider possono offrire i servizi nella loro rete virtuale e i clienti possono accedervi nella loro rete virtuale locale. La piattaforma di Collegamento privato gestirà la connettività tra l'utente e i servizi tramite la rete backbone di Azure. 
 
- **Reti locali e con peering**: accedere ai servizi in esecuzione in Azure dall'ambiente locale tramite peering privato ExpressRoute, tunnel VPN e reti virtuali con peering usando endpoint privati. Non è necessario configurare il peering Microsoft ExpressRoute o attraversare Internet per raggiungere il servizio. Collegamento privato offre un modo sicuro per eseguire la migrazione dei carichi di lavoro ad Azure.
 
- **Protezione dall'esfiltrazione di dati**: un endpoint privato viene mappato a un'istanza di una risorsa PaaS invece che all'intero servizio. Gli utenti possono connettersi solo alla risorsa specifica. L'accesso a qualsiasi altra risorsa del servizio è bloccato. Questo meccanismo offre protezione dai rischi di perdita dei dati. 
 
- **Copertura globale**: è possibile connettersi privatamente a servizi in esecuzione in altre aree. La rete virtuale dell'utente può pertanto trovarsi nell'area A e connettersi ai servizi con Collegamento privato nell'area B.  
 
- **Estensione a servizi personalizzati**: la stessa esperienza e le stesse funzionalità possono essere sfruttate per offrire privatamente un servizio personalizzato agli utenti in Azure. Posizionando un servizio dietro Azure Load Balancer, è possibile abilitarlo per Collegamento privato. L'utente potrà quindi connettersi direttamente al servizio usando un endpoint privato nella propria rete virtuale. È possibile gestire queste richieste di connessione con un semplice flusso di chiamate di approvazione. Collegamento privato di Azure funziona anche per utenti e servizi appartenenti a tenant di Azure Active Directory diversi. 

## <a name="availability"></a>Disponibilità 
 La tabella seguente elenca i servizi di Collegamento privato e le aree in cui sono disponibili. 

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|Servizi di Collegamento privato dietro Azure Load Balancer | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina  | Supportati in Load Balancer Standard | GA <br/> [Informazioni su come creare un servizio Collegamento privato.](create-private-link-service-portal.md) |
| Archiviazione BLOB di Azure (con Data Lake Storage Gen2)       |  Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici       |  Supportato nel tipo di account per utilizzo generico V2 | GA <br/> [Informazioni su come creare un endpoint privato per l'archiviazione BLOB.](tutorial-private-endpoint-storage-portal.md)  |
| File di Azure | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici      | |   GA <br/> [Informazioni su come configurare gli endpoint di rete di File di Azure.](../storage/files/storage-files-networking-endpoints.md)   |
| Sincronizzazione file di Azure | Tutte le aree pubbliche      | |   GA <br/> [Informazioni su come configurare gli endpoint di rete di File di Azure.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Archiviazione code di Azure       |  Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici       |  Supportato nel tipo di account per utilizzo generico V2 | GA <br/> [Informazioni su come creare un endpoint privato per l'archiviazione code.](tutorial-private-endpoint-storage-portal.md) |
| Archiviazione tabelle di Azure       |  Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici       |  Supportato nel tipo di account per utilizzo generico V2 | GA <br/> [Informazioni su come creare un endpoint privato per l'archiviazione tabelle.](tutorial-private-endpoint-storage-portal.md)  |
|  Database SQL di Azure         | Tutte le aree pubbliche <br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina      |  Supportato per i [criteri di connessione](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy | GA <br/> [Informazioni su come creare un endpoint privato per Azure SQL.](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| Tutte le aree pubbliche <br/> Tutte le aree per enti pubblici |  Supportato per i [criteri di connessione](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy |GA <br/> [Informazioni su come creare un endpoint privato per Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici</br> Tutte le aree della Cina | |GA <br/> [Informazioni su come creare un endpoint privato per Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Database di Azure per PostgreSQL - Server singolo         | Tutte le aree pubbliche <br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina     | Supportato per i piani tariffari per utilizzo generico e con ottimizzazione per la memoria | GA <br/> [Informazioni su come creare un endpoint privato per Database di Azure per PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Database di Azure per MySQL         | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina      |  | GA <br/> [Informazioni su come creare un endpoint privato per Database di Azure per MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Database di Azure per MariaDB         | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina     |  | GA <br/> [Informazioni su come creare un endpoint privato per Database di Azure per MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |
|  Insieme di credenziali chiave di Azure         | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici      |  | GA   <br/> [Informazioni su come creare un endpoint privato per Azure Key Vault.](../key-vault/general/private-link-service.md)   |
|Servizio Azure Kubernetes - API Kubernetes | Tutte le aree pubbliche      |  | GA   <br/> [Informazioni su come creare un endpoint privato per il servizio Azure Kubernetes.](../aks/private-clusters.md)   |
|Ricerca di Azure | Tutte le aree pubbliche <br/> Tutte le aree per enti pubblici | Supportato con il servizio in modalità privata | GA   <br/> [Informazioni su come creare un endpoint privato per Ricerca di Azure.](../search/service-create-private-endpoint.md)    |
|Registro Azure Container | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici    | Supportato con il livello Premium del registro contenitori. [Selezionare per informazioni sui livelli di servizio](../container-registry/container-registry-skus.md)| GA   <br/> [Informazioni su come creare un endpoint privato per Registro Azure Container.](../container-registry/container-registry-private-link.md)   |
|Configurazione app di Azure | Tutte le aree pubbliche      |  | Anteprima  </br> [Informazioni su come creare un endpoint privato per Configurazione app di Azure.](../azure-app-configuration/concept-private-endpoint.md) |
|Backup di Azure | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici   |  | GA   <br/> [Informazioni su come creare un endpoint privato per Backup di Azure.](../backup/private-endpoints.md)   |
|Hub eventi di Azure | Tutte le aree pubbliche<br/>Tutte le aree per enti pubblici      |   | GA   <br/> [Informazioni su come creare un endpoint privato per Hub eventi di Azure.](../event-hubs/private-link-service.md)  |
|Bus di servizio di Azure | Tutte le aree pubbliche<br/>Tutte le aree per enti pubblici  | Supportato con il livello Premium del bus di servizio di Azure. [Selezionare per informazioni sui livelli di servizio](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Informazioni su come creare un endpoint privato per Bus di servizio di Azure.](../service-bus-messaging/private-link-service.md)    |
|Servizio di inoltro di Azure | Tutte le aree pubbliche      |  | Anteprima <br/> [Informazioni su come creare un endpoint privato per Inoltro di Azure.](../azure-relay/private-link-service.md)  |
|Griglia di eventi di Azure| Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici       |  | GA   <br/> [Informazioni su come creare un endpoint privato per Griglia di eventi di Azure.](../event-grid/network-security.md) |
|App Web di Azure | Tutte le aree pubbliche      | Supportato con il piano PremiumV2, PremiumV3 o Funzioni Premium  | GA   <br/> [Informazioni su come creare un endpoint privato per App Web di Azure.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Machine Learning | Tutte le aree pubbliche    |  | GA   <br/> [Informazioni su come creare un endpoint privato per Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |
| Automazione di Azure  | Tutte le aree pubbliche |  | Anteprima </br> [Informazioni su come creare un endpoint privato per Automazione di Azure.](../automation/how-to/private-link-security.md)| |
| Hub IoT Azure | Tutte le aree pubbliche    |  | GA   <br/> [Informazioni su come creare un endpoint privato per Hub IoT di Azure.](../iot-hub/virtual-network-support.md) |
| Servizio Azure SignalR | STATI UNITI ORIENTALI, STATI UNITI CENTRO-MERIDIONALI,<br/>STATI UNITI OCCIDENTALI 2, tutte le aree della Cina      |  | Anteprima   <br/> [Informazioni su come creare un endpoint privato per il Servizio Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
| Monitoraggio di Azure <br/>(Log Analytics e Application Insights) | Tutte le aree pubbliche      |  | GA   <br/> [Informazioni su come creare un endpoint privato per Monitoraggio di Azure.](../azure-monitor/platform/private-link-security.md)   | 
| Azure Batch | Tutte le aree pubbliche, ad eccezione di: Germania CENTRALE, Germania NORD-ORIENTALE <br/> Tutte le aree per enti pubblici  | | GA <br/> [Informazioni su come creare un endpoint privato per Azure Batch.](../batch/private-connectivity.md) |
|Azure Data Factory | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina    | Le credenziali devono essere archiviate in un insieme di credenziali delle chiavi di Azure| GA   <br/> [Informazioni su come creare un endpoint privato per Azure Data Factory.](../data-factory/data-factory-private-link.md)   |



Per le notifiche più aggiornate, vedere la [pagina degli aggiornamenti relativi al collegamento privato di Azure](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

Collegamento privato di Azure prevede l'integrazione con Monitoraggio di Azure. Questa combinazione consente:

 - Archiviazione di log in un account di archiviazione.
 - Streaming di eventi nell'hub eventi.
 - Registrazione di Monitoraggio di Azure.

In Monitoraggio di Azure è possibile accedere alle informazioni seguenti. 
- **Endpoint privato**: 
    - dati elaborati dall'endpoint privato (IN/OUT)
 
- **Servizio Collegamento privato**:
    - Dati elaborati dal servizio Collegamento privato (IN/OUT)
    - Disponibilità di porte NAT  
 
## <a name="pricing"></a>Prezzi   
Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Domande frequenti  
Per domande frequenti, vedere [Domande frequenti su Collegamento privato di Azure](private-link-faq.md).
 
## <a name="limits"></a>Limiti  
Per informazioni sui limiti, vedere [Limiti di Collegamento privato di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Contratto di servizio
Per informazioni, vedere [Contratto di servizio per Collegamento privato di Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Creare un endpoint privato con il portale di Azure](create-private-endpoint-portal.md)
- [Avvio rapido: Creare un servizio Collegamento privato usando il portale di Azure](create-private-link-service-portal.md)


