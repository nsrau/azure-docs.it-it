---
title: Matrice di protezione MAB (server di Backup di Azure) v3 UR1
description: Questo articolo offre una matrice di supporto che elenca tutti i carichi di lavoro, i tipi di dati e le installazioni che vengono protetti dal server di Backup di Azure.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: 974820389e84f727a9aab284716b3be9020c6dd8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032596"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>Matrice di protezione MAB (server di Backup di Azure) v3 UR1

Questo articolo elenca i vari server e carichi di lavoro che è possibile proteggere con il server di Backup di Azure. La matrice seguente elenca cosa è possibile proteggere con il server di Backup di Azure.

Usare la matrice seguente per la UR1 di MAB V3:

* Carichi di lavoro - Tipo di carico di lavoro della tecnologia.

* Versione: versione di MAB supportata per i carichi di lavoro.

* Installazione di MAB: il computer o la posizione in cui si vuole installare MAB.

* Protezione e ripristino - Informazioni dettagliate sui carichi di lavoro, ad esempio il contenitore di archiviazione supportato o la distribuzione supportata.

## <a name="protection-support-matrix"></a>Matrice di supporto della protezione

Le sezioni seguenti illustrano in dettaglio la matrice di supporto per la protezione di MAB:

* [Backup di applicazioni](#applications-backup)
* [Backup di macchine virtuali](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Backup di applicazioni

| **Carico di lavoro**               | **Version**                                                  | **Installazione server di Backup di Azure**                       | **server di Backup di Azure supportati** | **Protezione e ripristino**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Computer client (64 bit) | Windows 10                                                  | Server fisico  <br><br>    Macchina virtuale Hyper-V   <br><br>   Macchina virtuale VMware | UR1 V3                            | Volume, condivisione, cartella, file, volumi deduplicati   <br><br>   I volumi protetti devono essere NTFS. FAT e FAT32 non sono supportati.  <br><br>    Le dimensioni dei volumi devono essere di almeno 1 GB. Server di Backup di Azure utilizza Servizio Copia Shadow del volume (VSS) per eseguire lo snapshot dei dati e lo snapshot funziona solo se il volume è di almeno 1 GB. |
| Server (64 bit)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Macchina virtuale di Azure (quando il carico di lavoro è in esecuzione come macchina virtuale di Azure)  <br><br>    Server fisico  <br><br>    Macchina virtuale Hyper-V <br><br>     Macchina virtuale VMware  <br><br>    Azure Stack | UR1 V3                            | Volume, condivisione, cartella, file, volumi deduplicati (NTFS e ReFS)  <br><br>   Stato del sistema e bare metal (non supportato quando il carico di lavoro è in esecuzione come macchina virtuale di Azure) |
| Server (64 bit)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (è necessario installare [Windows Management Frame 4,0](https://www.microsoft.com/download/details.aspx?id=40855)) | Server fisico  <br><br>    Macchina virtuale Hyper-V  <br><br>      Macchina virtuale VMware  <br><br>   Azure Stack | UR1 V3                            | Volume, condivisione, cartella, file, stato del sistema/bare metal        |
| SQL Server                | SQL Server 2019, 2017, 2016 e [SPS supportati](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 e [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) supportati | Server fisico  <br><br>     Macchina virtuale Hyper-V   <br><br>     Macchina virtuale VMware  <br><br>   Macchina virtuale di Azure (quando il carico di lavoro è in esecuzione come macchina virtuale di Azure)  <br><br>     Azure Stack | UR1 V3                            | Tutti gli scenari di distribuzione: database       <br><br>  MAB V3 UR1 supporta il backup dei database SQL su volumi ReFS                  |
| Exchange                   | Exchange 2019, 2016                                         | Server fisico   <br><br>   Macchina virtuale Hyper-V  <br><br>      Macchina virtuale VMware  <br><br>   Azure Stack  <br><br>    Macchina virtuale di Azure (quando il carico di lavoro è in esecuzione come macchina virtuale di Azure) | UR1 V3                            | Proteggi (tutti gli scenari di distribuzione): Exchange Server autonomo, database in un gruppo di disponibilità del database (DAG)  <br><br>    Ripristino (tutti gli scenari di distribuzione): cassetta postale, database delle cassette postali in un gruppo di disponibilità del database    <br><br>  Il backup di Exchange su ReFS è supportato con MAB V3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 con SPs più recente                       | Server fisico  <br><br>    Macchina virtuale Hyper-V <br><br>    Macchina virtuale VMware  <br><br>   Macchina virtuale di Azure (quando il carico di lavoro è in esecuzione come macchina virtuale di Azure)   <br><br>   Azure Stack | UR1 V3                            | Proteggi (tutti gli scenari di distribuzione): Farm, contenuto del server Web front-end  <br><br>    Ripristino (tutti gli scenari di distribuzione): Farm, database, applicazione Web, file o elemento elenco, ricerca SharePoint, server Web front-end  <br><br>    La protezione di una farm di SharePoint che usa la funzionalità AlwaysOn SQL Server 2012 per i database del contenuto non è supportata. |

## <a name="vm-backup"></a>Backup di macchine virtuali

| **Carico di lavoro**                                                 | **Version**                                             | **Installazione server di Backup di Azure**                      | **server di Backup di Azure supportati** | **Protezione e ripristino**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Host Hyper-V-agente protezione MAB in server host Hyper-V, cluster o macchina virtuale | Windows Server 2019, 2016, 2012 R2, 2012               | Server fisico  <br><br>    Macchina virtuale Hyper-V <br><br>    Macchina virtuale VMware | UR1 V3                             | Protezione: computer Hyper-V, volumi condivisi cluster (CSVs)  <br><br>    Ripristino: macchina virtuale, ripristino a livello di elemento di file e cartelle disponibili solo per Windows, volumi e dischi rigidi virtuali |
| Macchine virtuali VMware                                                  | Server VMware 5,5, 6,0 o 6,5, 6,7 (versione con licenza) | Macchina virtuale Hyper-V  <br><br>   Macchina virtuale VMware         | UR1 V3                             | Proteggere: macchine virtuali VMware in volumi condivisi cluster (CSVs), NFS e archiviazione SAN   <br><br>     Ripristino: macchina virtuale, ripristino a livello di elemento di file e cartelle disponibili solo per Windows, volumi e dischi rigidi virtuali <br><br>    VMware vApp non è supportato. |

## <a name="linux"></a>Linux

| **Carico di lavoro** | **Version**                               | **Installazione server di Backup di Azure**                      | **server di Backup di Azure supportati** | **Protezione e ripristino**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux in esecuzione come guest Hyper-V o VMware | Server fisico, macchina virtuale Hyper-V locale, VM Windows in VMWare | UR1 V3                             | Hyper-V deve essere in esecuzione in Windows Server 2012 R2, Windows Server 2016 o Windows Server 2019. Proteggi: intera macchina virtuale   <br><br>   Ripristino: intera macchina virtuale   <br><br>    Sono supportati solo snapshot coerenti a livello di file.    <br><br>   Per un elenco completo delle distribuzioni e delle versioni di Linux supportate, vedere l'articolo [relativo alle distribuzioni di Linux approvate da Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Supporto di Azure ExpressRoute

È possibile eseguire il backup dei dati tramite Azure ExpressRoute con il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft. Il backup sul peering privato non è supportato.

Con peering pubblico: garantire l'accesso ai seguenti domini/indirizzi:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Con il peering Microsoft selezionare i servizi/le aree e i valori della community pertinenti seguenti:

* Azure Active Directory (12076:5060)
* Area Microsoft Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)
* Archiviazione di Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)

Per ulteriori informazioni, vedere i [requisiti di routing di ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Il peering pubblico è deprecato per i nuovi circuiti.

## <a name="cluster-support"></a>Supporto di cluster

Il server di Backup di Azure può proteggere i dati nelle applicazioni in cluster seguenti:

* File server

* SQL Server

* Hyper-V: se si protegge un cluster Hyper-V usando l'agente protezione MAB con scalabilità orizzontale, non è possibile aggiungere la protezione secondaria per i carichi di lavoro di Hyper-V protetti.

* Exchange Server - Il server di Backup di Azure può proteggere i cluster di dischi non condivisi per le versioni di Exchange Server supportate (replica continua cluster) e può inoltre proteggere Exchange Server configurato per la replica continua locale.

* SQL Server - Il server di Backup di Azure non supporta il backup dei database di SQL Server ospitati in volumi condivisi cluster (CSV).

Server di Backup di Azure possibile proteggere i carichi di lavoro del cluster che si trovano nello stesso dominio del server MAB e in un dominio figlio o trusted. Per proteggere le origini dati in domini o gruppi di lavoro non attendibili, usare l'autenticazione di NTLM o del certificato per un singolo server, o l'autenticazione del certificato solo per un cluster.
