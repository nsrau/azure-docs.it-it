<properties
   pageTitle="Distribuire StorSimple Virtual Array: eseguire il provisioning in Hyper-V"
   description="Questa seconda esercitazione sulla distribuzione di StorSimple Virtual Array implica il provisioning di un dispositivo virtuale in Hyper-V."
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
   ms.date="07/14/2016"
   ms.author="alkohli"/>

# Distribuire StorSimple Virtual Array: eseguire il provisioning di un array virtuale in Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## Panoramica

Questa esercitazione sul provisioning si applica solo a Microsoft Azure StorSimple Virtual Array (noti anche come dispositivi virtuali locali StorSimple o dispositivi virtuali StorSimple) che eseguono la versione di disponibilità generale (GA) di marzo 2016. Questa esercitazione illustra come eseguire il provisioning di StorSimple Virtual Array in un sistema host che esegue Hyper-V in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. Questo articolo si applica alla distribuzione di StorSimple Virtual Arrays nel portale di Azure classico nonché nel cloud di Microsoft Azure per enti pubblici.

È necessario disporre dei privilegi di amministratore per eseguire il provisioning e configurare un dispositivo virtuale. Il tempo previsto per il completamento di provisioning e installazione iniziale è di circa 10 minuti.


## Prerequisiti di provisioning

Vengono illustrati i prerequisiti per l'esecuzione del provisioning di un dispositivo virtuale in un sistema host che esegue Hyper-V in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

### Per il servizio StorSimple Manager

Prima di iniziare, verificare che:

-   Tutti i passaggi sulla [preparazione del portale per StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md) siano stati completati.

-   L'immagine del dispositivo virtuale per Hyper-V è stata scaricata dal portale di Azure. Per ulteriori informazioni, vedere [Passaggio 3: Scaricare l'immagine del dispositivo virtuale](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

	> [AZURE.IMPORTANT] Il software in esecuzione in StorSimple Virtual Array può essere usato solo in combinazione con il servizio Storsimple Manager.

### Per il dispositivo virtuale StorSimple

Prima di distribuire un dispositivo virtuale, è necessario:

-   Avere accesso a un sistema host che esegue Hyper-V in Windows Server 2008 R2 o versioni successive da poter usare per il provisioning di un dispositivo.

-   Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning del dispositivo virtuale:

	-   Un minimo di 4 memorie centrali.

	-   Almeno 8 GB di RAM.

	-   Un'interfaccia di rete.

	-   Un disco virtuale da 500 GB per i dati di sistema.

### Per la rete nel datacenter

Prima di iniziare, rivedere i requisiti di rete per la distribuzione di un dispositivo virtuale StorSimple e la corretta configurazione della rete del data center. Per altre informazioni, vedere [Requisiti di sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md#networking-requirements).

## Provisioning passo per passo

Per eseguire il provisioning e connettersi a un dispositivo virtuale, è necessario eseguire i passaggi seguenti:

1.  Verificare che il sistema host disponga di risorse sufficienti a soddisfare i requisiti minimi del dispositivo virtuale.

2.  Eseguire il provisioning di un dispositivo virtuale in hypervisor.

3.  Avviare il dispositivo virtuale e ottenere l'indirizzo IP.

Ciascuno di questi passaggi viene spiegato nelle sezioni seguenti.

## Passaggio 1: Verificare che il sistema host soddisfi i requisiti minimi del dispositivo virtuale

Per creare un dispositivo virtuale, è necessario:

-   Ruolo di Hyper-V installato in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.

-   Console di gestione di Hyper-V di Microsoft in un client Microsoft Windows connesso all'host.

È necessario verificare che l'hardware sottostante (sistema host) in cui si sta creando il dispositivo virtuale sia in grado di dedicare le risorse seguenti al dispositivo virtuale:

- Un minimo di 4 memorie centrali.
- Almeno 8 GB di RAM.
- Un'interfaccia di rete.
- Un disco virtuale da 500 GB per i dati di sistema.

## Passaggio 2: Eseguire il provisioning di un dispositivo virtuale in hypervisor

Eseguire i passaggi seguenti per il provisioning di un dispositivo in hypervisor.

#### Per eseguire il provisioning di un dispositivo virtuale

1.  Nell'host di Windows Server copiare l'immagine del dispositivo virtuale in un'unità locale. Si tratta dell'immagine (VHD o VHDX) scaricata tramite il portale di Azure. Prendere nota della posizione in cui è stata copiata l'immagine da usare più avanti nella procedura.

2.  Aprire **Server Manager**. Nell'angolo in alto a destra fare clic su **Strumenti** e selezionare **Console di gestione di Hyper-V**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

	Se si esegue Windows Server 2008 R2, aprire la Console di gestione di Hyper-V. In Server Manager fare clic su **Ruoli > Hyper-V > Console di gestione di Hyper-V**.

1.  Nella **Console di gestione di Hyper-V**, nel riquadro dell'ambito, fare clic con il pulsante destro del mouse sul nodo del sistema per aprire il menu di scelta rapida e quindi fare clic su **Nuovo** > **Macchina virtuale**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Nella pagina **Prima di iniziare** della Creazione guidata macchina virtuale fare clic su **Avanti**

1.  Nella pagina **Impostazione nome e percorso** fornire un **nome** per il dispositivo virtuale. Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Nella pagina **Impostazione generazione** scegliere il tipo di immagine del dispositivo e fare clic su **Avanti**. Se si usa Windows Server 2008 R2, questa pagina non verrà visualizzata.

    * Se è stata scaricata un'immagine VHDX per Windows Server 2012 o versione successiva, scegliere **Generazione 2**.
    * Se è stata scaricata un'immagine VHD per Windows Server 2008 R2 o versione successiva, scegliere **Generazione 1**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  Nella pagina **Assegnazione memoria** specificare una **Memoria di avvio** di almeno **8192 MB**, non abilitare la memoria dinamica e quindi fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Nella pagina **Configurazione rete** selezionare il commutatore virtuale connesso a Internet e quindi fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Nella pagina **Connessione disco rigido virtuale** scegliere **Usa un disco rigido virtuale esistente**, specificare il percorso dell'immagine del dispositivo virtuale (VHDX o VHD) e quindi fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  Esaminare la pagina **Riepilogo** e quindi fare clic su **Fine** per creare la macchina virtuale. Per poter proseguire, è necessario aggiungere alcuni core CPU e una seconda unità.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Per soddisfare i requisiti minimi, sono necessarie 4 memorie centrali. Per aggiungere processori virtuali, con il sistema host selezionato nella finestra **Console di gestione di Hyper-V**, nell'elenco di **Macchine virtuali** del riquadro di destra, individuare la macchina virtuale appena creata. Selezionare e fare clic con il pulsante destro del mouse sul nome della macchina, quindi selezionare **Impostazioni**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Nella pagina **Impostazioni**, nel riquadro di sinistra, fare clic su **Processore**. Nel riquadro di destra, impostare **Numero di processori virtuali** su 4 (o oltre). Fare clic su **Apply**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Per soddisfare i requisiti minimi, è necessario anche aggiungere un disco dati virtuale da 500 GB. Nella pagina **Impostazioni**:

    1.  Nel riquadro di sinistra selezionare **Controller SCSI**.
    2.  Nel riquadro di destra selezionare **Disco rigido** e fare clic su **Aggiungi**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Nella pagina **Disco rigido** selezionare l'opzione **Disco rigido virtuale** e fare clic su **Nuovo**. Viene avviata la **Creazione guidata disco rigido virtuale**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Nella pagina **Prima di iniziare** della Creazione guidata disco rigido virtuale fare clic su **Avanti**.

1.  Nella pagina **Selezione formato disco** accettare l'opzione di formato predefinita **VHDX**. Fare clic su **Avanti**. Questa schermata non verrà visualizzata se si esegue Windows Server 2012 R2 o Windows Server 2008 R2.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Nella pagina **Selezione tipo di disco** impostare il tipo di disco rigido virtuale su **A espansione dinamica** (consigliato). Se si sceglie un disco **A dimensione fissa**, il funzionamento è garantito, ma può comportare tempi lunghi di attesa. Si consiglia di non usare l'opzione **Differenze**. Fare clic su **Avanti**. Notare che l'opzione **A espansione dinamica** è l'impostazione predefinita per Windows Server 2012 R2 e Windows Server 2012. In Windows Server 2008 R2 l'impostazione predefinita è **A dimensione fissa**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Nella pagina **Impostazione nome e percorso** fornire un **nome** e un **percorso** (è possibile farlo con il pulsante Sfoglia) per il disco dati. Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Nella pagina **Configurazione disco** selezionare l'opzione **Crea un nuovo disco rigido virtuale vuoto** e specificare la dimensione di **500 GB** (o oltre). Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Nella pagina **Riepilogo** esaminare i dettagli del disco dati virtuale e, se soddisfatti, fare clic su **Fine** per creare il disco. La procedura guidata viene chiusa e un disco rigido virtuale viene aggiunto al computer.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  Viene nuovamente visualizzata la pagina **Impostazioni**. Fare clic su **OK** per chiudere la pagina **Impostazioni** e tornare alla finestra Console di gestione di Hyper-V.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## Passaggio 3: Avviare il dispositivo virtuale e ottenere l'IP

Eseguire i passaggi seguenti per avviare il dispositivo virtuale a cui connettersi.

#### Per avviare il dispositivo virtuale

1.  Avviare il dispositivo virtuale.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Quando il dispositivo è in esecuzione, selezionarlo, fare clic con il pulsante destro del mouse e selezionare **Connetti**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  È necessario attendere 5-10 minuti prima che il dispositivo sia pronto. Un messaggio di stato viene visualizzato nella console per indicare l'avanzamento. Quando il dispositivo è pronto, passare ad **Azione**. Premere `Ctrl + Alt + Delete` per accedere al dispositivo virtuale. L'utente predefinito è *StorSimpleAdmin* e la password predefinita è *Password1*.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Per motivi di sicurezza, la password amministratore del dispositivo scade al primo accesso. Sarà richiesto di modificare la password.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

	Immettere una password contenente almeno 8 caratteri. La password deve soddisfare almeno 3 dei seguenti 4 requisiti: caratteri maiuscoli, minuscoli, numerici e speciali. Immettere nuovamente la password per confermarla. Si riceverà una notifica in cui si comunica che la password è stata modificata.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Dopo aver modificato correttamente la password, il dispositivo virtuale verrà riavviato. Attendere l'avvio del dispositivo.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

 	La console Windows PowerShell del dispositivo sarà visualizzata con un indicatore di stato.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  I passaggi da 6 a 8 si applicano solo all'avvio in un ambiente non DHCP. In presenza di un ambiente DHCP, ignorare questi passaggi e andare al passaggio 9. Se il dispositivo è stato avviato in un ambiente non DHCP, viene visualizzata la schermata seguente.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

 	È ora necessario configurare la rete.

1.  Usare il comando `Get-HcsIpAddress` per elencare le interfacce di rete abilitate nel dispositivo virtuale. Se il dispositivo dispone di una singola interfaccia di rete abilitata, il nome predefinito assegnato a tale interfaccia è `Ethernet`.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  Usare il cmdlet `Set-HcsIpAddress` per configurare la rete. Di seguito è riportato un esempio:

 	`Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

 	![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Dopo aver completato l'installazione iniziale e avviato il dispositivo, viene visualizzato il relativo testo intestazione. Prendere nota dell'indirizzo IP e dell'URL visualizzati nel testo intestazione per gestire il dispositivo. Usare questo indirizzo IP per connettersi all'interfaccia utente Web del dispositivo virtuale e completare l'installazione locale e la registrazione.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (Facoltativo) Eseguire questo passaggio solo se si distribuisce il dispositivo nel cloud per enti pubblici. Si abilita quindi la modalità FIPS (Federal Information Processing Standard per gli Stati Uniti) sul dispositivo. Lo standard FIPS 140 definisce gli algoritmi di crittografia approvati per l'uso da parte dei sistemi del governo federale degli Stati Uniti per la protezione dei dati sensibili.
	1. Per abilitare la modalità FIPS, eseguire il cmdlet seguente:

		`Enter-HcsFIPSMode`

	2. Riavviare il dispositivo dopo aver abilitato la modalità FIPS in modo che le convalide di crittografia abbiano effetto.

		> [AZURE.NOTE] È possibile abilitare o disabilitare la modalità FIPS sul dispositivo. Il dispositivo non supporta l'alternanza tra la modalità FIPS e una modalità diversa.

Se il dispositivo non soddisfa i requisiti minimi di configurazione, viene visualizzato un errore nel testo intestazione (riportato sotto). È necessario modificare la configurazione del dispositivo per garantire la presenza di risorse adeguate a soddisfare i requisiti minimi. È quindi possibile riavviare il dispositivo a cui connettersi. Vedere i requisiti minimi di configurazione in [Passaggio 1: Verificare che il sistema host soddisfi i requisiti minimi del dispositivo virtuale](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

In presenza di altri errori durante la configurazione iniziale con l'interfaccia utente Web locale, vedere i flussi di lavoro seguenti in [Usare l'interfaccia utente Web per amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

-   Eseguire i test diagnostici per [risolvere i problemi di installazione dell'interfaccia utente Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Generare un pacchetto di log e visualizzare i file di log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![icona video](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **Video disponibile**

Guardare il video per vedere come è possibile eseguire il provisioning di StorSimple Virtual Array in Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## Passaggi successivi

-   [Configurare StorSimple Virtual Array come file server](storsimple-ova-deploy3-fs-setup.md)

-   [Configurare StorSimple Virtual Array come server iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0720_2016-->