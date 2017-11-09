---
title: Appliance cloud StorSimple aggiornamento 3| Microsoft Docs
description: Informazioni su come creare, distribuire e gestire un'appliance cloud StorSimple in una rete virtuale di Microsoft Azure. Si applica a StorSimple aggiornamento 3 e versioni successive.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2017
ms.author: alkohli
ms.openlocfilehash: 1ece5b1b2ba8e4d26fe633fe7c7c60f4187f9d6b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Distribuire e gestire un'appliance cloud StorSimple in Azure (aggiornamento 3 e versioni successive)

## <a name="overview"></a>Panoramica

L'appliance cloud StorSimple serie 8000 è una funzionalità aggiuntiva disponibile con la soluzione Microsoft Azure StorSimple. L'appliance cloud StorSimple viene eseguita in una macchina virtuale in una rete virtuale di Microsoft Azure e può essere usata per clonare i dati ed eseguirne il backup dagli host.

Questo articolo illustra il processo dettagliato per distribuire e gestire un'appliance cloud StorSimple in Azure. Dopo avere letto l'articolo, si sarà in grado di:

* Comprendere le differenze tra appliance cloud e dispositivo fisico.
* Creare e configurare l'appliance cloud.
* Connettersi all'appliance cloud.
* Usare l'appliance cloud.

Questa esercitazione si applica a tutte le appliance cloud StorSimple con aggiornamento 3 e successivi.

#### <a name="cloud-appliance-model-comparison"></a>Confronto tra i modelli dell'appliance cloud

L'appliance cloud StorSimple è disponibile in due modelli, Standard 8010 (in precedenza noto come 1100) e Premium 8020 (introdotto nell'aggiornamento 2). La tabella seguente presenta un confronto tra i due modelli.

| Modello del dispositivo | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Capacità massima** |30 TB |64 TB |
| **Macchina virtuale di Azure** |Standard_A3 (4 core, 7 GB di memoria)| Standard_DS3 (4 core, 14 GB di memoria)|
| **Aree di disponibilità** |Tutte le aree di Azure |Le aree di Azure che supportano Archiviazione Premium e VM DS3 di Azure<br></br>Usare [questo elenco](https://azure.microsoft.com/regions/services/) per verificare se nella propria area sono disponibili sia le **macchine virtuali serie DS** che l'**archiviazione su disco**. |
| **Tipo di archiviazione** |Usa l'Archiviazione Standard di Azure<br></br> Altre informazioni su come [creare un account di archiviazione Standard](../storage/common/storage-create-storage-account.md) |Usa l'Archiviazione Standard di Azure<sup>2</sup> <br></br>Informazioni su come [creare un account di archiviazione Premium](../virtual-machines/windows/premium-storage.md) |
| **Indicazioni relative al carico di lavoro** |Recupero a livello di elemento per i file dai backup |Scenari di sviluppo e test basati su cloud <br></br>Bassa latenza e carichi di lavoro a prestazioni superiori<br></br>Dispositivo secondario per il ripristino di emergenza |

<sup>1</sup> *precedentemente noto come 1100*.

<sup>2</sup> *Entrambi i modelli 8010 e 8020 usano l'archiviazione di Azure Standard per il livello cloud. La differenza è solo nel livello locale nel dispositivo*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Differenze tra appliance cloud e dispositivo fisico

L'appliance cloud StorSimple è una versione esclusivamente software di StorSimple che viene eseguita in un singolo nodo in una macchina virtuale di Microsoft Azure. L'appliance cloud supporta gli scenari di ripristino di emergenza in cui il dispositivo fisico non è disponibile. L'appliance cloud può essere usata nel recupero a livello di elemento da backup, nel ripristino di emergenza locale e in scenari di sviluppo e test basati su cloud.

#### <a name="differences-from-the-physical-device"></a>Differenze rispetto al dispositivo fisico

La tabella seguente illustra alcune differenze chiave tra l'appliance cloud StorSimple e il dispositivo fisico StorSimple.

|  | Dispositivo fisico | Appliance cloud |
| --- | --- | --- |
| **Posizione** |Si trova nel data center. |Viene eseguito in Azure. |
| **Interfacce di rete** |Ha sei interfacce di rete: da DATA 0 a DATA 5. |Ha una sola interfaccia di rete: DATA 0. |
| **Registrazione** |La registrazione viene eseguita durante il passaggio della configurazione iniziale. |La registrazione è un'attività separata. |
| **Chiave DEK del servizio** |Rigenerare la chiave nel dispositivo fisico e quindi aggiornare l'appliance cloud con la nuova chiave. |Non è possibile rigenerare la chiave dall'appliance cloud. |
| **Tipi di volume supportati** |Supporta volumi sia aggiunti in locale che a livelli. |Supporta solo volumi a livelli. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Prerequisiti per l'appliance cloud

Le sezioni seguenti illustrano i prerequisiti di configurazione per l'appliance cloud StorSimple. Prima di distribuire un'appliance cloud, esaminare le considerazioni relative alla sicurezza per il relativo uso.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Requisiti di Azure

Prima di effettuare il provisioning dell'appliance cloud, è necessario eseguire queste operazioni preliminari nell'ambiente Azure:

* Assicurarsi che un dispositivo fisico StorSimple serie 8000 (modello 8100 o 8600) sia stato distribuito e sia in esecuzione nel data center. Registrare questo dispositivo con lo stesso servizio Gestione dispositivi StorSimple per cui si intende creare un'appliance cloud StorSimple.
* Per l'appliance cloud, [configurare una rete virtuale in Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Se si usa l'archiviazione Premium, sarà necessario creare una rete virtuale in un'area di Azure che supporta l'archiviazione Premium. Le aree con Archiviazione Premium corrispondono alla riga relativa all'archiviazione su disco nell'[elenco dei servizi di Azure in base all'area](https://azure.microsoft.com/regions/services/).
* È consigliabile usare il server DNS predefinito fornito da Azure anziché specificare il nome di un proprio server DNS. Se il nome del server DNS non è valido o se il server DNS non riesce a risolvere correttamente l'indirizzo IP, non sarà possibile creare l'appliance cloud.
* Le opzioni point-to-site e da sito a sito non sono obbligatorie, ma facoltative. Se si desidera, è possibile configurarle per scenari più avanzati.
* È possibile creare nella rete virtuale [macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-quick-create-portal.md) (server host) che possono usare i volumi esposti dall'appliance cloud. Tali server devono soddisfare i seguenti requisiti:

  * Svolgere il ruolo di macchine virtuali Windows o Linux nelle quali è installato il software iSCSI Initiator
  * Essere eseguiti nella stessa rete virtuale dell'appliance cloud
  * Potersi connettere alla destinazione iSCSI dell'appliance cloud tramite l'indirizzo IP interno dell'appliance cloud
  * Assicurarsi di aver configurato il supporto per il traffico iSCSI e cloud nella stessa rete virtuale.

#### <a name="storsimple-requirements"></a>Requisiti di StorSimple

Prima di creare un'appliance cloud, effettuare gli aggiornamenti seguenti nel servizio Gestione dispositivi StorSimple:

* Aggiungere [record controllo di accesso](storsimple-8000-manage-acrs.md) per le VM che svolgeranno il ruolo di server host per l'appliance cloud.
* Usare un [account di archiviazione](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) nella stessa area dell'appliance cloud. Gli account di archiviazione posti in aree diverse possono causare una riduzione delle prestazioni. Con l'appliance cloud è possibile usare un account di archiviazione Standard o Premium. Altre informazioni su come creare un [account di archiviazione Standard](../storage/common/storage-create-storage-account.md) o un [account di archiviazione Premium](../virtual-machines/windows/premium-storage.md)
* Per la creazione dell'appliance cloud usare un account di archiviazione diverso rispetto a quello usato per i dati. Se si utilizza lo stesso account di archiviazione, si potrebbe verificare una riduzione delle prestazioni.

Prima di iniziare, verificare quanto segue:

* Account del portale di Azure con credenziali di accesso.
* Copia della chiave di crittografia dei dati del servizio dal dispositivo fisico registrato nel servizio Gestione dispositivi StorSimple.

## <a name="create-and-configure-the-cloud-appliance"></a>Creare e configurare l'appliance cloud

Prima di eseguire queste procedure, verificare che siano stati soddisfatti i [prerequisiti per l'appliance cloud](#prerequisites-for-the-cloud-appliance).

Seguire questa procedura per creare un'appliance cloud StorSimple.

### <a name="step-1-create-a-cloud-appliance"></a>Passaggio 1: Creare un'appliance cloud

Seguire questa procedura per creare l'appliance cloud StorSimple.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Se la creazione dell'appliance cloud ha esito negativo in questo passaggio, potrebbe non essere disponibile la connettività a Internet. Per altre informazioni, vedere la sezione su come [risolvere gli errori di connettività Internet](#troubleshoot-internet-connectivity-errors) durante la creazione di un'appliance cloud.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>Passaggio 2: Configurare e registrare l'appliance cloud

Prima di iniziare questa procedura, verificare di avere una copia della chiave di crittografia dei dati del servizio. La chiave di crittografia dei dati del servizio viene creata durante la registrazione del primo dispositivo fisico StorSimple nel servizio Gestione dispositivi StorSimple. Le istruzioni prevedono che venga salvata in una posizione sicura. Se non si dispone di una copia della chiave DEK del servizio, è necessario contattare il supporto tecnico Microsoft e richiedere assistenza.

Seguire questa procedura per configurare e registrare l'appliance cloud StorSimple.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Passaggio 3: (Facoltativo) Modificare le impostazioni di configurazione del dispositivo

La sezione seguente illustra le impostazioni di configurazione del dispositivo che sono necessarie per l'appliance cloud StorSimple se si vuole usare CHAP o StorSimple Snapshot Manager o se si vuole modificare la password amministratore del dispositivo.

#### <a name="configure-the-chap-initiator"></a>Configurare l'iniziatore dell'autenticazione CHAP

Questo parametro contiene le credenziali che l'appliance cloud (destinazione) prevede di ricevere dagli iniziatori (server) che tentano di accedere ai volumi. Gli iniziatori forniscono un nome utente e una password CHAP per identificarsi nel dispositivo durante l'autenticazione. Per informazioni dettagliate sulla procedura, vedere [Configurare CHAP per il dispositivo StorSimple](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurare la destinazione dell'autenticazione CHAP

Questo parametro contiene le credenziali usate dall'appliance cloud quando un iniziatore abilitato per CHAP richiede l'autenticazione reciproca o bidirezionale. L'appliance cloud usa un nome utente e una password per l'autenticazione CHAP inversa per identificarsi nell'iniziatore durante il processo di autenticazione.

> [!NOTE]
> Le impostazioni della destinazione CHAP sono impostazioni globali. Quando vengono applicate, tutti i volumi connessi all'appliance cloud usano l'autenticazione CHAP.

Per informazioni dettagliate sulla procedura, vedere [Configurare CHAP per il dispositivo StorSimple](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurare la password di Gestione snapshot StorSimple

Il software di Gestione snapshot StorSimple si trova nell'host di Windows e consente agli amministratori di gestire i backup del dispositivo StorSimple sotto forma di snapshot locali e cloud.

> [!NOTE]
> Per l'appliance cloud, l'host Windows è una macchina virtuale di Azure.

Quando si configura un dispositivo in StorSimple Snapshot Manager, viene richiesto di specificare l'indirizzo IP del dispositivo StorSimple e la password per autenticare il dispositivo di archiviazione. Per informazioni dettagliate sulla procedura, vedere [Configurare la password di StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Configurare la password dell’amministratore del dispositivo

Quando si usa l'interfaccia di Windows PowerShell per accedere all'appliance cloud, è necessario immettere una password amministratore del dispositivo. Per la sicurezza dei dati, per poter usare l'appliance cloud è prima necessario modificare la password. Per informazioni dettagliate sulla procedura, vedere [Configurare la password dell'amministratore del dispositivo](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-cloud-appliance"></a>Connettersi in remoto all'appliance cloud

Per impostazione predefinita, l'accesso remoto all'appliance cloud tramite l'interfaccia di Windows PowerShell non è abilitato. È necessario abilitare la gestione remota prima nell'appliance cloud e quindi nel client usato per accedervi.

La procedura in due passaggi seguente illustra come connettersi in remoto all'appliance cloud.

### <a name="step-1-configure-remote-management"></a>Passaggio 1: Configurare la gestione remota

Seguire questa procedura per configurare la gestione remota per l'appliance cloud StorSimple.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>Passaggio 2: Accedere in remoto all'appliance cloud

Dopo aver abilitato la gestione remota nell'appliance cloud, usare la comunicazione remota di Windows PowerShell per connettersi all'appliance da un'altra macchina virtuale all'interno della stessa rete virtuale. Ad esempio, è possibile connettersi dalla VM host che è stata configurata e usata per connettere iSCSI. Nella maggior parte delle distribuzioni, per accedere alla VM host verrà aperto un endpoint pubblico che potrà essere usato per accedere all'appliance cloud.

> [!WARNING]
> **Per una maggiore sicurezza, si consiglia di utilizzare HTTPS quando ci si connette agli endpoint e di eliminare gli endpoint dopo aver completato la sessione remota in PowerShell.**

Eseguire le procedure descritte in [Connettersi in remoto al dispositivo StorSimple](storsimple-8000-remote-connect.md) per configurare la comunicazione remota per l'appliance cloud.

## <a name="connect-directly-to-the-cloud-appliance"></a>Connettersi direttamente all'appliance cloud

È anche possibile connettersi direttamente all'appliance cloud. Per connettersi direttamente all'appliance cloud da un altro computer all'esterno della rete virtuale o dell'ambiente Microsoft Azure, è necessario creare endpoint aggiuntivi.

Seguire questa procedura per creare un endpoint pubblico per l'appliance cloud.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

Si consiglia di connettersi da un'altra macchina virtuale presente all'interno della stessa rete virtuale; in questo modo si riduce il numero di endpoint pubblici nella rete virtuale. In questo caso, ci si connette alla macchina virtuale tramite una sessione Desktop remoto e si configura tale macchina virtuale per l'uso come qualsiasi altro client Windows in una rete locale. Non è necessario aggiungere il numero della porta pubblica perché la porta è già nota.

## <a name="work-with-the-storsimple-cloud-appliance"></a>Usare l'appliance cloud StorSimple

Ora che è stata creata e configurata l'appliance cloud StorSimple, si può iniziare a usarla. In un'appliance cloud è possibile usare contenitori di volumi, volumi e criteri di backup così come in un dispositivo fisico StorSimple. L'unica differenza è che è necessario assicurarsi di selezionare l'appliance cloud nell'elenco dei dispositivi. Per le procedure dettagliate per le varie attività di gestione dell'appliance cloud, vedere l'articolo su come [usare il servizio Gestione dispositivi StorSimple per gestire un'appliance cloud](storsimple-8000-manager-service-administration.md).

Le sezioni seguenti illustrano alcune differenze riscontrabili quando si usa l'appliance cloud.

### <a name="maintain-a-storsimple-cloud-appliance"></a>Gestire un'appliance cloud StorSimple

Dato che si tratta di un dispositivo esclusivamente software, la gestione dell'appliance cloud è minima rispetto a quella di un dispositivo fisico.

Non è possibile aggiornare un'appliance cloud. Usare l'ultima versione del software per creare una nuova appliance cloud.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Account di archiviazione per un'appliance cloud

Vengono creati account di archiviazione che verranno usati dal servizio Gestione dispositivi StorSimple, dall'appliance cloud e dal dispositivo fisico. Quando si creano gli account di archiviazione, è consigliabile usare un identificatore di area come nome descrittivo, in modo che l'area sia coerente in tutti i componenti di sistema. Per un'appliance cloud, è importante che tutti i componenti si trovino nella stessa area per evitare problemi di prestazioni.

Per una procedura dettagliata, vedere [Aggiungere un account di archiviazione](storsimple-8000-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-cloud-appliance"></a>Disattivare un'appliance cloud StorSimple

Quando si disattiva un'appliance cloud, vengono eliminate la VM e le risorse create durante il provisioning. Dopo la disattivazione dell'appliance cloud, non è possibile ripristinarne lo stato precedente. Prima di disattivare l'appliance cloud, assicurarsi di arrestare o eliminare i client e gli host che dipendono da essa.

La disattivazione di un'appliance cloud determina le azioni seguenti:

* L'appliance cloud viene rimossa.
* Il disco del sistema operativo e i dischi dati creati per l'appliance cloud vengono rimossi.
* Il servizio ospitato e la rete virtuale creata durante il provisioning vengono mantenuti. Se non si utilizzano, è necessario eliminarli manualmente.
* Gli snapshot cloud creati per l'appliance cloud vengono mantenuti.

Per una procedura dettagliata, vedere [Disattivare ed eliminare un dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

Non appena viene visualizzata come disattivata nel pannello del servizio Gestione dispositivi StorSimple, l'appliance cloud può essere eliminata dall'elenco dei dispositivi nel pannello **Dispositivi**.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Avviare, arrestare e riavviare un'appliance cloud
A differenza del dispositivo fisico StorSimple, per un'appliance cloud StorSimple non è disponibile un pulsante di accensione o spegnimento. In alcuni casi, tuttavia, potrebbe essere necessario arrestare e riavviare l'appliance cloud.

Il modo più semplice per avviare, arrestare e riavviare un'appliance cloud consiste nell'usare il pannello del servizio Macchine virtuali. Passare al servizio Macchine virtuali. Nell'elenco delle VM identificare quella corrispondente all'appliance cloud (con lo stesso nome) e fare clic sul nome della VM. Quando si visualizza il pannello della macchina virtuale, lo stato dell'appliance cloud è **In esecuzione** perché l'appliance viene avviata per impostazione predefinita al termine della creazione. È possibile avviare, arrestare e riavviare una macchina virtuale in qualsiasi momento.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Ripristino delle impostazioni di fabbrica
Se si decide di ripristinare l'appliance cloud, disattivarla ed eliminarla e quindi crearne una nuova.

## <a name="fail-over-to-the-cloud-appliance"></a>Effettuare il failover nell'appliance cloud
Il ripristino di emergenza è uno dei principali scenari per cui è stata progettata l'appliance cloud StorSimple. In questo scenario il dispositivo fisico StorSimple o il data center completo potrebbe non essere disponibile. Fortunatamente, è possibile usare un'appliance cloud per ripristinare le operazioni in una posizione alternativa. Durante il ripristino di emergenza, la proprietà dei contenitori di volumi del dispositivo di origine viene trasferita all'appliance cloud.

Per il ripristino di emergenza devono essere soddisfatti i prerequisiti seguenti:

* L'appliance cloud è stata creata e configurata.
* Tutti i volumi all'interno del contenitore sono offline.
* Al contenitore di volumi di cui si effettua il failover è associato uno snapshot cloud.

> [!NOTE]
> * Quando si usa un'appliance cloud come dispositivo secondario per il ripristino di emergenza, tenere presente che il modello 8010 ha 30 TB di spazio di archiviazione Standard e il modello 8020 ha 64 TB di spazio di archiviazione Premium. La capacità superiore dell'appliance cloud 8020 potrebbe essere più adatta per uno scenario di ripristino di emergenza.

Per una procedura dettagliata, vedere la sezione relativa al [failover in un'appliance cloud](storsimple-8000-device-failover-cloud-appliance.md).

## <a name="delete-the-cloud-appliance"></a>Eliminare l'appliance cloud
Se si vuole interrompere l'addebito di ore di calcolo per l'uso di un'appliance cloud StorSimple precedentemente configurata e usata, è necessario arrestare l'appliance cloud. L'arresto dell'appliance cloud determina la deallocazione della VM. Questa azione interromperà l'addebito di costi nella sottoscrizione. Continueranno a essere addebitati, invece, i costi di archiviazione per i dischi dati e del sistema operativo.

Per interrompere tutti gli addebiti, è necessario eliminare l'appliance cloud. Per eliminare i backup creati dall'appliance cloud, è possibile disattivare o eliminare il dispositivo. Per altre informazioni, vedere [Disattivare ed eliminare un dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Risolvere gli errori di connettività Internet
Se durante la creazione di un'appliance cloud non è disponibile connettività a Internet, il passaggio della creazione ha esito negativo. Per risolvere gli errori di connettività Internet, seguire questa procedura nel portale di Azure:

1. [Creare una macchina virtuale Windows Server 2012 in Azure](/articles/virtual-machines/windows/quick-create-portal.md). Questa macchina virtuale dovrà usare lo stesso account di archiviazione, la stessa rete virtuale e la stessa subnet usati dall'appliance cloud. Se esiste già un host Windows Server in Azure che usa lo stesso account di archiviazione, la stessa rete virtuale e la stessa subnet, è anche possibile risolvere i problemi di connettività Internet usando tale host.
2. Eseguire l'accesso remoto alla macchina virtuale creata nel passaggio precedente.
3. Aprire una finestra di comando nella macchina virtuale (premere tasto WINDOWS + R e quindi digitare `cmd`).
4. Al prompt eseguire questo comando:

    `nslookup windows.net`
5. Se `nslookup` ha esito negativo, l'errore di connettività Internet impedisce la registrazione dell'appliance cloud nel servizio Gestione dispositivi StorSimple.
6. Apportare alla rete virtuale le modifiche necessarie affinché l'appliance cloud possa accedere a siti di Azure come _windows.net_.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire un'appliance cloud](storsimple-8000-manager-service-administration.md).
* Informazioni su come [Ripristinare un volume StorSimple da un set di backup](storsimple-8000-restore-from-backup-set-u2.md).
