---
title: Esercitazione sul provisioning di Azure Data Box Gateway in VMware | Microsoft Docs
description: La seconda esercitazione per la distribuzione di Azure Data Box Gateway implica il provisioning di un dispositivo virtuale in VMware.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 00415cab4d5c36c74cf78a10cb71682d97236517
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099159"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware-preview"></a>Esercitazione: Effettuare il provisioning di Azure Data Box Gateway in VMware (anteprima)

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come effettuare il provisioning di un Data Box Gateway in un sistema host che esegue VMware ESXi 6.0, 6.5 o 6.7. 

È necessario disporre dei privilegi di amministratore per eseguire il provisioning e connettersi a un dispositivo virtuale. Il tempo previsto per il completamento di provisioning e installazione iniziale è di circa 10 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare che l'host soddisfi i requisiti minimi del dispositivo
> * Effettuare il provisioning di un dispositivo virtuale in VMware
> * Avviare il dispositivo virtuale e ottenere l'indirizzo IP

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> - Data Box Gateway è in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per il provisioning di un dispositivo virtuale in un sistema host che esegue VMware ESXi 6.0, 6.5 o 6.7 sono i seguenti.

### <a name="for-the-data-box-gateway-resource"></a>Per la risorsa Data Box Gateway

Prima di iniziare, verificare che:

* Tutti i passaggi di [preparazione del portale per Data Box Gateway](data-box-gateway-deploy-prep.md) siano stati completati.
* L'immagine del dispositivo virtuale per VMware sia stata scaricata dal portale di Azure come descritto in [Esercitazione: Preparare la distribuzione di Azure Data Box Gateway (anteprima)](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > Il software in esecuzione nel Data Box Gateway può essere usato solo con la risorsa Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Per il dispositivo virtuale Data Box Gateway

Prima di distribuire un dispositivo virtuale, assicurarsi che:

* Sia possibile accedere a un sistema host che VMware (ESXi 6.0, 6.5 o 6.7) utilizzabile per il provisioning di un dispositivo.
* Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning del dispositivo virtuale:

  * Un minimo di 4 memorie centrali.
  * Almeno 8 GB di RAM.
  * Un'interfaccia di rete.
  * Un disco del sistema operativo da 250 GB.
  * Un disco virtuale da 2 TB per i dati di sistema.

### <a name="for-the-network-in-datacenter"></a>Per la rete nel data center

Prima di iniziare:

- Verificare i requisiti di rete per distribuire un Data Box Gateway e configurare la rete del data center in base ai requisiti. Per altre informazioni, vedere i [requisiti di rete di Data Box Gateway](data-box-gateway-system-requirements.md#networking-requirements).
- Assicurarsi che la larghezza di banda Internet sia di almeno 20 Mbps per consentire il funzionamento ottimale del dispositivo.

## <a name="check-the-host-system"></a>Controllare il sistema host

Per creare un dispositivo virtuale, è necessario quanto segue:

* Accesso a un sistema host che esegue VMware ESXi Server 6.0, 6.5 o 6.7. Un sistema host in grado di dedicare le risorse seguenti al dispositivo virtuale:
 
  * Un minimo di 4 memorie centrali.
  * Almeno 8 GB di RAM. 
  * Un'interfaccia di rete connessa alla rete in grado di indirizzare il traffico a Internet. 
  * Un disco del sistema operativo da 250 GB.
  * Un disco virtuale da 2 TB per i dati.
* Client VMware vSphere nel sistema per gestire l'host ESXi.


## <a name="provision-a-virtual-device-in-hypervisor"></a>Effettuare il provisioning di un dispositivo virtuale nell'hypervisor

Eseguire i passaggi seguenti per il provisioning di un dispositivo virtuale in hypervisor.

1. Copiare l'immagine del dispositivo virtuale nel sistema. Si tratta dell'immagine virtuale (due file) scaricata tramite il portale di Azure. Prendere nota della posizione in cui è stata copiata l'immagine da usare più avanti nella procedura.

2. Accedere al server ESXi con il client Web di vSphere. È necessario disporre dei privilegi di amministratore per creare una macchina virtuale.

   ![](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. Caricare il file VMDK sul server ESXi. Nel riquadro di spostamento selezionare **Storage** (Archiviazione).

   ![](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. Nel riquadro a destra in **Datastores**selezionare l'archivio dati in cui si vuole caricare il file VMDK. 

    - L'archivio dati deve essere di tipo VMFS5. 
    - Deve inoltre disporre di spazio libero sufficiente per il sistema operativo e i dischi dati.
   
5. Fare clic con il pulsante destro del mouse e scegliere **Browse Datastore** (Sfoglia archivio dati).

   ![](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. Viene visualizzata una finestra **Datastore Browser** (Browser archivio dati).

   ![](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. Nella barra degli strumenti fare clic sull'icona **Create directory** (Crea directory) per creare una nuova cartella. Specificare il nome della cartella e prenderne nota. Il nome di questa cartella verrà usato più avanti durante la creazione di una macchina virtuale (procedura consigliata). Fare clic su **Create directory** (Crea directory).

   ![](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. La nuova cartella viene visualizzata nel riquadro a sinistra di **Datastore Browser** (Browser archivio dati). Fare clic sull'icona di**Upload** caricamento e selezionare **Upload File** (Carica file).

    ![](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. Trovare e scegliere i file VMDK scaricati. Sono disponibili due file. Selezionare un file da caricare.

    ![](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. Fare clic su **Apri**. Viene quindi avviato il caricamento del file VMDK nell'archivio dati specificato. Il caricamento del file può richiedere alcuni minuti.
11. Dopo aver completato il caricamento, il file viene visualizzato nell'archivio dati nella cartella creata. Caricare ora il secondo file VMDK nello stesso archivio dati. Una volta caricati entrambi, i file vengono uniti in un unico file. Nella directory viene quindi visualizzato un solo file.

    ![](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. Tornare alla finestra client di vSphere. Nel riquadro di spostamento selezionare **Virtual Machines** (Macchine virtuali). Nel riquadro destro fare clic su **Create/Register VM** (Crea/Registra VM).

    ![](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. Viene visualizzata una **nuova macchina virtuale**. In Select creation type (Seleziona il tipo di creazione) scegliere **Create a new virtual machine** (Crea una nuova macchina virtuale) e fare clic su **Next** (Avanti).
    ![](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. Nella pagina **Select a name and guest OS** (Seleziona un nome e un sistema operativo guest) specificare il **nome** della macchina virtuale. Questo nome deve corrispondere al nome della cartella (procedura consigliata) specificato in precedenza nel passaggio 7. In **Guest OS family** (Famiglia del sistema operativo guest) selezionare Windows e in **Guest OS version** (Versione del sistema operativo guest) selezionare Microsoft Windows Server 2016 (64-bit). Fare clic su **Avanti**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. Nella pagina **Select storage** (Seleziona risorsa di archiviazione) selezionare l'archivio dati che si vuole usare per il provisioning della macchina virtuale. Fare clic su **Avanti**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. Nella pagina **Customize settings** (Personalizza impostazioni) impostare **CPU** su 4, **Memory** (Memoria) su 8192 MB (o su un valore superiore) e **Hard disk 1** (Disco rigido 1) su 2 TB (o su un valore superiore). Scegliere il **disco rigido SCSI** da aggiungere. In questo caso si tratta di LSI Logic SAS. **I dischi IDE statici non sono supportati.** **Hard disk 1** (Disco rigido 1) è il disco dati virtuale. Si noti che, una volta effettuato il provisioning del disco, non è possibile ridurlo.

    ![](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    Nella stessa pagina fare clic su **Add hard disk** (Aggiungi disco rigido) e quindi selezionare **Existing hard disk** (Disco rigido esistente). Selezionare il file VMDK nell'archivio dati. Verrà aggiunto un disco del sistema operativo. 

     ![](./media/data-box-gateway-deploy-provision-vmware/image14.png)

    Scorrere verso il basso fino all'elenco a discesa **New hard disk** (Nuovo disco rigido) ed espanderlo per visualizzare le impostazioni. Impostare **Virtual Device Node** (Nodo dispositivo virtuale) su **IDE controller 0** (Controller IDE 0). Fare clic su **Avanti**.

     ![](./media/data-box-gateway-deploy-provision-vmware/image15.png)

27. Nella pagina **Ready to Complete** , verificare tutte le impostazioni associate alla nuova macchina virtuale. Verificare che il valore per la CPU sia 4, per la memoria sia 8192 MB, per la scheda di rete sia 1 e che il disco rigido 2 sia impostato sul controller IDE 0. Fare clic su **Fine**. 
   
    ![](./media/data-box-gateway-deploy-provision-vmware/image16.png)
    ![](./media/data-box-gateway-deploy-provision-vmware/image17.png)

La macchina virtuale viene ora sottoposta a provisioning. Verrà visualizzata una notifica e la nuova macchina virtuale verrà aggiunta all'elenco di macchine virtuali. 

![](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Il passaggio successivo consiste nell'accendere la macchina e ottenere l'indirizzo IP.

> [!NOTE]
> È consigliabile non installare strumenti VMware nel dispositivo virtuale (di cui è stato appena effettuato il provisioning). L'installazione di strumenti di VMware produrrà una configurazione non supportata.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Avviare il dispositivo virtuale e ottenere l'IP

Eseguire i passaggi seguenti per avviare il dispositivo virtuale a cui connettersi.

#### <a name="to-start-the-virtual-device"></a>Per avviare il dispositivo virtuale
1. Avviare il dispositivo virtuale. Nel riquadro destro selezionare il dispositivo nell'elenco delle macchine virtuali e fare clic con il pulsante destro del mouse per visualizzare il menu di scelta rapida. Selezionare **Power** (Alimentazione) e scegliere **Power on** (Alimentazione attiva). La macchina virtuale deve accendersi. È possibile visualizzare lo stato nel riquadro inferiore del client Web.

    ![](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. Selezionare di nuovo la macchina virtuale. Fare clic con il pulsante destro del mouse e scegliere **Console**, quindi **Open in a new window** (Apri in un'altra finestra).

    ![](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. La console della macchina virtuale si apre in una nuova finestra. 

    ![](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. Quando il dispositivo è in esecuzione, posizionare il cursore sulla scheda nella parte centrale superiore della finestra della console e fare clic. Selezionare **Guest OS (Sistema operativo guest) > Send keys (Invia chiavi) > CTRL+ALT+CANC**. La macchina virtuale verrà sbloccata.

   ![](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. Specificare la password per accedere alla macchina. La password predefinita è Password1.

   ![](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. I passaggi da 5 a 7 devono essere eseguiti solo in caso di avvio in un ambiente non DHCP. In presenza di un ambiente DHCP, ignorare questi passaggi e andare al passaggio 8. Se il dispositivo è stato avviato in un ambiente non DHCP, viene visualizzato un messaggio: **Usare il cmdlet Set-HcsIPAddress per configurare la rete**. 
   
7. Per configurare la rete, al prompt dei comandi usare il comando `Get-HcsIpAddress` per elencare le interfacce di rete abilitate nel dispositivo virtuale. Se il dispositivo dispone di una singola interfaccia di rete abilitata, il nome predefinito assegnato a questa interfaccia è `Ethernet`.

8. Usare il cmdlet `Set-HcsIpAddress` per configurare la rete. Di seguito è riportato un esempio:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. Dopo aver completato l'installazione iniziale e avviato il dispositivo, viene visualizzato il relativo testo intestazione. Prendere nota dell'indirizzo IP e dell'URL visualizzati nel testo intestazione per gestire il dispositivo. Si userà questo indirizzo IP per connettersi all'interfaccia utente Web del dispositivo virtuale e completare l'installazione locale e l'attivazione.

   ![](./media/data-box-gateway-deploy-provision-vmware/image24.png)

Se il dispositivo non soddisfa i requisiti minimi di configurazione, viene visualizzato un errore nel testo intestazione (riportato sotto). È necessario modificare la configurazione del dispositivo per garantire la presenza di risorse adeguate a soddisfare i requisiti minimi. È quindi possibile riavviare il dispositivo a cui connettersi. Vedere i requisiti minimi di configurazione in [Verificare che il sistema host soddisfi i requisiti minimi del dispositivo virtuale](#check-the-host-system).

<!---If you face any other error during the initial configuration using the local web UI, refer to the following workflows:

* Run diagnostic tests to [troubleshoot web UI setup](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generate log package and view log files](storsimple-ova-web-ui-admin.md#generate-a-log-package).-->

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Gateway, ad esempio:

> [!div class="checklist"]
> * Verificare che l'host soddisfi i requisiti minimi del dispositivo
> * Effettuare il provisioning di un dispositivo virtuale in VMware
> * Avviare il dispositivo virtuale e ottenere l'indirizzo IP

Passare all'esercitazione successiva per informazioni su come connettere, configurare e attivare il dispositivo virtuale.

* [Configurare e connettersi alle condivisioni in Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)

