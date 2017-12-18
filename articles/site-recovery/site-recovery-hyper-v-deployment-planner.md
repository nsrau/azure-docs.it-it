---
title: Azure Site Recovery Deployment Planner per distribuzioni da Hyper-V ad Azure | Microsoft Docs
description: Guida dell'utente di Azure Site Recovery Deployment Planner per lo scenario da Hyper-V ad Azure.
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
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: 815148d2a39ce8b18092619c9687a56b457c8339
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>Azure Site Recovery Deployment Planner per distribuzioni da Hyper-V ad Azure
Questo articolo contiene la guida dell'utente di Azure Site Recovery Deployment Planner per distribuzioni di produzione da Hyper-V ad Azure.

## <a name="overview"></a>Panoramica
Prima di iniziare a proteggere le macchine virtuali Hyper-V con Site Recovery, allocare larghezza di banda sufficiente in base alla frequenza di modifica dei dati giornaliera per soddisfare l'obiettivo del punto di ripristino (RPO) desiderato e allocare spazio libero di archiviazione sufficiente in ogni volume di archiviazione locale Hyper-V.

È anche necessario creare il tipo e il numero corretti di account di archiviazione di Azure di destinazione. Per creare account di archiviazione Standard o Premium, si considera la crescita nei server di produzione di origine a causa del maggiore utilizzo nel tempo. Si sceglie il tipo di archiviazione per ogni VM in base alle caratteristiche del carico di lavoro, ad esempio, le operazioni di I/O al secondo in lettura/scrittura o la varianza dati, e ai limiti di Azure Site Recovery. 

Azure Site Recovery Deployment Planner (versione 2) è uno strumento da riga di comando disponibile per gli scenari di ripristino di emergenza da Hyper-V ad Azure e da VMware ad Azure. Questo strumento consente di profilare le VM Hyper-V presenti in più host Hyper-V in modalità remota, senza alcun impatto sulla produzione, per determinare i requisiti di larghezza di banda e archiviazione di Azure per operazioni di replica e failover di test/failover. È possibile eseguire lo strumento senza installare alcun componente di Azure Site Recovery in locale. Per ottenere risultati accurati sulla velocità effettiva ottenuta, è tuttavia consigliabile eseguire la pianificazione in un server Windows con la stessa configurazione hardware dei server Hyper-V che si useranno per abilitare la protezione per il ripristino di emergenza in Azure. 

Lo strumento indica i dettagli seguenti:

**Valutazione della compatibilità**

* Valutazione dell'idoneità delle VM in base a numero di dischi, dimensioni dei dischi, operazioni di I/O al secondo, varianza e alcune caratteristiche delle VM.

**Valutazione della larghezza di banda di rete necessaria rispetto al valore RPO**

* Larghezza di banda stimata necessaria per la replica differenziale
* Velocità effettiva ottenibile da Azure Site Recovery dall'ambiente locale ad Azure
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
* Spazio di archiviazione libero necessario in ogni volume di archiviazione di Hyper-V per la replica iniziale e la replica delta corrette per assicurare che la replica delle VM non provochi tempi di inattività non desiderati per le applicazioni di produzione
* Frequenza di copia massima da impostare per la replica Hyper-V

**Linee guida sull'invio in batch della replica iniziale** 
* Numero di batch di VM da usare per la protezione
* Elenco delle VM in ogni batch
* Ordine in cui ogni batch deve essere protetto
* Tempo stimato per il completamento della replica iniziale di ogni batch

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
Lo strumento ha tre fasi principali per Hyper-V: ottenere l'elenco di VM, profilatura e generazione di report. È anche disponibile una quarta opzione per calcolare solo la velocità effettiva. I requisiti per il server in cui devono essere eseguite le diverse fasi sono elencati nella tabella seguente:

| Requisito server | Descrizione |
|---|---|
|Ottenere l'elenco di VM, profilatura e misurazione della velocità effettiva |<ul><li>Sistema operativo: Microsoft Windows Server 2016 o Microsoft Windows Server 2012 R2 </li><li>Configurazione del computer: 8 vCPU, 16 GB di RAM, disco rigido da 300 GB</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Microsoft Visual C++ Redistributable per Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Accesso Internet ad Azure da questo server</li><li>Account di archiviazione di Azure</li><li>Accesso di amministratore al server</li><li>Almeno 100 GB di spazio libero su disco (presumendo 1000 VM con una media di tre dischi ognuna, profilate per 30 giorni)</li><li>La VM da cui si esegue lo strumento Azure Site Recovery Deployment Planner deve essere aggiunta all'elenco TrustedHosts di tutti i server Hyper-V.</li><li>Le VM di tutti i server Hyper-V da profilare devono essere aggiunte all'elenco TrustedHosts della VM client da cui lo strumento viene eseguito. [Altre informazioni per aggiungere i server nell'elenco TrustedHosts](#steps-to-add-servers-into-trustedhosts-list). </li><li> Lo strumento deve essere eseguito con privilegi amministrativi da PowerShell o dalla console della riga di comando</ul></ul>|
| Generazione di report | Un PC o server Windows con Microsoft Excel 2013 o versione successiva |
| Autorizzazioni utente | Account amministratore per accedere al cluster Hyper-V/host Hyper-V durante le operazioni di acquisizione dell'elenco di VM e di profilatura.<br>Tutti gli host da profilare devono avere un account amministratore di dominio con le stesse credenziali, ovvero nome utente e password
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Procedura per aggiungere i server nell'elenco TrustedHosts
1.  La VM da cui lo strumento deve essere distribuito deve avere tutti gli host da profilare nell'elenco TrustedHosts. Per aggiungere il client nell'elenco Trustedhosts, eseguire il comando seguente da un'istanza di PowerShell con privilegi elevati sulla VM. La VM può essere Windows Server 2012 R2 o Windows Server 2016. 

            set-item wsman:\localhost\Client\TrustedHosts -value <ComputerName>[,<ComputerName>]

2.  Ogni host Hyper-V da profilare deve avere:

    a. La VM su cui lo strumento verrà eseguito nell'elenco TrustedHosts. Eseguire il comando seguente da una sessione di PowerShell con privilegi elevati sull'host Hyper-V.

            set-item wsman:\localhost\Client\TrustedHosts -value <ComputerName>[,<ComputerName>]

    b. Comunicazione remota di PowerShell abilitata.

            Enable-PSRemoting -Force

## <a name="download-and-extract-the-deployment-planner-tool"></a>Scaricare ed estrarre lo strumento Deployment Planner

1.  Scaricare la versione più recente di [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
Lo strumento è compresso in una cartella ZIP. Lo stesso strumento supporta entrambi gli scenari di ripristino di emergenza da VMware ad Azure e da Hyper-V ad Azure. È possibile usare questo strumento anche per lo scenario di ripristino di emergenza da Hyper-V al sito secondario, ignorando però la raccomandazione del report sull'infrastruttura di Azure.

2.  Copiare la cartella ZIP nel server Windows nel quale si intende eseguire lo strumento. È possibile eseguire lo strumento in Windows Server 2012 R2 o Windows Server 2016. Il server deve avere accesso alla rete per connettersi al cluster Hyper-V o all'host Hyper-V in cui si trovano le VM da profilare. È consigliabile che la VM su cui lo strumento verrà eseguito abbia la stessa configurazione hardware del server Hyper-V che si vuole proteggere. Tale configurazione assicura che la velocità effettiva ottenuta segnalata dallo strumento corrisponda alla velocità effettiva che Azure Site Recovery può raggiungere durante la replica. Il calcolo della velocità effettiva dipende dalla larghezza di banda di rete disponibile nel server e dalla configurazione hardware (CPU, memoria e così via) del server. La velocità effettiva viene calcolata dal server in cui lo strumento è in esecuzione in Azure. Se la configurazione hardware del server è diversa da quella del server Hyper-V, la velocità effettiva ottenuta che viene segnalata dallo strumento non sarà corretta.
Configurazione consigliata della VM: 8 vCPU, 16 GB di RAM, 300 GB di HDD.

3.  Estrarre la cartella ZIP.
La cartella contiene più file e sottocartelle. Il file eseguibile è ASRDeploymentPlanner.exe e si trova nella cartella padre.

Esempio: copiare il file ZIP nell'unità E:\ ed estrarlo. E:\ASR Deployment Planner_v2.0.zip

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
* [Eseguire Deployment Planner](site-recovery-hyper-v-deployment-planner-run.md).