<properties 
   pageTitle="Distribuire un dispositivo StorSimple locale nel portale di Azure Government"
   description="Passaggi e le procedure consigliate per la distribuzione del dispositivo nel portale di Azure per enti pubblici e dell'aggiornamento 1 del servizio e del dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/12/2015"
   ms.author="v-sharos" />

# Distribuire un dispositivo StorSimple locale nel portale di Azure Government

## Panoramica

Benvenuti nella distribuzione del dispositivo Microsoft Azure StorSimple.

Queste esercitazioni per la distribuzione si applicano a dispositivi StorSimple serie 8000 in cui viene eseguito l'aggiornamento 1 del software nel portale di Azure per enti pubblici.

In questa serie di esercitazioni viene descritto come configurare i dispositivi StorSimple e viene fornito un elenco di controllo preliminare all'installazione, i prerequisiti di configurazione e i passaggi di configurazione dettagliati.

> [AZURE.NOTE]Le informazioni sulla distribuzione di StorSimple pubblicate nel sito Web di Microsoft Azure e in MSDN Library si applicano solo ai dispositivi StorSimple serie 8000. Per informazioni complete sui dispositivi serie 7000, vedere: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Per informazioni sulla distribuzione della serie 7000, vedere la [Guida introduttiva al sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

Le informazioni contenute in queste esercitazioni prevedono che siano state esaminate le precauzioni di sicurezza e l'elenco di controllo di configurazione e che il dispositivo StorSimple sia stato disimballato, installato e cablato. Se queste operazioni non sono state ancora eseguite, consultare [Precauzioni di sicurezza](https://msdn.microsoft.com/library/azure/dn772366.aspx), [Elenco di controllo configurazione](https://msdn.microsoft.com/library/azure/dn757787.aspx) e [Installazione hardware del dispositivo](https://msdn.microsoft.com/library/azure/dn772375.aspx).

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. Si consiglia di esaminare l'elenco di controllo di pre-installazione prima di iniziare. Il processo di distribuzione e configurazione può richiedere parecchio tempo.

## Elenco di controllo di pre-installazione

L’elenco di controllo pre-installazione seguente descrive le informazioni che è necessario raccogliere prima di configurare il software sul dispositivo StorSimple. La preparazione di queste informazioni in anticipo consentirà di semplificare il processo di distribuzione del dispositivo StorSimple nell'ambiente.

| | Requisiti | Dettagli | Valori |
|---| --------------------- | ---------------------- | ------------- |
| 1 | Impostazioni di rete <ol><li>Interfacce di rete, 4x1 GbE, 2x10 GbE</li><li>IP controller fisso</li><li>Subnet mask</li><li>Gateway</li></ol> | Totale indirizzi IP richiesti: 8 <ol><li>Uno per ogni interfaccia di rete abilitata, totale 6</li><li>Uno per ogni controller, totale 2, richiesti per connettersi a Internet agli aggiornamenti del servizio</li><li>Uno per ogni indirizzo IP</li><li>Uno per ogni dispositivo</li></ol> | |
| 2 | Accesso seriale | Configurazione iniziale del dispositivo | Sì/No |
| 3 | Indirizzi IP del server DNS | Necessari per connettersi a Microsoft Azure: 2 totali necessari per la disponibilità elevata | |
| 4 | Indirizzi IP del server NTP | Necessari per sincronizzare l'ora con Azure: 1 necessario, 1 facoltativo | |
| 5 | Server proxy (facoltativo) | Indirizzo IP/nome di dominio completo del server proxy, porta da utilizzare | |
| 6 | Account di archiviazione di Azure | Credenziali di accesso quali nome dell'account e chiave di accesso per ogni account di archiviazione | |
| 7 | Chiave di crittografia di archiviazione cloud (consigliata) | Per ogni contenitore di volumi | |
| 8 | Nome qualificato iSCSI dell'host | Per ogni host | |

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

- Il dispositivo è stato completamente estratto dalla confezione come descritto in [Disimballare il dispositivo 8100](https://msdn.microsoft.com/library/azure/dn772327.aspx) o [Disimballare il dispositivo 8600](https://msdn.microsoft.com/library/azure/dn772418.aspx).

- Il dispositivo è stato montato su un rack come descritto in [Montare su rack il dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757749.aspx) o [Montare su rack il dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757745.aspx).

- Il dispositivo è completamente cablato per l’alimentazione, la rete e l’accesso seriale come descritto in [Cablare il dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) o [Cablare il dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

- Per consentire il traffico iSCSI e cloud vengono aperte le porte nel firewall del data center come descritto in [Requisiti di rete per il dispositivo StorSimple](https://msdn.microsoft.com/library/dn772371.aspx).

## Passaggi di distribuzione

Effettuare i seguenti passaggi necessari per configurare il dispositivo StorSimple e connettersi al servizio StorSimple Manager:

- Passaggio 1: Creare un nuovo servizio 
- Passaggio 2: Ottenere la chiave di registrazione del servizio
- Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple 
- Passaggio 4: Completare l'installazione minima del dispositivo
- Passaggio 5: Creare un contenitore di volumi 
- Passaggio 6: Creare un volume
- Passaggio 7: Montare, inizializzare e formattare un volume 
- Passaggio 8: Eseguire un backup

Oltre ai passaggi necessari esistono alcuni passaggi facoltativi che potrebbe essere necessario completare durante la distribuzione della soluzione. Tali passaggi facoltativi descrivono come:

- Configurare un nuovo account di archiviazione per il servizio
- Utilizzare PuTTY per connettersi alla console seriale del dispositivo
- Ottenere il nome qualificato iSCSI di un host di Windows Server
- Creazione di un backup manuale
- Configurare MPIO

Le istruzioni dettagliate di distribuzione indicano quando è necessario eseguire ciascuno di questi passaggi facoltativi.

## Passaggio 1: Creare un nuovo servizio

Un servizio StorSimple Manager può gestire più dispositivi StorSimple. Effettuare i passaggi seguenti per creare una nuova istanza del servizio StorSimple Manager.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT]Se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio, sarà necessario creare almeno un account di archiviazione dopo avere creato un servizio. Tale account di archiviazione verrà utilizzato in fase di creazione di un contenitore di volumi.
>
> * Se non è stato creato automaticamente un account di archiviazione, andare a [Configurare un nuovo account di archiviazione per il servizio](#configure-a-new-storage-account-for-the-service) per istruzioni dettagliate. 
> * Se è stata abilitata la creazione automatica di un account di archiviazione, andare al [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2:-get-the-service-registration-key).

## Passaggio 2: Ottenere la chiave di registrazione del servizio

Quando il servizio StorSimple Manager è attivo e in esecuzione, è necessario ottenere la chiave di registrazione. Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio.

Eseguire i passaggi seguenti nel portale per enti pubblici.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple

Utilizzare Windows PowerShell per StorSimple per completare l'installazione iniziale del dispositivo StorSimple, come illustrato nella procedura seguente. Per completare questo passaggio sarà necessario utilizzare il software di emulazione terminal. Per ulteriori informazioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

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

> [AZURE.IMPORTANT]StorSimple di Azure consente di creare solo volumi di thin provisioning. Non è possibile creare volumi con provisioning completo o parziale in un sistema StorSimple di Azure.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Passaggio 7: Montare, inizializzare e formattare un volume

Eseguire questi passaggi nell'host di Windows Server.

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

## Ottenere il nome qualificato iSCSI di un host di Windows Server

Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI (IQN) di un host di Windows che esegue Windows Server® 2012.

[AZURE.INCLUDE [Creazione di un backup manuale](../../includes/storsimple-get-iqn.md)]

## Creazione di un backup manuale

Eseguire i passaggi seguenti nel portale per enti pubblici per creare un backup manuale su richiesta di un singolo volume nel dispositivo StorSimple.

[AZURE.INCLUDE [Creazione di un backup manuale](../../includes/storsimple-create-manual-backup-gov.md)]

## Configurare MPIO

Multipath I/O (MPIO) è una funzionalità facoltativa e non è installata in Windows Server per impostazione predefinita. Deve essere installata come funzionalità tramite Server Manager.

> [AZURE.NOTE]La funzionalità MPIO non è supportata in un dispositivo virtuale StorSimple.

Per istruzioni sull'installazione di MPIO, vedere [Configurare MPIO per il dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

## Passaggi successivi

Configurare un [dispositivo virtuale](storsimple-virtual-device.md).

Utilizzare il [servizio StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) per gestire il dispositivo StorSimple.
 

<!---HONumber=July15_HO2-->