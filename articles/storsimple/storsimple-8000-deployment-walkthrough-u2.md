---
title: Distribuire il dispositivo StorSimple serie 8000 nel portale di Azure | Microsoft Docs
description: Descrive i passaggi e le procedure consigliate per la distribuzione del dispositivo StorSimple serie 8000 che esegue l'aggiornamento 3 e versioni successive e del servizio Gestione dispositivi StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: dc021d2277c419dd5a892aacd7bff0707e5564fa
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>Distribuire un dispositivo StorSimple locale (aggiornamento 3 e successivi)

## <a name="overview"></a>Panoramica
Benvenuti nella distribuzione del dispositivo Microsoft Azure StorSimple. Queste esercitazioni per la distribuzione si applicano all'aggiornamento 3 o successivi di StorSimple serie 8000. Questa serie di esercitazioni include un elenco di controllo della configurazione, di prerequisiti di configurazione e i passaggi di configurazione dettagliati per il dispositivo StorSimple.

Le informazioni contenute in queste esercitazioni prevedono che siano state esaminate le precauzioni di sicurezza e che il dispositivo StorSimple sia stato disimballato, installato e cablato. Se occorre eseguire tali attività, iniziare con la revisione di [precauzioni di sicurezza](storsimple-8000-safety.md). Seguire le istruzioni specifiche per il dispositivo per disimballare, montare su rack e cablare il dispositivo.

* [Decomprimere, montare su rack e cablare il dispositivo 8100](storsimple-8100-hardware-installation.md)
* [Decomprimere, montare su rack e cablare il dispositivo 8600](storsimple-8600-hardware-installation.md)

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. Si consiglia di esaminare l'elenco di controllo di pre-installazione prima di iniziare. Il processo di distribuzione e configurazione può richiedere parecchio tempo.

> [!NOTE]
> Le informazioni sulla distribuzione di StorSimple pubblicate nel sito Web di Microsoft Azure si applicano solo ai dispositivi di StorSimple serie 8000. Per informazioni complete sui dispositivi serie 7000, vedere: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Per informazioni sulla distribuzione della serie 7000, vedere la [Guida introduttiva al sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/). 


## <a name="deployment-steps"></a>Passaggi di distribuzione
Eseguire questi passaggi obbligatori per configurare il dispositivo StorSimple e connetterlo al servizio Gestione dispositivi StorSimple. Oltre ai passaggi necessari sono disponibili passaggi facoltativi e procedure che possono essere necessarie durante la distribuzione. Le istruzioni dettagliate di distribuzione indicano quando è necessario eseguire ciascuno di questi passaggi facoltativi.

| Passaggio | DESCRIZIONE |
| --- | --- |
| **PREREQUISITI** |Devono essere completati per preparare la successiva distribuzione. |
| [Elenco di controllo configurazione della distribuzione](#deployment-configuration-checklist) |Usare questo elenco di controllo per raccogliere e registrare informazioni prima e durante la distribuzione. |
| [Prerequisiti di distribuzione](#deployment-prerequisites) |Questi confermano che l'ambiente è pronto per la distribuzione. |
|  | |
| **DISTRIBUZIONE PASSO PER PASSO** |Questi passaggi sono necessari per distribuire il dispositivo StorSimple nell'ambiente di produzione. |
| [Passaggio 1: Creare un nuovo servizio](#step-1-create-a-new-service) |Impostare Gestione cloud e archiviazione per il dispositivo StorSimple. *Ignorare questo passaggio se si dispone di un servizio esistente per altri dispositivi StorSimple*. |
| [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key) |Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio di gestione. |
| [Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Per completare la configurazione usando il servizio di gestione, connettere il dispositivo alla rete e registrarlo con Azure. |
| [Passaggio 4: Completare l'installazione minima del dispositivo](#step-4-complete-minimum-device-setup)</br>[Procedura consigliata: aggiornare il dispositivo StorSimple](#scan-for-and-apply-updates) |Utilizzare il servizio di gestione per completare l'installazione del dispositivo e abilitarlo per fornire l'archiviazione. |
| [Passaggio 5: Creare un contenitore di volumi](#step-5-create-a-volume-container) |Creare un contenitore di volumi di provisioning. Un contenitore di volumi dispone di account di archiviazione, larghezza di banda e impostazioni di crittografia per tutti i volumi in esso contenuti. |
| [Passaggio 6: Creare un volume](#step-6-create-a-volume) |Effettuare il provisioning di volumi di archiviazione nel dispositivo StorSimple per i server. |
| [Passaggio 7: Montare, inizializzare e formattare un volume](#step-7-mount-initialize-and-format-a-volume)</br>[Facoltativo: Configurare MPIO](storsimple-8000-configure-mpio-windows-server.md) |Connettere i server all'archiviazione iSCSI fornita dal dispositivo. Facoltativamente, è possibile configurare MPIO per assicurarsi che i server possano di tollerare errori di collegamento, rete e interfaccia. |
| [Passaggio 8: Eseguire un backup](#step-8-take-a-backup) |Impostare criteri di backup per proteggere i dati |
|  | |
| **ALTRE PROCEDURE** |Potrebbe essere necessario fare riferimento a queste procedure quando si distribuisce la soluzione. |
| [Configurare un nuovo account di archiviazione per il servizio](#configure-a-new-storage-account-for-the-service) | |
| [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console) | |
| [Ottenere il nome qualificato iSCSI di un host di Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Creazione di un backup manuale](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Elenco di controllo configurazione della distribuzione
Prima di distribuire il dispositivo, è necessario raccogliere informazioni per configurare il software sul dispositivo StorSimple. La preparazione di alcune di queste informazioni in anticipo consente di semplificare il processo di distribuzione del dispositivo StorSimple nell'ambiente. Scaricare e utilizzare questo elenco di controllo per annotare i dettagli di configurazione quando si distribuisce il dispositivo.

* [Scaricare l'elenco di controllo configurazione della distribuzione StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Prerequisiti di distribuzione
Le sezioni seguenti illustrano i prerequisiti di configurazione per il servizio Gestione dispositivi StorSimple e per il dispositivo StorSimple.

### <a name="for-the-storsimple-device-manager-service"></a>Per il servizio Gestione dispositivi StorSimple
Prima di iniziare, verificare che:

* Si dispone dell'account Microsoft con credenziali di accesso.
* Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.
* La sottoscrizione di Microsoft Azure è abilitata per il servizio Gestione dispositivi StorSimple. La sottoscrizione deve essere acquistata tramite il [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* È possibile accedere a un software di emulazione terminal, ad esempio PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Per il dispositivo nel centro dati
Prima di configurare il dispositivo, assicurarsi che il dispositivo sia completamente disimballato, montato su un rack e cablato per l’alimentazione, la rete e l’accesso seriale come descritto in:

* [Decomprimere, montare su rack e cablare il dispositivo 8100](storsimple-8100-hardware-installation.md)
* [Decomprimere, montare su rack e cablare il dispositivo 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter
Prima di iniziare, verificare che:

* Per consentire il traffico iSCSI e cloud vengono aperte le porte nel firewall del data center come descritto in [Requisiti di rete per il dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>DISTRIBUZIONE PASSO PER PASSO
Utilizzare le seguenti istruzioni dettagliate per distribuire il dispositivo StorSimple nel datacenter.

## <a name="step-1-create-a-new-service"></a>Passaggio 1: Creare un nuovo servizio
Un servizio Gestione dispositivi StorSimple può gestire più dispositivi StorSimple. Eseguire i passaggi seguenti per creare una nuova istanza del servizio Gestione dispositivi StorSimple.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> Se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio, sarà necessario creare almeno un account di archiviazione dopo avere creato un servizio. Tale account di archiviazione viene usato in fase di creazione di un contenitore di volumi.
>
> * Se non è stato creato automaticamente un account di archiviazione, andare a [Configurare un nuovo account di archiviazione per il servizio](#configure-a-new-storage-account-for-the-service) per istruzioni dettagliate.
> * Se è stata abilitata la creazione automatica di un account di archiviazione, andare al [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Passaggio 2: Ottenere la chiave di registrazione del servizio
Quando il servizio Gestione dispositivi StorSimple è attivo e in esecuzione, è necessario ottenere la chiave di registrazione. Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio.

Seguire questa procedura nel portale di Azure.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple
Utilizzare Windows PowerShell per StorSimple per completare l'installazione iniziale del dispositivo StorSimple, come illustrato nella procedura seguente. Per completare questo passaggio, è necessario usare il software di emulazione terminal. Per ulteriori informazioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Passaggio 4: Completare l'installazione minima del dispositivo
Per la configurazione minima del dispositivo del dispositivo StorSimple, è necessario: 

* Specificare un nome descrittivo per il dispositivo.
* Impostare il fuso orario del dispositivo.
* Assegnare indirizzi IP fissi a entrambi i controller.

Seguire questa procedura nel portale di Azure per completare l'installazione minima del dispositivo.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

Dopo aver completato la configurazione minima del dispositivo, è consigliabile [cercare e applicare gli aggiornamenti più recenti](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>Passaggio 5: Creare un contenitore di volumi
Un contenitore di volumi dispone di account di archiviazione, larghezza di banda e impostazioni di crittografia per tutti i volumi in esso contenuti. Prima di iniziare il provisioning dei volumi nel dispositivo StorSimple è necessario creare un contenitore di volumi.

Seguire questa procedura nel portale di Azure per creare un contenitore del volume.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Passaggio 6: Creare un volume
Dopo aver creato un contenitore di volumi, è possibile eseguire il provisioning di un volume di archiviazione nel dispositivo StorSimple per i server. Seguire questa procedura nel portale di Azure per creare un volume.

> [!IMPORTANT]
> Gestione dispositivi StorSimple consente di creare volumi con thin provisioning e con provisioning completo. Tuttavia, non è possibile creare volumi con provisioning parziale.


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Passaggio 7: Montare, inizializzare e formattare un volume
I passaggi seguenti vengono eseguiti nell'host di Windows Server.

> [!IMPORTANT]
> * Per la disponibilità elevata della soluzione StorSimple, è consigliabile configurare MPIO nei server host (facoltativo) prima della configurazione iSCSI. Usare la funzionalità MPIO sui server host per assicurarsi che i server possano tollerare un errore di collegamento, rete o interfaccia.
> * Per istruzioni sull'installazione e sulla configurazione di MPIO e iSCSI sull’host Windows Server, vedere [Configurare MPIO per il dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md). Sono compresi anche i passaggi per montare, inizializzare e formattare volumi StorSimple.
> * Per istruzioni sull'installazione e sulla configurazione di MPIO e iSCSI su un host Linux, vedere [Configurare MPIO per l’host Linux di StorSimple](storsimple-configure-mpio-on-linux.md)

Se si decide di non configurare MPIO, eseguire la procedura seguente per montare, inizializzare e formattare i volumi StorSimple su un host Windows Server.

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Passaggio 8: Eseguire un backup
I backup garantiscono la protezione temporizzata dei volumi e migliorano la recuperabilità riducendo al minimo i tempi di ripristino. È possibile eseguire due tipi di backup su un dispositivo StorSimple: snapshot locali e snapshot nel cloud. Ciascuno di questi tipi di backup può essere **pianificato** o **manuale**.

Seguire questa procedura nel portale di Azure per creare un backup pianificato.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

È possibile eseguire un backup manuale in qualsiasi momento. Per le procedure, vedere [Creare un backup manuale](#create-a-manual-backup).

La configurazione del dispositivo è stata completata.

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurare un nuovo account di archiviazione per il servizio
Si tratta di un passaggio che è necessario eseguire solo se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio. Per creare un contenitore di volumi di StorSimple, è necessario un account di archiviazione di Microsoft Azure.

Se occorre creare un account di archiviazione di Azure in un'area diversa, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md) per istruzioni dettagliate.

Seguire questa procedura nella pagina del **servizio Gestione dispositivi StorSimple** nel portale di Azure.

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Utilizzare PuTTY per connettersi alla console seriale del dispositivo
Per connettersi a Windows PowerShell per StorSimple, è necessario utilizzare un software di emulazione terminal, ad esempio PuTTY. Quando si accede al dispositivo direttamente tramite la console seriale oppure aprendo una sessione telnet da un computer remoto, è possibile utilizzare PuTTY.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Cercare e applicare gli aggiornamenti
L’aggiornamento del dispositivo può richiedere diverse ore. Per istruzioni dettagliate sull'installazione dell'aggiornamento più recente, vedere [Installare l'aggiornamento 5](storsimple-8000-install-update-5.md).


## <a name="get-the-iqn-of-a-windows-server-host"></a>Ottenere il nome qualificato iSCSI di un host di Windows Server
Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI (IQN) di un host di Windows che esegue Windows Server® 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Creare un backup manuale
Seguire questa procedura nel portale di Azure per creare un backup manuale su richiesta per un singolo volume nel dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Configurare un'appliance cloud StorSimple](storsimple-8000-cloud-appliance-u2.md).
* [Use the StorSimple Device Manager service to manage your StorSimple device (Usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple)](storsimple-8000-manager-service-administration.md).

