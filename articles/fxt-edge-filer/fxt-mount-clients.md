---
title: Montare client nel cluster Microsoft Azure FXT Edge Filer
description: Come montare la cache di archiviazione ibrida di Azure FXT Edge Filer in computer client NFS
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: b48d8d74843947c3e40dc80234560b0147be6eea
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542985"
---
# <a name="tutorial-mount-the-cluster"></a>Esercitazione: Montare il cluster

Questa esercitazione descrive come montare client NFS nel cluster Azure FXT Edge Filer. I client montano i percorsi dello spazio dei nomi virtuale assegnati durante l'aggiunta dell'archiviazione back-end. 

Questa esercitazione descrive quanto segue: 

> [!div class="checklist"]
> * Strategie per il bilanciamento del carico dei client nell'intervallo di indirizzi IP esposti ai client
> * Come costruire un percorso di montaggio da un indirizzo IP esposto ai client e una giunzione dello spazio dei nomi
> * Argomenti da usare in un comando di montaggio

Il completamento di questa esercitazione richiede circa 45 minuti.

## <a name="steps-to-mount-the-cluster"></a>Passaggi per montare il cluster

Completare questi passaggi per connettere computer client al cluster Azure FXT Edge Filer.

1. Decidere come bilanciare il carico del traffico client tra i nodi del cluster. Leggere [Bilanciare il carico del client](#balance-client-load) di seguito per maggiori dettagli. 
1. Identificare l'indirizzo IP del cluster e il percorso di giunzione da montare.
1. Determinare il percorso esposto ai client per il montaggio.
1. Eseguire il [comando mount](#use-recommended-mount-command-options) con gli argomenti appropriati.

## <a name="balance-client-load"></a>Bilanciare il carico del client

Per consentire il bilanciamento delle richieste client tra tutti i nodi del cluster, è consigliabile montare i client per l'intera gamma di indirizzi IP lato client. Questa attività può essere automatizzata in diversi modi.

Per informazioni sul bilanciamento del carico del sistema DNS round robin per il cluster, vedere [Configurare DNS per il cluster Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing). Per usare questo metodo, è necessario mantenere un server DNS, ma questa parte non viene descritta in questi articoli.

Un metodo più semplice per le installazioni di piccole dimensioni consiste nell'usare uno script per assegnare indirizzi IP di tutto l'intervallo in fase di montaggio dei client. 

Altri metodi di bilanciamento del carico possono essere più appropriati per sistemi più complessi o di dimensioni più grandi. Rivolgersi al rappresentante Microsoft o aprire una richiesta di supporto per assistenza. Attualmente Azure Load Balancer *non è supportato* con Azure FXT Edge Filer.

## <a name="create-the-mount-command"></a>Creare il comando mount 

Dal client il comando ``mount`` esegue il mapping del server virtuale (vserver) nel cluster Azure FXT Edge Filer a un percorso nel file system locale. 

Il formato è ``mount <FXT cluster path> <local path> {options}``

Il comando mount è costituito da tre elementi: 

* percorso del cluster: combinazione di indirizzo IP e percorso di giunzione dello spazio dei nomi, descritta di seguito
* percorso locale - percorso nel client 
* opzioni del comando mount: elencate in [Usare le opzioni del comando mount consigliate](#use-recommended-mount-command-options)

### <a name="create-the-cluster-path"></a>Creare il percorso del cluster

Il percorso del cluster è una combinazione dell'*indirizzo IP* del server virtuale (vserver) e del percorso di una *giunzione dello spazio dei nomi*. La giunzione dello spazio dei nomi è un percorso virtuale definito durante l'[aggiunta del sistema di archiviazione](fxt-add-storage.md#create-a-junction).

Ad esempio, se è stato usato ``/fxt/files`` come percorso dello spazio dei nomi, i client monteranno *indirizzo_IP*:/fxt/files nel rispettivo punto di montaggio locale. 

![Finestra di dialogo "Add new junction" (Aggiungi nuova giunzione) con /avere/files nel campo del percorso dello spazio dei nomi](media/fxt-mount/fxt-junction-example.png)

L'indirizzo IP è uno degli indirizzi IP lato client definiti per il server virtuale. L'intervallo di indirizzi IP esposti ai client è disponibile in due posizioni del pannello di controllo del cluster:

* Tabella **VServers** (Server virtuali) (scheda Dashboard) - 

  ![Scheda Dashboard del pannello di controllo con la scheda VServer selezionata nella tabella dati sotto il grafico e la sezione degli indirizzi IP cerchiata](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Pagina delle impostazioni **Client Facing Network** (Rete lato client) - 

  ![Settings (Impostazioni) > VServer (Server virtuale) > pagina di configurazione Client Facing Network (Rete lato client) con evidenziata la sezione Address Range (Intervallo di indirizzi) della tabella per uno specifico server virtuale](media/fxt-mount/fxt-ip-addresses-settings.png)

Combinare l'indirizzo IP e il percorso dello spazio dei nomi in modo da formare il percorso del cluster per il comando mount. 

Esempio di comando mount per i client: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Creare il percorso locale

Il percorso locale per il comando mount deve essere creato dall'utente. È possibile impostare qualsiasi struttura di percorso desiderata come parte dello spazio dei nomi virtuale. Progettare un spazio dei nomi e un percorso locale appropriati per il flusso di lavoro client. 

Per altre informazioni sullo spazio dei nomi esposto ai client, vedere la [panoramica dello spazio dei nomi](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) nella guida alla configurazione del cluster.

Oltre ai percorsi, includere le [opzioni del comando mount](#use-recommended-mount-command-options) descritte di seguito durante il montaggio di ogni client.

### <a name="use-recommended-mount-command-options"></a>Usare le opzioni del comando mount consigliate

Per garantire un montaggio senza problemi del client, passare le impostazioni e gli argomenti seguenti nel comando mount: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Impostazioni obbligatorie | |
--- | --- 
``hard`` | Un montaggio debole per il cluster Azure FXT Edge Filer può provocare errori dell'applicazione e la possibile perdita di dati. 
``proto=netid`` | Questa opzione supporta la gestione appropriata degli errori di rete NFS.
``mountproto=netid`` | Questa opzione supporta la gestione appropriata degli errori di rete per le operazioni di montaggio.
``retry=n`` | Impostare ``retry=30`` per evitare errori di montaggio temporanei (per i montaggi in primo piano è consigliato un valore diverso).

| Impostazioni preferite  | |
--- | --- 
``nointr``            | Se i client usano kernel del sistema operativo meno recenti (prima di aprile 2008) che supportano questa opzione, usarla. L'opzione "intr" è quella predefinita.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver montato i client, è possibile testare il flusso di lavoro e iniziare a usare il cluster.

Se è necessario spostare dati in un nuovo archiviatore di base sul cloud, è possibile sfruttare la struttura della cache usando l'inserimento di dati in parallelo. Alcune strategie vengono descritte in [Spostamento di dati in un cluster vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). Avere vFXT per Azure è un prodotto basato sul cloud che usa una tecnologia di memorizzazione nella cache molto simile ad Azure FXT Edge Filer.

Per informazioni sulla risoluzione dei problemi hardware, vedere [Monitorare lo stato dell'hardware di Azure FXT Edge Filer](fxt-monitor.md). 
