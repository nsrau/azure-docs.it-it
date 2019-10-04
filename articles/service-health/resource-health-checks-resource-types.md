---
title: Tipi di risorse supportati tramite Integrità risorse di Azure|Microsoft Docs
description: Tipi di risorse supportati tramite Integrità risorse di Azure
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: 1692e8acda88a51173eb0eabcd90c377b431a3c3
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955027"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipi di risorse e controlli di integrità in Integrità risorse di Azure
Di seguito è riportato un elenco completo di tutti i controlli eseguiti tramite Integrità risorse suddivisi per tipi di risorse.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Controlli eseguiti|
|---|
|<ul><li>Il server è operativo?</li><li>La memoria del server è insufficiente?</li><li>Il server è in fase di avvio?</li><li>Il server è in fase di recupero?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Controlli eseguiti|
|---|
|<ul><li>Il servizio Gestione API è operativo?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Controlli eseguiti|
|---|
|<ul><li>L'account batch è in esecuzione?</li><li>È stata superata la quota del pool per questo account batch?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Controlli eseguiti|
|---|
|<ul><li>Tutti i nodi della cache sono operativi?</li><li>La cache è raggiungibile dal data center?</li><li>La cache ha raggiunto il numero massimo di connessioni?</li><li> La cache ha esaurito la memoria disponibile? </li><li>La cache sta riscontrando un numero elevato di errori di pagina?</li><li>Il carico della cache è elevato?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Controlli eseguiti|
|---|
|<ul> <li>Il portale supplementare è accessibile per le operazioni di configurazione della rete CDN?</li><li>Si stanno verificando problemi di recapito con gli endpoint della rete CDN?</li><li>Gli utenti possono modificare la configurazione delle risorse della rete CDN?</li><li>Le modifiche di configurazione si propagano come previsto?</li><li>Gli utenti possono gestire la configurazione della rete CDN tramite il portale di Azure, PowerShell o l'API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Controlli eseguiti|
|---|
|<ul><li>Il server host è operativo?</li><li>L'avvio del sistema operativo host è stato completato?</li><li>Sono stati eseguiti il provisioning e l'accensione del contenitore della macchina virtuale?</li><li>È disponibile la connettività di rete tra l'host e l'account di archiviazione?</li><li>L'avvio del sistema operativo guest è stato completato?</li><li>È in corso la manutenzione pianificata?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Controlli eseguiti|
|---|
|<ul><li>L'account è raggiungibile dal data center?</li><li>È disponibile il provider di risorse Servizi cognitivi?</li><li>I Servizi cognitivi sono disponibili nell'area appropriata?</li><li>Le operazioni di lettura possono essere eseguite nell'account di archiviazione contenente i metadati delle risorse?</li><li>È stata raggiunta la quota di chiamate dell'API?</li><li>È stato raggiunto il limite di lettura delle chiamate dell'API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Controlli eseguiti|
|---|
|<ul><li>Il server che ospita la macchina virtuale è operativo?</li><li>L'avvio del sistema operativo host è stato completato?</li><li>Sono stati eseguiti il provisioning e l'accensione del contenitore della macchina virtuale?</li><li>È disponibile la connettività di rete tra l'host e l'account di archiviazione?</li><li>L'avvio del sistema operativo guest è stato completato?</li><li>È in corso la manutenzione pianificata?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/Factory
|Controlli eseguiti|
|---|
|<ul><li>Si sono verificati errori di esecuzione della pipeline?</li><li>Il cluster ospita il Data Factory integro?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Controlli eseguiti|
|---|
|<ul><li>Gli utenti hanno riscontrato problemi nell'inviare o elencare i processi di Data Lake Analytics?</li><li>Non è possibile completare Data Lake Analytics processi a causa di errori di sistema?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Controlli eseguiti|
|---|
|<ul><li>Gli utenti riscontrano problemi quando caricano i dati in Data Lake Store?</li><li>Gli utenti riscontrano problemi quando scaricano i dati da Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Controlli eseguiti|
|---|
|<ul><li>Il provisioning del servizio migrazione del database non è riuscito?</li><li>Il servizio migrazione del database è stato interrotto a causa di inattività o di una richiesta dell'utente?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/accounts
|Controlli eseguiti|
|---|
|<ul><li>L'account di condivisione dati è attivo e in esecuzione?</li><li>Il cluster che ospita la condivisione dati è disponibile?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Controlli eseguiti|
|---|
|<ul><li>Il server non è disponibile a causa della manutenzione?</li><li>Il server non è disponibile a causa della riconfigurazione?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Controlli eseguiti|
|---|
|<ul><li>Il server non è disponibile a causa della manutenzione?</li><li>Il server non è disponibile a causa della riconfigurazione?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Controlli eseguiti|
|---|
|<ul><li>Il server non è disponibile a causa della manutenzione?</li><li>Il server non è disponibile a causa della riconfigurazione?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Controlli eseguiti|
|---|
|<ul><li>L'hub IoT è operativo?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Controlli eseguiti|
|---|
|<ul><li>Ci sono state richieste di database o raccolta non soddisfatte a causa della mancata disponibilità di un servizio di Azure Cosmos DB?</li><li>Ci sono state richieste di documenti non soddisfatte a causa della mancata disponibilità di un servizio di Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/Namespaces
|Controlli eseguiti|
|---|
|<ul><li>Lo spazio dei nomi di hub eventi riscontra errori generati dall'utente?</li><li>Lo spazio dei nomi di hub eventi è attualmente in fase di aggiornamento?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/cluster
|Controlli eseguiti|
|---|
|<ul><li>I servizi di base sono disponibili nel cluster HDInsight?</li><li>Il cluster HDInsight può accedere alla chiave per la crittografia dei BYOK inattivi?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Controlli eseguiti|
|---|
|<ul><li>Esistono richieste all'insieme di credenziali delle chiavi non riuscite a causa di problemi della piattaforma di Azure Key Vault?</li><li>Le richieste all'insieme di credenziali delle chiavi vengono limitate a causa di un numero eccessivo di richieste effettuate dal cliente?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Controlli eseguiti|
|---|
|<ul><li>Il servizio Web è in esecuzione?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices
|Controlli eseguiti|
|---|
|<ul><li>Il servizio multimediale è operativo?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationgateways
|Controlli eseguiti|
|---|
|<ul><li>Le prestazioni del gateway applicazione sono diminuite?</li><li>Il gateway applicazione è disponibile?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Controlli eseguiti|
|---|
|<ul><li>Il tunnel VPN è connesso?</li><li>Sono presenti conflitti di configurazione nella connessione?</li><li>Le chiavi precondivise sono configurate correttamente?</li><li>Il dispositivo VPN locale è raggiungibile?</li><li>Esistono mancate corrispondenze nei criteri di sicurezza IPSec/IKE?</li><li>Il provisioning della connessione VPN S2S è stato eseguito correttamente oppure la connessione è in stato di errore?</li><li>Il provisioning della connessione da rete virtuale a rete virtuale è stato eseguito correttamente oppure la connessione è in stato di errore?</li></ul>|

## <a name="microsoftnetworkexpressreoutecircuits"></a>Microsoft. Network/expressreoutecircuits
|Controlli eseguiti|
|---|
|<ul><li>Il circuito ExpressRoute è integro?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors
|Controlli eseguiti|
|---|
|<ul><li>I backend front door rispondono con errori ai Probe di integrità?</li><li>Le modifiche alla configurazione sono ritardate?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/LoadBalancers
|Controlli eseguiti|
|---|
|<ul><li>Gli endpoint di bilanciamento del carico sono disponibili?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Controlli eseguiti|
|---|
|<ul><li>Il gateway VPN è raggiungibile da Internet?</li><li>Il gateway VPN è in modalità standby?</li><li>Il servizio VPN è in esecuzione nel gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Controlli eseguiti|
|---|
|<ul><li>Le operazioni di runtime come la registrazione, l'installazione o l'invio possono essere eseguite nello spazio dei nomi?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Controlli eseguiti|
|---|
|<ul><li>Sono presenti ritardi di indicizzazione per l'area di lavoro?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Controlli eseguiti|
|---|
|<ul><li>La risorsa capacità è operativa?</li><li>Tutti i carichi di lavoro sono operativi?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Controlli eseguiti|
|---|
|<ul><li>Il sistema operativo host è operativo?</li><li>La raccolta di aree di lavoro è raggiungibile dall'esterno del data center?</li><li>Il provider di risorse Power BI è disponibile?</li><li>Il servizio Power BI è disponibile nell'area appropriata?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Controlli eseguiti|
|---|
|<ul><li>Le operazioni di diagnostica possono essere eseguite nel cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Controlli eseguiti|
|---|
|<ul><li>I clienti riscontrano errori del bus di servizio generati dall'utente?</li><li>Gli utenti riscontrano un aumento degli errori temporanei a causa di un aggiornamento dello spazio dei nomi del bus di servizio?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/database
|Controlli eseguiti|
|---|
|<ul><li>Il database è operativo?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft. SQL/Server/database
|Controlli eseguiti|
|---|
|<ul><li>Sono stati eseguiti accessi al database?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Controlli eseguiti|
|---|
|<ul><li>Esistono richieste per leggere i dati dall'account di archiviazione non riuscite a causa di problemi della piattaforma di Archiviazione di Azure?</li><li>Esistono richieste per scrivere i dati nell'account di archiviazione non riuscite a causa di problemi della piattaforma di Archiviazione di Azure?</li><li>Il cluster di archiviazione in cui risiede l'account di archiviazione non è disponibile?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Controlli eseguiti|
|---|
|<ul><li>Gli host in cui il processo è in esecuzione sono operativi?</li><li>Non è stato possibile avviare il processo?</li><li>Sono in corso aggiornamenti di runtime?</li><li>Il processo si trova in uno stato previsto, ad esempio in esecuzione o arrestato dal cliente?</li><li>Si sono verificate eccezioni di memoria insufficiente per il processo?</li><li>Sono in corso aggiornamenti di calcolo pianificati?</li><li>È disponibile Gestione esecuzioni (piano di controllo)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Controlli eseguiti|
|---|
|<ul><li>Il server host è operativo?</li><li>Internet Information Services è in esecuzione?</li><li>Il bilanciamento del carico è in esecuzione?</li><li>Il piano del servizio app è raggiungibile dal data center?</li><li>L'account di archiviazione che ospita il contenuto dei siti per ServerFarm è disponibile?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Controlli eseguiti|
|---|
|<ul><li>Il server host è operativo?</li><li>Internet Information Server è in esecuzione?</li><li>Il bilanciamento del carico è in esecuzione?</li><li>L'app Web è raggiungibile dal data center?</li><li>L'account di archiviazione che ospita il contenuto del sito è disponibile?</li></ul>|

## <a name="next-steps"></a>Fasi successive
-  Vedere [Introduzione al dashboard per l'integrità dei servizi di Azure](service-health-overview.md) e [Introduzione a Integrità risorse di Azure](resource-health-overview.md) per informazioni più specifiche. 
-  [Domande frequenti su Integrità risorse di Azure](resource-health-faq.md)
- Impostare gli avvisi per ricevere notifiche sui problemi di integrità. Per altre informazioni, vedere [Configurare gli avvisi per gli eventi di Integrità dei servizi](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
