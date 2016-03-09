<properties 
   pageTitle="Distribuire un dispositivo StorSimple nel portale per enti pubblici | Microsoft Azure"
   description="Descrive i passaggi e le procedure consigliate per la distribuzione nel portale di Azure per enti pubblici del servizio e del dispositivo di StorSimple Aggiornamento 2."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/22/2016"
   ms.author="v-sharos" />

# Distribuire un dispositivo StorSimple locale nel portale di Azure Government (aggiornamento 2)

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## Panoramica

Benvenuti nella distribuzione del dispositivo Microsoft Azure StorSimple. Queste esercitazioni per la distribuzione si applicano a dispositivi StorSimple serie 8000 in cui viene eseguito l'aggiornamento 2 del software nel portale di Azure per enti pubblici. Questa serie di esercitazioni include un elenco di controllo della configurazione, un elenco di prerequisiti di configurazione e i passaggi di configurazione dettagliati per il dispositivo StorSimple.

Le informazioni contenute in queste esercitazioni prevedono che siano state esaminate le precauzioni di sicurezza e che il dispositivo StorSimple sia stato disimballato, installato e cablato. Se occorre eseguire tali attività, iniziare con la revisione di[precauzioni di sicurezza](storsimple-safety.md). Seguire le istruzioni specifiche per il dispositivo per disimballare, montare su rack e cablare il dispositivo.

- [Decomprimere, montare su rack e cablare il dispositivo 8100](storsimple-8100-hardware-installation.md)
- [Decomprimere, montare su rack e cablare il dispositivo 8600](storsimple-8600-hardware-installation.md)

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. Si consiglia di esaminare l'elenco di controllo di pre-installazione prima di iniziare. Il processo di distribuzione e configurazione può richiedere parecchio tempo.

> [AZURE.NOTE] Le informazioni sulla distribuzione di StorSimple pubblicate nel sito Web di Microsoft Azure si applicano solo ai dispositivi di StorSimple serie 8000. Per informazioni complete sui dispositivi serie 7000, vedere: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Per informazioni sulla distribuzione della serie 7000, vedere la [Guida introduttiva al sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

## Passaggi di distribuzione

Effettuare i seguenti passaggi necessari per configurare il dispositivo StorSimple e connetterlo al servizio StorSimple Manager: Oltre ai passaggi necessari sono disponibili passaggi facoltativi e procedure che possono essere necessarie durante la distribuzione. Le istruzioni dettagliate di distribuzione indicano quando è necessario eseguire ciascuno di questi passaggi facoltativi.


| Passaggio | Descrizione |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **PREREQUISITI** | Questi elementi dovranno essere completati in preparazione per la prossima distribuzione. |
|[Elenco di controllo configurazione della distribuzione](#deployment-configuration-checklist) | Utilizzare questo elenco di controllo per raccogliere e registrare informazioni prima e durante la distribuzione. |
| [Prerequisiti di distribuzione](#deployment-prerequsites) | Questi confermano che l'ambiente è pronto per la distribuzione. |
| | |
| **DISTRIBUZIONE PASSO PER PASSO** | Questi passaggi sono necessari per distribuire il dispositivo StorSimple nell'ambiente di produzione. |
| [Passaggio 1: Creare un nuovo servizio](#step-1-create-a-new-service) | Impostare Gestione cloud e archiviazione per il dispositivo StorSimple. *Ignorare questo passaggio se si dispone di un servizio esistente per altri dispositivi StorSimple*. |
| [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key) | Utilizzare questa chiave per registrare e connettere il dispositivo StorSimple con il servizio di gestione. |
| [Passaggio 3: configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](step 3-configure-and-register-the-device-through-windows-powershell-for-storsimple) | Connettere il dispositivo alla rete e registrarlo con Azure per completare l'installazione utilizzando il servizio di gestione. |
| [Passaggio 4: Completare l'installazione minima del dispositivo](#step-4-complete-the-minimum-device-setup) </br>Facoltativo: aggiornare il dispositivo StorSimple. | Utilizzare il servizio di gestione per completare l'installazione del dispositivo e abilitarlo per fornire l'archiviazione. |
| [Passaggio 5: Creare un contenitore di volumi](#step-5-create-a-volume-container) | Creare un contenitore di volumi di provisioning. Un contenitore di volumi dispone di account di archiviazione, larghezza di banda e impostazioni di crittografia per tutti i volumi in esso contenuti. |
| [Passaggio 6: Creare un volume](#step-6-create-a-volume) | Eseguire il provisioning di volumi di archiviazione nel dispositivo StorSimple per i server. |
| [Passaggio 7: Montare, inizializzare e formattare un volume](#step-7-mount-initialize-and-format-a-volume) </br> Facoltativo: Configurare MPIO. | Connettere i server all'archiviazione iSCSI fornita dal dispositivo. Facoltativamente, è possibile configurare MPIO per assicurarsi che i server possano tollerare errori di collegamento, rete e interfaccia. |
| [Passaggio 8: Eseguire un backup](#step-8-take-a-backup) | Impostare criteri di backup per proteggere i dati |
| | |
| **ALTRE PROCEDURE** | Potrebbe essere necessario fare riferimento a queste procedure quando si distribuisce la soluzione. |
| [Configurare un nuovo account di archiviazione per il servizio](#configure-a-new-storage-account-for-the-service) | |
| [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console) | |
| [Cercare e applicare gli aggiornamenti](#scan-for-and-apply-updates) | |
| [Ottenere il nome qualificato iSCSI di un host di Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Creazione di un backup manuale](#create-a-manual-backup) | 
| [Configurare MPIO](#configure-mpio) |

## Elenco di controllo configurazione della distribuzione

Prima di distribuire il dispositivo StorSimple, è necessario raccogliere informazioni per configurare il software sul dispositivo. La preparazione di queste informazioni in anticipo consentirà di semplificare il processo di distribuzione del dispositivo StorSimple nell'ambiente. Scaricare e utilizzare questo elenco di controllo per annotare i dettagli di configurazione quando si distribuisce il dispositivo.

[Scaricare l'elenco di controllo configurazione della distribuzione StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)

## Prerequisiti di distribuzione

Le sezioni seguenti illustrano i prerequisiti di configurazione per il servizio StorSimple Manager e per il dispositivo StorSimple.

### Per il servizio StorSimple Manager

Prima di iniziare, verificare che:

- Si dispone dell'account Microsoft con credenziali di accesso.

- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

- La sottoscrizione di Microsoft Azure è abilitata per il servizio StorSimple Manager. La sottoscrizione deve essere acquistata tramite il [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

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

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT] Se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio, sarà necessario creare almeno un account di archiviazione dopo avere creato un servizio. Tale account di archiviazione verrà utilizzato in fase di creazione di un contenitore di volumi.
>
> * Se non è stato creato automaticamente un account di archiviazione, andare a [Configurare un nuovo account di archiviazione per il servizio](#configure-a-new-storage-account-for-the-service) per istruzioni dettagliate. 
> * Se è stata abilitata la creazione automatica di un account di archiviazione, andare al [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key).

## Passaggio 2: Ottenere la chiave di registrazione del servizio

Quando il servizio StorSimple Manager è attivo e in esecuzione, è necessario ottenere la chiave di registrazione. Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple al servizio.

Eseguire i passaggi seguenti nel portale per enti pubblici.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple

Utilizzare Windows PowerShell per StorSimple per completare l'installazione iniziale del dispositivo StorSimple, come illustrato nella procedura seguente. Per completare questo passaggio sarà necessario utilizzare il software di emulazione terminal. Per ulteriori informazioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov-u2.md)]

## Passaggio 4: Completare l'installazione minima del dispositivo

Per la configurazione minima del dispositivo del dispositivo StorSimple, è necessario:

- Installare il server DNS secondario.
- Abilitare iSCSI in almeno un'interfaccia di rete.
- Assegnare indirizzi IP fissi a entrambi i controller.

Eseguire i passaggi seguenti nel portale per enti pubblici per completare l'installazione minima del dispositivo.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Passaggio 5: Creare un contenitore di volumi

Un contenitore di volumi dispone di account di archiviazione, larghezza di banda e impostazioni di crittografia per tutti i volumi in esso contenuti. Prima di iniziare il provisioning dei volumi nel dispositivo StorSimple è necessario creare un contenitore di volumi.

Eseguire i passaggi seguenti nel portale per enti pubblici per creare un contenitore di volumi.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Passaggio 6: Creare un volume

Dopo aver creato un contenitore di volumi, è possibile eseguire il provisioning di un volume di archiviazione nel dispositivo StorSimple per i server. Eseguire i passaggi seguenti nel portale per enti pubblici per creare un volume.

> [AZURE.IMPORTANT] StorSimple di Azure consente di creare solo volumi di thin provisioning. Non è possibile creare volumi con provisioning completo o parziale in un sistema StorSimple di Azure.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## Passaggio 7: Montare, inizializzare e formattare un volume

Eseguire questi passaggi nell'host di Windows Server.

> [AZURE.IMPORTANT]

> - Per la disponibilità elevata della soluzione StorSimple, è consigliabile configurare MPIO nei server host (facoltativo) prima della configurazione iSCSI. Usare la funzionalità MPIO sui server host per assicurarsi che i server possano tollerare un errore di collegamento, rete o interfaccia.

> - Per istruzioni sull'installazione e sulla configurazione di MPIO e iSCSI sull’host Windows Server, vedere [Configurare MPIO per il dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Questi comprendono anche i passaggi per montare, inizializzare e formattare volumi StorSimple.

> - Per istruzioni sull'installazione e sulla configurazione di MPIO e iSCSI su un host Linux, vedere [Configurare MPIO per l’host Linux di StorSimple](storsimple-configure-mpio-on-linux.md).

Se si decide di non configurare MPIO, eseguire la procedura seguente per montare, inizializzare e formattare i volumi StorSimple su un host Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Passaggio 8: Eseguire un backup

I backup garantiscono la protezione temporizzata dei volumi e migliorano la recuperabilità riducendo al minimo i tempi di ripristino. È possibile eseguire due tipi di backup su un dispositivo StorSimple: snapshot locali e snapshot nel cloud. Ciascuno di questi tipi di backup può essere **Pianificato** o **Manuale**.

Eseguire i passaggi seguenti nel portale per enti pubblici per creare un backup pianificato.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

È possibile eseguire un backup manuale in qualsiasi momento. Per le procedure, vedere [Creare un backup manuale](#create-a-manual-backup).

## Configurare un nuovo account di archiviazione per il servizio

Si tratta di un passaggio che è necessario eseguire solo se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio. Per creare un contenitore di volumi di StorSimple, è necessario un account di archiviazione di Microsoft Azure.

Se occorre creare un account di archiviazione di Azure in un'area diversa, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md) per istruzioni dettagliate.

Eseguire i passaggi seguenti nel portale di gestione, nella pagina del **servizio StorSimple Manager**.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## Utilizzare PuTTY per connettersi alla console seriale del dispositivo

Per connettersi a Windows PowerShell per StorSimple, è necessario utilizzare un software di emulazione terminal, ad esempio PuTTY. Quando si accede al dispositivo direttamente tramite la console seriale oppure aprendo una sessione telnet da un computer remoto, è possibile utilizzare PuTTY.

[AZURE.INCLUDE [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](../../includes/storsimple-use-putty.md)]

## Cercare e applicare gli aggiornamenti

L’aggiornamento del dispositivo può richiedere diverse ore. Eseguire i passaggi seguenti per la ricerca e l’applicazione degli aggiornamenti sul dispositivo.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### Per aggiornare il dispositivo

1.	Nella pagina **Avvio rapido** del dispositivo fare clic su **Dispositivi**. Selezionare il dispositivo fisico, fare clic su **Manutenzione** quindi fare clic su **Analisi aggiornamenti**.  
2.	Viene creato un processo che consente di cercare gli aggiornamenti disponibili. Se sono disponibili aggiornamenti, **Analisi aggiornamenti** diventa **Installa aggiornamenti**. Fare clic su **Installa aggiornamenti**. 
3.	Verrà creato un processo di aggiornamento. Monitorare lo stato dell'aggiornamento passando a **Processi**.

	> [AZURE.NOTE] Quando viene avviato il processo di aggiornamento, immediatamente lo stato viene visualizzato al 50%. Lo stato passa quindi al 100% solo dopo che è stato completato il processo di aggiornamento. Non esiste alcuno stato in tempo reale per il processo di aggiornamento.

4.	Dopo aver completato l'aggiornamento del dispositivo, abilitare le interfacce di rete Data 2 e Data 3 se erano state disabilitate.

## Ottenere il nome qualificato iSCSI di un host di Windows Server

Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI (IQN) di un host di Windows che esegue Windows Server® 2012.

[AZURE.INCLUDE [Creazione di un backup manuale](../../includes/storsimple-get-iqn.md)]

## Creazione di un backup manuale

Eseguire i passaggi seguenti nel portale per enti pubblici per creare un backup manuale su richiesta di un singolo volume nel dispositivo StorSimple.

[AZURE.INCLUDE [Creazione di un backup manuale](../../includes/storsimple-create-manual-backup-gov.md)]

## Configurare MPIO

Multipath I/O (MPIO) è una funzionalità facoltativa e non è installata in Windows Server per impostazione predefinita. Deve essere installata come funzionalità tramite Server Manager. Per istruzioni sull'installazione di MPIO, vedere [Configurare MPIO per il dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

Per istruzioni sull'installazione di MPIO per un dispositivo StorSimple su un host Linux, vedere [Configurare MPIO per l’host Linux](storsimple-configure-mpio-on-linux.md).

> [AZURE.NOTE] La funzionalità MPIO non è supportata in un dispositivo virtuale StorSimple.

## Passaggi successivi

- Configurare un [dispositivo virtuale](storsimple-virtual-device.md).

- Utilizzare il [servizio StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) per gestire il dispositivo StorSimple.
 

<!------HONumber=AcomDC_0224_2016-->