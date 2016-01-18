<properties 
   pageTitle="Distribuire il dispositivo StorSimple Aggiornamento 1 | Microsoft Azure"
   description="Descrive i passaggi e le procedure consigliate per la distribuzione dell'aggiornamento 1 del servizio e del dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/05/2016"
   ms.author="alkohli" />

# Distribuire un dispositivo StorSimple locale (Aggiornamento 1)

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Update 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA Release](../articles/storsimple/storsimple-deployment-walkthrough.md)

## Panoramica

Benvenuti nella distribuzione del dispositivo Microsoft Azure StorSimple. Queste esercitazioni per la distribuzione si applicano all'aggiornamento 1.0 di StorSimple serie 8000. Questa serie di esercitazioni descrive come configurare i dispositivi StorSimple e fornisce un elenco di controllo preliminare all’installazione, i prerequisiti di configurazione e i passaggi di configurazione dettagliati.

Le informazioni contenute in queste esercitazioni prevedono che siano state esaminate le precauzioni di sicurezza e che il dispositivo StorSimple sia stato disimballato, installato e cablato. Se occorre eseguire tali attività, iniziare con la revisione di[precauzioni di sicurezza](storsimple-safety.md). A seconda del modello di dispositivo, è possibile decomprimere, montare su rack e cablare seguendo le istruzioni disponibili in:

- [Decomprimere, montare su rack e cablare il dispositivo 8100](storsimple-8100-hardware-installation.md)
- [Decomprimere, montare su rack e cablare il dispositivo 8600](storsimple-8600-hardware-installation.md)

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. Si consiglia di esaminare l'elenco di controllo di pre-installazione prima di iniziare. Il processo di distribuzione e configurazione può richiedere parecchio tempo.

> [AZURE.NOTE]Le informazioni sulla distribuzione di StorSimple pubblicate nel sito Web di Microsoft Azure si applicano solo ai dispositivi di StorSimple serie 8000. Per informazioni complete sui dispositivi serie 7000, vedere: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Per informazioni sulla distribuzione della serie 7000, vedere la [Guida introduttiva al sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

## Passaggi di distribuzione

Effettuare i seguenti passaggi necessari per configurare il dispositivo StorSimple e connetterlo al servizio StorSimple Manager: Oltre ai passaggi necessari sono disponibili passaggi facoltativi e procedure che possono essere necessarie durante la distribuzione. Le istruzioni dettagliate di distribuzione indicano quando è necessario eseguire ciascuno di questi passaggi facoltativi.


| Passaggio | Descrizione |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **PREREQUISITI** | Questi elementi dovranno essere completati in preparazione per la prossima distribuzione. |
| Elenco di controllo configurazione della distribuzione. | Utilizzare questo elenco di controllo per raccogliere e registrare informazioni prima e durante la distribuzione. |
| Prerequisiti di distribuzione. | Questi confermano che l'ambiente è pronto per la distribuzione. |
| | |
| **DISTRIBUZIONE PASSO PER PASSO** | Questi passaggi sono necessari per distribuire il dispositivo StorSimple nell'ambiente di produzione. |
| Passaggio 1: Creare un nuovo servizio. | Impostare Gestione cloud e archiviazione per il dispositivo StorSimple. Ignorare questo passaggio se si dispone di un servizio esistente per altri dispositivi StorSimple. |
| Passaggio 2: Ottenere la chiave di registrazione del servizio. | Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio di gestione. |
| Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple. | Connettere il dispositivo alla rete e registrarlo con Azure per completare l'installazione utilizzando il servizio di gestione. |
| Passaggio 4: Completare l'installazione minima del dispositivo</br>Facoltativo: aggiornare il dispositivo StorSimple. | Utilizzare il servizio di gestione per completare l'installazione del dispositivo e abilitarlo per fornire l'archiviazione. |
| Passaggio 5: Creare un contenitore di volumi. | Creare un contenitore di volumi di provisioning. Un contenitore di volumi dispone di account di archiviazione, larghezza di banda e impostazioni di crittografia per tutti i volumi in esso contenuti. |
| Passaggio 6: Creare un volume. | Eseguire il provisioning di volumi di archiviazione nel dispositivo StorSimple per i server. |
| Passaggio 7: Montare, inizializzare e formattare un volume. </br>Facoltativo: Configurare MPIO. | Connettere i server all'archiviazione iSCSI fornita dal dispositivo. Facoltativamente, è possibile configurare MPIO per assicurarsi che i server possano di tollerare errori di collegamento, rete e interfaccia. |
| Passaggio 8: Eseguire un backup. | Impostare criteri di backup per proteggere i dati |
| | |
| **ALTRE PROCEDURE** | Potrebbe essere necessario fare riferimento a queste procedure quando si distribuisce la soluzione. |
| Configurare un nuovo account di archiviazione per il servizio. | |
| Utilizzare PuTTY per connettersi alla console seriale del dispositivo. | |
| Ottenere il nome qualificato iSCSI di un host di Windows Server. | |
| Creare un backup manuale. | 


## Elenco di controllo configurazione della distribuzione

Il seguente elenco di controllo per la configurazione della distribuzione descrive le informazioni che è necessario raccogliere prima e mentre si configura il software sul dispositivo StorSimple. La preparazione di queste informazioni in anticipo consentirà di semplificare il processo di distribuzione del dispositivo StorSimple nell'ambiente. Utilizzare questo elenco di controllo per annotare inoltre i dettagli di configurazione quando si distribuisce il dispositivo.

| Fase | Parametro | Dettagli | Valori |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Cablare il dispositivo** | Accesso seriale | Configurazione iniziale del dispositivo | Sì/No |
| | | | |
| **Configurare e registrare il dispositivo** | Impostazioni di rete Data 0 | Indirizzo IP Data 0:</br>Subnet mask:</br>Gateway:</br>server DNS primario:</br>server NTP primario:</br>IP server proxy Web/FQDN (facoltativo):</br>porta proxy Web:| |
| | Password amministratore del dispositivo | La password deve avere una lunghezza compresa tra gli 8 e i 15 caratteri e deve contenere minuscole, maiuscole, numeri e caratteri speciali. | |
| | Password di Gestione snapshot StorSimple | La password deve avere una lunghezza compresa tra i 14 e i 15 caratteri e deve contenere caratteri minuscoli, maiuscoli, numerici e speciali.| |
| | Chiave di registrazione del servizio | Questa chiave viene generata dal portale di Azure classico. | |
| | Chiave DEK del servizio | Questa chiave viene creata quando il dispositivo è registrato con il servizio di gestione tramite Windows PowerShell per StorSimple. Copiare questo codice e salvarlo in un luogo sicuro.| |
| | | | |
| **Completare la configurazione minima del dispositivo** | Nome descrittivo del dispositivo | Si tratta di un nome descrittivo per il dispositivo. | |
| | Fuso orario | Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate. | |
| | Server DNS secondario | Si tratta di una configurazione necessaria. | |
| | Interfaccia di rete: controller IP fissi del controller Data 0 | Questi IP devono essere instradabili a Internet.</br>indirizzo IP fisso controller 0:</br>indirizzo IP fisso controller 1:|
| | | | |
| **Impostazioni interfaccia di rete aggiuntive** | Interfaccia di rete: Data 1</br>se iSCSI è abilitato, non configurare il Gateway. | Scopo: Cloud/ISCSI/non utilizzato</br>indirizzo IP:</br>Subnet mask:</br>Gateway:|
| | Interfaccia di rete: Data 2</br>se iSCSI è abilitato, non configurare il Gateway. | Scopo: Cloud/ISCSI/non utilizzato</br>indirizzo IP:</br>Subnet mask:</br>Gateway:|
| | Interfaccia di rete: Data 3</br>se iSCSI è abilitato, non configurare il Gateway. | Scopo: Cloud/ISCSI/non utilizzato</br>indirizzo IP:</br>Subnet mask:</br>Gateway:|
| | Interfaccia di rete: Data 4</br>se iSCSI è abilitato, non configurare il Gateway. | Scopo: Cloud/ISCSI/non utilizzato</br>indirizzo IP:</br>Subnet mask:</br>Gateway:|
| | Interfaccia di rete: Data 5</br>se iSCSI è abilitato, non configurare il Gateway. | Scopo: Cloud/ISCSI/non utilizzato</br>indirizzo IP:</br>Subnet mask:</br>Gateway:|
| | | | |
| **Creare un contenitore di volumi** | Nome del contenitore di volume: | Nome del contenitore | |
| | Account di archiviazione Azure: | Nome dell’account di archiviazione & chiave di accesso da associare al contenitore di volumi | |
| | Chiave di crittografia di archiviazione cloud: | Chiave di crittografia per l'archiviazione in ogni contenitore | |
| | | | |
| **Creare un volume** | Dettagli per ogni volume | Nome del volume: | |
| | | Dimensione: | |
| | | Tipo di utilizzo: | |
| | | Nome ACR: | |
| | | Criteri di backup predefinito: | |
| | | | |
| **Montare, inizializzare e formattare un volume** | Dettagli per ogni server host che si connette allo spazio di archiviazione | Nome del Server di Windows: | |
| | | Windows Server nome qualificato iSCSI: | |
| | | Nome di volume di Windows Server: | |
| | | Lettera di unità o punti di montaggio NTFS: | |

## Prerequisiti di distribuzione

Le sezioni seguenti illustrano i prerequisiti di configurazione per il servizio StorSimple Manager e per il dispositivo StorSimple.

### Per il servizio StorSimple Manager

Prima di iniziare, verificare che:

- Si dispone dell'account Microsoft con credenziali di accesso.

- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

- La sottoscrizione di Microsoft Azure è abilitata per il servizio StorSimple Manager. La sottoscrizione deve essere acquistata tramite il [Contratto Enterprise](http://azure.microsoft.com/pricing/enterprise-agreement/).

- È possibile accedere a un software di emulazione terminal, ad esempio PuTTY.

### Per il dispositivo nel centro dati

Prima di configurare il dispositivo, verificare che:

- Il dispositivo è completamente disimballato, montato su un rack e cablato per l’alimentazione, la rete e l’accesso seriale come descritto in:

	-  [Decomprimere, montare su rack e cablare il dispositivo 8100](storsimple-8100-hardware-installation.md)
	-  [Decomprimere, montare su rack e cablare il dispositivo 8600](storsimple-8600-hardware-installation.md)


### Per la rete nel datacenter

Prima di iniziare, verificare che:

- Per consentire il traffico iSCSI e cloud vengono aperte le porte nel firewall del data center come descritto in [Requisiti di rete per il dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).


## Distribuzione passo per passo

Utilizzare le seguenti istruzioni dettagliate per distribuire il dispositivo StorSimple nel datacenter.

## Passaggio 1: Creare un nuovo servizio

Un servizio StorSimple Manager può gestire più dispositivi StorSimple. Effettuare i passaggi seguenti per creare una nuova istanza del servizio StorSimple Manager.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT]Se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio, sarà necessario creare almeno un account di archiviazione dopo avere creato un servizio. Tale account di archiviazione verrà utilizzato in fase di creazione di un contenitore di volumi.
>
> * Se non è stato creato automaticamente un account di archiviazione, andare a [Configurare un nuovo account di archiviazione per il servizio](#configure-a-new-storage-account-for-the-service) per istruzioni dettagliate. 
> * Se è stata abilitata la creazione automatica di un account di archiviazione, andare al [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key).

## Passaggio 2: Ottenere la chiave di registrazione del servizio

Quando il servizio StorSimple Manager è attivo e in esecuzione, è necessario ottenere la chiave di registrazione. Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio.

Eseguire i passaggi seguenti nel portale di Azure classico.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple

Utilizzare Windows PowerShell per StorSimple per completare l'installazione iniziale del dispositivo StorSimple, come illustrato nella procedura seguente. Per completare questo passaggio sarà necessario utilizzare il software di emulazione terminal. Per ulteriori informazioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## Passaggio 4: Completare l'installazione minima del dispositivo

Per la configurazione minima del dispositivo del dispositivo StorSimple, è necessario:

- Installare il server DNS secondario.
- Abilitare iSCSI in almeno un'interfaccia di rete.
- Assegnare indirizzi IP fissi a entrambi i controller.

Eseguire i passaggi seguenti nel portale di Azure classico per completare l'installazione minima del dispositivo.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Passaggio 5: Creare un contenitore di volumi

Un contenitore di volumi dispone di account di archiviazione, larghezza di banda e impostazioni di crittografia per tutti i volumi in esso contenuti. Prima di iniziare il provisioning dei volumi nel dispositivo StorSimple è necessario creare un contenitore di volumi.

Eseguire i passaggi seguenti nel portale di Azure classico per creare un contenitore di volumi.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Passaggio 6: Creare un volume

Dopo aver creato un contenitore di volumi, è possibile eseguire il provisioning di un volume di archiviazione nel dispositivo StorSimple per i server. Eseguire i passaggi seguenti nel portale di Azure classico per creare un volume.

> [AZURE.IMPORTANT]StorSimple di Azure consente di creare solo volumi di thin provisioning. Non è possibile creare volumi con provisioning completo o parziale.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Passaggio 7: Montare, inizializzare e formattare un volume

I passaggi seguenti vengono eseguiti nell'host di Windows Server.


> [AZURE.IMPORTANT]

> - Per la disponibilità elevata della soluzione StorSimple, è consigliabile configurare MPIO nei server host (facoltativo) prima della configurazione iSCSI. Usare la funzionalità MPIO sui server host per assicurarsi che i server possano tollerare un errore di collegamento, rete o interfaccia.

> - Per istruzioni sull'installazione e sulla configurazione di MPIO e iSCSI sull’host Windows Server, vedere [Configurare MPIO per il dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Questi comprendono anche i passaggi per montare, inizializzare e formattare volumi StorSimple.

> - Per istruzioni sull'installazione e sulla configurazione di MPIO e iSCSI su un host Linux, vedere [Configurare MPIO per l’host Linux di StorSimple](storsimple-configure-mpio-on-linux.md).

Se si decide di non configurare MPIO, eseguire la procedura seguente per montare, inizializzare e formattare i volumi StorSimple su un host Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Passaggio 8: Eseguire un backup

I backup garantiscono la protezione temporizzata dei volumi e migliorano la recuperabilità riducendo al minimo i tempi di ripristino. È possibile eseguire due tipi di backup su un dispositivo StorSimple: snapshot locali e snapshot nel cloud. Ciascuno di questi tipi di backup può essere **Pianificato** o **Manuale**.

Eseguire i passaggi seguenti nel portale di Azure classico per creare un backup pianificato.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

È possibile eseguire un backup manuale in qualsiasi momento. Per le procedure, vedere [Creare un backup manuale](#create-a-manual-backup).

## Configurare un nuovo account di archiviazione per il servizio

Si tratta di un passaggio che è necessario eseguire solo se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio. Per creare un contenitore di volumi di StorSimple, è necessario un account di archiviazione di Microsoft Azure.

Se occorre creare un account di archiviazione di Azure in un'area diversa, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md) per istruzioni dettagliate.

Eseguire i passaggi seguenti nel portale di Azure classico, nella pagina del **servizio StorSimple Manager**.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## Utilizzare PuTTY per connettersi alla console seriale del dispositivo

Per connettersi a Windows PowerShell per StorSimple, è necessario utilizzare un software di emulazione terminal, ad esempio PuTTY. Quando si accede al dispositivo direttamente tramite la console seriale oppure aprendo una sessione telnet da un computer remoto, è possibile utilizzare PuTTY.

[AZURE.INCLUDE [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](../../includes/storsimple-use-putty.md)]


## Cercare e applicare gli aggiornamenti

L’aggiornamento del dispositivo può richiedere diverse ore. Eseguire i passaggi seguenti per la ricerca e l’applicazione degli aggiornamenti sul dispositivo.
<!--can take 1-4 hours-->

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### Per aggiornare il dispositivo

1.	Nella pagina **Avvio rapido** del dispositivo fare clic su **Dispositivi**. Selezionare il dispositivo fisico, fare clic su **Manutenzione** quindi fare clic su **Analisi aggiornamenti**.  

2.	Viene creato un processo che consente di cercare gli aggiornamenti disponibili. Se sono disponibili aggiornamenti, **Analisi aggiornamenti** diventa **Installa aggiornamenti**. Fare clic su **Installa aggiornamenti**.

3.	Verrà creato un processo di aggiornamento. Monitorare lo stato dell'aggiornamento passando a **Processi**.

	> [AZURE.NOTE]Quando viene avviato il processo di aggiornamento, immediatamente lo stato viene visualizzato al 50%. Lo stato passa quindi al 100% solo dopo che è stato completato il processo di aggiornamento. Non esiste alcuno stato in tempo reale per il processo di aggiornamento.

4.	Dopo aver completato l'aggiornamento del dispositivo, abilitare le interfacce di rete Data 2 e Data 3 se erano state disabilitate.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## Ottenere il nome qualificato iSCSI di un host di Windows Server

Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI (IQN) di un host di Windows che esegue Windows Server® 2012.

[AZURE.INCLUDE [Creazione di un backup manuale](../../includes/storsimple-get-iqn.md)]

## Creazione di un backup manuale

Eseguire i passaggi seguenti nel portale di Azure classico per creare un backup manuale su richiesta per un singolo volume nel dispositivo StorSimple.

[AZURE.INCLUDE [Creazione di un backup manuale](../../includes/storsimple-create-manual-backup.md)]

## Configurare MPIO

Multipath I/O (MPIO) è una funzionalità facoltativa e non è installata in Windows Server per impostazione predefinita. Deve essere installata come funzionalità tramite Server Manager. Per istruzioni sull'installazione di MPIO, vedere [Configurare MPIO per il dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

Per istruzioni sull'installazione di MPIO per un dispositivo StorSimple su un host Linux, vedere [Configurare MPIO per l’host Linux](storsimple-configure-mpio-on-linux.md).


> [AZURE.NOTE]La funzionalità MPIO non è supportata in un dispositivo virtuale StorSimple.



## Passaggi successivi

Configurare un [dispositivo virtuale](storsimple-virtual-device.md).

Utilizzare il [servizio StorSimple Manager](storsimple-manager-service-administration.md) per gestire il dispositivo StorSimple.
 

<!---HONumber=AcomDC_0107_2016-->