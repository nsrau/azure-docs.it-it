---
title: Panoramica della crittografia di Azure | Microsoft Docs
description: Informazioni sulle varie opzioni di crittografia in Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-overview"></a>Panoramica della crittografia di Azure

Questo articolo fornisce una panoramica dell'utilizzo della crittografia in Microsoft Azure. Vengono illustrate le principali aree di crittografia, tra cui la crittografia di dati inattivi, la crittografia in-flight e la gestione delle chiavi con Key Vault. Ogni sezione include i collegamenti per informazioni più dettagliate.

## <a name="encryption-of-data-at-rest"></a>Crittografia dei dati inattivi

I dati inattivi includono informazioni presenti nell'archivio permanente su un supporto fisico, in qualsiasi formato digitale. Sono inclusi i file su supporto ottico o magnetico, i dati archiviati e i backup di dati. Microsoft Azure offre un'ampia gamma di soluzioni di archiviazione dati per soddisfare le diverse esigenze, tra cui l'archiviazione su file, disco, BLOB e tabella. Microsoft fornisce anche la crittografia per proteggere il [database SQL di Azure](../sql-database/sql-database-technical-overview.md), [CosmosDB](../cosmos-db/introduction.md) e Azure Data Lake.

La crittografia dei dati inattivi è disponibile per i servizi nei modelli cloud Software-as-a-Service (SaaS), Platform-as-a-Service (PaaS) e Infrastructure-as-a-Service (IaaS) di Azure. Questo documento riepiloga e fornisce le risorse utili per usare le opzioni di crittografia di Azure.

Per altre informazioni sulla crittografia di dati inattivi in Azure, vedere il documento [Crittografia dei dati inattivi di Azure](azure-security-encryption-atrest.md)

## <a name="azure-encryption-models"></a>Modelli di crittografia di Azure

Azure supporta vari modelli di crittografia, tra cui la crittografia lato server usando chiavi gestite dal servizio, usando chiavi gestite dal cliente in Azure Key Vault o usando chiavi gestite dal cliente sull'hardware controllato dal cliente. La crittografia lato client consente di gestire e archiviare le chiavi in locale o in un'altra posizione protetta.

### <a name="client-side-encryption"></a>Crittografia lato client

La crittografia lato client viene eseguita all'esterno di Azure. La crittografia lato client include:

- I dati crittografati da un'applicazione in esecuzione nel data center del cliente o da un'applicazione di servizio
- I dati sono già crittografati quando vengono ricevuti da Azure.

Con la crittografia lato client il provider di servizi cloud non ha accesso alle chiavi di crittografia e non può decrittografare questi dati. Si mantiene il controllo completo delle chiavi.

### <a name="server-side-encryption"></a>Modello di crittografia lato server

I tre modelli di crittografia lato server offrono caratteristiche differenti di gestione delle chiavi che possono essere scelte in base ai requisiti.

- Le **chiavi gestite dal servizio** combinano controllo e comodità con un sovraccarico ridotto

- Le **chiavi gestite dal cliente** consentono di controllare le chiavi, inclusa la possibilità di usare chiavi personalizzate (BYOK) o di generarne nuove.

- Le **chiavi gestite dal servizio in ccustomer-controlledhardware** consentono di gestire le chiavi presenti nel repository proprietario al di fuori del controllo di Microsoft. Questo approccio viene definito HYOK (Host Your Own Key). Tuttavia, la configurazione è complessa e la maggior parte dei servizi di Azure non supporta questo modello.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Le macchine virtuali Linux e Windows possono essere protette usando [Crittografia dischi di Azure](azure-security-disk-encryption.md) che usa la tecnologia [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) e Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) per proteggere sia i dischi del sistema operativo sia i dischi di dati con la crittografia dell'intero volume.

Le chiavi e i segreti di crittografia vengono protetti nella sottoscrizione di [Azure Key Vault](../key-vault/key-vault-whatis.md). È possibile eseguire il backup e il ripristino delle macchine virtuali crittografate con la configurazione KEK usando il servizio Backup di Microsoft Azure.

### <a name="azure-storage-service-encryption"></a>Crittografia del servizio di Archiviazione di Azure

I dati inattivi in archiviazione di Azure (BLOB e file) possono essere crittografati in scenari sia sul lato server sia sul lato client.

[La crittografia del servizio di archiviazione Azure](../storage/storage-service-encryption.md) (SSE) può crittografare automaticamente i dati prima che vengano archiviati e decrittografati automaticamente in fase di recupero, rendendo il processo completamente trasparente all'utente. La crittografia del servizio di archiviazione usa la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bit, ovvero una delle crittografie a blocchi più attendibili disponibili e gestisce la crittografia, la decrittografia e la gestione delle chiavi in modo trasparente.

### <a name="client-side-encryption-of-azure-blobs"></a>Crittografia lato client di BLOB di Azure

La crittografia sul lato client di BLOB di Azure può essere eseguita in modi diversi.

È possibile usare il pacchetto NuGet Libreria client di archiviazione di Azure per .NET per crittografare i dati all'interno delle applicazioni client prima di caricarli in Archiviazione di Azure.

Per altre informazioni e per scaricare il pacchetto NuGet Libreria client di archiviazione di Azure per .NET, vedere il documento [Archiviazione di Microsoft Azure 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)

Quando si usa la crittografia sul lato client con Azure Key Vault, i dati vengono crittografati usando una chiave di crittografia del contenuto (CEK) simmetrica unica generato da Azure Storage client SDK. La chiave di crittografia del contenuto viene crittografata usando una chiave di crittografia della chiave, che può essere una chiave simmetrica o una coppia di chiavi asimmetriche. È possibile gestirla in locale o archiviarla in Azure Key Vault. I dati crittografati vengono quindi caricati nel servizio Archiviazione di Microsoft Azure.

Per altre informazioni sulla crittografia lato client con Azure Key Vault e per iniziare con istruzioni sulle procedure, vedere il documento [Esercitazione: Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite Azure Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md)

Infine, è possibile usare anche la 	Libreria client di archiviazione di Azure per Java per eseguire la crittografia sul lato client prima di caricare dati in Archiviazione di Azure e decrittografare i dati durante il download nel client. Questa libreria supporta anche l'integrazione con [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi dell'account di archiviazione.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Crittografia dei dati inattivi con il database SQL di Azure

Il [database SQL di Azure](../sql-database/sql-database-technical-overview.md) è un servizio di database relazionale generico in Microsoft Azure che supporta strutture come dati relazionali, JSON, dati spaziali e XML. SQL di Azure supporta sia la crittografia lato server tramite la funzionalità Transparent Data Encryption (TDE) sia la crittografia lato client tramite la funzionalità Always Encrypted.

#### <a name="transparent-data-encryption"></a>Transparent Data Encryption

La tecnologia [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) viene usata per crittografare i file di dati di [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), del [database SQL di Azure](../sql-database/sql-database-technical-overview.md) e di [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) in tempo reale, usando una chiave di crittografia del database (DEK) archiviata nel record di avvio del database per assicurare la disponibilità durante il ripristino.

La tecnologia TDE consente di proteggere i file di dati e di log usando gli algoritmi di crittografia AES e 3DES. La crittografia del file di database viene eseguita a livello di pagina; le pagine in un database crittografato vengono crittografate prima di essere scritte sul disco e vengono decrittografate quando vengono lette in memoria. La tecnologia Transparent Data Encryption è ora abilitata per impostazione predefinita nei nuovi database SQL di Azure.

#### <a name="always-encrypted"></a>Always Encrypted

La funzionalità [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) in Azure SQL consente di crittografare i dati all'interno delle applicazioni client prima dell'archiviazione nel database SQL di Azure e consente di abilitare la delega dell'amministrazione di database locale a terze parti e mantenere la separazione tra chi possiede e può visualizzare i dati e chi gestisce, ma non deve avere accesso ad essi.

#### <a name="cellcolumn-level-encryption"></a>Crittografia a livello di colonna/cella (CLE)

Il database SQL di Azure consente di applicare la crittografia simmetrica a una colonna di dati tramite Transact-SQL. Si tratta di una [crittografia a livello di cella o a livello di colonna](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (CLE) poiché è possibile usarla per crittografare determinate colonne o persino specifiche celle di dati con chiavi di crittografia differenti. Ciò consente una funzionalità di crittografia più granulare rispetto alla crittografia Transparent Data Encryption che crittografa i dati in pagine.

Le funzioni predefinite della crittografia CLE consentono di crittografare i dati usando chiavi simmetriche o asimmetriche, con la chiave pubblica di un certificato o con una passphrase usando 3DES.

### <a name="cosmos-db-database-encryption"></a>Crittografia del database Cosmos DB

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) è il database multimodello distribuito a livello globale di Microsoft. I dati utente archiviati in Cosmos DB nella memoria non volatile (unità SSD) vengono crittografati per impostazione predefinita: non sono presenti controlli per attivare o disattivare la crittografia. La crittografia dei dati inattivi viene implementata attraverso una serie di tecnologie di protezione, inclusi i sistemi di archiviazione protetta delle chiavi, le reti crittografate e le API di crittografia. Le chiavi di crittografia vengono gestite da Microsoft e ruotate in base alle linee guida interne di Microsoft.

### <a name="at-rest-encryption-in-azure-data-lake"></a>Crittografia di dati inattivi in Azure Data Lake

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) è un repository aziendale di ogni tipo di dati raccolti in un'unica posizione prima di qualsiasi definizione formale di schema o requisiti. Azure Data Lake Store supporta la crittografia trasparente dei dati inattivi "per impostazione predefinita", configurata durante la creazione dell'account. Per impostazione predefinita, Data Lake Store gestisce automaticamente le chiavi, ma è possibile gestirle manualmente.

Vengono usate tre tipi di chiavi per la crittografia e decrittografia dei dati: la chiave di crittografia principale (MEK), la chiave di crittografia dei dati (DEK) e la chiave di crittografia a blocchi (BEK). La chiave di crittografia principale viene usata per crittografare la chiave di crittografia dei dati, che viene archiviata su un supporto permanente e la chiave di crittografia a blocchi deriva dalla chiave di crittografia dei dati e dal blocco di dati. Se si stanno gestendo chiavi personalizzate, è possibile ruotare la chiave di crittografia principale.

## <a name="encryption-of-data-in-transit"></a>Crittografia dei dati in transito

Azure offre numerosi meccanismi per conservare i dati privati durante lo spostamento da una posizione a un'altra.

### <a name="tlsssl-encryption-in-azure"></a>Crittografia TLS/SSL in Azure

Microsoft usa il protocollo [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) per proteggere i dati durante il trasferimento tra i servizi cloud e i clienti. I data center Microsoft negoziano una connessione TLS con i sistemi client che si connettono ai servizi di Azure. Il protocollo TLS fornisce l'autenticazione avanzata, la riservatezza dei messaggi e l'integrità (abilitando il rilevamento di manomissioni, intercettazioni e falsificazioni di messaggi), l'interoperabilità, la flessibilità degli algoritmi, la facilità di distribuzione e di utilizzo.

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) protegge le connessioni tra i sistemi client dei clienti e i servizi cloud di Microsoft con chiavi univoche. Le connessioni usano anche lunghezze di chiavi di crittografia basate a 2.048 bit basate su RSA. Questa combinazione rende difficile ad altri utenti intercettare e accedere ai dati in transito.

### <a name="azure-storage-transactions"></a>Transazioni di archiviazione di Azure

Quando si interagisce con Archiviazione di Azure tramite il portale di Azure, tutte le transazioni hanno luogo tramite HTTPS. È possibile anche usare l'API REST di archiviazione su HTTPS per interagire con Archiviazione di Azure. È possibile imporre l'uso di HTTPS quando si chiamano le API REST per accedere a oggetti negli account di archiviazione abilitando l'opzione Trasferimento sicuro obbligatorio per l'account di archiviazione.

Le firme di accesso condiviso [SAS](../storage/storage-dotnet-shared-access-signature-part-1.md), che possono essere usate per delegare l'accesso agli oggetti di Archiviazione di Azure, includono la possibilità di specificare che quando si usano firme di accesso condiviso può essere usato solo il protocollo HTTPS. In questo modo si garantisce che chiunque invii collegamenti con token di firma di accesso condiviso usi il protocollo corretto.

Il protocollo [SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) usato per accedere alle condivisioni file di Azure supporta la crittografia ed è disponibile in Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, consentendo l'accesso tra aree e anche l'accesso sul desktop.

La crittografia sul lato client crittografa i dati prima che venga inviato ad Archiviazione di Azure, in modo che siano crittografati durante il trasferimento in rete.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Crittografia SMB su reti virtuali di Azure 

Il protocollo [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) nelle macchine virtuali di Azure che eseguono Windows Server 2012 e versioni successive offre la possibilità di rendere sicuri i trasferimenti di dati crittografando i dati in transito sulle reti virtuali di Azure, per evitare attacchi di intercettazione di dati e manomissioni. Gli amministratori possono abilitare la crittografia SMB per l'intero server o per condivisioni specifiche.

Per impostazione predefinita, dopo avere attivato la crittografia SMB per una condivisione o per un server, l'accesso alle condivisioni crittografate è consentito solo ai client SMB 3.

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Crittografia di dati in transito in Macchine virtuali di Azure

I dati in transito da e verso le macchine virtuali di Azure o tra di esse con Windows vengono crittografati in diversi modi, a seconda della natura della connessione.

### <a name="rdp-sessions"></a>Sessioni RDP

È possibile connettersi e accedere a una macchina virtuale di Azure usando [Remote Desktop Protocol](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) (RDP) da un computer client Windows o da un Mac con un client RDP installato. I dati in transito sulla rete nelle sessioni RDP possono essere protetti dal protocollo TLS.

È possibile anche usare Desktop remoto per connettersi a una VM Linux di Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Accesso sicuro alle macchine virtuali Linux con SSH

È possibile usare [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) per connettersi alle macchine virtuali Linux in esecuzione in Azure per la gestione remota. SSH è un protocollo per connessioni crittografate che consente accessi protetti su connessioni non sicure. È il protocollo di connessione predefinito per le VM Linux ospitate in Azure. L'utilizzo di chiavi SSH per l'autenticazione consente di eliminare la necessità di password per l'accesso. SSH usa una coppia di chiavi pubblica/privata (asimmetrica) per l'autenticazione.

## <a name="azure-vpn-encryption"></a>Crittografia VPN di Azure

È possibile connettersi ad Azure tramite una rete privata virtuale che crea un tunnel sicuro per proteggere la privacy dei dati inviati attraverso la rete.

### <a name="azure-vpn-gateway"></a>Gateway VPN di Azure

Un [gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) può essere usato per inviare traffico crittografato tra la rete virtuale e la posizione locale tramite una connessione pubblica o per inviare traffico tra reti virtuali.

La VPN da sito a sito usa [IPsec](https://en.wikipedia.org/wiki/IPsec) per la crittografia del trasporto. I gateway VPN di Azure usano un insieme di proposte predefinite. È possibile configurare i gateway VPN di Azure in modo da usare criteri IPsec/IKE personalizzati con algoritmi di crittografia specifici e attendibilità delle chiavi, anziché set di criteri predefiniti di Azure.

### <a name="point-to-site-vpn"></a>VPN da punto a sito

Le VPN Point-to-Site consentono ai singoli computer client di accedere a una rete virtuale di Azure. Il protocollo [Secure Socket Tunneling Protocol](https://technet.microsoft.com/library/2007.06.cableguy.aspx) (SSTP) viene usato per creare il tunnel VPN e può attraversare i firewall (il tunnel viene visualizzato come connessione HTTPS). È possibile usare la CA radice della PKI interna per la connettività Point-to-Site.

È possibile configurare una connessione VPN Point-to-Site a una rete virtuale usando il portale di Azure con l'autenticazione del certificato o PowerShell.

Per altre informazioni sulle connessioni VPN Point-to-Site per le reti virtuali di Azure, vedere: [Configurare una connessione Point-to-Site a una rete virtuale usando l'autenticazione del certificato: portale di Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) e

[Configurare una connessione Point-to-Site a una rete virtuale usando l'autenticazione del certificato: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpn"></a>Da sito a VPN 

Una connessione gateway VPN da sito a sito viene usata per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato.

È possibile configurare una connessione VPN da sito-a-sito a una rete virtuale usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

Leggere qui per altre informazioni:

[Creare una connessione da sito a sito nel portale di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Creare una connessione da sito a sito](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Creare una rete virtuale con una connessione VPN da sito a sito usando l'interfaccia della riga di comando](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Crittografia di dati in transito in Azure Data Lake

Anche i dati in transito (noti anche come dati in movimento) vengono sempre crittografati in Data Lake Store. I dati, oltre a essere crittografati prima dell'archiviazione in supporti persistenti, vengono sempre protetti anche mentre sono in transito usando HTTPS. HTTPS è l'unico protocollo supportato per le interfacce REST di Data Lake Store.

Per altre informazioni sulla crittografia dei dati in transito in Azure Data Lake, vedere il documento [Crittografia dei dati in Azure Data Lake Store.](../data-lake-store/data-lake-store-encryption.md)

## <a name="key-management-with-azure-key-vault"></a>Gestione delle chiavi con Azure Key Vault

Senza protezione e gestione delle chiavi adeguate la crittografia è inutile. Azure Key Vault è la soluzione consigliata di Microsoft per la gestione e il controllo dell'accesso alle chiavi di crittografia usate dai servizi cloud. È possibile assegnare autorizzazioni per le chiavi di accesso ai servizi o agli utenti tramite account di Azure Active Directory.

Con Azure Key Vault le organizzazioni non hanno più necessità di eseguire le operazioni di provisioning, configurazione, applicazione di patch e gestione di moduli di protezione hardware e software di gestione delle chiavi. Con Azure Key Vault, Microsoft non vede mai le chiavi e le applicazioni non hanno accesso diretto ad esse e quindi l'utente mantiene il controllo. È possibile anche importare o generare chiavi nei moduli di protezione hardware.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della sicurezza in Azure](security-get-started-overview.md)
- [Panoramica della sicurezza di rete di Azure](security-network-overview.md)
- [Panoramica della sicurezza del database di Azure](azure-database-security-overview.md)
- [Informazioni generali sulla sicurezza di Macchine virtuali di Azure](security-virtual-machines-overview.md)
- [Crittografia dei dati inattivi](azure-security-encryption-atrest.md)
- [Procedure consigliate per la sicurezza e la crittografia dei dati](azure-security-data-encryption-best-practices.md)
