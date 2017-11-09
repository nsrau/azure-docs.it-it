---
title: Eseguire la replica di macchine virtuali VMware e server fisici in un altro sito (portale di Azure classico) | Documentazione Microsoft
description: Usare questo articolo per eseguire la replica di VM VMware o server fisici Windows/Linux in un sito secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: nsoneji
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 01a6f35fe61290f8c7275c34273d66956a53d3f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site-in-the-classic-azure-portal"></a>Eseguire la replica di macchine virtuali VMware locali o server fisici in un sito secondario nel portale di Azure classico

## <a name="overview"></a>Panoramica
In Azure Site Recovery, InMage Scout fornisce la replica in tempo reale tra più siti VMware locali. InMage Scout è incluso nelle sottoscrizioni del servizio Azure Site Recovery. 

## <a name="prerequisites"></a>Prerequisiti
**Account Azure**: sarà necessario un account [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.

## <a name="step-1-create-a-vault"></a>Passaggio 1: Creare un insieme di credenziali
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su Nuovo > Gestione > Backup e Site Recovery (OMS). In alternativa, è possibile fare clic su Esplora > Insieme di credenziali dei servizi di ripristino > Aggiungi.
3. In **Nome** specificare un nome descrittivo per identificare l'insieme di credenziali. Se è disponibile più di una sottoscrizione, selezionarne una.
4. In **Gruppo di risorse** creare un nuovo gruppo di risorse o selezionarne uno esistente. Specificare un'area di Azure per completare i campi obbligatori.
5. In **Posizione** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree supportate, vedere [Prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su Aggiungi al dashboard e quindi su Crea.
7. Il nuovo insieme di credenziali verrà visualizzato in Dashboard > Tutte le risorse e nel pannello Insiemi di credenziali dei servizi di ripristino principale.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Passaggio 2: Configurare l'insieme di credenziali e scaricare i componenti InMage Scout
1. Nel pannello Insiemi di credenziali dei servizi di ripristino selezionare l'insieme di credenziali e fare clic su Impostazioni.
2. In **Impostazioni** > **Guida introduttiva** fare clic su **Site Recovery** > Passaggio 1: **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Obiettivo di protezione** selezionare Nel sito di ripristino e scegliere Sì, con Hypervisor VMware vSphere. Fare quindi clic su OK.
4. In **Installazione di Scout** fare clic su download per scaricare il software InMage Scout 8.0.1 GA e la chiave di registrazione. I file di installazione per tutti i componenti necessari sono nel file ZIP scaricato.

## <a name="step-3-install-component-updates"></a>Passaggio 3: Installare gli aggiornamenti dei componenti
Scopri gli ultimi [aggiornamenti](#updates). È necessario installare i file di aggiornamento nei server nell'ordine seguente:

1. Server RX se è presente
2. Server di configurazione
3. Server di elaborazione
4. Server di destinazione master
5. Server vContinuum
6. Server di origine (sia Windows che Linux)

Installare gli aggiornamenti come segue:

1. Scaricare il file ZIP dell'[aggiornamento](https://aka.ms/asr-scout-update5). Il file ZIP contiene i file seguenti:

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * Bit per update4 dell'agente utente per RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. Estrarre i file ZIP.<br>
3. **Per il server RX**: copiare **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** nel server RX ed estrarlo. Nella cartella estratta eseguire **/Install**.<br>
4. **Per il server di elaborazione/configurazione**: copiare **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** nel server di configurazione e in quello di elaborazione. Fare doppio clic per eseguirlo.<br>
5. **Per il server di destinazione master Windows**: per aggiornare l'agente unificato, copiare **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** nel server di destinazione master. Fare doppio clic per eseguirlo. Si noti che l'agente unificato è applicabile anche al server di origine, se l'origine non è aggiornata fino a Update 4. È consigliabile installarlo anche nel server di origine, come indicato più avanti in questo elenco.<br>
6. **Per il server vContinuum**: copiare **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** nel server vContinuum.  Assicurarsi che la procedura guidata vContinuum sia stata chiusa. Fare doppio clic sul file per eseguirlo.<br>
7. **Per il server master di destinazione Linux**: per aggiornare l'agente unificato, copiare **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** nel server master di destinazione ed estrarre i file. Nella cartella estratta eseguire **/Install**.<br>
8. **Per il server di origine Windows**: non è necessario installare l'agente Update 5 nell'origine se l'origine è già aggiornata fino a Update 4. Se la versione è inferiore a Update 4, applicare l'agente Update 5.
Per aggiornare l'agente unificato, copiare **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** nel server di origine. Fare doppio clic per eseguirlo. <br>
9. **Per il server di origine Linux**: per aggiornare l'agente unificato, copiare la versione corrispondente al file dell'agente utente nel server Linux ed estrarre il file. Nella cartella estratta eseguire **/Install**.  Esempio: per il server RHEL 6.7 a 64 bit, copiare **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** nel server ed estrarre il file. Nella cartella estratta eseguire **/Install**.

## <a name="step-4-set-up-replication"></a>Passaggio 4: Configurare la replica
1. Impostare la replica tra i siti di origine e di destinazione VMware.
2. Per istruzioni, utilizzare la documentazione InMage Scout che viene scaricata con il prodotto. In alternativa, è possibile accedere alla documentazione come segue:

   * [Note sulla versione](https://aka.ms/asr-scout-release-notes)
   * [Matrice di compatibilità](https://aka.ms/asr-scout-cm)
   * [manuale dell'utente](https://aka.ms/asr-scout-user-guide)
   * [manuale dell'utente RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guida all'installazione rapida](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Aggiornamenti
### <a name="azure-site-recovery-scout-801-update-5"></a>Azure Site Recovery Scout 8.0.1 Update 5
Scout Update 5 è un aggiornamento cumulativo. Include tutte le correzioni apportate da Update 1 a Update 4, nonché le nuove correzioni di bug e i nuovi miglioramenti indicati di seguito.
Le correzioni aggiunte ad Azure Site Recovery Scout da Update 4 a Update 5 sono specifiche dei componenti di destinazione master e vContinuum. Se tutti i server di origine, ovvero il server di destinazione master, il server di configurazione, il server di elaborazione ed RX, sono già aggiornati ad Azure Site Recovery Scout Update 4, è necessario applicare la versione Update 5 solo al server di destinazione master. 

**Nuovo supporto della piattaforma**
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)

> [!NOTE]
> SLES 11 SP4 a 64 bit **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** include il pacchetto Scout di base con disponibilità generale **InMage_Scout_Standard_8.0.1 GA.zip**. Scaricare il pacchetto Scout GA dal portale come indicato nel [passaggio 1](#step-1-create-a-vault).
>

**Correzioni di bug e miglioramenti**

* Maggiore affidabilità del supporto per cluster Windows
    * Correzione di un problema per cui a volte alcuni dei dischi del cluster MSCS P2V diventano RAW dopo il ripristino
    * Correzione di un problema per cui il ripristino del cluster MSCS P2V non riesce a causa della mancata corrispondenza dell'ordine dei dischi
    * Correzione di un problema per cui l'operazione di aggiunta di dischi al cluster MSCS non riesce a causa della mancata corrispondenza delle dimensioni dei dischi
    * Correzione di un problema per cui il controllo di conformità dei mapping del cluster MSCS di origine con LUN RDM non riesce per quanto riguarda la verifica delle dimensioni
    * Correzione di un problema per cui la protezione dei cluster a nodo singolo non riesce a causa della mancata corrispondenza dell'interfaccia SCSI 
    * Correzione di un problema per cui la riprotezione del server del cluster Windows P2V non riesce se sono presenti dischi del cluster di destinazione. 
    
* Se durante la protezione del failback il server di destinazione master selezionato non si trova nello stesso server ESXi di quello del computer di origine protetto (durante la protezione successiva), vContinuum sceglie il server di destinazione master non corretto durante il ripristino del failback e la successiva operazione di ripristino non riesce.

> [!NOTE]
> 
> * Le correzioni indicate sopra per il cluster P2V sono applicabili solo ai cluster MSCS fisici appena protetti con Azure Site Recovery Scout Update 5. Per sfruttare le correzioni per i cluster in un cluster MSCS P2V già protetto con gli aggiornamenti precedenti, è necessario seguire i passaggi di aggiornamento specificati nella sezione 12 relativa all'aggiornamento di un cluster MSCS P2V protetto a Scout Update 5 delle [note sulla versione di Azure Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> 
> * La riprotezione del cluster MSCS fisico può riutilizzare dischi di destinazione esistenti solo se al momento della riprotezione in ognuno dei nodi del cluster è attivo lo stesso set di dischi rispetto alla protezione iniziale. In caso contrario, sono disponibili passaggi manuali, descritti nella sezione 12 delle [note sulla versione di Azure Site Recovery Scout](https://aka.ms/asr-scout-release-notes), per spostare i dischi lato destinazione nel percorso dell'archivio dati corretto in modo da riutilizzarli durante la riprotezione. Se si riprotegge il cluster MSCS in modalità P2V senza seguire i passaggi di aggiornamento, viene creato un nuovo disco nel server ESXi di destinazione. È necessario eliminare manualmente i vecchi dischi dall'archivio dati.
> 
> * Ogni volta che l'istanza di SLES11 o di SLES11 di origine con qualsiasi Service Pack viene riavviata normalmente, è necessario contrassegnare manualmente le coppie di replica del disco **radice** per la risincronizzazione, in quanto questo non verrà specificato nell'interfaccia utente CX. Se non si contrassegna il disco radice per la risincronizzazione, potrebbero verificarsi problemi di integrità dei dati.
> 

### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 è un aggiornamento cumulativo. Include tutte le correzioni apportate dall'update1 all'update3, oltre alle nuove correzioni di bug e ai nuovi miglioramenti.

**Nuovo supporto della piattaforma**

* È stato aggiunto il supporto per vCenter/vSphere 6.0, 6.1 e 6.2
* È stato aggiunto il supporto per i sistemi operativi Linux seguenti:
  * Red Hat Enterprise Linux (RHEL)7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 a 64 bit: **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** viene fornito nel pacchetto base Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Scaricare il pacchetto Scout GA dal portale come indicato nel [passaggio 1](#step-1-create-a-vault).
>
>

**Correzioni di bug e miglioramenti**

* Arresto di gestione migliorato per i sistemi operativi Linux e cloni successivi per evitare problemi di risincronizzazione indesiderati.
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* Per Linux, le autorizzazioni di accesso alle cartelle complete nella directory di installazione dell'agente unificato sono ora limitate solo per l'utente locale.
* In Windows, si verifica il problema di timeout durante il rilascio di segnalibri di coerenza comuni distribuiti sulle applicazioni distribuite con carico elevato, come i cluster di SQL e SharePoint.
* Aggiunta della correzione relativa ai log nel programma di installazione di base di CX.
* Aggiunta del collegamento per il download di VMware vCLI 6.0 viene aggiunto al programma di installazione di base di Windows Master Target.
* Aggiunta di ulteriori controlli e log per le modifiche delle configurazioni di rete durante il failover e le esercitazioni sul ripristino di emergenza.
* A volte le informazioni di conservazione non vengono segnalate a CX.  
* Per un cluster fisico, l'operazione di ridimensionamento del volume tramite la procedura guidata vContinuum non riesce se è stata eseguita la riduzione del volume di origine.
* La protezione del cluster non riesce e restituisce l'errore "Impossibile trovare la firma del disco" quando il disco del cluster è PRDM.
* Arresto anomalo del server di trasporto cxps a causa di un'eccezione non compresa nell'intervallo.
* Ora è possibile ridimensionare il nome del server e le colonne IP nella pagina di installazione push della procedura guidata vContinuum.
* Miglioramenti dell'API RX
  * Fornisce i cinque punti di coerenza comuni disponibili più recenti (con tag Solo garantiti).
  * Fornisce i dettagli relativi alla capacità e allo spazio libero per tutti i dispositivi protetti.
  * Fornisce lo stato del driver Scout nel server di origine.

> [!NOTE]
> * Il pacchetto base **InMage_Scout_Standard_8.0.1_GA.zip** ora contiene il file **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** del programma di installazione di base di CX aggiornato e il file **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe** del programma di installazione di base di Master di Windows. Per tutte le nuove installazioni, usare la nuova GA di CX e Windows Master Target.
> * Update 4 può essere applicato direttamente in 8.0.1 GA.
> * Non è possibile eseguire il rollback degli aggiornamenti del server di configurazione e di RX dopo che sono stati applicati nel sistema.
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3
Update 3 include le correzioni di bug e i miglioramenti seguenti:

* Il server di configurazione e RX non riescono a registrarsi nell'insieme di credenziali di Site Recovery quando si trovano dietro il proxy.
* Il numero di ore per cui l'obiettivo del punto di ripristino (RPO) non viene rispettato non viene aggiornato nel report sull'integrità.
* Il server di configurazione non si sincronizza con RX quando i dettagli dell'hardware ESX o della rete contengono caratteri UTF-8.
* I controller di dominio Windows Server 2008 R2 non si riavviano dopo il ripristino.
* La sincronizzazione offline non funziona come previsto.
* Dopo il failover della macchina virtuale (VM), l'eliminazione della coppia di replica si blocca a lungo nell'interfaccia utente di CX e gli utenti non possono completare l'operazione di failback o ripresa.
* Le operazioni per la creazione di snapshot complessive eseguite dal processo di coerenza sono state ottimizzate per ridurre le disconnessioni delle applicazioni, come i client SQL.
* Le prestazioni dello strumento di coerenza (VACP.exe) sono state migliorate riducendo l'utilizzo di memoria necessario per creare snapshot in Windows.
* Il servizio di installazione push si arresta in modo anomalo quando la password supera 16 caratteri.
* vContinuum non verifica le nuove credenziali di vCenter e non richiede conferma quando le credenziali vengono modificate.
* In Linux, il gestore della cache di destinazione master (cachemgr) non scarica i file dal server di elaborazione, con conseguente limitazione della coppia di replica.
* Quando l'ordine dei dischi fisici del cluster di failover (MSCS) non è uguale in tutti i nodi, la replica non viene impostata per alcuni volumi del cluster.
  <br/>Si noti che il cluster deve essere riprotetto per usufruire di questa correzione.  
* La funzionalità SMTP non funziona come previsto dopo l'aggiornamento di RX da Scout 7.1 a Scout 8.0.1.
* Sono state aggiunte altre statistiche nel log per l'operazione di rollback per tenere traccia del tempo impiegato per completarla.
* È stato aggiunto il supporto per sistemi operativi Linux nel server di origine:
  * Red Hat Enterprise Linux (RHEL) 6 Update 7
  * CentOS 6 Update 7
* L'interfaccia utente di RX e CX visualizzano ora la notifica per la coppia, che passa alla modalità bitmap.
* In RX sono state aggiunte le correzioni per la sicurezza seguenti:

| **Descrizione del problema** | **Procedure di implementazione** |
| --- | --- |
| Bypass dell'autorizzazione tramite manomissione dei parametri |Accesso limitato agli utenti non applicabili. |
| Richiesta intersito falsa |Implementazione del concetto di token di pagina, generato in modo casuale per ogni pagina. <br/>Con questa operazione, verrà visualizzato: <li> Esiste una sola istanza di accesso singolo per lo stesso utente.</li><li>L'aggiornamento della pagina non funziona e verrà eseguito il reindirizzamento al dashboard.</li> |
| Caricamento di file dannosi |Limitazione dei file a determinate estensioni. Le estensioni consentite sono: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip. |
| Scripting intersito persistente |Aggiunta di convalide dell'input. |

> [!NOTE]
> * Tutti gli aggiornamenti di Site Recovery sono cumulativi. Update 3 include tutte le correzioni di Update 1 e Update 2. Update 3 può essere applicato direttamente in 8.0.1 GA.
> * Non è possibile eseguire il rollback degli aggiornamenti del server di configurazione e di RX dopo che sono stati applicati nel sistema.
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (aggiornamento del 3 dicembre 15)
Le correzioni apportate in Update 2 includono:

* **Server di configurazione**: risoluzione di un problema che impediva il corretto funzionamento della funzionalità di misurazione gratuita per 31 giorni quando il server di configurazione veniva registrato in Site Recovery.
* **Agente unificato**: risoluzione di un problema di Update 1 a causa del quale l'aggiornamento non veniva installato nel server di destinazione master quando veniva aggiornato dalla versione 8.0 alla versione 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Update 1 include le correzioni di bug e le nuove funzionalità seguenti:

* 31 giorni di protezione gratuita per istanza del server. Consente di testare le funzionalità o impostare un proof-of-concept.
  * Tutte le operazioni sul server, inclusi failover e failback, sono gratuite per i primi 31 giorni a partire dal momento in cui un server viene protetto con Site Recovery Scout.
  * Dal 32esimo giorno in poi, per ogni server protetto verrà addebitata al sito del cliente la tariffa per istanza standard per la protezione di Azure Site Recovery.
  * E’ possibile consultare il numero di server protetti addebitati in qualsiasi momento nella pagina Dashboard delle credenziali di Azure Site Recovery.
* Aggiunta del supporto per vCLI (vSphere Command-Line Interface) 5.5 Update 2.
* Supporto aggiunto per i sistemi operativi Linux nel server di origine:
  * RHEL 6 Update 6
  * RHEL 5 Update 11
  * CentOS 6 Update 6
  * CentOS 5 Update 11
* Correzioni dei bug per risolvere i problemi seguenti:
  * La registrazione delle credenziali ha esito negativo per il server di configurazione o RX.
  * I volumi del cluster non vengono visualizzati come previsto quando le macchine virtuali vengono riprotette durante la ripresa.
  * Il failback ha esito negativo quando il server di destinazione master è ospitato in un altro server ESXi dalle macchine virtuali di produzione locali.
  * Le autorizzazioni del file di configurazione vengono modificate durante l'aggiornamento a 8.0.1, con impatto sulla protezione e sulle operazioni.
  * La soglia di risincronizzazione non è applicata come previsto, con conseguente comportamento incoerente della replica.
  * Le impostazioni RPO non vengono visualizzate correttamente nell'interfaccia del server di configurazione. Il valore di dati non compressi mostra erroneamente il valore compresso.
  * L'operazione di rimozione non elimina come previsto nella procedura guidata vContinuum e la replica non viene eliminata dall'interfaccia del server di configurazione.
  * Nella procedura guidata vContinuum il disco viene deselezionato automaticamente quando si fa clic su **Dettagli** nella visualizzazione del disco durante la protezione delle macchine virtuali MSCS.
  * Nello scenario da macchina fisica a virtuale (P2V), i servizi HP obbligatori, come CIMnotify e CqMgHost, non vengono impostati su manuale nel ripristino della macchina virtuale. Questo causa tempo di avvio aggiuntivo.
  * La protezione di macchine virtuali Linux ha esito negativo quando sono presenti più di 26 dischi nel server di destinazione master.

## <a name="next-steps"></a>Passaggi successivi
Pubblicare eventuali domande nel [forum Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
