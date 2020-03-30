---
title: Connessione della macchina virtuale SQL di Azure per l'indicizzazione della ricercaAzure SQL VM connection for search indexing
titleSuffix: Azure Cognitive Search
description: Abilitare le connessioni crittografate e configurare il firewall per consentire le connessioni a SQL Server in una macchina virtuale di Azure (VM) da un indicizzatore in Ricerca cognitiva di Azure.Enable encrypted connections and configure the firewall to allow connections to SQL Server on an Azure virtual machine (VM) from an indexer on Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1ab2b7860e8a75da5f8acef2fc4fa54d4b73a30d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256964"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurare una connessione da un indicizzatore di Ricerca cognitiva di Azure a SQL Server in una macchina virtuale di AzureConfigure a connection from an Azure Cognitive Search indexer to SQL Server on an Azure VM

Come indicato in Connessione del database SQL di Azure alla ricerca cognitiva di Azure usando [gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), la creazione di indicizzatori con **SQL Server nelle macchine virtuali** di Azure (o nelle macchine virtuali di **SQL Azure** in breve) è supportata da Ricerca cognitiva di Azure, ma esistono alcuni prerequisiti relativi alla sicurezza di cui prestare attenzione per primi. 

Le connessioni da Ricerca cognitiva di Azure a SQL Server in una macchina virtuale è una connessione Internet pubblica. Tutte le misure di sicurezza da seguire in genere per queste connessioni, sono applicabili anche per:

+ Ottenere un certificato da un [provider dell'autorità di certificazione](https://en.wikipedia.org/wiki/Certificate_authority#Providers) per il nome di dominio completo dell'istanza di SQL Server nella VM di Azure.
+ Installare il certificato nella macchina virtuale, abilitare e configurare le connessioni crittografate nella VM seguendo le istruzioni riportate in questo articolo.

## <a name="enable-encrypted-connections"></a>Abilitare le connessioni crittografate
Ricerca cognitiva di Azure richiede un canale crittografato per tutte le richieste di indicizzatore tramite una connessione Internet pubblica. Questa sezione elenca i passaggi necessari per eseguire questa operazione.

1. Controllare le proprietà del certificato per verificare che il nome del soggetto sia il nome di dominio completo (FQDN) della VM di Azure. È possibile usare uno strumento come CertUtils o lo snap-in Certificati per visualizzare le proprietà. È possibile ottenere il nome FQDN dalla sezione Informazioni di base del pannello dei servizi della VM nel campo **Etichetta Indirizzo IP pubblico/Nome DNS** del [portale di Azure](https://portal.azure.com/).
   
   * Per le VM create con il nuovo modello di **Gestione risorse**, il nome FQDN segue la formattazione `<your-VM-name>.<region>.cloudapp.azure.com`
   * Per le macchine virtuali meno recenti create come VM **classica**, il nome FQDN segue la formattazione `<your-cloud-service-name.cloudapp.net>`.

2. Configurare SQL Server per l'uso del certificato con l'editor del Registro di sistema (regedit). 
   
    Anche se Gestione configurazione SQL Server viene usato spesso per questa attività, non è possibile usarlo per questo scenario. Non troverà il certificato importato perché il nome FQDN della VM in Azure non corrisponde al nome FQDN determinato dalla VM. Identifica il dominio come computer locale o come dominio di rete a cui è aggiunto. Quando i nomi non corrispondono, usare regedit per specificare il certificato.
   
   * In regedit passare a questa chiave del Registro di sistema: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     La parte `[MSSQL13.MSSQLSERVER]` varia a seconda della versione e del nome dell'istanza. 
   * Impostare il valore della chiave **Certificate** **sull'identificazione personale** del certificato TLS/SSL importato nella macchina virtuale.
     
     È possibile ottenere l'identificazione personale in diversi modi, alcuni dei quali preferibili ad altri. Se la si copia dallo snap-in **Certificati** in MMC, esiste il rischio di selezionare un carattere iniziale invisibile, [come illustrato in questo articolo del supporto](https://support.microsoft.com/kb/2023869/), e di ottenere un errore quando si prova a stabilire una connessione. Esistono diverse soluzioni alternative per risolvere il problema. La più semplice consiste nel premere il tasto BACKSPACE e quindi nel digitare di nuovo il primo carattere dell'identificazione personale per rimuovere il carattere iniziale nel campo del valore della chiave in regedit. In alternativa, è possibile usare un altro strumento per copiare l'identificazione personale.

3. Concedere autorizzazioni all'account del servizio. 
   
    Assicurarsi che all'account del servizio SQL ServerSQL Server sia concessa l'autorizzazione appropriata per la chiave privata del certificato TLS/SSL. Se si ignora questo passaggio, SQL Server non verrà avviato. È possibile usare lo snap-in **Certificates** o **CertUtils** per questa attività.
    
4. Riavviare il servizio SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurare la connettività di SQL Server nella VM
Dopo aver configurato la connessione crittografata richiesta da Ricerca cognitiva di Azure, sono necessari passaggi di configurazione aggiuntivi intrinseci per SQL Server nelle macchine virtuali di Azure.After you set up the encrypted connection required by Azure Cognitive Search, there are additional configuration steps intrinsic to SQL Server on Azure VMs. Se non è già stato fatto, il passaggio successivo prevede di completare la configurazione seguendo le istruzioni contenute in uno di questi articoli:

* Per una VM di **Resource Manager** , vedere [Connettersi a una macchina virtuale di SQL Server in Azure (Gestione risorse)](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Per una VM **classica** , vedere [Connettersi a una macchina virtuale di SQL Server in Azure (distribuzione classica)](../virtual-machines/windows/classic/sql-connect.md).

In particolare, vedere in ogni articolo la sezione relativa alla "connessione via Internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurare il gruppo di sicurezza di rete
Non è insolito configurare il gruppo di sicurezza di rete e il corrispondente endpoint o elenco di controllo di accesso (ACL) di Azure in modo da rendere accessibile la VM di Azure ad altri soggetti. Probabilmente questa operazione è stata eseguita prima per consentire alla logica applicativa di connettersi alla VM di SQL Azure. Non è diverso per una connessione di Ricerca cognitiva di Azure alla macchina virtuale di SQL Azure.It's no different for an Azure Cognitive Search connection to your SQL Azure VM. 

I collegamenti seguenti forniscono istruzioni sulla configurazione del gruppo di sicurezza di rete per le distribuzioni VM. Usare queste istruzioni per ACL di un endpoint di Ricerca cognitiva di Azure in base al relativo indirizzo IP.

> [!NOTE]
> Per informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/security-overview.md)
> 
> 

* Per una VM di **Resource Manager** , vedere [Come creare gruppi di sicurezza di rete per le distribuzioni Azure Resource Manager](../virtual-network/tutorial-filter-network-traffic.md). 
* Per una VM **classica** , vedere [Come creare gruppi di sicurezza di rete per le distribuzioni classiche](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

L'indirizzamento IP può creare alcune difficoltà facilmente superabili se si conoscono il problema e le potenziali soluzioni alternative. Le sezioni rimanenti contengono suggerimenti per gestire i problemi correlati agli indirizzi IP nell'elenco di controllo di accesso.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Limitare l'accesso a Ricerca cognitiva di AzureRestrict access to the Azure Cognitive Search
È consigliabile limitare l'accesso all'indirizzo IP del servizio `AzureCognitiveSearch` di ricerca e all'intervallo di indirizzi IP del tag del [servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) nell'elenco di controllo di accesso anziché rendere le macchine virtuali DI SQL Azure aperte a tutte le richieste di connessione.

È possibile individuare l'indirizzo IP eseguendo il ping `<your-search-service-name>.search.windows.net`del nome di dominio completo (ad esempio, ) del servizio di ricerca.

È possibile individuare l'intervallo di indirizzi IP del `AzureCognitiveSearch` [tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) del servizio utilizzando [i file JSON scaricabili](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) o tramite l'API [di individuazione dei tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)del servizio . L'intervallo di indirizzi IP viene aggiornato settimanalmente.

#### <a name="managing-ip-address-fluctuations"></a>Gestire le fluttuazioni degli indirizzi IP
Se il servizio di ricerca ha solo un'unità di ricerca (vale a dire una replica e una partizione), l'indirizzo IP cambierà durante il riavvio del servizio di routine, invalidando un elenco di controllo di accesso esistente con l'indirizzo IP del servizio di ricerca.

One way to avoid the subsequent connectivity error is to use more than one replica and one partition in Azure Cognitive Search. In questo modo, pur aumentando il costo, viene risolto il problema degli indirizzi IP. In Ricerca cognitiva di Azure gli indirizzi IP non cambiano quando sono disponibili più unità di ricerca.

Un altro approccio è quello di consentire che la connessione non riesca e quindi di riconfigurare gli elenchi di controllo di accesso nel gruppo di sicurezza di rete. In media, gli indirizzi IP vengono modificati a distanza di alcune settimane. Per i clienti che hanno controllato l'indicizzazione raramente, questo potrebbe essere un approccio valido.

Un terzo approccio valido (ma non particolarmente sicuro) consiste nello specificare l'intervallo di indirizzi IP dell'area di Azure in cui viene effettuato il provisioning del servizio di ricerca. L'elenco degli intervalli IP da cui gli indirizzi IP pubblici vengono allocati alle risorse di Azure è pubblicato negli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Includere gli indirizzi IP del portale di Ricerca cognitiva di AzureInclude the Azure Cognitive Search portal IP addresses
Se si usa il portale di Azure per creare un indicizzatore, la logica del portale di Ricerca cognitiva di Azure deve inoltre accedere alla macchina virtuale di SQL Azure durante la creazione. Gli indirizzi IP del portale di `stamp2.search.ext.azure.com`Ricerca cognitiva di Azure possono essere trovati eseguendo il ping di .

## <a name="next-steps"></a>Passaggi successivi
Con la configurazione in disconnessione, è ora possibile specificare un SQL Server nella macchina virtuale di Azure come origine dati per un indicizzatore di Ricerca cognitiva di Azure.With configuration out the way, you can now specify a SQL Server on Azure VM as the data source for an Azure Cognitive Search indexer. Per altre informazioni, vedere Connessione del database SQL di Azure alla ricerca cognitiva di Azure usando [gli indicizzatori.](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

