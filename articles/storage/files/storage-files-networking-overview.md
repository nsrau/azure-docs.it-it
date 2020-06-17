---
title: Considerazioni sulla rete per File di Azure | Microsoft Docs
description: Panoramica delle opzioni di rete per File di Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 804e469a01be042b4c299fd608f11426e7274b72
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84464811"
---
# <a name="azure-files-networking-considerations"></a>Considerazioni sulla rete per File di Azure 
È possibile connettersi a una condivisione file di Azure in due modi:

- Accedendo direttamente alla condivisione tramite protocolli SMB o FileREST. Questo modello di accesso viene usato principalmente per eliminare il maggior numero possibile di server locali.
- Creando una cache della condivisione file di Azure in un server locale o in una VM di Azure con Sincronizzazione file di Azure e accedendo ai dati della condivisione file dal server locale con un protocollo a scelta (SMB, NFS, FTPS e così via) in base al caso d'uso specifico. Questo modello di accesso è utile perché rappresenta la combinazione ideale tra prestazioni locali, scalabilità del cloud e servizi collegabili in modalità serverless, ad esempio Backup di Azure.

Questo articolo è incentrato sulla configurazione della rete per i casi d'uso che richiedono l'accesso diretto alla condivisione file di Azure invece che tramite Sincronizzazione file di Azure. Per altre informazioni sulle considerazioni di rete per una distribuzione di Sincronizzazione file di Azure, vedere [Considerazioni sulla rete per Sincronizzazione file di Azure](storage-sync-files-networking-overview.md).

La configurazione di rete per le condivisioni file di Azure viene eseguita nell'account di archiviazione di Azure. Un account di archiviazione è un costrutto di gestione che rappresenta un pool di archiviazione condiviso in cui è possibile distribuire più condivisioni file oltre ad altre risorse di archiviazione, ad esempio contenitori BLOB o code. Gli account di archiviazione espongono più impostazioni che consentono di proteggere l'accesso tramite rete alle condivisioni file: endpoint di rete, impostazioni del firewall dell'account di archiviazione e crittografia in transito. 

Prima di seguire questa guida concettuale, è consigliabile leggere [Pianificazione della distribuzione di File di Azure](storage-files-planning.md).

## <a name="accessing-your-azure-file-shares"></a>Accesso alle condivisioni file di Azure
Le condivisioni file di Azure distribuite all'interno di un account di archiviazione diventano immediatamente accessibili tramite l'endpoint pubblico dell'account di archiviazione. Questo significa che le richieste autenticate, ad esempio quelle autorizzate dall'identità di accesso di un utente, possono avere origine in modo sicuro all'interno o all'esterno di Azure. 

In molti ambienti dei clienti, un montaggio iniziale della condivisione file di Azure nella workstation locale avrà esito negativo, anche se i montaggi dalle macchine virtuali di Azure riescono. Il motivo è che molte organizzazioni e provider di servizi Internet (ISP) bloccano la porta usata da SMB per comunicare, ovvero la porta 445. Questa pratica deriva dalle linee guida sulla sicurezza relative alle versioni legacy e deprecate del protocollo SMB. Mentre SMB 3.0 è un protocollo sicuro su Internet, le versioni precedenti, in particolare SMB 1.0, non lo sono. Le condivisioni file di Azure possono essere accessibili all'esterno solo tramite SMB 3.0 e il protocollo FileREST (che è anche un protocollo sicuro su Internet) tramite l'endpoint pubblico.

Poiché il modo più semplice per accedere alla condivisione file di Azure dall'ambiente locale consiste nell'aprire la rete locale sulla porta 445, Microsoft consiglia di eseguire i passaggi seguenti per rimuovere SMB 1.0 dall'ambiente:

1. Assicurarsi che SMB 1.0 venga rimosso o disabilitato nei dispositivi dell'organizzazione. Tutte le versioni attualmente supportate di Windows e Windows Server consentono la rimozione o la disabilitazione di SMB 1.0 e, a partire da Windows 10 versione 1709, SMB 1.0 non viene installato in Windows per impostazione predefinita. Per altre informazioni su come disabilitare SMB 1.0, vedere le pagine specifiche del sistema operativo in uso:
    - [Protezione di Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Protezione di Linux](storage-how-to-use-files-linux.md#securing-linux)
2. Assicurarsi che nessun prodotto all'interno dell'organizzazione richieda SMB 1.0 e rimuovere quelli che lo richiedono. È disponibile la pagina [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1), che contiene tutti i prodotti di Microsoft e di terzi parti noti a Microsoft che richiedono SMB 1.0. 
3. (Facoltativo) Usare un firewall di terze parti con la rete locale dell'organizzazione per impedire il traffico SMB 1.0 in uscita dai confini dell'organizzazione.

Se l'organizzazione richiede che la porta 445 sia bloccata in base a criteri o normative oppure se richiede che il traffico verso Azure segua un percorso deterministico, è possibile usare il gateway VPN di Azure o ExpressRoute per il tunneling del traffico alle condivisioni file di Azure.

> [!Important]  
> Anche se si decide di usare un metodo alternativo per accedere alle condivisioni file di Azure, Microsoft consiglia comunque di rimuovere SMB 1.0 dall'ambiente.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneling del traffico su una rete privata virtuale o ExpressRoute
Quando si stabilisce un tunnel di rete tra la rete locale e Azure, si esegue il peering della rete locale con una o più reti virtuali in Azure. Una [rete virtuale](../../virtual-network/virtual-networks-overview.md) è simile a una rete tradizionale gestita in locale. Analogamente a un account di archiviazione o a una VM di Azure, una rete virtuale è una risorsa di Azure distribuita in un gruppo di risorse. 

File di Azure supporta i meccanismi seguenti per eseguire il tunneling del traffico tra le workstation e i server locali e Azure:

- [Gateway VPN di Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md): un gateway VPN è un tipo specifico di gateway di rete virtuale, usato per inviare traffico crittografato tra una rete virtuale di Azure e una posizione alternativa, ad esempio l'ambiente locale, attraverso Internet. Un gateway VPN di Azure è una risorsa di Azure che può essere distribuita in un gruppo di risorse insieme a un account di archiviazione o ad altre risorse di Azure. I gateway VPN espongono due tipi diversi di connessioni:
    - [VPN da punto a sito](../../vpn-gateway/point-to-site-about.md), ovvero connessioni VPN tra Azure e un singolo client. Questa soluzione è particolarmente utile per i dispositivi che non fanno parte della rete locale dell'organizzazione, ad esempio quelli di telelavoratori che vogliono avere la possibilità di montare la condivisione file di Azure da casa, dal bar o da un hotel mentre sono in viaggio. Per usare una connessione VPN da punto a sito con File di Azure, è necessario configurarla per ogni client da connettere. Per semplificare la distribuzione di una connessione VPN da punto a sito, vedere [Configurare una VPN da punto a sito in Windows per l'uso con File di Azure](storage-files-configure-p2s-vpn-windows.md) e [Configurare una VPN da punto a sito in Linux per l'uso con File di Azure](storage-files-configure-p2s-vpn-linux.md).
    - [VPN da sito a sito](../../vpn-gateway/design.md#s2smulti), ovvero connessioni VPN tra Azure e la rete dell'organizzazione. Una connessione VPN da sito a sito consente di configurare una connessione VPN una sola volta, per un dispositivo o un server VPN ospitato nella rete dell'organizzazione, invece che per ogni dispositivo client che ha la necessità di accedere alla condivisione file di Azure. Per semplificare la distribuzione di una connessione VPN da sito a sito, vedere [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), che consente di creare una route definita tra Azure e la rete locale che non attraversa Internet. Fornendo un percorso dedicato tra il data center locale e Azure, ExpressRoute può risultare utile quando le prestazioni di rete rappresentano un aspetto da considerare. ExpressRoute è un'opzione valida anche laddove i criteri dell'organizzazione o i requisiti ambientali impongono un percorso deterministico verso le risorse nel cloud.

Indipendentemente dal metodo di tunneling usato per accedere alle condivisioni file di Azure, è necessario un meccanismo per garantire che il traffico verso l'account di archiviazione passi attraverso il tunnel invece che sulla normale connessione Internet. È tecnicamente possibile eseguire il routing all'endpoint pubblico dell'account di archiviazione, ma in questo caso è necessario impostare come hardcoded tutti gli indirizzi IP per i cluster di archiviazione di Azure in un'area, perché gli account di archiviazione possono essere spostati tra cluster di archiviazione in qualsiasi momento. È anche necessario aggiornare costantemente i mapping degli indirizzi IP, perché vengono sempre aggiunti nuovi cluster.

Invece di impostare come hardcoded l'indirizzo IP degli account di archiviazione nelle regole di routing VPN, è consigliabile usare endpoint privati, che assegnano all'account di archiviazione un indirizzo IP dallo spazio di indirizzi di una rete virtuale di Azure. Poiché la creazione di un tunnel in Azure stabilisce il peering tra la rete locale e una o più reti virtuali, questo approccio consente il routing corretto in modo durevole.

### <a name="private-endpoints"></a>Endpoint privati
Oltre all'endpoint pubblico predefinito per un account di archiviazione, File di Azure offre la possibilità di avere uno o più endpoint privati. Un endpoint privato è accessibile solo all'interno di una rete virtuale di Azure. Quando si crea un endpoint privato per l'account di archiviazione, quest'ultimo ottiene un indirizzo IP privato dallo spazio di indirizzi della rete virtuale, in modo simile a un dispositivo file server o NAS locale che riceve un indirizzo IP all'interno dello spazio di indirizzi dedicato della rete locale. 

Un singolo endpoint privato è associato a una specifica subnet di rete virtuale di Azure. Un account di archiviazione può avere endpoint privati in più reti virtuali.

L'uso di endpoint privati con File di Azure consente di:
- Connettersi in modo sicuro alle condivisioni file di Azure dalle reti locali usando una connessione VPN o ExpressRoute con peering privato.
- Proteggere le condivisioni file di Azure configurando il firewall dell'account di archiviazione in modo da bloccare tutte le connessioni all'endpoint pubblico. Per impostazione predefinita, la creazione di un endpoint privato non blocca le connessioni all'endpoint pubblico.
- Aumentare la sicurezza per la rete virtuale consentendo di bloccare l'esfiltrazione dei dati dalla rete virtuale e dai limiti di peering.

Per creare un endpoint privato, vedere [Configurazione di endpoint privati per File di Azure](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Endpoint privati e DNS
Quando si crea un endpoint privato, per impostazione predefinita si crea anche una zona DNS privato (o se ne aggiorna una esistente) corrispondente al sottodominio `privatelink`. Per quanto riguarda la creazione di una zona DNS privato, non è in realtà necessario usare un endpoint privato per l'account di archiviazione, ma è consigliabile in generale ed è necessario in modo esplicito quando si monta la condivisione file di Azure con un'entità utente di Active Directory o si accede dall'API FileREST.

> [!Note]  
> Questo articolo usa il suffisso DNS dell'account di archiviazione per le aree pubbliche di Azure, `core.windows.net`. Questo commento si applica anche ai cloud sovrani di Azure, ad esempio il cloud di Azure per il governo degli Stati Uniti e il cloud di Azure Cina. È sufficiente sostituire i suffissi appropriati per l'ambiente in uso. 

Nella zona DNS privato viene creato un record A per `storageaccount.privatelink.file.core.windows.net` e un record CNAME per il nome regolare dell'account di archiviazione, che segue il modello `storageaccount.file.core.windows.net`. Poiché la zona DNS privato di Azure è connessa alla rete virtuale contenente l'endpoint privato, è possibile osservare la configurazione DNS chiamando il cmdlet `Resolve-DnsName` da PowerShell in una VM di Azure (in alternativa `nslookup` in Windows e Linux):

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

Il motivo è che l'account di archiviazione può esporre sia l'endpoint pubblico che uno o più endpoint privati. Per assicurarsi che il nome dell'account di archiviazione venga risolto nell'indirizzo IP privato dell'endpoint privato, è necessario cambiare la configurazione nei server DNS locali. A questo scopo è possibile procedere in vari modi:

- Modificare il file hosts nei client per fare in modo che `storageaccount.file.core.windows.net` venga risolto nell'indirizzo IP privato dell'endpoint privato desiderato. Questa scelta è assolutamente sconsigliata per gli ambienti di produzione, perché sarà necessario apportare queste modifiche in ogni client in cui si vogliono montare le condivisioni file di Azure e le modifiche apportate all'account di archiviazione o all'endpoint privato non verranno gestite automaticamente.
- Creare un record A per `storageaccount.file.core.windows.net` nei server DNS locali. Questa opzione offre il vantaggio che è possibile risolvere automaticamente l'account di archiviazione per i client nell'ambiente locale senza la necessità di configurarli singolarmente. Tuttavia, questa soluzione è altrettanto debole della modifica del file hosts, perché le modifiche non vengono rispecchiate. Ciononostante, potrebbe rivelarsi la scelta migliore per alcuni ambienti.
- Inoltrare la zona `core.windows.net` dai server DNS locali alla zona DNS privato di Azure. L'host DNS privato di Azure è raggiungibile tramite un indirizzo IP speciale (`168.63.129.16`) accessibile solo all'interno delle reti virtuali collegate alla zona DNS privato di Azure. Per aggirare questa limitazione, è possibile eseguire ulteriori server DNS all'interno della rete virtuale che inoltreranno `core.windows.net` alla zona DNS privato di Azure. Per semplificare questa configurazione, sono disponibili cmdlet di PowerShell che distribuiscono automaticamente i server DNS nella rete virtuale di Azure e li configurano come si desidera. Per informazioni su come configurare l'inoltro DNS, vedere [Configurazione di DNS con File di Azure](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Impostazioni del firewall dell'account di archiviazione
Un firewall è un criterio di rete che controlla quali richieste sono autorizzate ad accedere all'endpoint pubblico per un account di archiviazione. Usando il firewall dell'account di archiviazione, è possibile limitare l'accesso all'endpoint pubblico dell'account di archiviazione consentendolo solo a determinati indirizzi IP o intervalli oppure a una rete virtuale. In generale, la maggior parte dei criteri del firewall per un account di archiviazione limiterà l'accesso di rete a una o più reti virtuali. 

Per limitare l'accesso a un account di archiviazione consentendolo solo a una rete virtuale, sono disponibili due approcci:
- Creare uno o più endpoint privati per l'account di archiviazione e limitare tutto l'accesso all'endpoint pubblico. In questo modo si garantisce che solo il traffico originato dalle reti virtuali desiderate possa accedere alle condivisioni file di Azure nell'account di archiviazione.
- Limitare l'endpoint pubblico a una o più reti virtuali. Questa operazione può essere eseguita usando una funzionalità della rete virtuale denominata *endpoint di servizio*. Quando si limita il traffico verso un account di archiviazione tramite un endpoint di servizio, si può comunque accedere all'account di archiviazione tramite l'indirizzo IP pubblico.

Per altre informazioni su come configurare il firewall dell'account di archiviazione, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Crittografia in transito
Per impostazione predefinita, la crittografia in transito è abilitata per tutti gli account di archiviazione di Azure. Quindi, quando si monta una condivisione file su SMB o vi si accede tramite il protocollo FileREST (ad esempio tramite il portale di Azure, PowerShell/interfaccia della riga di comando o Azure SDK), File di Azure consentirà la connessione solo se viene eseguita con SMB 3.0 o versione successiva con crittografia oppure HTTPS. I client che non supportano SMB 3.0 oppure che supportano SMB 3.0 ma non la crittografia SMB non potranno montare la condivisione file di Azure se è abilitata la crittografia in transito. Per altre informazioni sui sistemi operativi che supportano SMB 3.0 con crittografia, vedere la documentazione dettagliata per [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Tutte le versioni correnti di PowerShell, interfaccia della riga di comando e SDK supportano HTTPS.  

È possibile disabilitare la crittografia in transito per un account di archiviazione di Azure. Quando la crittografia è disabilitata, File di Azure consentirà anche l'uso di SMB 2.1, SMB 3.0 senza crittografia e chiamate API FileREST non crittografate su HTTP. La crittografia in transito viene in genere disabilitata principalmente per supportare un'applicazione legacy che deve essere eseguita in un sistema operativo meno recente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente. File di Azure consente connessioni SMB 2.1 solo all'interno della stessa area di Azure della condivisione file di Azure. Un client SMB 2.1 situato all'esterno dell'area di Azure in cui si trova la condivisione file di Azure, ad esempio in locale o in un'area di Azure differente, non potrà accedere alla condivisione file.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Vedere anche
- [Panoramica di File di Azure](storage-files-introduction.md)
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)