---
title: Strategie di accesso ai dati
description: Azure Data Factory supporta ora intervalli di indirizzi IP statici.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 015feac819467cf60bfb2faab27af769fadc3cfa
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86522874"
---
# <a name="data-access-strategies"></a>Strategie di accesso ai dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Un obiettivo di sicurezza strategico per qualsiasi organizzazione è quello di proteggere i propri archivi dati da accessi casuali tramite Internet, indipendentemente dal fatto che si tratti di archivi locali o Cloud/ SaaS. 

In genere, un archivio dati cloud controlla l'accesso mediante i meccanismi seguenti:
* Collegamento privato da una rete virtuale a origini dati abilitate per endpoint privato
* Regole del firewall che limitano la connettività in base all'indirizzo IP.
* Meccanismi di autenticazione che richiedono all'utente di dimostrare la propria identità.
* Meccanismi di autorizzazione che limitano l'accesso degli utenti ad azioni e dati specifici

> [!TIP]
> Con l'[introduzione di un intervallo di indirizzi IP statici](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses), è ora possibile specificare un elenco di indirizzi IP consentiti per una determinata area Azure Integration Runtime, in modo da non dover consentire tutti gli indirizzi IP di Azure nei propri archivi dati cloud, ma limitarne l'accesso solo agli indirizzi IP autorizzati.

> [!NOTE] 
> Gli intervalli di indirizzi IP sono bloccati per Azure Integration Runtime e attualmente vengono usati solo per lo spostamento dei dati, la pipeline e le attività esterne. I flussi di data e Azure Integration Runtime che abilitano la rete virtuale gestita ora non usano questi intervalli IP. 

In questo modo dovrebbe essere coperta la maggior parte degli scenari e, sebbene sia preferibile avere un unico indirizzo IP statico per il runtime di integrazione, attualmente non sarebbe possibile poiché Azure Integration Runtime è serverless. Se necessario, è sempre possibile configurare un runtime di integrazione self-hosted e usare l'indirizzo IP statico. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Strategie di accesso ai dati tramite Azure Data Factory

* **[Collegamento privato](https://docs.microsoft.com/azure/private-link/private-link-overview)** : è possibile creare un Azure Integration Runtime all'interno di Azure Data Factory rete virtuale gestita e sfruttare gli endpoint privati per connettersi in modo sicuro agli archivi dati supportati. Il traffico tra la rete virtuale gestita e le origini dati attraversa la rete dorsale Microsoft e non è esposta alla rete pubblica.
* **[Servizio attendibile](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** : Archiviazione di Azure (BLOB, ADLS Gen2) supporta la configurazione del firewall, che consente di selezionare servizi della piattaforma Azure attendibili per accedere in modo sicuro all'account di archiviazione. Servizi attendibili impone l'autenticazione Identità gestita, con cui si garantisce che nessun'altra data factory possa connettersi a questa risorsa di archiviazione, a meno che non sia autorizzata tramite la relativa identità gestita. Per informazioni più dettagliate, vedere **[questo blog](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** . Questa strategia, quindi, è estremamente sicura ed è consigliata. 
* **IP statico univoco**: è necessario configurare un runtime di integrazione self-hosted per ottenere un indirizzo IP statico per i connettori di Data Factory. Questo meccanismo offre la possibilità di bloccare l'accesso da qualsiasi altro indirizzo IP. 
* **[Intervallo IP statico](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** : è possibile usare gli indirizzi IP di Azure Integration Runtime per aggiungerli come elenco di IP consentiti nella risorsa di archiviazione (ad esempio, S3, Salesforce e così via). Questa strategia non solo limita il numero di indirizzi IP che possono connettersi agli archivi dati, ma si basa anche sulle regole di autenticazione/autorizzazione.
* **[Tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** : un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP di un determinato servizio di Azure, ad esempio Azure Data Factory. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag in base alla modifica degli indirizzi, riducendo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete. Questa strategia è particolarmente utile quando si stabilisce l'accesso ai dati in archivi dati IaaS ospitati nella rete virtuale.
* **Consenti Servizi di Azure**: alcuni servizi offrono la possibilità di autorizzare tutti i servizi di Azure a connettersi (scegliendo questa opzione). 

Per ulteriori informazioni sui meccanismi di sicurezza di rete supportati negli archivi dati in Azure Integration Runtime e Integration Runtime indipendenti, vedere sotto due tabelle.  
* **Azure Integration Runtime**

    | Archivi dati                  | Meccanismo di sicurezza di rete supportato negli archivi dati | Collegamento privato     | Servizio attendibile     | Intervallo IP statico | Tag di servizio | Consenti Servizi di Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Archivi dati Azure PaaS       | Azure Cosmos DB                                     | Sì              | -                   | Sì             | -            | Sì                  |
    |                              | Esplora dati di Azure                                 | -                | -                   | Sì*            | Sì*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Sì             | -            | Sì                  |
    |                              | Database di Azure per MariaDB, MySQL, PostgreSQL       | -                | -                   | Sì             | -            | Sì                  |
    |                              | Archiviazione file di Azure                                  | Sì              | -                   | Sì             | -            | .                    |
    |                              | Archiviazione di Azure (BLOB, ADLS Gen2)                     | Sì              | Sì (solo autenticazione MSI) | Sì             | -            | .                    |
    |                              | Azure SQL DB, SQL DW (Synapse Analytics), SQL   Ml  | Sì (solo database SQL di Azure/DW)        | -                   | Sì             | -            | Sì                  |
    |                              | Azure Key Vault (per il recupero di segreti/stringa di connessione) | sì      | Sì                 | Sì             | -            | -                    |
    | Altri archivi dati PaaS/SaaS | AWS   S3, SalesForce, Google Cloud Storage, ecc.    | -                | -                   | Sì             | -            | -                    |
    | laaS Azure                   | SQL Server, Oracle, ecc.                          | -                | -                   | Sì             | Sì          | -                    |
    | laaS locale              | SQL Server, Oracle, ecc.                          | -                | -                   | Sì             | -            | -                    |
    
    **Applicabile solo se Esplora dati di Azure è inserito in una rete virtuale ed è possibile applicare l'intervallo di indirizzi IP al gruppo di sicurezza di rete/firewall.* 

* **Runtime di integrazione self-hosted (in rete virtuale/locale)**
    
    | Archivi dati                  | Meccanismo di sicurezza di rete supportato negli archivi dati         | IP statico | Servizi attendibili  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Archivi dati Azure PaaS       | Azure Cosmos DB                                               | Sì       | -                   |
    |                                | Esplora dati di Azure                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Sì       | -                   |
    |                                | Database di Azure per MariaDB, MySQL, PostgreSQL               | Sì       | -                   |
    |                                | Archiviazione file di Azure                                            | Sì       | -                   |
    |                                | Archiviazione di Azure (Blog,   ADLS Gen2)                             | Sì       | Sì (solo autenticazione MSI) |
    |                                | Azure SQL DB, SQL DW   (Synapse Analytics), SQL   Ml          | Sì       | -                   |
    |                                | Azure Key Vault (per il recupero di segreti/stringa di connessione) | Sì       | Sì                 |
    | Altri archivi dati PaaS/SaaS | AWS   S3, SalesForce, Google Cloud Storage, ecc.              | Sì       | -                   |
    | laaS Azure                     | SQL Server,   Oracle,   ecc.                                  | Sì       | -                   |
    | laaS locale              | SQL Server,   Oracle,   ecc.                                  | Sì       | -                   |    

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli correlati seguenti:
* [Archivi dati supportati](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Servizi attendibili di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Servizi Microsoft attendibili di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Identità gestita per Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
