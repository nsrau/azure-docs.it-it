---
title: Prerequisiti della cache HPC di AzureAzure HPC Cache prerequisites
description: Prerequisiti per l'utilizzo della cache HPC di AzurePrerequisites for using Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: ab342dba5c8be2ff3793c0eb36926969b3e364e5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537288"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Prerequisiti per la cache HPC di AzurePrerequisites for Azure HPC Cache

Prima di usare il portale di Azure per creare una nuova cache HPC di Azure, assicurati che l'ambiente soddisfi questi requisiti.

## <a name="azure-subscription"></a>Sottoscrizione di Azure

Si consiglia un abbonamento a pagamento.

## <a name="network-infrastructure"></a>Infrastruttura di rete

Prima di poter utilizzare la cache, è necessario impostare due prerequisiti relativi alla rete:

* Una subnet dedicata per l'istanza della cache HPC di AzureA dedicated subnet for the Azure HPC Cache instance
* Supporto DNS in modo che la cache possa accedere all'archiviazione e ad altre risorse

### <a name="cache-subnet"></a>Subnet cache

La cache HPC di Azure richiede una subnet dedicata con queste qualità:The Azure HPC Cache needs a dedicated subnet with these qualities:

* La subnet deve avere almeno 64 indirizzi IP disponibili.
* La subnet non può ospitare altre macchine virtuali, anche per i servizi correlati, ad esempio i computer client.
* Se si usano più istanze della cache HPC di Azure, ognuna di esse necessita della propria subnet.

La procedura consigliata consiste nel creare una nuova subnet per ogni cache. È possibile creare una nuova rete virtuale e una nuova subnet come parte della creazione della cache.

### <a name="dns-access"></a>Accesso DNS

La cache deve essere DNS per accedere alle risorse esterne alla rete virtuale. A seconda delle risorse in uso, potrebbe essere necessario configurare un server DNS personalizzato e configurare l'inoltro tra tale server e i server DNS di Azure:

* Per accedere agli endpoint di archiviazione BLOB di Azure e ad altre risorse interne, è necessario il server DNS basato su Azure.To access Azure Blob storage endpoints and other internal resources, you need the Azure-based DNS server.
* Per accedere all'archiviazione locale, è necessario configurare un server DNS personalizzato in grado di risolvere i nomi host di archiviazione.

Se è necessario solo l'accesso all'archiviazione BLOB, è possibile usare il server DNS predefinito fornito da Azure per la cache. Tuttavia, se è necessario accedere ad altre risorse, è necessario creare un server DNS personalizzato e configurarlo per inoltrare eventuali richieste di risoluzione specifiche di Azure al server DNS di Azure.However, if you need access to other resources, you should create a custom DNS server and configure it to forward any Azure-specific resolution requests to the Azure DNS server.

Un semplice server DNS può essere utilizzato anche per bilanciare il carico delle connessioni client tra tutti i punti di montaggio della cache disponibili.

Per altre informazioni sulle reti virtuali di Azure e sulle configurazioni del server DNS, vedere [Risoluzione dei](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)nomi per le risorse nelle reti virtuali di Azure.Learn more about Azure virtual networks and DNS server configurations in Name resolution for resources in Azure virtual networks .

## <a name="permissions"></a>Autorizzazioni

Controllare questi prerequisiti relativi alle autorizzazioni prima di iniziare a creare la cache.

* L'istanza della cache deve essere in grado di creare interfacce di rete virtuale (NIC). L'utente che crea la cache deve disporre di privilegi sufficienti nella sottoscrizione per creare schede di interfaccia di rete.

* Se si usa l'archiviazione BLOB, la cache HPC di Azure richiede l'autorizzazione per accedere all'account di archiviazione. Usare il controllo degli accessi in base al ruolo per concedere alla cache l'accesso all'archivio BLOB. Sono necessari due ruoli: Collaboratore account di archiviazione e Collaboratore dati BLOB di archiviazione.

  Seguire le istruzioni in [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per aggiungere i ruoli.

## <a name="storage-infrastructure"></a>Infrastruttura di storage

La cache supporta i contenitori BLOB di Azure o le esportazioni di archiviazione hardware NFS. Aggiungere le destinazioni di archiviazione dopo aver creato la cache.

Ogni tipo di archiviazione presenta prerequisiti specifici.

### <a name="blob-storage-requirements"></a>Requisiti di archiviazione BLOBBlob storage requirements

Se si vuole usare l'archiviazione BLOB di Azure con la cache, è necessario un account di archiviazione compatibile e un contenitore BLOB vuoto popolato con i dati formattati dalla cache HPC di Azure, come descritto in [Spostare i dati nell'archiviazione BLOB](hpc-cache-ingest.md)di Azure.If you want to use Azure Blob storage with your cache, you need a compatible storage account and either an empty Blob container or a container that is populated with Azure HPC Cache formatted data as described in Move data to Azure Blob storage .

Creare l'account prima di tentare di aggiungere una destinazione di archiviazione. È possibile creare un nuovo contenitore quando si aggiunge la destinazione.

Per creare un account di archiviazione compatibile, usare queste impostazioni:To create a compatible storage account, use these settings:

* Prestazioni: **Standard**
* Tipo di account: **StorageV2 (uso generale v2)**
* Replica: **archiviazione con ridondanza locale (LRS)Replication: Locally redundant storage (LRS)**
* Livello di accesso (impostazione predefinita): **Hot**

È consigliabile usare un account di archiviazione nella stessa posizione della cache.

È inoltre necessario concedere all'applicazione cache l'accesso all'account di archiviazione di Azure, come indicato in [Autorizzazioni](#permissions), precedente. Seguire la procedura descritta in [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per assegnare alla cache i ruoli di accesso necessari. Se non si è il proprietario dell'account di archiviazione, fare in modo che il proprietario eseri questo passaggio.

### <a name="nfs-storage-requirements"></a>Requisiti di archiviazione NFS
<!-- linked from configuration.md -->

Se si utilizza un sistema di archiviazione NFS (ad esempio, un sistema NAS hardware locale), assicurarsi che soddisfi questi requisiti. Potrebbe essere necessario collaborare con gli amministratori di rete o i gestori del firewall per il sistema di archiviazione (o il data center) per verificare queste impostazioni.

> [!NOTE]
> La creazione della destinazione di archiviazione avrà esito negativo se la cache non dispone di accesso sufficiente al sistema di archiviazione NFS.

Ulteriori informazioni sono incluse in [Risoluzione dei problemi relativi alla configurazione NAS e](troubleshoot-nas.md)alla destinazione di archiviazione NFS .

* **Connettività di rete:** La cache HPC di Azure richiede l'accesso di rete a larghezza di banda elevata tra la subnet della cache e il data center del sistema NFS. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) o accesso simile è consigliato. Se si utilizza una VPN, potrebbe essere necessario configurarla per bloccare TCP MSS a 1350 per assicurarsi che i pacchetti di grandi dimensioni non siano bloccati. Leggere le restrizioni relative alle dimensioni dei [pacchetti VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) per ulteriori informazioni sulla risoluzione dei problemi relativi alle impostazioni VPN.

* **Accesso alla porta:** La cache deve accedere a porte TCP/UDP specifiche nel sistema di archiviazione. Tipi diversi di storage hanno requisiti di porta diversi.

  Per verificare le impostazioni del sistema di archiviazione, seguire questa procedura.

  * Eseguire `rpcinfo` un comando al sistema di archiviazione per controllare le porte necessarie. Il comando seguente elenca le porte e formatta i risultati pertinenti in una tabella. (Utilizzare l'indirizzo IP del sistema al posto del *<storage_IP>* termine.)

    È possibile emettere questo comando da qualsiasi client Linux in cui è installata l'infrastruttura NFS. Se si utilizza un client all'interno della subnet del cluster, può anche aiutare a verificare la connettività tra la subnet e il sistema di archiviazione.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Assicurarsi che tutte le porte ``rpcinfo`` restituite dalla query consentano il traffico senza restrizioni dalla subnet della cache HPC di Azure.

  * Se non è possibile `rpcinfo` utilizzare il comando, assicurarsi che queste porte di uso comune consentano il traffico in ingresso e in uscita:If you can't use the command, make sure that these commonly used ports allow inbound and outbound traffic:

    | Protocollo | Porta  | Service  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | montato   |
    | TCP/UDP  | 4047  | status   |

    Alcuni sistemi utilizzano numeri di porta diversi per questi servizi - consultare la documentazione del sistema di archiviazione per essere sicuri.

  * Controllare le impostazioni del firewall per assicurarsi che consentano il traffico su tutte queste porte necessarie. Assicurarsi di controllare i firewall usati in Azure e i firewall locali nel data center.

* **Accesso alla directory:** Abilitare `showmount` il comando sul sistema di archiviazione. Cache HPC di Azure usa questo comando per verificare che la configurazione di destinazione dell'archiviazione punti a un'esportazione valida e anche per assicurarsi che più supporti non accedano alle stesse sottodirectory (un rischio di conflitto tra file).

  > [!NOTE]
  > Se il sistema di archiviazione NFS utilizza il sistema operativo ONTAP 9.2 di NetApp, **non abilitare `showmount` **. [Contattare](hpc-cache-support-ticket.md) il servizio e il supporto tecnico Microsoft per assistenza.

  Per ulteriori informazioni sull'accesso all'elenco di directory, vedere [l'articolo relativo](troubleshoot-nas.md#enable-export-listing)alla risoluzione dei problemi di archiviazione NFS .

* **Accesso root** (lettura/scrittura): la cache si connette al sistema back-end come ID utente 0. Controllare queste impostazioni sul sistema di archiviazione:
  
  * Abilitare `no_root_squash`. Questa opzione assicura che l'utente root remoto possa accedere ai file di proprietà di root.

  * Controllare i criteri di esportazione per assicurarsi che non includano restrizioni sull'accesso root dalla subnet della cache.

  * Se nell'archiviazione sono presenti esportazioni che sono sottodirectory di un'altra esportazione, assicurarsi che la cache disponga dell'accesso root al segmento più basso del percorso. Per informazioni dettagliate, leggere [l'accesso root sui percorsi](troubleshoot-nas.md#allow-root-access-on-directory-paths) di directory nell'articolo relativo alla risoluzione dei problemi di archiviazione NFS.

* L'archiviazione back-end NFS deve essere una piattaforma hardware/software compatibile. Per informazioni dettagliate, contattare il team della cache HPC di Azure.Contact the Azure HPC Cache team for details.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'istanza](hpc-cache-create.md) della cache HPC di Azure dal portale di AzureCreate an Azure HPC Cache instance from the Azure portal
