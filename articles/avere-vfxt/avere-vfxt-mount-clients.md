---
title: Montare il cluster Avere vFXT - Azure
description: Come montare i client con Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: c461b379629927e8f367fad9bfc70b87413f47b7
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255395"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montare il cluster Avere vFXT  

Seguire questa procedura per connettere i computer client al cluster vFXT.

1. Decidere come bilanciare il carico del traffico client tra i nodi del cluster. Leggere [Bilanciare il carico del client](#balance-client-load) di seguito per maggiori dettagli. 
1. Identificare l'indirizzo IP e il percorso di giunzione da montare.
1. Eseguire il [comando mount](#mount-command-arguments) con gli argomenti appropriati.

## <a name="balance-client-load"></a>Bilanciare il carico del client

Per consentire il bilanciamento delle richieste client tra tutti i nodi del cluster, è consigliabile montare i client per l'intera gamma di indirizzi IP lato client. Sono disponibili diverse semplici modalità per automatizzare questa attività.

> [!TIP] 
> Per sistemi più complessi o di dimensioni maggiori potrebbero essere appropriati altri metodi di bilanciamento del carico ([aprire un ticket di supporto](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) per assistenza).
> 
> Se si preferisce usare un server DNS per il bilanciamento del carico automatico lato server, è necessario configurare e gestire il server DNS all'interno di Azure. In tal caso, è possibile configurare il DNS round robin per il cluster vFXT in base alle indicazioni di questo documento: [Configurazione DNS del cluster Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Esempio di script di montaggio di un client con bilanciamento del carico

Questo esempio di codice usa indirizzi IP client come elemento di casualità per distribuire i client a tutti gli indirizzi IP disponibili del cluster vFXT.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default 
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

La funzione precedente fa parte dell'esempio di Batch disponibile nel sito degli [esempi di Avere vFXT](https://github.com/Azure/Avere#tutorials).

## <a name="create-the-mount-command"></a>Creare il comando mount 

> [!NOTE]
> Se non è stato creato un nuovo contenitore BLOB durante la creazione del cluster Avere vFXT, seguire la procedura descritta in [Configurare l'archiviazione](avere-vfxt-add-storage.md) prima di provare a montare i client.

Dal client, il comando ``mount`` esegue il mapping del server virtuale (vserver) nel cluster vFXT a un percorso nel file system locale. Il formato è ``mount <vFXT path> <local path> {options}``

Il comando mount è costituito da tre elementi: 

* percorso vFXT - combinazione di indirizzo IP e percorso della giunzione dello spazio dei nomi descritta di seguito
* percorso locale - percorso nel client 
* opzioni del comando mount - elencate in [Argomenti del comando mount](#mount-command-arguments)

### <a name="junction-and-ip"></a>Giunzione e IP

Il percorso del server virtuale è una combinazione del relativo *indirizzo IP* e del percorso a una *giunzione dello spazio dei nomi*. La giunzione dello spazio dei nomi è un percorso virtuale definito quando è stato aggiunto il sistema di archiviazione.

Se il cluster è stato creato con l'archiviazione BLOB, il percorso dello spazio dei nomi è `/msazure`

Esempio: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Se l'archiviazione è stata aggiunta dopo la creazione del cluster, il percorso della giunzione dello spazio dei nomi corrisponde al valore impostato in **Namespace path** (Percorso dello spazio dei nomi) al momento della creazione della giunzione. Ad esempio, se è stato usato ``/avere/files`` come percorso dello spazio dei nomi, i client monteranno *indirizzo_IP*:/avere/files nel relativo punto di montaggio locale.

![Finestra di dialogo "Add new junction" (Aggiungi nuova giunzione) con /avere/files nel campo del percorso dello spazio dei nomi](media/avere-vfxt-create-junction-example.png)


L'indirizzo IP è uno degli indirizzi IP lato client definiti per il server virtuale. L'intervallo di indirizzi IP lato client è disponibile in due posizioni nel pannello di controllo di Avere:

* Tabella **VServers** (Server virtuali) (scheda Dashboard) - 

  ![Scheda Dashboard del pannello di controllo di Avere con la scheda VServers (Server virtuali) selezionata nella tabella dati sotto il grafico e la sezione dell'indirizzo IP evidenziata](media/avere-vfxt-ip-addresses-dashboard.png)

* Pagina delle impostazioni **Client Facing Network** (Rete lato client) - 

  ![Settings (Impostazioni) > VServer (Server virtuale) > pagina di configurazione Client Facing Network (Rete lato client) con evidenziata la sezione Address Range (Intervallo di indirizzi) della tabella per uno specifico server virtuale](media/avere-vfxt-ip-addresses-settings.png)

Oltre ai percorsi, includere gli [argomenti del comando mount](#mount-command-arguments) descritti di seguito durante il montaggio di ogni client.

### <a name="mount-command-arguments"></a>Argomenti del comando mount

Per garantire un montaggio senza problemi del client, passare le impostazioni e gli argomenti seguenti nel comando mount: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Impostazioni obbligatorie | |
--- | --- 
``hard`` | A un soft mount al cluster vFXT sono associati errori dell'applicazione e possibili perdite di dati. 
``proto=netid`` | Questa opzione supporta la gestione appropriata degli errori di rete NFS.
``mountproto=netid`` | Questa opzione supporta la gestione appropriata degli errori di rete per le operazioni di montaggio.
``retry=n`` | Impostare ``retry=30`` per evitare errori di montaggio temporanei (per i montaggi in primo piano è consigliato un valore diverso).

| Impostazioni preferite  | |
--- | --- 
``nointr``            | L'opzione "nointr" è preferibile per i client con kernel legacy (antecedenti ad aprile 2008) che supportano questa opzione. Si noti che l'opzione "intr" è l'impostazione predefinita.


## <a name="next-steps"></a>Passaggi successivi

Dopo che i client sono stati montati, è possibile usarli per popolare l'archivio dati back-end (core filer). Fare riferimento a questi documenti per altre informazioni sulle attività di configurazione aggiuntive:

* [Spostare dati nel core filer del cluster](avere-vfxt-data-ingest.md): come usare più client e thread per caricare dati in modo efficiente
* [Personalizzare l'ottimizzazione dei cluster](avere-vfxt-tuning.md): adattare le impostazioni dei cluster in base al carico di lavoro
* [Gestire il cluster](avere-vfxt-manage-cluster.md): come avviare o interrompere il cluster e gestire i nodi
