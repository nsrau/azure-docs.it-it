---
title: Considerazioni di rete per Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni su come configurare la rete per usare Sincronizzazione file di Azure per memorizzare nella cache i file locali.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629360"
---
# <a name="azure-file-sync-networking-considerations"></a>Considerazioni di rete per Sincronizzazione file di Azure
È possibile connettersi a una condivisione file di Azure in due modi:

- Accedendo direttamente alla condivisione tramite protocolli SMB o FileREST. Questo modello di accesso viene usato principalmente per eliminare il maggior numero possibile di server locali.
- Creando una cache della condivisione file di Azure in un server locale o in una VM di Azure con Sincronizzazione file di Azure e accedendo ai dati della condivisione file dal server locale con un protocollo a scelta (SMB, NFS, FTPS e così via) in base al caso d'uso specifico. Questo modello di accesso è utile perché rappresenta la combinazione ideale tra prestazioni locali, scalabilità del cloud e servizi collegabili in modalità serverless, ad esempio Backup di Azure.

Questo articolo è incentrato sulla configurazione della rete per i casi d'uso che richiedono l'uso di Sincronizzazione file di Azure per memorizzare i file nella cache in locale invece di montare direttamente la condivisione file di Azure tramite SMB. Per altre informazioni sulle considerazioni di rete per una distribuzione di File di Azure, vedere [Considerazioni sulla rete per File di Azure](storage-files-networking-overview.md).

La configurazione di rete per Sincronizzazione file di Azure riguarda due oggetti di Azure diversi, ovvero un servizio di sincronizzazione archiviazione e un account di archiviazione di Azure. Un account di archiviazione è un costrutto di gestione che rappresenta un pool di archiviazione condiviso in cui è possibile distribuire più condivisioni file oltre ad altre risorse di archiviazione, ad esempio contenitori BLOB o code. Il servizio di sincronizzazione archiviazione è un costrutto di gestione che rappresenta i server registrati, ovvero file server Windows con una relazione di trust stabilita con Sincronizzazione file di Azure, e i gruppi di sincronizzazione, che definiscono la topologia della relazione di sincronizzazione. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Connessione del file server Windows ad Azure con Sincronizzazione file di Azure 
Per configurare e usare File di Azure e Sincronizzazione file di Azure con un file server Windows locale, non è necessaria alcuna rete speciale in Azure oltre a una connessione Internet di base. Per distribuire Sincronizzazione file di Azure, installare il relativo agente nel file server Windows da sincronizzare con Azure. L'agente di Sincronizzazione file di Azure esegue la sincronizzazione con una condivisione file di Azure tramite due canali:

- Il protocollo FileREST, basato su HTTPS, per l'accesso alla condivisione file di Azure. Poiché il protocollo FileREST usa HTTPS standard per il trasferimento dei dati, solo la porta 443 deve essere accessibile in uscita. Sincronizzazione file di Azure non usa il protocollo SMB per trasferire i dati dai server Windows locali alla condivisione file di Azure.
- Il protocollo di Sincronizzazione file di Azure, basato su HTTPS, per lo scambio di informazioni di sincronizzazione, ovvero informazioni sulle versioni di file e cartelle dell'ambiente, tra gli endpoint dell'ambiente. Questo protocollo viene usato anche per scambiare i metadati su file e cartelle dell'ambiente, ad esempio timestamp ed elenchi di controllo di accesso (ACL). 

Poiché File di Azure offre accesso diretto tramite protocollo SMB nelle condivisioni file di Azure, i clienti spesso si chiedono se è necessario configurare una rete speciale per montare le condivisioni file di Azure con SMB per consentire l'accesso dell'agente di Sincronizzazione file di Azure. Questa operazione non solo non è necessaria, ma è anche sconsigliata, tranne che per scenari di amministrazione, perché non è possibile rilevare rapidamente le modifiche apportate direttamente alla condivisione file di Azure. Le modifiche potrebbero non essere rilevate per più di 24 ore, a seconda delle dimensioni e del numero di elementi della condivisione file di Azure. Per usare direttamente la condivisione file di Azure, ovvero senza usare Sincronizzazione file di Azure per la memorizzazione nella cache in locale, vedere [Panoramica della rete di File di Azure](storage-files-networking-overview.md) per altre informazioni sulle considerazioni di rete per l'accesso diretto.

Anche se Sincronizzazione file di Azure non richiede alcuna configurazione di rete speciale, i clienti potrebbero scegliere di configurare impostazioni di rete avanzate per gli scenari seguenti:

- Interoperabilità con la configurazione del server proxy dell'organizzazione.
- Aprire il firewall locale dell'organizzazione per i servizi File di Azure e Sincronizzazione file di Azure.
- Impostare il tunneling di File di Azure e Sincronizzazione file di Azure tramite ExpressRoute o una connessione VPN.

### <a name="configuring-proxy-servers"></a>Configurazione dei server proxy
Molte organizzazioni usano un server proxy come intermediario tra le risorse interne alla rete locale e le risorse esterne, ad esempio in Azure. I server proxy sono utili per molte applicazioni, ad esempio l'isolamento e la sicurezza della rete, oltre al monitoraggio e alla registrazione. Sincronizzazione file di Azure è completamente interoperabile con un server proxy, ma è necessario configurare manualmente le impostazioni dell'endpoint proxy per l'ambiente con Sincronizzazione file di Azure. Questa operazione deve essere eseguita tramite PowerShell usando i cmdlet server di Sincronizzazione file di Azure. 

Per altre informazioni su come configurare Sincronizzazione file di Azure con un server proxy, vedere [Configurazione di Sincronizzazione file di Azure con un server proxy](storage-sync-files-firewall-and-proxy.md).

### <a name="configuring-firewalls-and-service-tags"></a>Configurazione di firewall e tag di servizio
È possibile isolare i file server dalla maggior parte dei percorsi Internet per motivi di sicurezza. Per usare Sincronizzazione file di Azure nell'ambiente, è necessario modificare il firewall per aprirlo per determinati servizi di Azure. A tale scopo, è possibile recuperare gli intervalli di indirizzi IP per i servizi richiesti tramite il meccanismo dei [tag di servizio](../../virtual-network/service-tags-overview.md).

Sincronizzazione file di Azure richiede gli intervalli di indirizzi IP per i servizi seguenti, identificati dai rispettivi tag del servizio:

| Service | Descrizione | Tag di servizio |
|---------|-------------|-------------|
| Sincronizzazione file di Azure | Il servizio Sincronizzazione file di Azure, rappresentato dall'oggetto servizio di sincronizzazione archiviazione, è responsabile dell'attività principale di sincronizzazione dei dati tra una condivisione file di Azure e una file server Windows. | `StorageSyncService` |
| File di Azure | Tutti i dati sincronizzati tramite Sincronizzazione file di Azure vengono archiviati nella condivisione file di Azure. I file cambiati nei file server Windows vengono replicati nella condivisione file di Azure e i file suddivisi in livelli nel file server locale vengono scaricati facilmente quando un utente li richiede. | `Storage` |
| Azure Resource Manager | Azure Resource Manager è l'interfaccia di gestione per Azure. Tutte le chiamate di gestione, incluse le attività di registrazione e sincronizzazione ricorrente del server di Sincronizzazione file di Azure, vengono effettuate tramite Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory, o Azure AD, contiene le entità utente necessarie per autorizzare la registrazione server per un servizio di sincronizzazione archiviazione, oltre alle entità servizio necessarie per autorizzare l'accesso di Sincronizzazione file di Azure alle risorse cloud. | `AzureActiveDirectory` |

Se si usa Sincronizzazione file di Azure all'interno di Azure, anche in un'area diversa, è possibile usare il nome del tag di servizio direttamente nel gruppo di sicurezza di rete per consentire il traffico verso tale servizio. Per altre informazioni, vedere [Gruppi di sicurezza di rete](../../virtual-network/network-security-groups-overview.md). 

Se si usa Sincronizzazione file di Azure in locale, è possibile usare l'API dei tag di servizio per ottenere gli specifici intervalli di indirizzi IP per l'elenco di elementi consentiti del firewall. Per recuperare queste informazioni, sono disponibili due metodi:

- L'elenco corrente degli intervalli di indirizzi IP per tutti i servizi di Azure che supportano i tag di servizio viene pubblicato settimanalmente nell'Area download Microsoft sotto forma di documento JSON. Ogni cloud di Azure è associato a uno specifico documento JSON con gli intervalli di indirizzi IP pertinenti:
    - [Pubblico di Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure per la Cina](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germania](https://www.microsoft.com/download/details.aspx?id=57064)
- L'API di individuazione di tag di servizio (anteprima) consente il recupero a livello di codice dell'elenco corrente di tag di servizio. Nella versione di anteprima questa API potrebbe restituire informazioni meno aggiornate di quelle restituite dai documenti JSON pubblicati nell'Area download Microsoft. È possibile usare la superficie dell'API in base alle preferenze di automazione:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Interfaccia della riga di comando di Azure](/cli/azure/network#az-network-list-service-tags)

Per altre informazioni su come usare l'API dei tag di servizio per recuperare gli indirizzi dei servizi, vedere [Elenco di elementi consentiti per gli indirizzi IP di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneling del traffico su una rete privata virtuale o ExpressRoute
Alcune organizzazioni richiedono che la comunicazione con Azure passi attraverso un tunnel di rete, ad esempio una VPN virtuale o ExpressRoute, per avere un livello aggiuntivo di sicurezza o per fare in modo che la comunicazione con Azure segua una route deterministica. 

Quando si stabilisce un tunnel di rete tra la rete locale e Azure, si esegue il peering della rete locale con una o più reti virtuali in Azure. Una [rete virtuale](../../virtual-network/virtual-networks-overview.md) è simile a una rete tradizionale gestita in locale. Analogamente a un account di archiviazione o a una VM di Azure, una rete virtuale è una risorsa di Azure distribuita in un gruppo di risorse. 

File di Azure e Sincronizzazione file di Azure supportano i meccanismi seguenti per il tunneling del traffico tra server locali e Azure:

- [Gateway VPN di Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md): un gateway VPN è un tipo specifico di gateway di rete virtuale, usato per inviare traffico crittografato tra una rete virtuale di Azure e una posizione alternativa, ad esempio l'ambiente locale, attraverso Internet. Un gateway VPN di Azure è una risorsa di Azure che può essere distribuita in un gruppo di risorse insieme a un account di archiviazione o ad altre risorse di Azure. Poiché Sincronizzazione file di Azure è destinato a essere usato con un file server Windows locale, in genere si usa una [VPN da sito a sito](../../vpn-gateway/design.md#s2smulti), anche se tecnicamente è possibile usare una [VPN da punto a sito](../../vpn-gateway/point-to-site-about.md). 

    Le VPN da sito a sito connettono la rete virtuale di Azure e la rete locale dell'organizzazione. Una connessione VPN da sito a sito consente di configurare una connessione VPN una sola volta, per un dispositivo o un server VPN ospitato nella rete dell'organizzazione, invece che per ogni dispositivo client che ha la necessità di accedere alla condivisione file di Azure. Per semplificare la distribuzione di una connessione VPN da sito a sito, vedere [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md).

- [ExpressRoute](../../expressroute/expressroute-introduction.md), che consente di creare una route definita tra Azure e la rete locale che non attraversa Internet. Fornendo un percorso dedicato tra il data center locale e Azure, ExpressRoute può risultare utile quando le prestazioni di rete rappresentano un aspetto da considerare. ExpressRoute è un'opzione valida anche laddove i criteri dell'organizzazione o i requisiti ambientali impongono un percorso deterministico verso le risorse nel cloud.

### <a name="private-endpoints"></a>Endpoint privati
Oltre agli endpoint pubblici predefiniti forniti tramite l'account di archiviazione e il servizio di sincronizzazione archiviazione, File di Azure e Sincronizzazione file offrono la possibilità di avere uno o più endpoint privati per ogni risorsa. Un endpoint privato creato per una risorsa di Azure ottiene un indirizzo IP privato dallo spazio di indirizzi della rete virtuale, in modo simile a un file server Windows locale che riceve un indirizzo IP all'interno dello spazio di indirizzi dedicato della rete locale. 

> [!Important]  
> Per usare endpoint privati nella risorsa servizio di sincronizzazione archiviazione, è necessario usare l'agente di Sincronizzazione file di Azure versione 10.1 o successiva. Le versioni dell'agente precedenti alla 10.1 non supportano gli endpoint privati nel servizio di sincronizzazione archiviazione. Tutte le versioni precedenti degli agenti supportano gli endpoint privati nella risorsa account di archiviazione.

Un singolo endpoint privato è associato a una specifica subnet di rete virtuale di Azure. Un account di archiviazione e i servizi di sincronizzazione archiviazione possono avere endpoint privati in più reti virtuali.

L'uso di endpoint privati consente di:
- Connettersi in modo sicuro alle risorse di Azure da reti locali tramite una connessione VPN o ExpressRoute con peering privato.
- Proteggere le risorse di Azure disabilitando gli endpoint pubblici per File di Azure e Sincronizzazione file. Per impostazione predefinita, la creazione di un endpoint privato non blocca le connessioni all'endpoint pubblico.
- Aumentare la sicurezza per la rete virtuale consentendo di bloccare l'esfiltrazione dei dati dalla rete virtuale e dai limiti di peering.

Per creare un endpoint privato, vedere [Configurazione di endpoint privati per Sincronizzazione file di Azure](storage-sync-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Endpoint privati e DNS
Quando si crea un endpoint privato, per impostazione predefinita si crea anche una zona DNS privato (o se ne aggiorna una esistente) corrispondente al sottodominio `privatelink`. Per le aree del cloud pubblico, queste zone DNS sono `privatelink.file.core.windows.net` per File di Azure e `privatelink.afs.azure.net` per Sincronizzazione file di Azure.

> [!Note]  
> Questo articolo usa il suffisso DNS dell'account di archiviazione per le aree pubbliche di Azure, `core.windows.net`. Questo commento si applica anche ai cloud sovrani di Azure, ad esempio il cloud di Azure per il governo degli Stati Uniti e il cloud di Azure Cina. È sufficiente sostituire i suffissi appropriati per l'ambiente in uso. 

Quando si creano endpoint privati per un account di archiviazione e un servizio di sincronizzazione archiviazione, viene creato un record A per le rispettive zone DNS privato. Viene inoltre aggiornata la voce del DNS pubblico in modo che i nomi di dominio completi regolari siano di tipo CNAME per il nome di privatelink pertinente. In questo modo i nomi di dominio completo puntano agli indirizzi IP degli endpoint privati quando il richiedente si trova all'interno della rete virtuale e agli indirizzi IP degli endpoint pubblici quando il richiedente si trova all'esterno. 

Per File di Azure, ogni endpoint privato ha un singolo nome di dominio completo, seguendo il modello `storageaccount.privatelink.file.core.windows.net`, mappato a un indirizzo IP privato per l'endpoint privato. Per Sincronizzazione file di Azure, ogni endpoint privato ha quattro nomi di dominio completi, per i quattro endpoint diversi esposti da Sincronizzazione file di Azure, ossia gestione, sincronizzazione (primaria), sincronizzazione (secondaria) e monitoraggio. I nomi di dominio completi per questi endpoint seguiranno in genere il nome del servizio di sincronizzazione archiviazione, a meno che non contenga caratteri non ASCII. Se ad esempio il nome del servizio di sincronizzazione archiviazione è `mysyncservice` nell'area Stati Uniti occidentali 2, gli endpoint equivalenti saranno `mysyncservicemanagement.westus2.afs.azure.net`, `mysyncservicesyncp.westus2.afs.azure.net`, `mysyncservicesyncs.westus2.afs.azure.net` e `mysyncservicemonitoring.westus2.afs.azure.net`. Ogni endpoint privato per un servizio di sincronizzazione archiviazione conterrà 4 indirizzi IP distinti. 

Poiché la zona DNS privato di Azure è connessa alla rete virtuale contenente l'endpoint privato, è possibile osservare la configurazione DNS chiamando il cmdlet `Resolve-DnsName` da PowerShell in una VM di Azure (in alternativa `nslookup` in Windows e Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Per questo esempio, l'account di archiviazione `storageaccount.file.core.windows.net` viene risolto nell'indirizzo IP privato dell'endpoint privato, che è `192.168.0.4`.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Se si esegue lo stesso comando dall'ambiente locale, si noterà che lo stesso nome dell'account di archiviazione viene invece risolto nell'indirizzo IP pubblico dell'account di archiviazione; `storageaccount.file.core.windows.net` è un record CNAME per `storageaccount.privatelink.file.core.windows.net`, che a sua volta è un record CNAME per il cluster di archiviazione di Azure che ospita l'account di archiviazione:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Ciò riflette il fatto che File di Azure e Sincronizzazione file di Azure possono esporre sia gli endpoint pubblici che uno o più endpoint privati per ogni risorsa. Per assicurarsi che i nomi di dominio completi delle risorse si risolvano negli indirizzi IP privati degli endpoint privati, è necessario cambiare la configurazione nei server DNS locali. A questo scopo è possibile procedere in vari modi:

- Modificare il file hosts nei client per risolvere i nomi di dominio completi per gli account di archiviazione e i servizi di sincronizzazione archiviazione negli indirizzi IP privati desiderati. Questa procedura è fortemente sconsigliata per gli ambienti di produzione, perché sarà necessario apportare le modifiche in ogni client che richiede l'accesso agli endpoint privati. Le modifiche apportate alle risorse e agli endpoint privati (eliminazioni, modifiche e così via) non verranno gestite automaticamente.
- Creare zone DNS nei server locali per `privatelink.file.core.windows.net` e `privatelink.afs.azure.net` con i record A per le risorse di Azure. Questa opzione offre il vantaggio che è possibile risolvere automaticamente le risorse di Azure per i client nell'ambiente locale senza la necessità di configurarli singolarmente. Tuttavia, questa soluzione è altrettanto debole della modifica del file hosts, perché le modifiche non vengono rispecchiate. Ciononostante, potrebbe rivelarsi la scelta migliore per alcuni ambienti.
- Inoltrare le zone `core.windows.net` e `afs.azure.net` dai server DNS locali alla zona DNS privato di Azure. L'host DNS privato di Azure è raggiungibile tramite un indirizzo IP speciale (`168.63.129.16`) accessibile solo all'interno delle reti virtuali collegate alla zona DNS privato di Azure. Per aggirare questa limitazione, è possibile eseguire ulteriori server DNS all'interno della rete virtuale che inoltreranno `core.windows.net` e `afs.azure.net` alla zona DNS privato di Azure equivalenti. Per semplificare questa configurazione, sono disponibili cmdlet di PowerShell che distribuiscono automaticamente i server DNS nella rete virtuale di Azure e li configurano come si desidera. Per informazioni su come configurare l'inoltro DNS, vedere [Configurazione di DNS con File di Azure](storage-files-networking-dns.md).

## <a name="encryption-in-transit"></a>Crittografia dei dati in transito
Le connessioni effettuate dall'agente di Sincronizzazione file di Azure alla condivisione file di Azure o al servizio di sincronizzazione archiviazione sono sempre crittografate. Anche se gli account di archiviazione di Azure includono un'impostazione per disabilitare l'obbligo della crittografia in transito per le comunicazioni con File di Azure (e con gli altri servizi di archiviazione di Azure gestiti al di fuori dell'account di archiviazione), la disabilitazione di questa impostazione non influisce sulla crittografia di Sincronizzazione file di Azure per le comunicazioni con File di Azure. Per impostazione predefinita, la crittografia in transito è abilitata per tutti gli account di archiviazione di Azure. 

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Vedere anche
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)