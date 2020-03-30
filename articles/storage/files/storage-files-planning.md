---
title: Pianificazione per la distribuzione dei File di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76a96d36387f55889b65f16ea1ca6ec07359c377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502443"
---
# <a name="planning-for-an-azure-files-deployment"></a>Pianificazione per la distribuzione dei file di Azure
[File](storage-files-introduction.md) di Azure possono essere distribuiti in due modi principali: montando direttamente le condivisioni file di Azure senza server o memorizzando nella cache le condivisioni file di Azure in locale usando Sincronizzazione file di Azure.Azure Files can be deployed in two main ways: by mounting the serverless Azure file shares or by caching Azure file shares on-premises using Azure File Sync. L'opzione di distribuzione scelta consente di modificare gli aspetti da considerare durante la pianificazione della distribuzione. 

- **Montaggio diretto di una condivisione file di Azure:** poiché File di Azure fornisce l'accesso SMB, è possibile montare le condivisioni file di Azure in locale o nel cloud usando il client SMB standard disponibile in Windows, macOS e Linux.Direct mount of an Azure file share : Since Azure Files provides SMB access, you can mount Azure file shares on-premises or in the cloud using the standard SMB client available in Windows, macOS, and Linux. Poiché le condivisioni file di Azure sono senza server, la distribuzione per gli scenari di produzione non richiede la gestione di un file server o di un dispositivo NAS. Ciò significa che non è necessario applicare patch software o scambiare i dischi fisici. 

- **Memorizzare nella cache**la condivisione file di Azure in locale con Sincronizzazione file di Azure: Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure, mantenendo la flessibilità, le prestazioni e la compatibilità di un file server locale. Sincronizzazione file di Azure trasforma un server Windows Server locale (o cloud) in una cache rapida della condivisione file di Azure.Azure File Sync transforms an on-premises (or cloud) Windows Server into a quick cache of your Azure file share. 

Questo articolo affronta principalmente le considerazioni sulla distribuzione per la distribuzione di una condivisione file di Azure che venga montata direttamente da un client locale o cloud. Per pianificare una distribuzione di Sincronizzazione file di Azure, vedere Pianificazione di una distribuzione di Sincronizzazione file di Azure.To plan for an Azure File Sync deployment, see [Planning for an Azure File Sync deployment.](storage-sync-files-planning.md)

## <a name="management-concepts"></a>Concetti relativi alla gestione
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Quando si distribuiscono condivisioni file di Azure in account di archiviazione, è consigliabile:When deploying Azure file shares into storage accounts, we recommend:

- Distribuire le condivisioni file di Azure solo in account di archiviazione con altre condivisioni file di Azure.Deploying Azure file shares into only storage accounts with other Azure file shares. Anche se gli account di archiviazione GPv2 consentono di avere account di archiviazione misti, poiché le risorse di archiviazione, ad esempio condivisioni file di Azure e contenitori BLOB, condividono i limiti dell'account di archiviazione, la combinazione di risorse può rendere più difficile la risoluzione dei problemi problemi di prestazioni in un secondo momento. 

- Prestare attenzione alle limitazioni delle operazioni di I/O al secondo di un account di archiviazione durante la distribuzione di condivisioni file di Azure.Prepaying attention to a storage account's IOPS limitations when deploying Azure file shares. Idealmente, è consigliabile eseguire il mapping delle condivisioni file 1:1 con gli account di archiviazione, tuttavia ciò potrebbe non essere sempre possibile a causa di vari limiti e restrizioni, sia dall'organizzazione che da Azure. Quando non è possibile distribuire una sola condivisione file in un account di archiviazione, considerare quali condivisioni saranno altamente attive e quali condivisioni saranno meno attive per garantire che le condivisioni file più calde non vengano messe insieme nello stesso account di archiviazione.

- Distribuire gli account GPv2 e FileStorage e aggiornare GPv1 e gli account di archiviazione classici solo quando vengono trovati nell'ambiente. 

## <a name="identity"></a>Identità
Per accedere a una condivisione file di Azure, l'utente della condivisione file deve essere autenticato e disporre dell'autorizzazione per accedere alla condivisione. Questa operazione viene eseguita in base all'identità dell'utente che accede alla condivisione file. File di Azure si integra con tre provider di identità principali:Azure Files integrates with three main identity providers:
- **Active Directory di proprietà del cliente** (anteprima): gli account di archiviazione di Azure possono essere aggiunti a un dominio di proprietà del cliente, Windows Server Active Directory, proprio come un file server di Windows Server o un dispositivo NAS. Il controller di dominio Active Directory può essere distribuito in locale, in una macchina virtuale di Azure o anche come macchina virtuale in un altro provider di cloud. File di Azure è indipendente dalla posizione in cui è ospitato il controller di dominio. Dopo aver aggiunto un account di archiviazione a un dominio, l'utente finale può montare una condivisione file con l'account utente con cui ha eseguito l'accesso al PC. L'autenticazione basata su Active Directory utilizza il protocollo di autenticazione Kerberos.
- **Servizi di dominio Azure Active Directory (Azure AD DS):** Servizi di dominio Active Directory di Azure offre un controller di dominio Active Directory gestito da Microsoft che può essere usato per le risorse di Azure.Azure Active Directory Domain Services (Azure AD DS): Azure AD DS provides a Microsoft-managed Active Directory Domain Controller that can be used for Azure resources. L'aggiunta di un dominio all'account di archiviazione a Servizi di dominio Active Directory di Azure AD offre vantaggi simili all'aggiunta di un dominio ad Active Directory di proprietà del cliente. Questa opzione di distribuzione è particolarmente utile per gli scenari lift-and-shift delle applicazioni che richiedono autorizzazioni basate su Active Directory.This deployment option is most useful for application lift-and-shift scenarios that require AD-based permissions. Poiché Servizi di dominio Active Directory fornisce l'autenticazione basata su Active Directory, questa opzione usa anche il protocollo di autenticazione Kerberos.Since Azure AD DS provides AD-based authentication, this option also uses the Kerberos authentication protocol.
- **Chiave dell'account di archiviazione**di Azure: le condivisioni file di Azure possono anche essere montate con una chiave dell'account di archiviazione di Azure.Azure storage account key : Azure file shares may also be mounted with an Azure storage account key. Per montare una condivisione file in questo modo, il nome dell'account di archiviazione viene usato come nome utente e la chiave dell'account di archiviazione come password. L'uso della chiave dell'account di archiviazione per montare la condivisione file di Azure è in effetti un'operazione di amministratore, poiché la condivisione file montata dirà autorizzazioni complete per tutti i file e le cartelle nella condivisione, anche se dispongono di ACL. Quando si usa la chiave dell'account di archiviazione per l'montaggio su SMB, viene usato il protocollo di autenticazione NTLMv2.When using the storage account key to mount over SMB, the NTLMv2 authentication protocol is used.

Per i clienti che eseguono la migrazione da file server locali o la creazione di nuove condivisioni file in File di Azure che si comportano come file server Windows o appliance NAS, è consigliabile aggiungere il dominio **all'account** di archiviazione ad Active Directory di proprietà del cliente. Per altre informazioni sull'aggiunta al dominio dell'account di archiviazione ad un'Active Directory di proprietà del cliente, vedere [Panoramica](storage-files-active-directory-overview.md)di Azure Files Active Directory .

Se si intende usare la chiave dell'account di archiviazione per accedere alle condivisioni file di Azure, è consigliabile usare gli endpoint del servizio come descritto nella sezione [Rete.If](#networking) you intend to use the storage account key to access your Azure file shares, we recommend using service endpoints as described in the Networking section.

## <a name="networking"></a>Rete
Le condivisioni file di Azure sono accessibili da qualsiasi posizione tramite l'endpoint pubblico dell'account di archiviazione. Ciò significa che le richieste autenticate, ad esempio le richieste autorizzate dall'identità di accesso di un utente, possono avere origine in modo sicuro dall'interno o dall'esterno di Azure.This means that authenticated requests, such as requests authorized by a user's logon identity, can originate securely from inside or outside of Azure. In molti ambienti dei clienti, un montaggio iniziale della condivisione file di Azure nella workstation locale avrà esito negativo, anche se i rimonti dalle macchine virtuali di Azure hanno esito positivo. La ragione di questo è che molte organizzazioni e provider di servizi Internet (ISP) bloccano la porta utilizzata da SMB per comunicare, porta 445. Passare a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

Per sbloccare l'accesso alla condivisione file di Azure, sono disponibili due opzioni principali:To unblock access to your Azure file share, you have two main options:

- Sbloccare la porta 445 per la rete locale dell'organizzazione. Azure file shares may only be externally accessed via the public endpoint using internet safe protocols such as SMB 3.0 and the FileREST API. Questo è il modo più semplice per accedere alla condivisione file di Azure da locale poiché non richiede una configurazione di rete avanzata oltre a modificare le regole delle porte in uscita dell'organizzazione, tuttavia, è consigliabile rimuovere le versioni legacy e deprecate di SMB protocollo, vale a dire SMB 1.0. Per informazioni su come eseguire questa operazione, vedere [Protezione di Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e Protezione di [Linux](storage-how-to-use-files-linux.md#securing-linux).

- Accedere alle condivisioni file di Azure tramite una connessione ExpressRoute o VPN. Quando si accede alla condivisione file di Azure tramite un tunnel di rete, è possibile montare la condivisione file di Azure come una condivisione file locale poiché il traffico SMB non attraversa i limiti dell'organizzazione.   

Anche se da un punto di vista tecnico è notevolmente più semplice montare le condivisioni file di Azure tramite l'endpoint pubblico, ci aspettiamo che la maggior parte dei clienti sceglierà di montare le condivisioni file di Azure su una connessione ExpressRoute o VPN. A tale scopo, è necessario configurare quanto segue per l'ambiente:  

- Tunneling di rete tramite ExpressRoute, da sito a sito o VPN da punto a sito: il tunneling in una rete virtuale consente l'accesso alle condivisioni file di Azure da locale, anche se la porta 445 è bloccata.Network tunneling using **ExpressRoute, Site-to-Site VPN or Point-to-Site VPN**: Tunneling into a virtual network allows accessing Azure file shares from on-premises, even if port 445 is blocked.
- **Endpoint privati:** gli endpoint privati assegnano all'account di archiviazione un indirizzo IP dedicato dall'interno dello spazio di indirizzi della rete virtuale. Ciò consente il tunneling di rete senza la necessità di aprire reti locali fino a tutti gli intervalli di indirizzi IP di proprietà dei cluster di archiviazione di Azure.This enables network tunneling without needing to open on-premises networks up to all of the IP address ranges owned by the Azure storage clusters. 
- **Inoltro DNS:** configurare il DNS locale per risolvere il nome `storageaccount.file.core.windows.net` dell'account di archiviazione (ad esempio per le aree del cloud pubblico) da risolvere nell'indirizzo IP degli endpoint privati.

Per pianificare la rete associata alla distribuzione di una condivisione file di Azure, vedere [Considerazioni](storage-files-networking-overview.md)sulla rete di File di Azure.To plan for the networking associated with deploying an Azure file share, see Azure Files networking considerations .

## <a name="encryption"></a>Crittografia
File di Azure supporta due diversi tipi di crittografia: la crittografia in transito, correlata alla crittografia usata durante il montaggio/accesso alla condivisione file di Azure, e la crittografia inattivi, correlata alla modalità di crittografia dei dati quando vengono archiviati su disco. 

### <a name="encryption-in-transit"></a>Crittografia in transito
Per impostazione predefinita, la crittografia in transito è abilitata per tutti gli account di archiviazione di Azure. Quindi, quando si monta una condivisione file su SMB o vi si accede tramite il protocollo FileREST (ad esempio tramite il portale di Azure, PowerShell/interfaccia della riga di comando o Azure SDK), File di Azure consentirà la connessione solo se viene eseguita con SMB 3.0 o versione successiva con crittografia oppure HTTPS. I client che non supportano SMB 3.0 oppure che supportano SMB 3.0 ma non la crittografia SMB non potranno montare la condivisione file di Azure se è abilitata la crittografia in transito. Per altre informazioni sui sistemi operativi che supportano SMB 3.0 con crittografia, vedere la documentazione dettagliata per [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Tutte le versioni correnti di PowerShell, interfaccia della riga di comando e SDK supportano HTTPS.  

È possibile disabilitare la crittografia in transito per un account di archiviazione di Azure. Quando la crittografia è disabilitata, File di Azure consentirà anche SMB 2.1, SMB 3.0 senza crittografia e chiamate API FileREST non crittografate su HTTP. La crittografia in transito viene in genere disabilitata principalmente per supportare un'applicazione legacy che deve essere eseguita in un sistema operativo meno recente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente. File di Azure consente connessioni SMB 2.1 solo all'interno della stessa area di Azure della condivisione file di Azure. Un client SMB 2.1 situato all'esterno dell'area di Azure in cui si trova la condivisione file di Azure, ad esempio in locale o in un'area di Azure differente, non potrà accedere alla condivisione file.

È consigliabile garantire l'abilitazione della crittografia dei dati in transito.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Livelli di archiviazione
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

In generale, le funzionalità di File di Azure e l'interoperabilità con altri servizi sono le stesse tra le condivisioni file premium e le condivisioni file standard, tuttavia esistono alcune differenze importanti:In general, Azure Files features and interoperability with other services are the same between premium file shares and standard file shares, however there are a few important differences:
- **Modello di fatturazione**
    - Le condivisioni file Premium vengono fatturate utilizzando un modello di fatturazione di cui è stato eseguito il provisioning, il che significa che si paga per la quantità di spazio di archiviazione di cui si esegue il provisioning anziché per la quantità di spazio di archiviazione effettivamente richiesto. 
    - Le condivisioni file standard vengono fatturate utilizzando un modello con pagamento in base al consumo, che include un costo di archiviazione di base per la quantità di spazio di archiviazione effettivamente utilizzata e quindi un costo di transazione aggiuntivo in base a come si utilizza la condivisione. Con le condivisioni file standard, la fattura aumenterà se si usa (lettura/scrittura/montaggio) più la condivisione file di Azure.With standard file shares, your bill will increase if you use (read/write/mount) the Azure file share more.
- **Opzioni di ridondanza**
    - Le condivisioni file Premium sono disponibili solo per l'archiviazione con ridondanza locale (LRS) e di zona ridondante. 
    - Sono disponibili condivisioni file standard per l'archiviazione con ridondanza locale, ridondante di zona, ridondanza geografica (GRS) e geozona ridondante.
- **Dimensione massima della condivisione file**
    - È possibile eseguire il provisioning di condivisioni file Premium per un massimo di 100 TiB senza alcun lavoro aggiuntivo.
    - Per impostazione predefinita, le condivisioni file standard possono estendersi solo fino a 5 TiB, anche se il limite di condivisione può essere aumentato a 100 TiB scegliendo il flag di funzionalità dell'account di archiviazione di condivisione file di *grandi dimensioni.* Le condivisioni file standard possono estendersi fino a 100 TiB solo per gli account di archiviazione ridondanti localmente ridondanti o di zona. Per ulteriori informazioni sull'aumento delle dimensioni delle condivisioni file, vedere [Abilitare e creare condivisioni file](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)di grandi dimensioni.
- **Disponibilità a livello di area**
    - Le condivisioni file Premium non sono disponibili in tutte le aree geografiche e il supporto ridondante della zona è disponibile in un sottoinsieme più piccolo di aree. Per scoprire se le condivisioni file premium sono attualmente disponibili nella tua area geografica, vedi la pagina [dei prodotti disponibili per area](https://azure.microsoft.com/global-infrastructure/services/?products=storage) geografica per Azure.To find out if premium file shares are currently available in your region, see the products available by region page for Azure. Per sapere quali aree supportano il supporto per l'area di disponibilità di Azure, vedere Supporto della zona di disponibilità di [Azure per area.](../../availability-zones/az-overview.md#services-support-by-region) Per aiutarci a dare priorità a nuove regioni e funzionalità di livello premium, compila questo [sondaggio](https://aka.ms/pfsfeedback).
    - Le condivisioni file standard sono disponibili in ogni area di Azure.Standard file shares are available in every Azure region.
- Il servizio Azure Kubernetes (AKS) supporta le condivisioni file premium nella versione 1.13 e successive.

Una volta creata una condivisione file come servizio Premium o una condivisione file standard, non è possibile convertirla automaticamente nell'altro livello. Se si desidera passare all'altro livello, è necessario creare una nuova condivisione file in tale livello e copiare manualmente i dati dalla condivisione originale alla nuova condivisione creata. Ti consigliamo `robocopy` di `rsync` usare per Windows o per macOS e Linux per eseguire tale copia.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Informazioni sul provisioning per condivisioni file premiumUnderstanding provisioning for premium file shares
Il provisioning delle condivisioni file premium viene effettuato in base a un rapporto fisso tra GiB, operazioni di I/O al secondo e velocità effettiva. Per ogni GiB di cui viene effettuato il provisioning, alla condivisione viene assegnata un'operazione di I/O al secondo con 0,1 MiB/s di velocità effettiva fino ai limiti massimi per singola condivisione. Il valore minimo di provisioning consentito è 100 GiB con il minimo di operazioni di I/O al secondo e velocità effettiva.

In base ad approssimazioni ottimali, tutte le condivisioni possono essere potenziate fino a tre operazioni di I/O al secondo per ogni GiB di archiviazione di cui viene effettuato il provisioning per 60 minuti o più, a seconda della dimensione della condivisione. Le nuove condivisioni iniziano con il credito di burst totale in base alla capacità sottoposta a provisioning.

È necessario effettuare il provisioning delle azioni in incrementi di 1 GiB. La dimensione minima è 100 GiB, la dimensione successiva è 101 GiB e così via.

> [!TIP]
> IOPS di base : 1 - GiB di cui è stato eseguito il provisioning. (Fino a un massimo di 100.000 operazioni di I/O al secondo).
>
> Limite di burst: 3 operazioni di I/O al secondo della linea di base. (Fino a un massimo di 100.000 operazioni di I/O al secondo).
>
> Velocità di uscita: 60 MiB/s - 0,06 GiB con provisioning
>
> velocità d'ingresso: 40 MiB/s , 0,04 usd, giB con provisioning

La dimensione della condivisione di cui è stato eseguito il provisioning è specificata dalla quota di condivisione. La quota di condivisione può essere aumentata in qualsiasi momento, ma può essere diminuita solo dopo 24 ore dall'ultimo aumento. Dopo aver atteso 24 ore senza un aumento della quota, è possibile ridurre la quota di condivisione tutte le volte che si desidera, fino a quando non si aumenta di nuovo. Le modifiche alla scala di IOPS/velocità effettiva saranno effettive entro pochi minuti dalla modifica delle dimensioni.

È possibile ridurre le dimensioni della quota di cui è stato eseguito il provisioning al di sotto del GiB usato. In questo caso, non si perderanno dati, ma verranno comunque fatturati le dimensioni utilizzate e si riceveranno le prestazioni (operazioni di I/O al secondo di base, velocità effettiva e iOPS burst) della condivisione di cui è stato eseguito il provisioning, non le dimensioni utilizzate.

Nella tabella seguente vengono illustrati alcuni esempi di queste formule per le dimensioni delle condivisioni di cui è stato eseguito il provisioning:

|Capacità (GiB) | Operazioni di I/O al secondo di base | Burst IOPS | Uscita (MiB/s) | Ingresso (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Fino a 300     | 66   | 44   |
|500         | 500     | Fino a 1.500   | 90   | 60   |
|1.024       | 1.024   | Fino a 3.072   | 122   | 81   |
|5120       | 5120   | Fino a 15.360  | 368   | 245   |
|10,240      | 10,240  | Fino a 30.720  | 675 | 450   |
|33,792      | 33,792  | Fino a 100.000 | 2,088 | 1,392   |
|51,200      | 51,200  | Fino a 100.000 | 3.132 | 2,088   |
|102,400     | 100,000 | Fino a 100.000 | 6,204 | 4,136   |

> [!NOTE]
> Le prestazioni delle condivisioni file sono soggette ai limiti di rete del computer, alla larghezza di banda di rete disponibile, alle dimensioni di I/O, al parallelismo e a molti altri fattori. Ad esempio, in base ai test interni con 8 dimensioni di I/O di lettura/scrittura KiB, una singola macchina virtuale Windows, *Standard F16s_v2*, connessa alla condivisione file premium su SMB potrebbe ottenere 20K operazioni di I/O al secondo di lettura e operazioni di I/O al secondo di scrittura 15K. Con 512 MiB di dimensioni di I/O di lettura/scrittura, la stessa macchina virtuale potrebbe raggiungere 1.1 GiB/s in ingresso e 370 MiB/s velocità effettiva in ingresso. Per ottenere la massima scalabilità delle prestazioni, distribuire il carico tra più macchine virtuali. Fare riferimento alla [guida alla risoluzione dei problemi](storage-troubleshooting-files-performance.md) per alcuni problemi di prestazioni comuni e soluzioni alternative.

#### <a name="bursting"></a>Scoppio
Le condivisioni file Premium possono eseguire il burst delle operazioni di I/O al secondo fino a un fattore di tre. Il bursting è automatizzato e funziona sulla base di un sistema di credito. Il bursting funziona al massimo sforzo e il limite di burst non è una garanzia, le condivisioni file possono *raggiungere* il limite.

I crediti si accumulano in un bucket burst ogni volta che il traffico per la condivisione file è inferiore alle operazioni di I/O al secondo di base. Ad esempio, una quota 100 GiB ha 100 Operazioni di I/O al secondo di base. Se il traffico effettivo nella condivisione era di 40 operazioni di I/O al secondo per un intervallo specifico di 1 secondo, le 60 operazioni di I/O al secondo inutilizzate vengono accreditate in un bucket burst. Questi crediti verranno quindi utilizzati in un secondo momento quando le operazioni supererebbero le operazioni di BASE IOPs.

> [!TIP]
> Dimensione del bucket burst: IOPS di base : 2 x 3600.

Ogni volta che una quota supera le operazioni di I/O al secondo di base e dispone di crediti in un bucket burst, verrà scoppiata. Le azioni possono continuare a scoppiare fino a quando i crediti rimangono, anche se le azioni inferiori a 50 TiB rimarranno solo al limite di burst per un massimo di un'ora. Le azioni superiori a 50 TiB possono tecnicamente superare questo limite di un'ora, fino a due ore, ma si basa sul numero di crediti burst maturati. Ogni I/O oltre le operazioni di I/O al secondo di base consuma un credito e una volta che tutti i crediti vengono consumati la quota tornerebbe alle operazioni di I/O al secondo di base.

I crediti azionari hanno tre stati:

- Accumulare, quando la condivisione file utilizza meno delle operazioni di I/O al secondo di base.
- Rifiutare, quando la condivisione file è bursting.
- Costante rimanente, quando non sono presenti crediti o operazioni di I/O al secondo di base sono in uso.

Le nuove condivisioni file iniziano con il numero completo di crediti nel bucket burst. I crediti burst non verranno accumulati se le operazioni di I/O al secondo della condivisione scendono al di sotto delle operazioni di I/O al secondo di base a causa della limitazione da parte del server.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Consentire alle condivisioni file standard di estendersi fino a 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilità a livello di area
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Ridondanza
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrazione
In molti casi, non si stabilirà una nuova condivisione file netta per l'organizzazione, benvale la migrazione di una condivisione file esistente da un file server locale o da un dispositivo NAS a File di Azure. Esistono molti strumenti, forniti sia da Microsoft che da terze parti, per eseguire una migrazione a una condivisione file, ma possono essere suddivisi approssimativamente in due categorie:

- Strumenti che gestiscono gli attributi del **file system, ad esempio ACL e timestamp**:
    - **[Sincronizzazione file di](storage-sync-files-planning.md)** Azure: Sincronizzazione file di Azure può essere usato come metodo per inserire dati in una condivisione file di Azure, anche quando la distribuzione finale desiderata non deve mantenere una presenza locale. Sincronizzazione file di Azure può essere installata sul posto nelle distribuzioni esistenti di Windows Server 2012 R2, Windows Server 2016 e Windows Server 2019.Azure File Sync can be installed in place on existing Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019 deployments. Un vantaggio dell'uso di Sincronizzazione file di Azure come meccanismo di inserimento è che gli utenti finali possono continuare a usare la condivisione file esistente sul posto. La condivisione file di Azure può essere eseguita al termine del caricamento in background di tutti i dati.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy è uno strumento di copia ben noto fornito con Windows e Windows Server. Robocopy può essere usato per trasferire i dati in File di Azure montando la condivisione file in locale e quindi usando il percorso montato come destinazione del comando Robocopy.

- **Strumenti che non gestiscono gli attributi del file system**:
    - **Data Box:** Data Box fornisce un meccanismo di trasferimento dati offline per la spedizione fisica dei dati in Azure.Data Box : Data Box provides an offline data transfer mechanism to physical ship data into Azure. Questo metodo è progettato per aumentare la velocità effettiva e risparmiare larghezza di banda, ma attualmente non supporta gli attributi del file system come timestamp e ACL.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: AzCopy è un'utilità della riga di comando progettata per copiare i dati da e verso File di Azure, oltre ad Archiviazione BLOB di Azure usando semplici comandi con prestazioni ottimali.

## <a name="next-steps"></a>Passaggi successivi
* [Planning for an Azure File Sync Deployment](storage-sync-files-planning.md) (Pianificazione della distribuzione di Sincronizzazione file di Azure)
* [Distribuzione di File di Azure](storage-files-deployment-guide.md)
* [Distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
