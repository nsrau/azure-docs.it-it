---
title: Dispositivo virtuale StorSimple Aggiornamento 2| Microsoft Docs
description: Informazioni su come creare, distribuire e gestire un dispositivo virtuale StorSimple in una rete virtuale di Microsoft Azure. (Si applica a StorSimple Update 2).
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: f37752a5-cd0c-479b-bef2-ac2c724bcc37
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: ead9fcc5ec582420d731faccb7abba0dc815ed84
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Distribuire e gestire un dispositivo virtuale StorSimple in Azure
## <a name="overview"></a>Overview
Il dispositivo virtuale StorSimple serie 8000 rappresenta una funzionalità aggiuntiva fornita con la soluzione Microsoft Azure StorSimple. Il dispositivo virtuale StorSimple viene eseguito in una macchina virtuale della rete virtuale di Microsoft Azure e può essere utilizzato per eseguire il backup e duplicare i dati dagli host. Questa esercitazione descrive come distribuire e gestire un dispositivo virtuale in Azure ed è applicabile a tutti i dispositivi virtuali che eseguono la versione del software Update 2 e precedenti.

#### <a name="virtual-device-model-comparison"></a>Confronto tra modelli di dispositivi virtuali
Il dispositivo virtuale StorSimple è disponibile in due modelli, Standard 8010 (in precedenza noto come 1100) e Premium 8020 (introdotto nell'aggiornamento 2). La tabella seguente illustra un confronto tra i due modelli.

| Modello del dispositivo | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Capacità massima** |30 TB |64 TB |
| **Macchina virtuale di Azure** |Standard_A3 (4 core, 7 GB di memoria) |Standard_DS3 (4 core, 14 GB di memoria) |
| **Compatibilità tra le versioni** |Versioni con aggiornamenti precedenti a Update 2 o successivi |Versioni con aggiornamenti Update 2 o successivi |
| **Aree di disponibilità** |Tutte le aree di Azure |Tutte le aree di Azure che supportano Archiviazione Premium e VM DS3 di Azure<br></br> Usare [questo elenco](https://azure.microsoft.com/en-us/regions/services) per verificare se nella propria area sono disponibili sia le *macchine virtuali serie DS* che l'*archiviazione su disco*. |
| **Tipo di archiviazione** |Usa l'Archiviazione Standard di Azure<br></br> Altre informazioni su come [creare un account di archiviazione Standard](../storage/storage-create-storage-account.md) |Usa l'Archiviazione Standard di Azure<sup>2</sup> <br></br>Informazioni su come [creare un account di archiviazione Premium](../storage/storage-premium-storage.md) |
| **Indicazioni relative al carico di lavoro** |Recupero a livello di elemento per i file dai backup |Scenari di sviluppo e test cloud, bassa latenza, carichi di lavoro a prestazioni superiori  <br></br>Dispositivo secondario per il ripristino di emergenza |

<sup>1</sup> *precedentemente noto come 1100*.

<sup>2</sup> *Entrambi i modelli 8010 e 8020 usano l'archiviazione di Azure Standard per il livello cloud. La differenza è solo nel livello locale nel dispositivo*.

Questo articolo illustra il processo dettagliato per la distribuzione di un dispositivo virtuale StorSimple in Azure. Dopo avere letto l'articolo, si sarà in grado di:

* Comprendere le differenze tra il dispositivo virtuale e il dispositivo fisico.
* Creare e configurare il dispositivo virtuale.
* Connettersi al dispositivo virtuale.
* Usare il dispositivo virtuale.

Questa esercitazione si applica a tutti i dispositivi virtuali StorSimple con versione Update 2 e successiva.

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Differenze tra il dispositivo virtuale e il dispositivo fisico
Il dispositivo virtuale StorSimple consiste in una versione esclusivamente software di StorSimple che viene eseguita in un singolo nodo di una macchina virtuale di Microsoft Azure. Il dispositivo virtuale supporta il ripristino di emergenza nei casi in cui il dispositivo fisico non sia disponibile e può essere usato in scenari di recupero a livello di elemento dai backup, ripristino di emergenza locale e negli scenari di sviluppo e test basati su cloud.

#### <a name="differences-from-the-physical-device"></a>Differenze rispetto al dispositivo fisico
La tabella seguente illustra alcune differenze fondamentali tra il dispositivo StorSimple virtuale e quello fisico.

|  | Dispositivo fisico | Dispositivo virtuale |
| --- | --- | --- |
| **Posizione** |Si trova nel data center. |Viene eseguito in Azure. |
| **Interfacce di rete** |Ha sei interfacce di rete: da DATA 0 a DATA 5. |Ha una sola interfaccia di rete: DATA 0. |
| **Registrazione** |Registrato durante il passaggio di configurazione. |La registrazione è un'attività separata. |
| **Chiave DEK del servizio** |Rigenerare la chiave nel dispositivo fisico, quindi aggiornare il dispositivo virtuale con la nuova chiave. |Non è possibile rigenerarla dal dispositivo virtuale. |

## <a name="prerequisites-for-the-virtual-device"></a>Prerequisiti per il dispositivo virtuale
Le sezioni seguenti illustrano i prerequisiti di configurazione per il dispositivo StorSimple virtuale. Prima di distribuire un dispositivo virtuale, esaminare le [considerazioni relative alla sicurezza per l'uso di un dispositivo virtuale](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Requisiti di Azure
Prima di eseguire il provisioning del dispositivo virtuale, è necessario effettuare le seguenti operazioni preliminari in Azure:

* Nel caso del dispositivo virtuale, [configurare una rete virtuale in Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Se si usa l'archiviazione Premium, sarà necessario creare una rete virtuale in un'area di Azure che supporta l'archiviazione Premium. Le aree di archiviazione Premium corrispondono alla riga relativa all'*archiviazione su disco* nell'elenco dei [servizi di Azure in base all'area](https://azure.microsoft.com/en-us/regions/services).
* È consigliabile utilizzare il server DNS predefinito fornito da Azure invece di specificare il nome del proprio server DNS. Se il nome del server DNS non è valido o se il server DNS non riesce a risolvere correttamente l'indirizzo IP, non sarà possibile creare il dispositivo virtuale.
* Le opzioni point-to-site e da sito a sito non sono obbligatorie, ma facoltative. Se si desidera, è possibile configurarle per scenari più avanzati.
* È possibile creare [Macchine virtuali di Azure](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (server host) nella rete virtuale che possono usare i volumi esposti dal dispositivo virtuale. Tali server devono soddisfare i seguenti requisiti:                             

  * Svolgere il ruolo di macchine virtuali Windows o Linux nelle quali è installato il software iSCSI Initiator
  * Essere in esecuzione nella stessa rete virtuale del dispositivo virtuale
  * Essere in grado di connettersi alla destinazione iSCSI del dispositivo virtuale utilizzando l'indirizzo IP interno del dispositivo virtuale
* Assicurarsi di aver configurato il supporto per il traffico iSCSI e cloud nella stessa rete virtuale.

#### <a name="storsimple-requirements"></a>Requisiti di StorSimple
Effettuare i seguenti aggiornamenti del servizio StorSimple di Azure, prima di creare un dispositivo virtuale:

* Aggiungere i [record controllo di accesso](storsimple-manage-acrs.md) per le macchine virtuali che svolgeranno il ruolo di server host per il dispositivo virtuale.
* Usare un [account di archiviazione](storsimple-manage-storage-accounts.md#add-a-storage-account) nella stessa area del dispositivo virtuale. Gli account di archiviazione posti in aree diverse possono causare una riduzione delle prestazioni. È possibile usare un account di archiviazione Standard o Premium con il dispositivo virtuale. Altre informazioni su come creare un [account di archiviazione Standard](../storage/storage-create-storage-account.md) o un [account di archiviazione Premium](../storage/storage-premium-storage.md)
* Usare un account di archiviazione differente rispetto a quello usato per creare i dati. Se si utilizza lo stesso account di archiviazione, si potrebbe verificare una riduzione delle prestazioni.

Prima di iniziare, verificare quanto segue:

* L'account del portale di Azure classico con le credenziali di accesso.
* Copia della chiave DEK del servizio ottenuta dal dispositivo fisico.

## <a name="create-and-configure-the-virtual-device"></a>Creare e configurare il dispositivo virtuale
Prima di eseguire queste procedure, assicurarsi che siano soddisfatti i [prerequisiti per il dispositivo virtuale](#prerequisites-for-the-virtual-device).

Dopo aver creato una rete virtuale, configurato un servizio StorSimple Manager e registrato il dispositivo StorSimple fisico nel servizio, è possibile seguire questa procedura per creare e configurare un dispositivo virtuale StorSimple.

### <a name="step-1-create-a-virtual-device"></a>Passaggio 1: Creare un dispositivo virtuale
Per creare il dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito:

[!INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Se la creazione del dispositivo virtuale ha esito negativo in questo passaggio, potrebbe non essere disponibile la connettività a Internet. Per altre informazioni, vedere la sezione [Risolvere gli errori di connettività Internet](#troubleshoot-internet-connectivity-errors) quando si crea un dispositivo virtuale.

### <a name="step-2-configure-and-register-the-virtual-device"></a>Passaggio 2: Configurare e registrare il dispositivo virtuale
Prima di iniziare questa procedura, assicurarsi di avere una copia della chiave DEK del servizio. La chiave DEK del servizio è stata creata durante la procedura di configurazione del primo dispositivo StorSimple ed è stata salvata in un luogo sicuro. Se non si dispone di una copia della chiave DEK del servizio, è necessario contattare il supporto tecnico Microsoft e richiedere assistenza.

Per configurare e registrare il dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito.

[!INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Passaggio 3: (Facoltativo) Modificare le impostazioni di configurazione del dispositivo
La sezione seguente illustra le impostazioni di configurazione del dispositivo necessarie per il dispositivo StorSimple virtuale se si vuole usare CHAP, StorSimple Snapshot Manager o si vuole modificare la password dell'amministratore del dispositivo.

#### <a name="configure-the-chap-initiator"></a>Configurare l'iniziatore dell'autenticazione CHAP
Questo parametro include le credenziali che il dispositivo virtuale (destinazione) prevede di ricevere dagli iniziatori (server) che tentano di accedere ai volumi. Gli iniziatori forniranno un nome utente e una password CHAP per identificarsi nel dispositivo, durante questa procedura di autenticazione. Per informazioni dettagliate sulla procedura, vedere [Configurare CHAP per il dispositivo StorSimple](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurare la destinazione dell'autenticazione CHAP
Questo parametro contiene le credenziali utilizzate dal dispositivo virtuale quando un iniziatore abilitato all'autenticazione CHAP richiede l'autenticazione reciproca o bidirezionale. Il dispositivo virtuale utilizzerà un nome utente e una password CHAP inversi per identificarsi nell'iniziatore durante la procedura di autenticazione. Tenere presente che le impostazioni di destinazione CHAP sono di tipo globale. Quando vengono applicate, tutti i volumi connessi al dispositivo virtuale di archiviazione utilizzano l'autenticazione CHAP. Per informazioni dettagliate sulla procedura, vedere [Configurare CHAP per il dispositivo StorSimple](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurare la password di Gestione snapshot StorSimple
Il software di Gestione snapshot StorSimple si trova nell'host di Windows e consente agli amministratori di gestire i backup del dispositivo StorSimple sotto forma di snapshot locali e cloud.

> [!NOTE]
> Per il dispositivo virtuale, l'host di Windows è costituito da una macchina virtuale di Azure.
>
>

Quando si configura un dispositivo in StorSimple Snapshot Manager, verrà richiesto di specificare l'indirizzo IP del dispositivo StorSimple e la password per autenticare il dispositivo di archiviazione. Per informazioni dettagliate sulla procedura, vedere [Configurare la password di StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Configurare la password dell’amministratore del dispositivo
Quando si utilizza l'interfaccia di Windows PowerShell per accedere al dispositivo virtuale, è necessario immettere una password amministratore. Per garantire la sicurezza dei dati, è necessario modificare la password prima di utilizzare il dispositivo virtuale. Per informazioni dettagliate sulla procedura, vedere [Configurare la password dell'amministratore del dispositivo](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Connettersi in modalità remota al dispositivo virtuale.
Per impostazione predefinita, non è abilitato l'accesso remoto al dispositivo virtuale tramite l'interfaccia di Windows PowerShell. È necessario abilitare innanzitutto la gestione remota nel dispositivo virtuale e abilitarla nel client che verrà utilizzato per accedere al dispositivo virtuale.

Il processo in due passaggi per la connessione remota è illustrato di seguito.

### <a name="step-1-configure-remote-management"></a>Passaggio 1: Configurare la gestione remota
Per configurare la gestione remota del dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito.

[!INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Passaggio 2: Accedere al dispositivo virtuale in modalità remota
Dopo avere abilitato la gestione remota nella pagina di configurazione del dispositivo StorSimple, è possibile usare la comunicazione remota di PowerShell per connettersi al dispositivo virtuale da un'altra macchina virtuale presente all'interno della stessa rete virtuale. Ad esempio, è possibile connettersi dalla macchina virtuale host configurata e usata per connettere iSCSI. Nella maggior parte delle distribuzioni, l'utente avrà già aperto un endpoint pubblico per accedere alla macchina virtuale che è possibile utilizzare per accedere al dispositivo virtuale.

> [!WARNING]
> **Per una maggiore sicurezza, si consiglia di utilizzare HTTPS quando ci si connette agli endpoint e di eliminare gli endpoint dopo aver completato la sessione remota in PowerShell.**
>
>

Eseguire le procedure descritte in [Connettersi in remoto al dispositivo StorSimple](storsimple-remote-connect.md) per impostare la connessione in remoto al dispositivo virtuale.

## <a name="connect-directly-to-the-virtual-device"></a>Connettersi direttamente al dispositivo virtuale
È anche possibile connettersi direttamente al dispositivo virtuale. Se ci si vuole connettere direttamente al dispositivo virtuale da un altro computer non compreso nella rete virtuale oppure nell'ambiente Microsoft Azure, è necessario creare endpoint aggiuntivi, come descritto nella procedura seguente.

Per creare  un endpoint pubblico nel dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito:

[!INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Si consiglia di connettersi da un'altra macchina virtuale presente all'interno della stessa rete virtuale; in questo modo si riduce il numero di endpoint pubblici nella rete virtuale. Quando si utilizza questo metodo, ci si connette alla macchina virtuale tramite una sessione Desktop remoto e si configura tale macchina virtuale affinché venga utilizzata come qualsiasi altro client di Windows in una rete locale. Non è necessario aggiungere il numero di porta pubblica poiché è già noto.

## <a name="work-with-the-storsimple-virtual-device"></a>Utilizzare il dispositivo virtuale StorSimple
Dopo aver creato e configurato il dispositivo virtuale StorSimple, è possibile iniziare a utilizzarlo. È possibile utilizzare contenitori di volumi, criteri per volumi e backup su un dispositivo virtuale esattamente come si farebbe con un dispositivo StorSimple fisico; l'unica differenza consiste nella necessità di selezionare il dispositivo virtuale dall'elenco dei dispositivi. Per procedure dettagliate per le diverse attività di gestione per il dispositivo virtuale, vedere la pagina relativa all' [uso del servizio StorSimple Manager per la gestione di un dispositivo virtuale](storsimple-manager-service-administration.md) .

Le sezioni seguenti illustrano alcune delle differenze relative all'utilizzo del dispositivo virtuale.

### <a name="maintain-a-storsimple-virtual-device"></a>Gestire un dispositivo virtuale StorSimple
Dal momento che si tratta di un dispositivo solo software, la gestione del dispositivo virtuale è minore rispetto a quella di un dispositivo fisico. L'utente ha a disposizione le seguenti opzioni:

* **Aggiornamenti software** : è possibile visualizzare la data dell'ultimo aggiornamento del software insieme a eventuali messaggi sullo stato dell'aggiornamento. È possibile usare il pulsante **Verifica la disponibilità di aggiornamenti** nella parte inferiore della pagina per eseguire una ricerca manuale di nuovi aggiornamenti. Se sono disponibili aggiornamenti, fare clic su **Installa aggiornamenti** per eseguire l'installazione. Dal momento che non esiste un'unica interfaccia sul dispositivo virtuale, durante l'applicazione degli aggiornamenti si verificherà una breve interruzione del servizio. Il dispositivo virtuale verrà automaticamente arrestato e riavviato (se necessario) per applicare gli aggiornamenti disponibili. Per una procedura dettagliata, vedere la pagina relativa all' [aggiornamento del dispositivo](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
* **Pacchetto di supporto** : è possibile creare e caricare un pacchetto di supporto per consentire al supporto tecnico Microsoft di risolvere i problemi relativi al dispositivo virtuale. Per una procedura dettagliata, vedere [Creare e gestire un pacchetto di supporto StorSimple](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Account di archiviazione di un dispositivo virtuale
Gli account di archiviazione vengono creati per essere utilizzati dal servizio StorSimple Manager, dal dispositivo virtuale e da quello fisico. Quando si crea un account di archiviazione, è opportuno utilizzare un identificatore di area con nome descrittivo, in modo che l'area sia coerente in tutti i componenti di sistema. È importante che tutti i componenti di un dispositivo virtuale si trovino nella stessa area, al fine di evitare problemi di prestazioni.

Per una procedura dettagliata, vedere [Aggiungere un account di archiviazione](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Disattivare un dispositivo virtuale StorSimple
Se si disattiva un dispositivo virtuale, vengono eliminate la macchina virtuale e le risorse create durante l'esecuzione del provisioning. Dopo aver disattivato il dispositivo, non è possibile ripristinarne lo stato precedente. Prima di disattivare il dispositivo virtuale, assicurarsi di arrestare o eliminare i client e gli host che dipendono da esso.

La disattivazione di un dispositivo virtuale determina le azioni seguenti:

* Il dispositivo virtuale viene rimosso.
* Il disco del sistema operativo e i dischi dati creati per il dispositivo virtuale vengono rimossi.
* Il servizio ospitato e la rete virtuale creata durante il provisioning vengono mantenuti. Se non si utilizzano, è necessario eliminarli manualmente.
* Le snapshot cloud create per il dispositivo virtuale vengono conservate.

Per una procedura dettagliata, vedere [Disattivare ed eliminare un dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

Non appena il dispositivo virtuale viene indicato come disattivato nella pagina del servizio StorSimple Manager, è possibile eliminare il dispositivo virtuale dall'elenco dispositivi nella pagina **Dispositivi** .

### <a name="start-stop-and-restart-a-virtual-device"></a>Avviare, arrestare e riavviare un dispositivo virtuale
A differenza del dispositivo StorSimple fisico, in quello virtuale StorSimple non sono disponibili pulsanti di accensione o spegnimento. Tuttavia, in alcuni casi potrebbe essere necessario arrestare e riavviare il dispositivo virtuale. Ad esempio, per completare l'applicazione di alcuni aggiornamenti potrebbe essere richiesto di riavviare la macchina virtuale. Il modo più semplice per avviare, arrestare e riavviare un dispositivo virtuale consiste nell'utilizzare la console di gestione delle macchine virtuali.

Quando si apre la console di gestione, il dispositivo virtuale viene visualizzato in **esecuzione** poiché viene avviato per impostazione predefinita al termine della procedura di creazione. È possibile avviare, arrestare e riavviare una macchina virtuale in qualsiasi momento.

[!INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Ripristino delle impostazioni di fabbrica
Se si decide di ripristinare il dispositivo virtuale, sarà sufficiente disattivarlo, eliminarlo e successivamente crearne uno nuovo. Analogamente a quanto accade per il ripristino del dispositivo fisico, nel nuovo dispositivo virtuale non saranno presenti aggiornamenti. Pertanto, assicurarsi di cercare gli aggiornamenti prima di utilizzarlo.

## <a name="fail-over-to-the-virtual-device"></a>Eseguire il failover nel dispositivo virtuale
Il ripristino di emergenza (DR, Disaster Recovery) rappresenta una delle situazioni principali per le quali il dispositivo è stato realizzato. In questo scenario, è possibile che il dispositivo fisico StorSimple o il data center completo non sia disponibile. Fortunatamente, è possibile utilizzare un dispositivo virtuale per ripristinare le operazioni in un percorso alternativo. Durante il ripristino di emergenza, i contenitori del volume del dispositivo di origine cambiano la proprietà e vengono trasferiti nel dispositivo virtuale. I prerequisiti per il ripristino di emergenza prevedono che sia stato creato e configurato il dispositivo virtuale, che tutti i volumi nel contenitore siano offline e che al contenitore del volume sia associata una snapshot cloud.

> [!NOTE]
> * Quando si usa un dispositivo virtuale come dispositivo secondario per il ripristino di emergenza, occorre ricordare che 8010 ha 30 TB di archiviazione Standard e 8020 ha 64 TB di archiviazione Premium. La capacità superiore del dispositivo virtuale 8020 potrebbe essere più adatta per uno scenario di ripristino di emergenza.
> * Non è possibile eseguire il failover o la clonazione da un dispositivo con versione del software con Update 2 a un dispositivo con una versione precedente a Update 1. È tuttavia possibile eseguire il failover di un dispositivo che esegue la versione con Update 2 in un dispositivo con Update 1 (1.1 o 1.2)
>
>

Per una procedura dettagliata, vedere [Failover su un dispositivo virtuale StorSimple](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).

## <a name="shut-down-or-delete-the-virtual-device"></a>Arrestare o eliminare il dispositivo virtuale
Se in precedenza è stato configurato e utilizzato un dispositivo virtuale StorSimple, ma si desidera arrestare l'addebito dei costi di calcolo relativi all'utilizzo, è possibile arrestare il dispositivo virtuale. Se si arresta il dispositivo virtuale, non viene eliminato il sistema operativo o i dischi dati nell'archiviazione. Viene interrotto l'addebito dei costi di sottoscrizione, ma non quello dei costi per il sistema operativo e per i dischi dati.

Se si elimina o si arresta il dispositivo virtuale, quest'ultimo verrà visualizzato come **Offline** nella pagina Dispositivi del servizio StorSimple Manager. È possibile disattivare o eliminare il dispositivo se si vogliono eliminare anche i backup creati dal dispositivo virtuale. Per altre informazioni, vedere [Disattivare ed eliminare un dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

[!INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[!INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Risolvere gli errori di connettività Internet
Se durante la creazione di un dispositivo virtuale non è disponibile connettività a Internet, il passaggio di creazione avrà esito negativo. Se l'errore è causato dalla connettività Internet, per risolvere il problema seguire questa procedura nel portale di Azure classico:

1. Creare una macchina virtuale Windows Server 2012 in Azure. Questa macchina virtuale dovrà usare lo stesso account di archiviazione, la stessa rete virtuale e la stessa subnet usati dal dispositivo virtuale. Se si ha già un host Windows Server esistente in Azure che usa lo stesso account di archiviazione, la stessa rete virtuale e la stessa subnet, è anche possibile risolvere i problemi di connettività Internet usando tale host.
2. Eseguire l'accesso remoto alla macchina virtuale creata nel passaggio precedente.
3. Aprire una finestra di comando nella macchina virtuale (premere tasto WINDOWS + R e quindi digitare `cmd`).
4. Al prompt eseguire questo comando:

    `nslookup windows.net`
5. Se `nslookup` ha esito negativo, l'errore di connettività Internet impedisce la registrazione del dispositivo virtuale nel servizio StorSimple Manager.
6. Apportare alla rete virtuale le modifiche necessarie perché il dispositivo virtuale possa accedere a siti di Azure come "windows.net".

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Usare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
* Informazioni su come [Ripristinare un volume StorSimple da un set di backup](storsimple-restore-from-backup-set.md).

