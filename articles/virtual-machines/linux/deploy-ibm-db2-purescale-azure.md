---
title: Distribuire IBM DB2 pureScale in Azure
description: Informazioni su come distribuire un'architettura di esempio usata di recente per eseguire la migrazione dall'ambiente IBM DB2 in esecuzione su z/OS a IBM DB2 pureScale in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 8eb8075454dc3a49e9525d566c34c64bab8be5a0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083440"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Distribuire IBM DB2 pureScale in Azure

Questo articolo illustra come distribuire un'[architettura di esempio](ibm-db2-purescale-azure.md) usata di recente per eseguire la migrazione dall'ambiente IBM DB2 in esecuzione su z/OS a IBM DB2 pureScale in Azure.

Per seguire le procedure di migrazione, vedere gli script di installazione nel repository [DB2onAzure](https://aka.ms/db2onazure) su GitHub. Questi script sono basati sull'architettura per un tipico carico di lavoro OLTP (Online Transaction Processing) di medie dimensioni.

## <a name="get-started"></a>Attività iniziali

Per distribuire questa architettura, scaricare ed eseguire lo script deploy.sh disponibile nel repository [DB2onAzure](https://aka.ms/db2onazure) su GitHub.

Il repository contiene anche script da usare per configurare un dashboard Grafana che può essere usato per eseguire query su Prometheus, il sistema open-source di monitoraggio e invio di avvisi incluso in DB2.

> [!NOTE]
> Lo script deploy.sh nel client crea chiavi SSH private e le passa al modello di distribuzione tramite HTTPS. Per una maggiore sicurezza, è consigliabile usare [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) per archiviare segreti, chiavi e password.

## <a name="how-the-deployment-script-works"></a>Funzionamento dello script di distribuzione

Lo script deploy.sh crea e configura le risorse di Azure per questa architettura. Lo script chiede all'utente di specificare la sottoscrizione di Azure e le macchine virtuali usate nell'ambiente di destinazione, quindi esegue le operazioni seguenti:

-   Configura il gruppo di risorse, la rete virtuale e le subnet in Azure per l'installazione

-   Configura i gruppi di sicurezza di rete e SSH per l'ambiente

-   Configura le schede di interfaccia di rete nelle macchine virtuali GlusterFS e DB2 pureScale

-   Crea le macchine virtuali di archiviazione GlusterFS

-   Crea la macchina virtuale jumpbox

-   Crea le macchine virtuali DB2 pureScale

-   Crea la macchina virtuale di controllo a cui DB2 pureScale effettua il ping

-   Crea una macchina virtuale Windows da usare per i test ma non vi installa nulla

Gli script di distribuzione configurano quindi la rete di archiviazione virtuale (vSAN) iSCSI per lo spazio di archiviazione condiviso in Azure. In questo esempio iSCSI si connette a GlusterFS. Questa soluzione consente anche di installare le destinazioni iSCSI come singolo nodo di Windows. iSCSI fornisce un'interfaccia di archiviazione a blocchi condivisa su TCP/IP che consente alla procedura di configurazione di DB2 pureScale di usare l'interfaccia di un dispositivo per connettersi a uno spazio di archiviazione condiviso. Per informazioni di base su GlusterFS, vedere l'argomento [Architecture: Types of volumes](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) (Architettura - Tipi di volumi) nella documentazione di Gluster.

Gli script di distribuzione eseguono questi passaggi generali:

1.  Usare GlusterFS per configurare un cluster di archiviazione condivisa in Azure. Per questo passaggio servono almeno due nodi Linux. Per informazioni dettagliate sulla configurazione, vedere [Setting up Red Hat Gluster Storage in Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) (Configurazione di Red Hat Gluster Storage in Microsoft Azure) nella documentazione di Red Hat Gluster.

2.  Configurare un'interfaccia iSCSI Direct nei server Linux di destinazione per GlusterFS. Per informazioni dettagliate sulla configurazione, vedere [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) (iSCSI in GlusterFS) nella guida all'amministrazione di GlusterFS.

3.  Configurare l'iniziatore iSCSI nelle macchine virtuali Linux. L'iniziatore accederà al cluster GlusterFS mediante una destinazione iSCSI. Per informazioni dettagliate sulla configurazione, vedere [How To Configure An iSCSI Target And Initiator In Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) (Come configurare una destinazione e un iniziatore iSCSI in Linux) nella documentazione di RootUsers.

4.  Installare GlusterFS come livello di archiviazione dell'interfaccia iSCSI.

Dopo che gli script creano il dispositivo iSCSI, il passaggio finale consiste nell'installare DB2 pureScale. Nell'ambito dell'installazione di DB2 pureScale, [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (noto in precedenza come GPFS) viene compilato e installato nel cluster GlusterFS. Questo file system a cluster consente a DB2 pureScale di condividere dati tra le macchine virtuali che eseguono il motore di DB2 pureScale. Per altre informazioni, vedere la documentazione di [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) nel sito Web IBM.

## <a name="db2-purescale-response-file"></a>File di risposta di DB2 pureScale

Il repository GitHub include il file di risposta DB2server.rsp, che consente di generare uno script automatizzato per l'installazione di DB2 pureScale. La tabella seguente contiene un elenco delle opzioni di DB2 pureScale usate dal file di risposta per l'installazione. È possibile personalizzare il file di risposta in base alle esigenze dell'ambiente.

> [!NOTE]
> Un file di risposta di esempio, DB2server.rsp, è incluso nel repository [DB2onAzure](https://aka.ms/db2onazure) su GitHub. Se si usa questo file, è necessario modificarlo in modo che sia compatibile con l'ambiente in uso.

| Nome schermata               | Campo                                        | Valore                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Procedura guidata                   |                                              | New Install (Nuova installazione)                                                                                           |
| Choose a Product (Scegliere un prodotto)          |                                              | DB2 Version 11.1.3.3. Server Editions with DB2 pureScale                                              |
| Configurazione             | Directory                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Select the installation type (Selezionare il tipo di installazione)                 | Typical (Tipica)                                                                                               |
|                           | I agree to the IBM terms (Accetto le condizioni IBM)                     | Selezionato                                                                                               |
| Instance Owner (Proprietario istanza)            | Existing User For Instance, User name (Utente esistente per istanza, Nome utente)        | DB2sdin1                                                                                              |
| Fenced User (Utente confinato)               | Existing User, User name (Utente esistente, Nome utente)                     | DB2sdfe1                                                                                              |
| Cluster File System (File system cluster)       | Shared disk partition device path (Percorso dispositivo partizione disco condiviso)            | /dev/dm-2                                                                                             |
|                           | Punto di montaggio                                  | /DB2sd\_1804a                                                                                         |
|                           | Shared disk for data (Disco condiviso per i dati)                         | /dev/dm-1                                                                                             |
|                           | Mount point (Data) (Punto di montaggio - Dati)                           | /DB2fs/datafs1                                                                                        |
|                           | Shared disk for log (Disco condiviso per il log)                          | /dev/dm-0                                                                                             |
|                           | Mount point (Log) (Punto di montaggio - Log)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 Cluster Services Tiebreaker, Device path (Tiebreaker Servizi cluster DB2, Percorso dispositivo) | /dev/dm-3                                                                                             |
| Host List (Elenco host)                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | Preferred primary CF (CF primario preferito)                         | cf1                                                                                                   |
|                           | Preferred secondary CF (CF secondario preferito)                       | cf2                                                                                                   |
| Response File and Summary (File di risposta e riepilogo) | prima opzione                                 | Install DB2 Server Edition with the IBM DB2 pureScale feature and save my settings in a response file (Installa DB2 Server Edition con la funzionalità IBM DB2 pureScale e salva le impostazioni in un file di risposta) |
|                           | Response file name (Nome file di risposta)                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Note su questa distribuzione

- I valori di /dev-dm0, /dev-dm1, /dev-dm2 e /dev-dm3 possono cambiare dopo il riavvio della macchina virtuale in cui si sta eseguendo l'installazione (d0 nello script automatizzato). Per trovare i valori corretti, è possibile eseguire il comando seguente prima di completare il file di risposta sul server in cui verrà eseguita l'installazione:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Gli script di installazione usano alias per i dischi iSCSI in modo che i nomi reali possano essere trovati facilmente.

- Quando lo script di installazione viene eseguito in d0, i valori di **/dev/dm-\*** potrebbero essere diversi in d1, cf0 e cf1. La differenza dei valori non influisce sull'installazione di DB2 pureScale.

## <a name="troubleshooting-and-known-issues"></a>Risoluzione dei problemi e problemi noti

Il repository GitHub include una knowledge base gestita dagli autori in cui sono elencati i problemi che possono verificarsi e le soluzioni che è possibile adottare. Questo può avvenire, ad esempio, nei casi seguenti:

-   Si cerca di connettersi all'indirizzo IP del gateway.

-   Si sta compilando la licenza pubblica generale.

-   L'handshake di sicurezza tra gli host non riesce.

-   Il programma di installazione di DB2 rileva un file system esistente.

-   IBM Spectrum Scale viene installato manualmente.

-   DB2 pureScale viene installato dopo che IBM Spectrum Scale è stato creato.

-   DB2 pureScale e IBM Spectrum Scale vengono rimossi.

Per altre informazioni su questi e altri problemi noti, vedere il file kb.md nel repository [DB2onAzure](https://aka.ms/DB2onAzure).

## <a name="next-steps"></a>Passaggi successivi

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) (iSCSI in GlusterFS)

-   [Creating required users for a DB2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2) (Creazione degli utenti necessari per l'installazione della funzionalità DB2 pureScale)

-   [DB2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html) (DB2icrt - Comando Crea istanza)

-   [DB2 pureScale Clusters Data Solution](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1) (Soluzione dati cluster DB2 pureScale)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform Modernization Alliance: IBM DB2 on Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx) (Platform Modernization Alliance: IBM DB2 in Azure)

-   [Data center virtuale di Azure: guida al lift and shift](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
