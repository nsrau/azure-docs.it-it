<properties
   pageTitle="Distribuire StorSimple Virtual Array: eseguire il provisioning in VMware"
   description="Questa seconda esercitazione sulla serie di distribuzione di StorSimple Virtual Array implica il provisioning di un dispositivo virtuale in VMware."
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
   ms.date="03/01/2016"
   ms.author="alkohli"/>


# Distribuire StorSimple Virtual Array: eseguire il provisioning di un array virtuale in VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## Panoramica 
Questa esercitazione sul provisioning si applica solo a StorSimple Virtual Array (noti anche come dispositivi virtuali locali StorSimple o dispositivi virtuali StorSimple) che eseguono la versione di disponibilità generale (GA) di marzo 2016. Questa esercitazione illustra come eseguire il provisioning di StorSimple Virtual Array e connettersi all'array in un sistema host che esegue VMware ESXi 5.5 e versioni successive.

È necessario disporre dei privilegi di amministratore per eseguire il provisioning e connettersi a un dispositivo virtuale. Il tempo previsto per il completamento di provisioning e installazione iniziale è di circa 10 minuti.


## Prerequisiti di provisioning

Ecco i prerequisiti per il provisioning di un dispositivo virtuale in un sistema host che esegue VMware ESXi 5.5 e versioni successive.

### Per il servizio StorSimple Manager

Prima di iniziare, verificare che:

-   Tutti i passaggi sulla [preparazione del portale per StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md) siano stati completati.

-   L'immagine del dispositivo virtuale per VMware sia stata scaricata dal portale di Azure. Per ulteriori informazioni, vedere [Passaggio 3: Scaricare l'immagine del dispositivo virtuale](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### Per il dispositivo virtuale StorSimple 

Prima di distribuire un dispositivo virtuale, è necessario:

-   Avere accesso a un sistema host che esegue Hyper-V (2008 R2 o versioni successive) da poter usare per eseguire il provisioning di un dispositivo.

-   Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning del dispositivo virtuale:

	-   Un minimo di 4 memorie centrali.

	-   Almeno 8 GB di RAM.

	-   Un'interfaccia di rete.

	-   Un disco virtuale da 500 GB per i dati di sistema.

### Per la rete nel data center 

Prima di iniziare, verificare che:

-   I requisiti di rete per distribuire un dispositivo virtuale StorSimple siano stati esaminati e la rete del data center sia stata configurata in base ai requisiti. Per ulteriori informazioni, vedere la guida ai requisiti di sistema per Microsoft Azure StorSimple Virtual Array.

## Provisioning passo per passo 

Per eseguire il provisioning e connettersi a un dispositivo virtuale, è necessario eseguire i passaggi seguenti:

1.  Verificare che il sistema host disponga di risorse sufficienti a soddisfare i requisiti minimi del dispositivo virtuale.

2.  Eseguire il provisioning di un dispositivo virtuale in hypervisor.

3.  Avviare il dispositivo virtuale e ottenere l'indirizzo IP.

## Passaggio 1: Verificare che il sistema host soddisfi i requisiti minimi del dispositivo virtuale

Per creare un dispositivo virtuale, è necessario:

-   Accedere a un sistema host che esegue VMware ESXi Server 5.5 e versioni successive.

-   Client VMware vSphere nel sistema per gestire l'host ESXi.

	-   Un minimo di 4 memorie centrali.

	-   Almeno 8 GB di RAM.

	-   Un'interfaccia di rete connessa alla rete in grado di indirizzare il traffico a Internet. La larghezza di banda minima di Internet deve essere di 5 Mbps e consentire un funzionamento ottimale del dispositivo.

	-   Un disco virtuale da 500 GB per i dati.

## Passaggio 2: Eseguire il provisioning di un dispositivo virtuale in hypervisor

Eseguire i passaggi seguenti per il provisioning di un dispositivo virtuale in hypervisor.

1.  Copiare l'immagine del dispositivo virtuale nel sistema. Questa è l'immagine scaricata tramite il portale di Azure classico. Prendere nota della posizione in cui è stata copiata l'immagine da usare più avanti nella procedura.

2.  Accedere al server ESXi tramite il client di vSphere. È necessario disporre dei privilegi di amministratore per creare una macchina virtuale.

	![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  Nel client di vSphere, nella sezione dell'inventario nel riquadro a sinistra, selezionare il server ESXi.

	![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  Prima di tutto, è necessario caricare il file VMDK sul server ESXi. Passare alla scheda **Configuration** nel riquadro di destra. In **Hardware** selezionare **Storage**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  Nel riquadro a destra in **Datastores** selezionare l'archivio dati in cui si desidera caricare il file VMDK. L'archivio dati deve disporre di spazio libero sufficiente per il sistema operativo e i dischi dati.

	![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Fare clic con il tasto destro del mouse e selezionare **Browse Datastore**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Viene visualizzata una finestra **Datastore Browser**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  Nella barra degli strumenti fare clic sull'icona ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) per creare una nuova cartella. Specificare il nome della cartella e prenderne nota. Il nome di questa cartella verrà usato più avanti durante la creazione di una macchina virtuale (procedura consigliata). Fare clic su **OK**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  La nuova cartella viene visualizzata nel riquadro a sinistra di **Datastore Browser**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Fare clic sull'icona relativa al caricamento ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) e selezionare **Upload File**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  È ora necessario individuare e scegliere il file VMDK scaricato.

	![](./media/storsimple-ova-deploy2-provision-vmware/image12.png)

1.  Fare clic su **Apri**. Viene quindi avviato il caricamento del file VMDK nell'archivio dati specificato.

	![](./media/storsimple-ova-deploy2-provision-vmware/image13.png)

1.  Il caricamento del file può richiedere alcuni minuti. Una volta completato il caricamento, il file viene visualizzato nell'archivio dati nella cartella creata.

	![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

1.  Tornare alla finestra client di vSphere. Con il server ESXi selezionato, fare click con il tasto destro del mouse e selezionare **New Virtual Machine**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Viene visualizzata una finestra **Create New Virtual Machine**. Nella pagina **Configuration**, selezionare l'opzione **Custom**. Fare clic su **Next**. ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Nella pagina **Name and Location** specificare il nome della macchina virtuale. Questo nome deve corrispondere al nome della cartella (procedura consigliata) specificato in precedenza nel passaggio 8.

	![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Nella pagina **Storage** selezionare l'archivio dati che si desidera usare per eseguire il provisioning della VM.

	![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Nella pagina **Virtual Machine Version** selezionare **Virtual Machine Version: 8**. Notare che questa è l'unica opzione supportata per questa versione.

	![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Nella pagina **Guest Operating System** specificare il **sistema operativo guest** **Windows**. Per **Version** nell'elenco a discesa selezionare **Microsoft Windows Server 2012 (64-bit)**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  Nella pagina **CPU**, regolare **Number of virtual sockets** e **Number of cores per virtual socket** in modo che **Total number of cores** sia 4 (o oltre). Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  Nella pagina **Memory** specificare 8 GB (o oltre) di RAM. Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Nella pagina **Network** specificare il numero delle interfacce di rete. Il requisito minimo è un'interfaccia di rete.

	![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  Nella pagina **SCSI Controller** accettare il valore predefinito **controller LSI Logic SAS**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  Nella pagina **Select a Disk** scegliere **Use an existing virtual disk**. Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Nella pagina **Select Existing Disk** in **Disk File Path** fare clic su **Browse**. Viene visualizzata la finestra di dialogo **Browse Datastores**. Passare alla posizione in cui è stato caricato il file VMDK. Selezionare il file e fare clic su **OK**. Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Nella pagina **Advanced Options** accettare il valore predefinito e fare clic su **Next**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Nella pagina **Ready to Complete** verificare tutte le impostazioni associate alla nuova macchina virtuale. Selezionare **Edit the virtual machine settings before completion**. Fare clic su **Continue**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Nella pagina **Virtual Machines Properties** nella scheda **Hardware** individuare l'hardware del dispositivo. Selezionare **New Hard Disk**. Fare clic su **Aggiungi**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Viene visualizzata la finestra **Add Hardware**. Nella pagina **Device Type** in **Choose the type of device you wish to add** selezionare **Hard Disk** e fare clic su **Next**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Nella pagina **Select a Disk** scegliere **Create a new virtual disk**. Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Nella pagina **Create a Disk** impostare **Disk Size** su 500 GB o più. In **Disk Provisioning** selezionare **Thin Provisioning**. Fare clic su **Avanti**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Nella pagina **Advanced Options** accettare il valore predefinito.

	![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Nella pagina **Ready to Complete** esaminare le opzioni del disco. Fare clic su **Finish**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Si torna quindi alla pagina delle proprietà della macchina virtuale. Un nuovo disco rigido viene aggiunto alla macchina virtuale. Fare clic su **Finish**.
  
	![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Con la macchina virtuale selezionata nel riquadro a destra, passare alla scheda **Summary**. Rivedere le impostazioni per la macchina virtuale.

	![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

La macchina virtuale viene ora sottoposta a provisioning. Il passaggio successivo consiste nell'accendere la macchina e ottenere l'indirizzo IP.

## Passaggio 3: Avviare il dispositivo virtuale e ottenere l'IP

Eseguire i passaggi seguenti per avviare il dispositivo virtuale a cui connettersi.

#### Per avviare il dispositivo virtuale

1.  Avviare il dispositivo virtuale. Nel vSphere Configuration Manager, nel riquadro a sinistra, selezionare il dispositivo e fare clic con il tasto destro del mouse per visualizzare il menu di scelta rapida. Selezionare **Power**, quindi **Power on**. La macchina virtuale deve accendersi. È possibile visualizzare lo stato nella parte inferiore del riquadro **Recent Tasks** del client di vSphere.

	![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  Il completamento delle attività di installazione richiede alcuni minuti. Quando il dispositivo è in esecuzione, passare alla scheda **Console**. Usare la combinazione di tasti CTRL+ALT+CANC per accedere al dispositivo. In alternativa, è possibile posizionare il cursore sulla finestra della console e premere CTRL+ALT+INSERT. L'utente predefinito è *StorSimpleAdmin* e la password predefinita è *Password1*.

	![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Per motivi di sicurezza, la password amministratore del dispositivo scade al primo accesso. Sarà richiesto di modificare la password.

	![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Immettere una password contenente almeno 8 caratteri. La password deve soddisfare 3 di questi 4 requisiti: lettere maiuscole, minuscole, numeri e caratteri speciali. Immettere nuovamente la password per confermarla. Si riceverà una notifica in cui si comunica che la password è stata modificata.

	![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Dopo aver modificato correttamente la password, il dispositivo virtuale verrà riavviato. Attendere il completamento dell'operazione di riavvio. La console di Windows PowerShell del dispositivo può essere visualizzata con un indicatore di stato.

	![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  I passaggi da 6 a 8 si applicano solo all'avvio in un ambiente non DHCP. In presenza di un ambiente DHCP, ignorare questi passaggi e andare al passaggio 9. Se il dispositivo è stato avviato in un ambiente non DHCP, viene visualizzata la schermata seguente.

	![](./media/storsimple-ova-deploy2-provision-vmware/image42.png)

	È ora necessario configurare la rete.

1.  Usare il comando `Get-HcsIpAddress` per elencare le interfacce di rete abilitate nel dispositivo virtuale. Se il dispositivo dispone di una singola interfaccia di rete abilitata, il nome predefinito assegnato a questa interfaccia è `Ethernet`.

	![](./media/storsimple-ova-deploy2-provision-vmware/image43.png)

1.  Usare il cmdlet Set-HcsIpAddress per configurare la rete. Di seguito è riportato un esempio:


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

	![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Dopo aver completato l'installazione iniziale e avviato il dispositivo, viene visualizzato il relativo testo intestazione. Prendere nota dell'indirizzo IP e dell'URL visualizzati nel testo intestazione per gestire il dispositivo. Usare questo indirizzo IP per connettersi all'interfaccia utente Web del dispositivo virtuale e completare l'installazione locale e la registrazione.

	![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)

Se il dispositivo non soddisfa i requisiti minimi di configurazione, viene visualizzato un errore nel testo intestazione (riportato sotto). È necessario modificare la configurazione del dispositivo per garantire la presenza di risorse adeguate a soddisfare i requisiti minimi. È quindi possibile riavviare il dispositivo a cui connettersi. Fare riferimento ai requisiti minimi di configurazione in [Passaggio 1: Verificare che il sistema host soddisfi i requisiti minimi del dispositivo virtuale](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

In presenza di altri errori durante la configurazione iniziale tramite l'interfaccia utente Web locale, fare riferimento ai flussi di lavoro seguenti nell'articolo sulla [gestione di StorSimple Virtual Array tramite interfaccia utente Web locale](storsimple-ova-web-ui-admin.md).

-   Eseguire i test diagnostici per [risolvere i problemi di installazione dell'interfaccia utente Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Generare pacchetto di log e visualizzare file di log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## Passaggi successivi

-   [Configurare StorSimple Virtual Array come file server](storsimple-ova-deploy3-fs-setup.md)

-   [Configurare StorSimple Virtual Array come server iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0302_2016-->