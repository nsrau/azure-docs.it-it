---
title: Azure Site Recovery Deployment Planner per distribuzioni da VMware ad Azure | Microsoft Docs
description: Guida dell'utente di Azure Site Recovery Deployment Planner.
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/04/2017
ms.author: nisoneji
ms.openlocfilehash: 0910d5802d64ca637b3ecd1e392a6df8629c7f25
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Azure Site Recovery Deployment Planner per distribuzioni da VMware ad Azure
Questo articolo contiene la guida dell'utente di Azure Site Recovery Deployment Planner per distribuzioni di produzione da VMware ad Azure.

## <a name="overview"></a>Panoramica

Prima di iniziare a proteggere le macchine virtuali VMware con Site Recovery, allocare larghezza di banda sufficiente, in base alla frequenza di modifica dei dati giornaliera per soddisfare l'obiettivo del punto di ripristino (RPO) desiderato. Assicurarsi di distribuire il numero corretto di server di configurazione e di server di elaborazione in locale.

È anche necessario creare il tipo e il numero corretti di account di archiviazione di Azure di destinazione. Per creare account di archiviazione Standard o Premium, si considera la crescita nei server di produzione di origine a causa del maggiore utilizzo nel tempo. Si sceglie il tipo di archiviazione per ogni VM in base alle caratteristiche del carico di lavoro (ad esempio, le operazioni di I/O al secondo in lettura/scrittura o la varianza dati) e ai limiti di Site Recovery.

Azure Site Recovery Deployment Planner (versione 2) è uno strumento da riga di comando disponibile per gli scenari di ripristino di emergenza da Hyper-V ad Azure e da VMware ad Azure. Questo strumento consente di profilare le VM VMware in modalità remota, senza alcun impatto sulla produzione, per determinare i requisiti di larghezza di banda e archiviazione di Azure per operazioni di replica e failover di test. È possibile eseguire lo strumento senza installare alcun componente di Site Recovery in locale. Per ottenere risultati accurati sulla velocità effettiva ottenuta, è tuttavia consigliabile eseguire lo strumento di pianificazione in un server Windows che soddisfi i requisiti minimi del server di configurazione di Site Recovery che dovrà essere successivamente distribuito nell'ambito di uno dei primi passaggi della distribuzione di produzione.

Lo strumento indica i dettagli seguenti:

**Valutazione della compatibilità**

* Valutazione dell'idoneità delle VM in base a numero di dischi, dimensioni dei dischi, operazioni di I/O al secondo, varianza, tipo di avvio (EFI/BIOS) e versione del sistema operativo
 
**Valutazione della larghezza di banda di rete necessaria rispetto al valore RPO**

* Larghezza di banda stimata necessaria per la replica differenziale
* Velocità effettiva ottenibile da Site Recovery dall'ambiente locale ad Azure
* Numero di VM da riunire in batch in base alla larghezza di banda stimata per completare la replica iniziale in un determinato periodo di tempo
* RPO che può essere ottenuto per una determinata larghezza di banda
* Impatto sull'obiettivo RPO desiderato se viene effettuato il provisioning di una larghezza di banda inferiore.

**Requisiti dell'infrastruttura di Azure**

* Requisito relativo al tipo di archiviazione (account di archiviazione Standard o Premium) per ogni VM
* Numero totale di account di archiviazione Standard e Premium da configurare per la replica
* Suggerimenti di denominazione degli account di archiviazione in base alle linee guida di archiviazione di Azure
* Selezione host degli account di archiviazione per tutte le VM
* Numero di core di Azure da configurare prima del failover di test o del failover nella sottoscrizione
* Dimensioni consigliate per ogni VM di Azure locale

**Requisiti dell'infrastruttura locale**
* Numero di server di configurazione e di server di elaborazione da distribuire in locale

**Stima dei costi del ripristino di emergenza in Azure**
* Stima dei costi totali del ripristino di emergenza in Azure: costi di calcolo, archiviazione, rete e licenza di Azure Site Recovery
* Analisi dei costi dettagliata per ogni VM


>[!IMPORTANT]
>
>Poiché è probabile che l'utilizzo aumenti nel corso del tempo, tutti i calcoli dello strumento precedenti vengono eseguiti presumendo un fattore di crescita del 30% nelle caratteristiche del carico di lavoro e usando un valore di 95° percentile di tutte le metriche di profilatura (operazioni di I/O al secondo in lettura/scrittura, varianza e così via). Entrambi questi elementi (fattore di crescita e calcolo percentile) sono configurabili. Per altre informazioni sul fattore di crescita, vedere la sezione "Considerazioni sul fattore di crescita". Per altre informazioni sul valore percentile, vedere la sezione "Valore percentile usato per il calcolo".
>

## <a name="support-matrix"></a>Matrice di supporto

| | **Da VMware ad Azure** |**Da Hyper-V ad Azure**|**Da Azure ad Azure**|**Da Hyper-V al sito secondario**|**Da VMware al sito secondario**
--|--|--|--|--|--
Scenari supportati |Sì|Sì|No|Sì*|No
Versione supportata | vCenter 6.5, 6.0 o 5.5| Windows Server 2016, Windows Server 2012 R2 | ND |Windows Server 2016, Windows Server 2012 R2|ND
Configurazione supportata|vCenter, ESXi| Cluster Hyper-V, host Hyper-V|ND|Cluster Hyper-V, host Hyper-V|ND|
Numero di server che è possibile profilare per ogni istanza in esecuzione di Azure Site Recovery Deployment Planner |Singolo (è possibile profilare le VM appartenenti a un solo server vCenter o a un solo server ESXi alla volta)|Multipli (è possibile profilare contemporaneamente le VM in più host o cluster di host)| ND |Multipli (è possibile profilare contemporaneamente le VM in più host o cluster di host)| ND

*Lo strumento è destinato principalmente allo scenario di ripristino di emergenza da Hyper-V ad Azure. Per il ripristino di emergenza da Hyper-V al sito secondario, può essere usato solo per conoscere le raccomandazioni sul lato di origine, ad esempio larghezza di banda di rete necessaria, spazio di archiviazione libero necessario in ogni server Hyper-V di origine e numeri di invio in batch e definizioni dei batch della replica iniziale.  Ignorare le raccomandazioni di Azure e i costi del report. L'operazione per misurare la velocità effettiva non è inoltre applicabile per lo scenario di ripristino di emergenza da Hyper-V al sito secondario.

## <a name="prerequisites"></a>Prerequisiti
Lo strumento prevede due fasi principali: profilatura e generazione di report. È anche disponibile una terza opzione per calcolare solo la velocità effettiva. I requisiti per il server da cui vengono avviate la profilatura e la misurazione della velocità effettiva sono elencati nella tabella seguente:

| Requisito server | Descrizione|
|---|---|
|Profilatura e misurazione della velocità effettiva| <ul><li>Sistema operativo: Microsoft Windows Server 2016 o Microsoft Windows Server 2012 R2<br>(idealmente corrispondente almeno alle [dimensioni consigliate per il server di configurazione](https://aka.ms/asr-v2a-on-prem-components))</li><li>Configurazione del computer: 8 vCPU, 16 GB di RAM, disco rigido da 300 GB</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Microsoft Visual C++ Redistributable per Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Accesso Internet ad Azure da questo server</li><li>Account di archiviazione di Azure</li><li>Accesso di amministratore al server</li><li>Almeno 100 GB di spazio libero su disco (presumendo 1000 VM con una media di tre dischi ognuna, profilate per 30 giorni)</li><li>Le impostazioni a livello di statistiche di VMware vCenter devono essere impostate su 2 o su un valore superiore</li><li>Consentire la porta 443, usata da ASR Deployment Planner per la connessione al server vCenter o all'host ESXi</ul></ul>|
| Generazione di report | Un PC o server Windows con Microsoft Excel 2013 o versione successiva |
| Autorizzazioni utente | Autorizzazioni di sola lettura per l'account utente usato per accedere al server VMware vCenter o all'host VMware vSphere ESXi durante la profilatura |

> [!NOTE]
>
>Lo strumento può profilare solo VM con dischi VMDK e RDM. Non può profilare VM con dischi iSCSI o NFS. Site Recovery supporta dischi iSCSI e NFS per i server VMware, ma, poiché lo strumento di pianificazione della distribuzione non è nel guest ed esegue la profilatura usando solo i contatori delle prestazioni di vCenter, lo strumento non ha visibilità per questi tipi di dischi.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Scaricare ed estrarre lo strumento Deployment Planner
1. Scaricare la versione più recente di [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).  
Lo strumento è compresso in una cartella ZIP. La versione corrente dello strumento supporta solo lo scenario da VMware ad Azure.

2. Copiare la cartella ZIP nel server Windows dal quale si intende eseguire lo strumento.  
È possibile eseguire lo strumento da Windows Server 2012 R2 se il server ha accesso alla rete per connettersi al server vCenter o all'host vSphere ESXi in cui si trovano le VM da profilare. È tuttavia consigliabile eseguire lo strumento in un server la cui configurazione hardware soddisfi la [linea guida per il ridimensionamento del server di configurazione](https://aka.ms/asr-v2a-on-prem-components). Se i componenti di Site Recovery sono già stati distribuiti in locale, eseguire lo strumento dal server di configurazione.

 È consigliabile avere la stessa configurazione hardware del server di configurazione (che ha un server di elaborazione predefinito) per il server in cui si esegue lo strumento. Tale configurazione assicura che la velocità effettiva ottenuta segnalata dallo strumento corrisponda alla velocità effettiva che Site Recovery può raggiungere durante la replica. Il calcolo della velocità effettiva dipende dalla larghezza di banda di rete disponibile nel server e dalla configurazione hardware (CPU, memoria e così via) del server. Se si esegue lo strumento da un altro server, la velocità effettiva viene calcolata da tale server a Microsoft Azure. Poiché inoltre la configurazione hardware del server può essere diversa da quella del server di configurazione, la velocità effettiva ottenuta che viene segnalata dallo strumento può non essere corretta.

3. Estrarre la cartella ZIP.  
La cartella contiene più file e sottocartelle. Il file eseguibile è ASRDeploymentPlanner.exe e si trova nella cartella padre.

    Esempio:  
    Copiare il file ZIP nell'unità E:\ ed estrarlo.
   E:\ASR Deployment Planner_v2.0zip

    E:\ASR Deployment Planner_v2.0\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Aggiornamento alla versione più recente di Deployment Planner
Se si ha una versione precedente di Deployment Planner, eseguire una di queste operazioni:
 * Se la versione più recente non contiene una correzione della profilatura e la profilatura è già in corso nella versione corrente dell'utilità di pianificazione, continuare la profilatura.
 * Se la versione più recente contiene una correzione della profilatura, è consigliabile arrestare la profilatura nella versione corrente e riavviare la profilatura con la nuova versione.


 >[!NOTE]
 >
 >Quando si avvia la profilatura con la nuova versione, passare lo stesso percorso della directory di output in modo che lo strumento accodi i dati del profilo nei file esistenti. Un set completo di dati profilati verrà usato per generare il report. Se si passa una directory di output diversa, vengono creati nuovi file e i dati profilati precedenti non vengono usati per generare il report.
 >
 >Ogni nuova utilità di pianificazione delle distribuzioni è un aggiornamento cumulativo del file ZIP. Non è necessario copiare i file più recenti nella cartella precedente. È possibile creare e usare una nuova cartella.

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire Deployment Planner](site-recovery-vmware-deployment-planner-run.md).
