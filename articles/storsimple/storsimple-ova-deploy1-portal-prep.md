<properties
   pageTitle="Distribuire StorSimple Virtual Array 1: preparazione del portale"
   description="La prima esercitazione per la distribuzione dell'array virtuale StorSimple include la preparazione del portale"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# Distribuire StorSimple Virtual Array: preparare il portale

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## Panoramica

Le informazioni contenute in questo articolo si applicano solo a Microsoft Azure StorSimple Virtual Array (noto anche come dispositivo virtuale locale StorSimple o dispositivo virtuale StorSimple) che esegue la versione di disponibilità generale (GA) di marzo 2016. Questo è il primo articolo della serie di esercitazioni per la distribuzione necessarie per la distribuzione completa dell'array virtuale come file server o server iSCSI. Questo articolo illustra la preparazione necessaria per creare e configurare il servizio StorSimple Manager prima di eseguire il provisioning di un array virtuale. Questo articolo contiene anche i collegamenti a un elenco di controllo configurazione della distribuzione, oltre ai prerequisiti di configurazione.

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. Si consiglia di consultare l'elenco di controllo configurazione della distribuzione prima di iniziare. La preparazione del portale richiede meno di 10 minuti.

Le informazioni pubblicate in questo articolo si applicano alla distribuzione di StorSimple Virtual Arrays nel portale di Azure classico nonché nel cloud di Microsoft Azure per enti pubblici.

### Introduzione

Il flusso di lavoro di distribuzione consiste nella preparazione del portale, nel provisioning di un array virtuale nell'ambiente virtualizzato e nel completamento dell'installazione. Per iniziare la distribuzione di StorSimple Virtual Array come file server o server iSCSI, è necessario fare riferimento alle seguenti risorse catalogate (articoli e video).

#### Articoli sulla distribuzione

Consultare gli articoli seguenti nella sequenza indicata per distribuire StorSimple Virtual Array.

| **#** | **In questo passaggio** | **Contenuto dell'esercitazione** | **Usare questi documenti**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1\. | **Configurare il portale di Azure classico** | Creare e configurare il servizio StorSimple Manager prima di eseguire il provisioning di un dispositivo virtuale StorSimple. |[Preparare il portale](storsimple-ova-deploy1-portal-prep.md)|
|2\. | **Eseguire il provisioning dell'array virtuale** | Per Hyper-V, eseguire il provisioning e connettersi al dispositivo virtuale StorSimple su un sistema host che esegue Hyper-V in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. <br></br> <br></br> Per VMware, eseguire il provisioning e connettersi a un dispositivo virtuale locale StorSimple in un sistema host che esegue VMware ESXi 5.5 e versioni successive.<br></br>| [Eseguire il provisioning di un array virtuale in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Eseguire il provisioning di un array virtuale in VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3\. | **Configurare l'array virtuale** | Per il file server, eseguire l'installazione iniziale, registrare il file server StorSimple e completare l'installazione del dispositivo. È quindi possibile eseguire il provisioning delle condivisioni SMB. <br></br> <br></br> Per il server iSCSI, eseguire l'installazione iniziale, registrare il server iSCSI StorSimple e completare l'installazione del dispositivo. È quindi possibile eseguire il provisioning dei volumi iSCSI.| [Configurare l'array virtuale come file server](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurare l'array virtuale come server iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### Video sulla distribuzione

| **Per eseguire questo passaggio** | **Guardare questo video**|
|----------------|-------------|
| Istruzioni dettagliate per iniziare a usare StorSimple Virtual Array. | [Introduzione a StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Istruzioni dettagliate per eseguire il provisioning di StorSimple Virtual Array in Hyper-V.|[Creare uno StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|Istruzioni dettagliate per configurare e registrare StorSimple Virtual Array|[Configurare uno StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|Istruzioni dettagliate per creare condivisioni, eseguire il backup di condivisioni e il ripristino dei dati in uno StorSimple Virtual Array configurato come file server|[Usare StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Istruzioni dettagliate per failover e ripristino di emergenza di StorSimple Virtual Array|[Ripristino di emergenza di StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

È ora possibile iniziare a configurare il portale di Azure classico.

## Elenco di controllo configurazione

L'elenco di controllo configurazione descrive le informazioni che è necessario raccogliere prima di configurare il software nel dispositivo StorSimple. La preparazione di queste informazioni in anticipo consentirà di semplificare il processo di distribuzione del dispositivo StorSimple nell'ambiente. In base alla distribuzione del dispositivo virtuale StorSimple come file server o server iSCSI, è necessario disporre di uno dei seguenti elenchi di controllo.

-   Scaricare l'[elenco di controllo configurazione StorSimple Virtual Array come file server](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Scaricare l'[elenco di controllo configurazione StorSimple Virtual Array come server iSCSI](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## Prerequisiti

Ecco i prerequisiti di configurazione per il servizio StorSimple Manager, il dispositivo virtuale StorSimple e la rete del data center.

### Per il servizio StorSimple Manager

Prima di iniziare, verificare che:

-   Si dispone dell'account Microsoft con credenziali di accesso.

-   Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

-   La sottoscrizione di Microsoft Azure deve essere abilitata per il servizio StorSimple Manager.

### Per il dispositivo virtuale StorSimple

Prima di distribuire un dispositivo virtuale, è necessario:

-   Avere accesso a un sistema host che esegue Hyper-V in Windows Server 2008 R2 o versioni successive o VMware (ESXi 5.5 o versioni successive) da poter usare per il provisioning di un dispositivo.

-   Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning del dispositivo virtuale:

	-   Un minimo di 4 memorie centrali.

	-   Almeno 8 GB di RAM.

	-   Un'interfaccia di rete.

	-   Un disco virtuale da 500 GB per i dati di sistema.

### Per la rete del data center

Prima di iniziare, verificare che:

-   La rete nel data center viene configurata in base ai requisiti di rete per il dispositivo StorSimple. Per ulteriori informazioni, vedere l'articolo relativo ai [requisiti di sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md).

-   Il dispositivo virtuale StorSimple dispone di una larghezza di banda Internet dedicata a 5 Mbps (o superiore) sempre disponibile. La larghezza di banda non deve essere condivisa con altre applicazioni.

## Preparazione dettagliata

Seguire le istruzioni dettagliate riportate sotto per preparare il portale per il servizio StorSimple Manager.

## Passaggio 1: Creare un nuovo servizio

Una singola istanza del servizio StorSimple Manager può gestire più dispositivi di StorSimple 1200. Effettuare i passaggi seguenti per creare una nuova istanza del servizio StorSimple Manager. Se si dispone di un servizio StorSimple Manager esistente per gestire i dispositivi 1200, ignorare questo passaggio e andare a [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> Se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio, sarà necessario creare almeno un account di archiviazione dopo avere creato un servizio.
>

> - Se non è stato creato automaticamente un account di archiviazione, andare a [Configurare un nuovo account di archiviazione per il servizio](#optional-step-configure-a-new-storage-account-for-the-service) per istruzioni dettagliate.
>

> - Se è stata abilitata la creazione automatica di un account di archiviazione, andare al [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key).


## Passaggio 2: Ottenere la chiave di registrazione del servizio


Quando il servizio StorSimple Manager è attivo e in esecuzione, è necessario ottenere la chiave di registrazione. Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio.

Eseguire i passaggi seguenti nel [portale di Azure classico](https://manage.windowsazure.com/).


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> La chiave di registrazione del servizio viene usata per registrare tutti i dispositivi StorSimple Manager che è necessario registrare con il servizio StorSimple Manager personale.

## Passaggio 3: Scaricare l'immagine del dispositivo virtuale

Una volta in possesso della chiave di registrazione, è necessario scaricare l'immagine del dispositivo virtuale appropriato per eseguire il provisioning di un dispositivo virtuale nel sistema host. Le immagini dei dispositivi virtuali sono specifiche del sistema operativo e si possono scaricare dalla pagina Avvio rapido nel portale di Azure classico.

> [AZURE.IMPORTANT] Il software in esecuzione in StorSimple Virtual Array può essere usato solo in combinazione con il servizio Storsimple Manager.


Eseguire i passaggi seguenti nel [portale di Azure classico](https://manage.windowsazure.com/).

#### Per ottenere l'immagine del dispositivo virtuale

1.  Nella pagina del **servizio StorSimple Manager**, fare clic sul servizio creato. Verrà visualizzata la pagina **Avvio rapido**. È possibile fare clic sull'icona di avvio rapido ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) per accedere alla pagina **Avvio rapido** in qualsiasi momento.

1.  Fare clic sul collegamento corrispondente all'immagine che si desidera scaricare dall'Area download Microsoft. I file di immagine sono pari a circa 4,8 GB.

	-   VHDX per Hyper-V in Windows Server 2012 e versioni successive

	-   VHD per Hyper-V in Windows Server 2008 R2 e versioni successive

	-   VMDK per VMWare ESXi 5.5 e versioni successive

2.  Scaricare e decomprimere il file in un'unità locale, prendendo nota della posizione del file decompresso.

![icona video](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Video disponibile**

Guardare il video per istruzioni dettagliate su come iniziare a usare StorSimple Virtual Array.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## Passaggio facoltativo: configurare un nuovo account di archiviazione per il servizio

Si tratta di un passaggio facoltativo che è necessario eseguire solo se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio.

Se è necessario creare un account di archiviazione di Azure in un'area diversa, vedere [Creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account) per istruzioni dettagliate.

Eseguire i passaggi seguenti nel [portale di Azure classico](https://manage.windowsazure.com/), nella pagina del servizio StorSimple Manager, per aggiungere un account di archiviazione di Microsoft Azure esistente.

#### Per aggiungere un account di archiviazione

1.  Nella pagina di destinazione del servizio StorSimple Manager, selezionare il servizio e fare doppio clic su di esso. Verrà visualizzata la pagina **Avvio rapido**. Selezionare la pagina **Configura**.

2.  Fare clic su **Aggiungi/modifica account di archiviazione**. Nella finestra di dialogo **Aggiungi/modifica account di archiviazione** effettuare le seguenti operazioni:

	1.  Fare clic su **Aggiungi nuovo**.

	1.  Fornire un nome per l'account di archiviazione.

	1.  Fornire la **chiave di accesso** per l’account di archiviazione di Microsoft Azure.

	1.  Selezionare **Abilita modalità SSL** per creare un canale sicuro per la comunicazione di rete tra il dispositivo e il cloud. Deselezionare la casella di controllo **Abilita modalità SSL** solo se si opera all'interno di un cloud privato.

	1.  Fare clic sull’icona del segno di spunta ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Quando la creazione dell’account di archiviazione sarà completata si riceverà una notifica.

		![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  L'account di archiviazione appena creato verrà visualizzato nella pagina **Configura** in **Account di archiviazione**. Fare clic su **Salva** per salvare l’account di archiviazione appena creato. Fare clic su **OK** quando viene richiesto di confermare.


## Passaggio successivo

Il passaggio successivo riguarda il provisioning di una macchina virtuale per il dispositivo virtuale StorSimple. A seconda del sistema operativo host, vedere le istruzioni dettagliate in:

-   [Eseguire il provisioning di StorSimple Virtual Array in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Eseguire il provisioning di StorSimple Virtual Array in VMware](storsimple-ova-deploy2-provision-vmware.md)

<!---HONumber=AcomDC_0601_2016-->