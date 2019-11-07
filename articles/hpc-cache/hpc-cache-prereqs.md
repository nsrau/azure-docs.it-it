---
title: Prerequisiti della cache HPC di Azure
description: Prerequisiti per l'uso della cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: ca7a12f45f8d907ee65df85e349883e4c14af47a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582142"
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

Se è necessario accedere solo all'archiviazione BLOB, è possibile usare il server DNS predefinito fornito da Azure per la cache. Tuttavia, se è necessario accedere ad altre risorse, è necessario creare un server DNS personalizzato e configurarlo per l'invio di eventuali richieste di risoluzione specifiche di Azure al server DNS di Azure. È anche possibile usare un semplice server DNS per bilanciare il carico delle connessioni client tra tutti i punti di montaggio della cache disponibili.

Per altre informazioni sulle reti virtuali di Azure e sulle configurazioni del server DNS, vedere [risoluzione dei nomi per le risorse nelle reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Autorizzazioni

Verificare questi prerequisiti relativi alle autorizzazioni prima di iniziare a creare la cache.

* L'istanza della cache deve essere in grado di creare interfacce di rete virtuali (NIC). Per creare schede di interfaccia di rete, l'utente che crea la cache deve disporre di privilegi sufficienti nella sottoscrizione.

* Se si usa l'archiviazione BLOB, la cache HPC di Azure deve disporre dell'autorizzazione per accedere all'account di archiviazione. È possibile usare il controllo degli accessi in base al ruolo (RBAC) per concedere all'archivio BLOB l'accesso alla cache. Sono necessari due ruoli: collaboratore account di archiviazione e collaboratore dati BLOB di archiviazione. Per aggiungere i ruoli, seguire le istruzioni riportate in [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) .

## <a name="storage-infrastructure"></a>Infrastruttura di archiviazione

La cache supporta i contenitori BLOB di Azure o le esportazioni di archiviazione hardware NFS. Aggiungere le destinazioni di archiviazione dopo la creazione della cache.

Ogni tipo di archiviazione ha prerequisiti specifici.

### <a name="nfs-storage-requirements"></a>Requisiti di archiviazione NFS

Se si usa l'archiviazione hardware locale, la cache deve avere un accesso di rete a larghezza di banda elevata al Data Center dalla relativa subnet. È consigliabile usare [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) o un accesso simile.

L'archiviazione back-end NFS deve essere una piattaforma hardware/software compatibile. Per informazioni dettagliate, contattare il team di cache HPC di Azure.

### <a name="blob-storage-requirements"></a>Requisiti per l'archiviazione BLOB

Se si vuole usare l'archiviazione BLOB di Azure con la cache, è necessario un account di archiviazione compatibile e un contenitore BLOB vuoto o un contenitore popolato con i dati formattati della cache HPC di Azure, come descritto in [spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md).

Creare l'account e il contenitore prima di tentare di aggiungerlo come destinazione di archiviazione.

Per creare un account di archiviazione compatibile, usare le impostazioni seguenti:

* Prestazioni: **standard**
* Tipo di account: **archiviazione V2 (utilizzo generico v2)**
* Replica: **archiviazione con ridondanza locale (con ridondanza locale)**
* Livello di accesso (predefinito): **attivo**

È consigliabile usare un account di archiviazione nella stessa posizione della cache.
<!-- clarify location - same region or same resource group or same virtual network? -->

È anche necessario concedere all'applicazione cache l'accesso all'account di archiviazione di Azure. Seguire la descrizione in [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per assegnare alla cache il collaboratore per l'account di archiviazione per i ruoli di accesso e i dati BLOB di archiviazione. Se non si è il proprietario dell'account di archiviazione, chiedere al proprietario di eseguire questo passaggio.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'istanza di cache HPC di Azure](hpc-cache-create.md) dalla portale di Azure
