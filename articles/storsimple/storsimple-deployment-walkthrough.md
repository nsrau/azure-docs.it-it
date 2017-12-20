---
title: Distribuire il dispositivo StorSimple locale | Documentazione Microsoft
description: Descrive i passaggi e le procedure consigliate per la distribuzione del servizio e del dispositivo StorSimple. (Si applica alla versione 3 di Microsoft Azure StorSimple e alle versioni precedenti.)
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b27f87a2-1363-4e0d-90f7-37b5dd1f21c9
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 5a056bf1ef6b13a2989922e135b99d31d84637c7
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="deploy-your-on-premises-storsimple-device"></a>Distribuire un dispositivo StorSimple locale
> [!NOTE]
> Il portale classico per StorSimple è deprecato. Le istanze di Gestione dispositivi StorSimple passeranno automaticamente al nuovo portale di Azure in base alla pianificazione per la funzionalità deprecata. Si riceveranno un messaggio di posta elettronica e una notifica del portale relativi a questo passaggio. Anche questo documento verrà ritirato a breve. Per domande relative al passaggio, vedere [Domande frequenti: spostamento nel portale di Azure](storsimple-8000-move-azure-portal-faq.md).

> [!div class="op_single_selector"]
> * [Aggiornamento 2](storsimple-deployment-walkthrough-u2.md)
> * [Aggiornamento 1](storsimple-deployment-walkthrough-u1.md)
> * [Versione di disponibilità generale (GA)](storsimple-deployment-walkthrough.md)
> 
> 

## <a name="overview"></a>Panoramica
Benvenuti nella distribuzione del dispositivo Microsoft Azure StorSimple. Queste esercitazioni sulla distribuzione si applicano a StorSimple serie 8000 (versione di rilascio), StorSimple serie 8000 aggiornamento 0.1, StorSimple serie 8000 aggiornamento 0.2 e StorSimple serie 8000 aggiornamento 0.3. Questa serie di esercitazioni descrive come configurare i dispositivi StorSimple e fornisce un elenco di controllo preliminare all’installazione, i prerequisiti di configurazione e i passaggi di configurazione dettagliati.

Le informazioni contenute in queste esercitazioni prevedono che siano state esaminate le precauzioni di sicurezza e che il dispositivo StorSimple sia stato disimballato, installato e cablato. Se occorre eseguire tali attività, iniziare con la revisione di [precauzioni di sicurezza](storsimple-safety.md). A seconda del modello di dispositivo, è possibile decomprimere, montare su rack e cablare seguendo le istruzioni disponibili in:

* [Decomprimere, montare su rack e cablare il dispositivo 8100](storsimple-8100-hardware-installation.md)
* [Decomprimere, montare su rack e cablare il dispositivo 8600](storsimple-8600-hardware-installation.md)

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. Si consiglia di esaminare l'elenco di controllo di pre-installazione prima di iniziare. Il processo di distribuzione e configurazione può richiedere parecchio tempo.

> [!NOTE]
> Le informazioni sulla distribuzione di StorSimple pubblicate nel sito Web di Microsoft Azure si applicano solo ai dispositivi di StorSimple serie 8000. Per informazioni complete sui dispositivi serie 5000 e 7000, vedere: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Per informazioni sulla distribuzione delle serie 5000 e 7000, vedere la [Guida introduttiva al sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).
> 
> 

## <a name="deployment-steps"></a>Passaggi di distribuzione
Effettuare i seguenti passaggi necessari per configurare il dispositivo StorSimple e connetterlo al servizio StorSimple Manager: Oltre ai passaggi necessari sono disponibili passaggi facoltativi e procedure che possono essere necessarie durante la distribuzione. Le istruzioni dettagliate di distribuzione indicano quando è necessario eseguire ciascuno di questi passaggi facoltativi.

| Passaggio | Descrizione |
| --- | --- |
| **PREREQUISITI** |Questi elementi dovranno essere completati in preparazione per la prossima distribuzione. |
| Elenco di controllo configurazione della distribuzione. |Utilizzare questo elenco di controllo per raccogliere e registrare informazioni prima e durante la distribuzione. |
| Prerequisiti di distribuzione. |Questi prerequisiti confermano che l'ambiente è pronto per la distribuzione. |
|  | |
| **DISTRIBUZIONE PASSO PER PASSO** |Questi passaggi sono necessari per distribuire il dispositivo StorSimple nell'ambiente di produzione. |
| Passaggio 1: Creare un nuovo servizio. |Configurare la gestione del cloud e l'archiviazione per il dispositivo StorSimple. Ignorare questo passaggio se si dispone di un servizio esistente per altri dispositivi StorSimple. |
| Passaggio 2: Ottenere la chiave di registrazione del servizio. |Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio di gestione. |
| Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple. |Connettere il dispositivo alla rete e registrarlo con Azure per completare l'installazione utilizzando il servizio di gestione. |
| Passaggio 4: Completare l'installazione minima del dispositivo</br>Facoltativo: aggiornare il dispositivo StorSimple. |Utilizzare il servizio di gestione per completare l'installazione del dispositivo e abilitarlo per fornire l'archiviazione. |
| Passaggio 5: Creare un contenitore di volumi. |Creare un contenitore di volumi di provisioning. Un contenitore di volumi include le impostazioni relative ad account di archiviazione, larghezza di banda e crittografia per tutti i volumi in esso contenuti. |
| Passaggio 6: Creare un volume. |Eseguire il provisioning di volumi di archiviazione nel dispositivo StorSimple per i server. |
| Passaggio 7: Montare, inizializzare e formattare un volume.</br>Facoltativo: Configurare MPIO. |Connettere i server all'archiviazione iSCSI fornita dal dispositivo. Facoltativamente, è possibile configurare MPIO per assicurarsi che i server possano di tollerare errori di collegamento, rete e interfaccia. |
| Passaggio 8: Eseguire un backup. |Impostare criteri di backup per proteggere i dati |
|  | |
| **ALTRE PROCEDURE** |Potrebbe essere necessario fare riferimento a queste procedure quando si distribuisce la soluzione. |
| Configurare un nuovo account di archiviazione per il servizio. | |
| Utilizzare PuTTY per connettersi alla console seriale del dispositivo. | |
| Ottenere il nome qualificato iSCSI di un host di Windows Server. | |
| Creare un backup manuale. | |

## <a name="deployment-configuration-checklist"></a>Elenco di controllo configurazione della distribuzione
Il seguente elenco di controllo per la configurazione della distribuzione descrive le informazioni che è necessario raccogliere prima e mentre si configura il software sul dispositivo StorSimple. La preparazione di queste informazioni in anticipo consentirà di semplificare il processo di distribuzione del dispositivo StorSimple nell'ambiente. Utilizzare questo elenco di controllo per annotare inoltre i dettagli di configurazione quando si distribuisce il dispositivo.

| Fase | Parametro | Dettagli | Valori |
| --- | --- | --- | --- |
| **Cablare il dispositivo** |Accesso seriale |Configurazione iniziale del dispositivo |Sì/No |
|  | | | |
| **Configurare e registrare il dispositivo** |Impostazioni di rete Data 0 |Indirizzo IP Data 0:</br>Subnet mask:</br>Gateway:</br>Server DNS primario:</br>Server NTP primario:</br>IP/FQDN server proxy Web (facoltativo):</br>porta proxy Web: | |
| &nbsp; |Password amministratore del dispositivo |La password deve avere una lunghezza compresa tra gli 8 e i 15 caratteri e deve contenere minuscole, maiuscole, numeri e caratteri speciali. | |
| &nbsp; |Password di Gestione snapshot StorSimple |La password deve avere una lunghezza compresa tra i 14 e i 15 caratteri e deve contenere caratteri minuscoli, maiuscoli, numerici e speciali. | |
| &nbsp; |Chiave di registrazione del servizio |Questa chiave viene generata dal portale di Azure classico. | |
| &nbsp; |Chiave DEK del servizio |Questa chiave viene creata quando il dispositivo è registrato con il servizio di gestione tramite Windows PowerShell per StorSimple. Copiare questo codice e salvarlo in un luogo sicuro. | |
|  | | | |
| **Completare la configurazione minima del dispositivo** |Nome descrittivo del dispositivo |Si tratta di un nome descrittivo per il dispositivo. | |
| &nbsp; |Fuso orario |Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate. | |
| &nbsp; |Server DNS secondario |Si tratta di una configurazione necessaria. | |
| &nbsp; |Interfaccia di rete: controller IP fissi del controller Data 0 |Questi IP devono essere instradabili a Internet.</br>Indirizzo IP fisso controller 0:</br>indirizzo IP fisso controller 1: | |
|  | | | |
| **Impostazioni interfaccia di rete aggiuntive** |Interfaccia di rete: Data 1</br>se iSCSI è abilitato, non configurare il Gateway. |Scopo: Cloud/ISCSI/non utilizzato</br>Indirizzo IP:</br>Subnet mask:</br>Gateway: | |
| &nbsp; |Interfaccia di rete: Data 2</br>se iSCSI è abilitato, non configurare il Gateway. |Scopo: Cloud/ISCSI/non utilizzato</br>Indirizzo IP:</br>Subnet mask:</br>Gateway: | |
| &nbsp; |Interfaccia di rete: Data 3</br>se iSCSI è abilitato, non configurare il Gateway. |Scopo: Cloud/ISCSI/non utilizzato</br>Indirizzo IP:</br>Subnet mask:</br>Gateway: | |
| &nbsp; |Interfaccia di rete: Data 4</br>se iSCSI è abilitato, non configurare il Gateway. |Scopo: Cloud/ISCSI/non utilizzato</br>Indirizzo IP:</br>Subnet mask:</br>Gateway: | |
| &nbsp; |Interfaccia di rete: Data 5</br>se iSCSI è abilitato, non configurare il Gateway. |Scopo: Cloud/ISCSI/non utilizzato</br>Indirizzo IP:</br>Subnet mask:</br>Gateway: | |
|  | | | |
| **Creare un contenitore di volumi** |Nome del contenitore di volume: |Nome del contenitore | |
| &nbsp; |Account di archiviazione Azure: |Nome dell’account di archiviazione & chiave di accesso da associare al contenitore di volumi | |
| &nbsp; |Chiave di crittografia di archiviazione cloud: |Chiave di crittografia per l'archiviazione in ogni contenitore | |
|  | | | |
| **Creare un volume** |Dettagli per ogni volume |Nome del volume: | |
| &nbsp; |&nbsp; |Dimensione: | |
| &nbsp; |&nbsp; |Tipo di utilizzo: | |
| &nbsp; |&nbsp; |Nome ACR: | |
| &nbsp; |&nbsp; |Criteri di backup predefinito: | |
|  | | | |
| **Montare, inizializzare e formattare un volume** |Dettagli per ogni server host che si connette allo spazio di archiviazione |Nome del Server di Windows: | |
| &nbsp; |&nbsp; |Windows Server nome qualificato iSCSI: | |
| &nbsp; |&nbsp; |Nome di volume di Windows Server: | |
| &nbsp; |&nbsp; |Lettera di unità o punti di montaggio NTFS: | |

## <a name="deployment-prerequisites"></a>Prerequisiti di distribuzione
Le sezioni seguenti illustrano i prerequisiti di configurazione per il servizio StorSimple Manager, per il dispositivo StorSimple e la rete nel datacenter.

### <a name="for-the-storsimple-manager-service"></a>Per il servizio StorSimple Manager
Prima di iniziare, verificare che:

* Si dispone dell'account Microsoft con credenziali di accesso.
* Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.
* La sottoscrizione di Microsoft Azure è abilitata per il servizio StorSimple Manager. La sottoscrizione deve essere acquistata tramite il [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* È possibile accedere a un software di emulazione terminal, ad esempio PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Per il dispositivo nel centro dati
Prima di configurare il dispositivo, verificare che:

* Il dispositivo è completamente disimballato, montato su un rack e cablato per l’alimentazione, la rete e l’accesso seriale come descritto in:
  
  * [Decomprimere, montare su rack e cablare il dispositivo 8100](storsimple-8100-hardware-installation.md)
  * [Decomprimere, montare su rack e cablare il dispositivo 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter
Prima di iniziare, verificare che:

* Per consentire il traffico iSCSI e cloud vengono aperte le porte nel firewall del data center come descritto in [Requisiti di rete per il dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* Il dispositivo nel datacenter può connettersi alla rete esterna. Eseguire il seguente cmdlet [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) (inserite sotto) per convalidare la connettività alla rete esterna. Esegui la convalida in un computer (nella rete datacenter) che dispone di connettività in Azure e in cui verrà distribuito il dispositivo StorSimple.  

| Per questo parametro... | Per verificare la validità... | Eseguire i comandi/cmdlet seguenti. |
| --- | --- | --- |
| **IP**</br>**Subnet**</br>**Gateway** |Si tratta di un indirizzo IPv4 o IPv6 valido?</br>Si tratta di una subnet valida?</br>Si tratta di un gateway valido?</br>Si tratta di un indirizzo IP duplicato sulla rete? |`ping ip`</br>`arp -a`</br>I comandi `ping` e `arp` devono avere esito negativo che indica che non esiste alcun dispositivo nella rete del datacenter che usa tale IP. |
|  | | |
| **DNS** |Si tratta di un DNS valido e può risolvere gli URL di Azure? |`Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>Un comando alternativo che può essere usato è:</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com` |
| &nbsp; |Verificare che la porta 53 sia aperta. Questa opzione è disponibile solo se si utilizza un server DNS esterno per il dispositivo. I DNS interni dovrebbero risolvere automaticamente gli URL esterni. |`Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[Ulteriori informazioni su questo cmdlet](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/) |
|  | | |
| **NTP** |È attiva la sincronizzazione dell'ora non appena il server NTP è di input. Controllare che la porta 123 sia aperta durante l'input `time.windows.com` o server di riferimento ora pubblico). |[Scaricare e utilizzare questo script](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca). |
|  | | |
| **Proxy (facoltativo)** |Si tratta di una porta e URI del proxy validi? </br> La modalità di autenticazione è corretta? |<code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>Questo comando deve essere eseguito immediatamente dopo la configurazione del proxy Web. Se viene restituito un codice di stato 200, significa che la connessione ha avuto esito positivo. |
| &nbsp; |Il traffico è indirizzabile tramite proxy? |Eseguire la convalida di DNS, il controllo NTP o HTTP dopo la configurazione del proxy sul dispositivo. Questo fornirà un quadro preciso se il traffico viene bloccato nel proxy o altrove. |
|  | | |
| **Registrazione** |Controllare che le porte TCP in uscita 443, 80, 9354 siano aperte. |`Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[Ulteriori informazioni per il cmdlet Test-NetConnection](https://technet.microsoft.com/library/dn372891.aspx) |

## <a name="step-by-step-deployment"></a>DISTRIBUZIONE PASSO PER PASSO
Utilizzare le seguenti istruzioni dettagliate per distribuire il dispositivo StorSimple nel datacenter.

## <a name="step-1-create-a-new-service"></a>Passaggio 1: Creare un nuovo servizio
Un servizio StorSimple Manager può gestire più dispositivi StorSimple. Per la distribuzione del primo dispositivo StorSimple, è necessario creare un nuovo servizio StorSimple Manager.

> [!IMPORTANT]
> Ignorare questo passaggio se si dispone di un servizio StorSimple Manager esistente e si prevede di distribuire il dispositivo StorSimple con tale servizio.
> 
> 

Effettuare i passaggi seguenti per creare una nuova istanza del servizio StorSimple Manager.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [!IMPORTANT]
> Se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio, sarà necessario creare almeno un account di archiviazione dopo avere creato un servizio. Tale account di archiviazione verrà utilizzato in fase di creazione di un contenitore di volumi.
> 
> Se non è stato creato automaticamente un account di archiviazione, andare a [Configurare un nuovo account di archiviazione per il servizio](#configure-a-new-storage-account-for-the-service) per istruzioni dettagliate.
> Se è stata abilitata la creazione automatica di un account di archiviazione, andare al [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2:-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Passaggio 2: Ottenere la chiave di registrazione del servizio
Quando il servizio StorSimple Manager è attivo e in esecuzione, è necessario ottenere la chiave di registrazione. Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio.

Eseguire i passaggi seguenti nel portale di Azure classico.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple
> [!IMPORTANT]
> Prima di eseguire questa configurazione, scollegare tutte le interfacce di rete diverse da DATA 0 su entrambi i controller (attivo e passivo).
> 
> 

Utilizzare Windows PowerShell per StorSimple per completare l'installazione iniziale del dispositivo StorSimple, come illustrato nella procedura seguente. Per completare questo passaggio sarà necessario utilizzare il software di emulazione terminal. Per ulteriori informazioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Passaggio 4: Completare l'installazione minima del dispositivo
Per la configurazione minima del dispositivo del dispositivo StorSimple, è necessario:

* Installare il server DNS secondario.
* Abilitare iSCSI in almeno un'interfaccia di rete.
* Assegnare indirizzi IP fissi a entrambi i controller.

Eseguire i passaggi seguenti nel portale di Azure classico per completare l'installazione minima del dispositivo.

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Una volta completata la configurazione del dispositivo, è necessario analizzare gli aggiornamenti e, se disponibili, installarli. Gli aggiornamenti possono richiedere diverse ore. Seguire le istruzioni in [cercare e applicare gli aggiornamenti](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>Passaggio 5: Creare un contenitore di volumi
Un contenitore di volumi dispone di account di archiviazione, larghezza di banda e impostazioni di crittografia per tutti i volumi in esso contenuti. Prima di iniziare il provisioning dei volumi nel dispositivo StorSimple è necessario creare un contenitore di volumi.

Eseguire i passaggi seguenti nel portale di Azure classico per creare un contenitore di volumi.

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Passaggio 6: Creare un volume
Dopo aver creato un contenitore di volumi, è possibile eseguire il provisioning di un volume di archiviazione nel dispositivo StorSimple per i server. Eseguire i passaggi seguenti nel portale di Azure classico per creare un volume.

> [!IMPORTANT]
> StorSimple di Azure consente di creare solo volumi di thin provisioning.  Non è possibile creare volumi con provisioning completo o parziale.
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Passaggio 7: Montare, inizializzare e formattare un volume
> [!IMPORTANT]
> * Per la disponibilità elevata della soluzione StorSimple, è consigliabile configurare MPIO nell'host di Windows Server (facoltativo) prima della configurazione iSCSI nell'host di Windows Server. Usare la funzionalità MPIO sui server host per assicurarsi che i server possano tollerare un errore di collegamento, rete o interfaccia.
> * Per istruzioni sull'installazione e sulla configurazione di MPIO e iSCSI, vedere [Configurare MPIO per il dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Questi comprendono anche i passaggi per montare, inizializzare e formattare volumi StorSimple.
> 
> 

Se si decide di non configurare MPIO, eseguire la procedura seguente per montare, inizializzare e formattare i volumi StorSimple.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Passaggio 8: Eseguire un backup
I backup garantiscono la protezione temporizzata dei volumi e migliorano la recuperabilità riducendo al minimo i tempi di ripristino. È possibile eseguire due tipi di backup su un dispositivo StorSimple: snapshot locali e snapshot nel cloud. Ciascuno di questi tipi di backup può essere **pianificato** o **manuale**.

Eseguire i passaggi seguenti nel portale di Azure classico per creare un backup pianificato.

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

È possibile eseguire un backup manuale in qualsiasi momento. Per le procedure, vedere [Creare un backup manuale](#Create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurare un nuovo account di archiviazione per il servizio
Si tratta di un passaggio che è necessario eseguire solo se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio. Per creare un contenitore di volumi di StorSimple, è necessario un account di archiviazione di Microsoft Azure.

Se occorre creare un account di archiviazione di Azure in un'area diversa, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md) per istruzioni dettagliate.

Eseguire i passaggi seguenti nel portale di Azure classico, nella pagina del **servizio StorSimple Manager** .

[!INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Utilizzare PuTTY per connettersi alla console seriale del dispositivo
Per connettersi a Windows PowerShell per StorSimple, è necessario utilizzare un software di emulazione terminal, ad esempio PuTTY. Quando si accede al dispositivo direttamente tramite la console seriale oppure aprendo una sessione telnet da un computer remoto, è possibile utilizzare PuTTY.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>cercare e applicare gli aggiornamenti
L’aggiornamento del dispositivo può richiedere da 1 a 4 ore. Eseguire i passaggi seguenti per la ricerca e l’applicazione degli aggiornamenti sul dispositivo.

> [!NOTE]
> Se si dispone di un gateway configurato su un’interfaccia di rete diversa da Data 0, è necessario disabilitare le interfacce di rete Data 2 e Data 3 prima di installare l'aggiornamento. Passare a **Dispositivi > Configura**e disabilitare le interfacce Data 2 e Data 3. Dopo aver aggiornato il dispositivo, è necessario attivare nuovamente tali interfacce.
> 
> 

#### <a name="to-update-your-device"></a>Per aggiornare il dispositivo
1. Nella pagina **Avvio rapido** del dispositivo fare clic su **Dispositivi**. Selezionare il dispositivo fisico, fare clic su **Manutenzione** e quindi su **Verifica aggiornamenti**.  
2. Viene creato un processo che consente di cercare gli aggiornamenti disponibili. Se sono disponibili aggiornamenti, **Verifica aggiornamenti** diventa **Installa aggiornamenti**. Fare clic su **Installa aggiornamenti**. Può essere richiesto di disabilitare Data 2 e Data 3 prima di installare gli aggiornamenti. È necessario disabilitare queste interfacce di rete o gli aggiornamenti non avranno esito positivo.
3. Verrà creato un processo di aggiornamento. Monitorare lo stato dell'aggiornamento passando a **Processi**.
   
   > [!NOTE]
   > Quando viene avviato il processo di aggiornamento, immediatamente lo stato viene visualizzato al 50%. Lo stato passa quindi al 100% solo dopo che è stato completato il processo di aggiornamento. Non esiste alcuno stato in tempo reale per il processo di aggiornamento.
   > 
   > 
4. Dopo aver completato l'aggiornamento del dispositivo, abilitare le interfacce di rete Data 2 e Data 3 se erano state disabilitate.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Ottenere il nome qualificato iSCSI di un host di Windows Server
Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI (IQN) di un host di Windows che esegue Windows Server 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Creare un backup manuale
Eseguire i passaggi seguenti nel portale di Azure classico per creare un backup manuale su richiesta per un singolo volume nel dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="next-steps"></a>Passaggi successivi
* Configurare un [dispositivo virtuale](storsimple-virtual-device-u2.md).
* Utilizzare il [servizio StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) per gestire il dispositivo StorSimple.

