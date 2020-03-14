---
title: Prerequisiti della cache HPC di Azure
description: Prerequisiti per l'uso della cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: 40d282ad30a800a5e5a36a8d2211ec8da7ce63ec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271850"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Prerequisiti per cache HPC di Azure

Prima di usare la portale di Azure per creare una nuova cache HPC di Azure, assicurarsi che l'ambiente soddisfi questi requisiti.

## <a name="azure-subscription"></a>Sottoscrizione di Azure

È consigliabile usare una sottoscrizione A pagamento.

> [!NOTE]
> Durante i primi mesi della versione GA, il team di cache HPC di Azure deve aggiungere la sottoscrizione all'elenco di accesso prima di poterla usare per creare un'istanza della cache. Questa procedura consente di garantire che ogni cliente ottenga una risposta di alta qualità dalle proprie cache. Compilare [questo modulo](https://aka.ms/onboard-hpc-cache) per richiedere l'accesso.

## <a name="network-infrastructure"></a>Infrastruttura di rete

Prima di poter usare la cache, è necessario configurare due prerequisiti relativi alla rete:

* Una subnet dedicata per l'istanza di cache HPC di Azure
* Supporto DNS in modo che la cache possa accedere all'archiviazione e ad altre risorse

### <a name="cache-subnet"></a>Subnet cache

Per la cache HPC di Azure è necessaria una subnet dedicata con queste qualità:

* La subnet deve avere almeno 64 indirizzi IP disponibili.
* La subnet non può ospitare altre macchine virtuali, anche per servizi correlati come i computer client.
* Se si usano più istanze di cache HPC di Azure, ciascuna di esse richiede una propria subnet.

La procedura consigliata consiste nel creare una nuova subnet per ogni cache. È possibile creare una nuova rete virtuale e una nuova subnet come parte della creazione della cache.

### <a name="dns-access"></a>Accesso DNS

Per accedere alle risorse all'esterno della rete virtuale, la cache deve essere DNS. A seconda delle risorse usate, potrebbe essere necessario configurare un server DNS personalizzato e configurare l'invio tra il server e i server DNS di Azure:

* Per accedere agli endpoint di archiviazione BLOB di Azure e ad altre risorse interne, è necessario il server DNS basato su Azure.
* Per accedere all'archiviazione locale, è necessario configurare un server DNS personalizzato in grado di risolvere i nomi host di archiviazione.

Se è necessario accedere solo all'archiviazione BLOB, è possibile usare il server DNS predefinito fornito da Azure per la cache. Tuttavia, se è necessario accedere ad altre risorse, è necessario creare un server DNS personalizzato e configurarlo per l'invio di eventuali richieste di risoluzione specifiche di Azure al server DNS di Azure.

Un server DNS semplice può anche essere usato per bilanciare il carico delle connessioni client tra tutti i punti di montaggio della cache disponibili.

Per altre informazioni sulle reti virtuali di Azure e sulle configurazioni del server DNS, vedere [risoluzione dei nomi per le risorse nelle reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Autorizzazioni

Verificare questi prerequisiti relativi alle autorizzazioni prima di iniziare a creare la cache.

* L'istanza della cache deve essere in grado di creare interfacce di rete virtuali (NIC). Per creare schede di interfaccia di rete, l'utente che crea la cache deve disporre di privilegi sufficienti nella sottoscrizione.

* Se si usa l'archiviazione BLOB, la cache HPC di Azure deve disporre dell'autorizzazione per accedere all'account di archiviazione. Usare il controllo degli accessi in base al ruolo (RBAC) per concedere all'archivio BLOB l'accesso alla cache. Sono necessari due ruoli: collaboratore account di archiviazione e collaboratore dati BLOB di archiviazione.

  Per aggiungere i ruoli, seguire le istruzioni riportate in [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) .

## <a name="storage-infrastructure"></a>Infrastruttura di archiviazione

La cache supporta i contenitori BLOB di Azure o le esportazioni di archiviazione hardware NFS. Aggiungere le destinazioni di archiviazione dopo la creazione della cache.

Ogni tipo di archiviazione ha prerequisiti specifici.

### <a name="blob-storage-requirements"></a>Requisiti per l'archiviazione BLOB

Se si vuole usare l'archiviazione BLOB di Azure con la cache, è necessario un account di archiviazione compatibile e un contenitore BLOB vuoto o un contenitore popolato con i dati formattati della cache HPC di Azure, come descritto in [spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md).

Creare l'account prima di provare ad aggiungere una destinazione di archiviazione. È possibile creare un nuovo contenitore quando si aggiunge la destinazione.

Per creare un account di archiviazione compatibile, usare le impostazioni seguenti:

* Prestazioni: **standard**
* Tipo di account: **archiviazione V2 (utilizzo generico v2)**
* Replica: **archiviazione con ridondanza locale (con ridondanza locale)**
* Livello di accesso (predefinito): **attivo**

È consigliabile usare un account di archiviazione nella stessa posizione della cache.
<!-- clarify location - same region or same resource group or same virtual network? -->

È anche necessario concedere all'applicazione cache l'accesso all'account di archiviazione di Azure, come indicato nelle [autorizzazioni](#permissions)sopra riportate. Seguire la procedura descritta in [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per assegnare alla cache i ruoli di accesso necessari. Se non si è il proprietario dell'account di archiviazione, chiedere al proprietario di eseguire questo passaggio.

### <a name="nfs-storage-requirements"></a>Requisiti di archiviazione NFS

Se si usa un sistema di archiviazione NFS (ad esempio, un sistema NAS hardware locale), assicurarsi che soddisfi questi requisiti. Per verificare queste impostazioni, potrebbe essere necessario collaborare con gli amministratori di rete o i responsabili del firewall per il sistema di archiviazione (o data center).

> [!NOTE]
> La creazione della destinazione di archiviazione avrà esito negativo se la cache non dispone di accesso sufficiente al sistema di archiviazione NFS.

Altre informazioni sono disponibili in [risolvere i problemi relativi alla configurazione di NAS e alla destinazione di archiviazione NFS](troubleshoot-nas.md).

* **Connettività di rete:** La cache HPC di Azure necessita di un accesso di rete a larghezza di banda elevata tra la subnet della cache e il data center del sistema NFS. È consigliabile usare [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) o un accesso simile. Se si usa una VPN, potrebbe essere necessario configurarla in modo da bloccare la connessione MSS TCP a 1350 per assicurarsi che i pacchetti di grandi dimensioni non vengano bloccati. Per ulteriori informazioni sulla risoluzione dei problemi relativi alle impostazioni VPN, vedere [restrizioni sulle dimensioni del pacchetto VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) .

* **Accesso alla porta:** La cache deve accedere a porte TCP/UDP specifiche sul sistema di archiviazione. Tipi diversi di archiviazione hanno requisiti di porta diversi.

  Per verificare le impostazioni del sistema di archiviazione, seguire questa procedura.

  * Eseguire un comando `rpcinfo` per il sistema di archiviazione per verificare le porte necessarie. Il comando seguente elenca le porte e formatta i risultati rilevanti in una tabella. (Usare l'indirizzo IP del sistema al posto del *< storage_IP termine >* .)

    È possibile eseguire questo comando da qualsiasi client Linux in cui è installata l'infrastruttura NFS. Se si usa un client all'interno della subnet del cluster, può essere utile anche per verificare la connettività tra la subnet e il sistema di archiviazione.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Assicurarsi che tutte le porte restituite dalla query di ``rpcinfo`` consentano il traffico illimitato dalla subnet della cache HPC di Azure.

  * Oltre alle porte restituite dal comando `rpcinfo`, assicurarsi che queste porte utilizzate comunemente consentano il traffico in ingresso e in uscita:

    | Protocollo | Porta  | Service  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

  * Controllare le impostazioni del firewall per assicurarsi che consentano il traffico su tutte le porte necessarie. Assicurarsi di controllare i firewall usati in Azure e nei firewall locali nel data center.

* **Accesso alla directory:** Abilitare il comando `showmount` nel sistema di archiviazione. Cache HPC di Azure usa questo comando per verificare che la configurazione di destinazione di archiviazione punti a un'esportazione valida e anche per assicurarsi che più montaggi non accedano alle stesse sottodirectory (rischio per collisioni di file).

  > [!NOTE]
  > Se il sistema di archiviazione NFS usa il sistema operativo ONTAP 9,2 di NetApp, non **abilitare `showmount`** . Per assistenza, [contattare il servizio Microsoft e il supporto tecnico](hpc-cache-support-ticket.md) .

  Altre informazioni sull'accesso all'elenco delle directory sono disponibili nell' [articolo sulla risoluzione dei problemi](troubleshoot-nas.md#enable-export-listing)di destinazione di archiviazione NFS.

* **Accesso alla radice:** La cache si connette al sistema back-end come ID utente 0. Controllare le impostazioni seguenti nel sistema di archiviazione:
  
  * Abilitare `no_root_squash`. Questa opzione assicura che l'utente root remoto possa accedere ai file di proprietà della radice.

  * Controllare Esporta criteri per assicurarsi che non includano restrizioni per l'accesso radice dalla subnet della cache.

  * Se per l'archiviazione sono presenti esportazioni che sono sottodirectory di un'altra esportazione, verificare che la cache disponga dell'accesso radice al segmento più basso del percorso. Per informazioni dettagliate, vedere [accesso alla radice nei percorsi di directory](troubleshoot-nas.md#allow-root-access-on-directory-paths) nell'articolo Risoluzione dei problemi di destinazione archiviazione NFS.

* L'archiviazione back-end NFS deve essere una piattaforma hardware/software compatibile. Per informazioni dettagliate, contattare il team di cache HPC di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'istanza di cache HPC di Azure](hpc-cache-create.md) dalla portale di Azure
