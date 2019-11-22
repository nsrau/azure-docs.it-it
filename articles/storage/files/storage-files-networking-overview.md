---
title: Considerazioni sulla rete per File di Azure | Microsoft Docs
description: Panoramica delle opzioni di rete per File di Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126537"
---
# <a name="azure-files-networking-considerations"></a>Considerazioni sulla rete per File di Azure 
È possibile connettersi a una condivisione file di Azure in due modi:

- Accedendo direttamente alla condivisione tramite protocolli SMB o FileREST. Questo modello di accesso viene usato principalmente per eliminare il maggior numero possibile di server locali.
- Creando una cache della condivisione file di Azure in un server locale con Sincronizzazione file di Azure e accedendo ai dati della condivisione file dal server locale con un protocollo a scelta (SMB, NFS, FTPS e così via) in base al caso d'uso specifico. Questo modello di accesso è utile perché rappresenta la combinazione ideale tra prestazioni locali, scalabilità del cloud e servizi collegabili in modalità serverless, ad esempio Backup di Azure.

Questo articolo è incentrato sulla configurazione della rete per i casi d'uso che richiedono l'accesso diretto alla condivisione file di Azure invece che tramite Sincronizzazione file di Azure. Per altre informazioni sulle considerazioni di rete per una distribuzione di Sincronizzazione file di Azure, vedere [Configurazione delle impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Impostazioni account di archiviazione
Un account di archiviazione è un costrutto di gestione che rappresenta un pool di archiviazione condiviso in cui è possibile distribuire più condivisioni file oltre ad altre risorse di archiviazione, ad esempio contenitori BLOB o code. Gli account di archiviazione di Azure espongono due set di impostazioni di base per proteggere la rete: crittografia in transito e firewall e reti virtuali.

### <a name="encryption-in-transit"></a>Crittografia in transito
Per impostazione predefinita, la crittografia in transito è abilitata per tutti gli account di archiviazione di Azure. Quindi, quando si monta una condivisione file su SMB o vi si accede tramite il protocollo FileREST (ad esempio tramite il portale di Azure, PowerShell/interfaccia della riga di comando o Azure SDK), File di Azure consentirà la connessione solo se viene eseguita con SMB 3.0 o versione successiva con crittografia oppure HTTPS. I client che non supportano SMB 3.0 oppure che supportano SMB 3.0 ma non la crittografia SMB non potranno montare la condivisione file di Azure se è abilitata la crittografia in transito. Per altre informazioni sui sistemi operativi che supportano SMB 3.0 con crittografia, vedere la documentazione dettagliata per [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Tutte le versioni correnti di PowerShell, interfaccia della riga di comando e SDK supportano HTTPS.  

È possibile disabilitare la crittografia in transito per un account di archiviazione di Azure. Quando la crittografia è disabilitata, File di Azure consentirà anche l'uso di SMB 2.1, SMB 3.0 senza crittografia e chiamate API FileREST non crittografate su HTTP. La crittografia in transito viene in genere disabilitata principalmente per supportare un'applicazione legacy che deve essere eseguita in un sistema operativo meno recente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente. File di Azure consente connessioni SMB 2.1 solo all'interno della stessa area di Azure della condivisione file di Azure. Un client SMB 2.1 situato all'esterno dell'area di Azure in cui si trova la condivisione file di Azure, ad esempio in locale o in un'area di Azure differente, non potrà accedere alla condivisione file.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Firewall e reti virtuali 
Un firewall è un criterio di rete per stabilire a quali richieste consentire l'accesso a condivisioni file di Azure e ad altre risorse di archiviazione nell'account di archiviazione. Quando un account di archiviazione viene creato con le impostazioni di rete predefinite, non è limitato a una rete specifica e pertanto è accessibile da Internet. Questo non significa che chiunque su Internet può accedere ai dati archiviati nelle condivisioni file di Azure ospitate nell'account di archiviazione, ma piuttosto che l'account di archiviazione accetterà le richieste autorizzate da qualsiasi rete. Le richieste possono essere autorizzate con una chiave dell'account di archiviazione, un token di firma di accesso condiviso (solo FileREST) o un'entità utente di Active Directory. 

Il criterio firewall per un account di archiviazione può essere usato per limitare l'accesso a determinati indirizzi o intervalli IP oppure a una rete virtuale. In generale, la maggior parte dei criteri firewall per un account di archiviazione limiterà l'accesso di rete a una rete virtuale. 

Una [rete virtuale](../../virtual-network/virtual-networks-overview.md) è simile a una rete tradizionale che verrà gestita nel proprio data center. Consente di creare un canale di comunicazione sicuro tra le risorse di Azure, ad esempio condivisioni file, macchine virtuali, database SQL di Azure e così via. Analogamente a un account di archiviazione o a una VM di Azure, una rete virtuale è una risorsa di Azure distribuita in un gruppo di risorse. Con una configurazione di rete aggiuntiva, le reti virtuali di Azure possono anche essere connesse alle reti locali.

Quando si aggiungono risorse, ad esempio una VM di Azure, a una rete virtuale, un'interfaccia di rete virtuale collegata alla macchina virtuale è limitata specificamente a tale rete virtuale. Ciò è possibile perché le VM di Azure sono computer virtualizzati, che ovviamente sono dotati di schede di interfaccia di rete. Le macchine virtuali vengono offerte come parte della linea di prodotti IaaS (infrastruttura distribuita come servizio) di Azure. Poiché le condivisioni file di Azure sono serverless, non hanno una scheda di interfaccia di rete da aggiungere a una rete virtuale. In altri termini, i servizi File di Azure vengono offerti come parte della linea di prodotti PaaS (piattaforma distribuita come servizio) di Azure. Per offrire la possibilità di includere un account di archiviazione in una rete virtuale, Azure supporta il concetto di endpoint di servizio nell'ambito dei servizi PaaS. Grazie a un endpoint di servizio, i servizi PaaS possono far parte di una rete virtuale. Per altre informazioni sugli endpoint del servizio, vedere [Endpoint servizio di rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md).

Un account di archiviazione può essere aggiunto a una o più reti virtuali. Per altre informazioni su come aggiungere l'account di archiviazione a una rete virtuale o configurare altre impostazioni del firewall, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Rete di Azure
Per impostazione predefinita, è possibile accedere ai servizi di Azure, tra cui File di Azure, tramite Internet. Poiché per impostazione predefinita il traffico verso l'account di archiviazione è crittografato (e i montaggi SMB 2.1 non sono mai consentiti al di fuori di un'area di Azure), l'accesso alle condivisioni file di Azure tramite Internet non presenta condizioni intrinsecamente pericolose. In base ai criteri dell'organizzazione o a requisiti normativi specifici, potrebbe essere necessaria una comunicazione più restrittiva con Azure e, di conseguenza, Azure offre diversi modi per limitare il traffico verso File di Azure proveniente dall'esterno di Azure. È possibile proteggere ulteriormente la rete per l'accesso alla condivisione file di Azure usando le offerte di servizi seguenti:

- [Gateway VPN di Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md): un gateway VPN è un tipo specifico di gateway di rete virtuale, usato per inviare traffico crittografato tra una rete virtuale di Azure e una posizione alternativa, ad esempio l'ambiente locale, attraverso Internet. Un gateway VPN di Azure è una risorsa di Azure che può essere distribuita in un gruppo di risorse insieme a un account di archiviazione o ad altre risorse di Azure. I gateway VPN espongono due tipi diversi di connessioni:
    - [VPN da punto a sito](../../vpn-gateway/point-to-site-about.md), ovvero connessioni VPN tra Azure e un singolo client. Questa soluzione è particolarmente utile per i dispositivi che non fanno parte della rete locale dell'organizzazione, ad esempio quelli di telelavoratori che vogliono avere la possibilità di montare la condivisione file di Azure da casa, dal bar o da un hotel mentre sono in viaggio. Per usare una connessione VPN da punto a sito con File di Azure, è necessario configurarla per ogni client da connettere. 
    - [VPN da sito a sito](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), ovvero connessioni VPN tra Azure e la rete dell'organizzazione. Una connessione VPN da sito a sito consente di configurare una connessione VPN una sola volta, per un dispositivo o un server VPN ospitato nella rete dell'organizzazione, invece che per ogni dispositivo client che ha la necessità di accedere alla condivisione file di Azure.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), che consente di creare una route definita tra Azure e la rete locale che non attraversa Internet. Fornendo un percorso dedicato tra il data center locale e Azure, ExpressRoute può risultare utile quando le prestazioni di rete rappresentano un aspetto da considerare. ExpressRoute è un'opzione valida anche laddove i criteri dell'organizzazione o i requisiti ambientali impongono un percorso deterministico verso le risorse nel cloud.

## <a name="securing-access-from-on-premises"></a>Protezione dell'accesso dall'ambiente locale 
Quando si esegue la migrazione a File di Azure di condivisioni file per utilizzo generico (ad esempio documenti di Office, file PDF, documenti CAD e così via), gli utenti in genere continueranno ad avere l'esigenza di accedere ai loro file da dispositivi locali come workstation, computer portatili e tablet. L'aspetto più importante da considerare per una condivisione file per utilizzo generico riguarda la sicurezza dell'accesso degli utenti locali tramite Internet o rete WAN.

Il modo più semplice per accedere alla condivisione file di Azure dall'ambiente locale consiste nell'aprire la rete locale sulla porta 445, ovvero quella usata da SMB, e montare il percorso UNC fornito dal portale di Azure. Per questo scenario non è necessaria alcuna rete speciale. Molti clienti sono riluttanti ad aprire la porta 445 a causa di indicazioni di sicurezza obsolete riguardanti SMB 1.0, che Microsoft non considera un protocollo Internet sicuro. File di Azure non implementa SMB 1.0. 

SMB 3.0 è stato progettato con il requisito esplicito di essere un protocollo di condivisione file sicuro su Internet. Pertanto, quando si usa SMB 3.0 o versione successiva, dal punto di vista della rete di computer l'apertura della porta 445 non è diversa dall'apertura della porta 443, ovvero quella usata per le connessioni HTTPS. Invece di bloccare la porta 445 per impedire il traffico SMB 1.0 non sicuro, Microsoft consiglia la procedura seguente:

> [!Important]  
> Anche se si decide di lasciare chiusa la porta 445 per il traffico in uscita, Microsoft consiglia comunque di seguire questa procedura per rimuovere SMB 1.0 dall'ambiente.

1. Assicurarsi che SMB 1.0 venga rimosso o disabilitato nei dispositivi dell'organizzazione. Tutte le versioni attualmente supportate di Windows e Windows Server consentono la rimozione o la disabilitazione di SMB 1.0 e, a partire da Windows 10 versione 1709, SMB 1.0 non viene installato in Windows per impostazione predefinita. Per altre informazioni su come disabilitare SMB 1.0, vedere le pagine specifiche del sistema operativo in uso:
    - [Protezione di Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Protezione di Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Assicurarsi che nessun prodotto all'interno dell'organizzazione richieda SMB 1.0 e rimuovere quelli che lo richiedono. È disponibile la pagina [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1), che contiene tutti i prodotti di Microsoft e di terzi parti noti a Microsoft che richiedono SMB 1.0. 
1. (Facoltativo) Usare un firewall di terze parti con la rete locale dell'organizzazione per impedire il traffico SMB 1.0.

Se l'organizzazione richiede che la porta 445 sia bloccata in base a criteri o normative, è possibile usare Gateway VPN o ExpressRoute per instradare il traffico sulla porta 443. Per altre informazioni sui passaggi specifici per distribuire questi componenti, vedere le relative pagine di procedure:
- [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md)
- [Configurare una VPN da punto a sito in Windows per l'uso con File di Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configurare una VPN da punto a sito in Linux per l'uso con File di Azure](storage-files-configure-p2s-vpn-linux.md)

L'organizzazione potrebbe avere un requisito aggiuntivo per cui il traffico in uscita dal sito locale deve seguire un percorso deterministico verso le risorse nel cloud. In tal caso, ExpressRoute è in grado di soddisfare questo requisito.

## <a name="securing-access-from-cloud-resources"></a>Protezione dell'accesso dalle risorse cloud
In generale, quando un'applicazione locale viene trasferita nel cloud tramite migrazione shift-and-lift, viene spostata contemporaneamente ai dati che contiene. Quindi, l'aspetto principale da considerare per le migrazioni shift-and-lift riguarda il blocco dell'accesso alla condivisione file di Azure consentendolo solo alle macchine virtuali o ai servizi di Azure specifici il cui funzionamento richiede tale accesso. 

È possibile scegliere di usare le reti virtuali per limitare le VM o altre risorse di Azure autorizzate a effettuare connessioni di rete (montaggi SMB o chiamate API REST alla condivisione file di Azure). Se si consente il traffico non crittografato verso l'account di archiviazione, è sempre consigliabile inserire la condivisione file di Azure in una rete virtuale. Altrimenti, la decisione di usare o meno le reti virtuali dovrebbe essere dettata da requisiti aziendali e criteri organizzativi.

Il motivo principale per cui consentire il traffico non crittografato verso la condivisione file di Azure è per supportare Windows Server 2008 R2, Windows 7 o altri sistemi operativi meno recenti che accedono alla condivisione file di Azure con SMB 2.1 (o SMB 3.0 senza crittografia per alcune distribuzioni Linux). Non è consigliabile usare SMB 2.1 o SMB 3.0 senza crittografia nei sistemi operativi che supportano SMB 3.0 o versione successiva con crittografia.

## <a name="see-also"></a>Vedere anche
- [Panoramica di File di Azure](storage-files-introduction.md)
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)