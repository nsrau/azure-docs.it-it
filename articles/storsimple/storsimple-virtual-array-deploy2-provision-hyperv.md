---
title: Eseguire il provisioning di un array virtuale StorSimple in Hyper-V | Documentazione Microsoft
description: Questa seconda esercitazione sulla distribuzione di array virtuali StorSimple implica il provisioning di un array virtuale in Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8209eb8df45d8c7c42e303ab3377dbde3255154a
ms.openlocfilehash: 924b3621e03b678e158cd1240864a903db6c8510
ms.lasthandoff: 03/01/2017

---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Distribuire StorSimple Virtual Array: eseguire il provisioning in Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come eseguire il provisioning di un array virtuale StorSimple in un sistema host che esegue Hyper-V in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. Le informazioni all'interno di questo articolo si applicano alla distribuzione di array virtuali StorSimple nel portale di Azure nonché nel cloud di Microsoft Azure per enti pubblici.

È necessario disporre dei privilegi di amministratore per eseguire il provisioning e configurare un array virtuale. Il tempo previsto per il completamento di provisioning e installazione iniziale è di circa 10 minuti.

## <a name="provisioning-prerequisites"></a>Prerequisiti di provisioning
Vengono illustrati i prerequisiti per l'esecuzione del provisioning di un array virtuale in un sistema host che esegue Hyper-V in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Per il servizio Gestione dispositivi StorSimple
Prima di iniziare, verificare che:

* Tutti i passaggi sulla [preparazione del portale per StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)siano stati completati.
* L'immagine dell'array virtuale per Hyper-V è stata scaricata dal portale di Azure. Per altre informazioni, vedere **Passaggio 3: Scaricare l'immagine dell'array virtuale** nella [guida alla preparazione del portale per l'array virtuale StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Il software in esecuzione nell'array virtuale StorSimple può essere usato solo in combinazione con il servizio Gestione dispositivi StorSimple.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Per l'array virtuale StorSimple
Prima di distribuire un array virtuale, è necessario:

* Avere accesso a un sistema host che esegue Hyper-V in Windows Server 2008 R2 o versioni successive da poter usare per il provisioning di un dispositivo.
* Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning dell'array virtuale:

  * Un minimo di 4 memorie centrali.
  * Almeno 8 GB di RAM. Se si prevede di configurare la matrice virtuale come file server, 8 GB supportano poco meno di 2 milioni di file. Sono necessari 16 GB di RAM per supportare 2-4 milioni di file.
  * Un'interfaccia di rete.
  * Un disco virtuale da 500 GB per i dati.

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter
Prima di iniziare, rivedere i requisiti di rete per la distribuzione di un array virtuale StorSimple e la corretta configurazione della rete del datacenter. Per altre informazioni, vedere [Requisiti di sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Provisioning passo per passo
Per eseguire il provisioning e connettersi a un array virtuale, è necessario eseguire i passaggi seguenti:

1. Verificare che il sistema host disponga di risorse sufficienti a soddisfare i requisiti minimi dell'array virtuale.
2. Eseguire il provisioning di un array virtuale in hypervisor.
3. Avviare l'array virtuale e ottenere l'indirizzo IP.

Ciascuno di questi passaggi viene spiegato nelle sezioni seguenti.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Passaggio 1: Verificare che il sistema host soddisfi i requisiti minimi dell'array virtuale
Per creare un array virtuale, è necessario:

* Ruolo di Hyper-V installato in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.
* Console di gestione di Hyper-V di Microsoft in un client Microsoft Windows connesso all'host.

Verificare che l'hardware sottostante (sistema host) in cui si sta creando l'array virtuale sia in grado di dedicare le risorse seguenti all'array virtuale:

* Un minimo di 4 memorie centrali.
* Almeno 8 GB di RAM. Se si prevede di configurare la matrice virtuale come file server, 8 GB supportano poco meno di 2 milioni di file. Sono necessari 16 GB di RAM per supportare 2-4 milioni di file.
* Un'interfaccia di rete.
* Un disco virtuale da 500 GB per i dati di sistema.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Passaggio 2: Eseguire il provisioning di un array virtuale in hypervisor
Eseguire i passaggi seguenti per il provisioning di un dispositivo in hypervisor.

#### <a name="to-provision-a-virtual-array"></a>Per eseguire il provisioning di un array virtuale
1. Nell'host di Windows Server copiare l'immagine dell'array virtuale in un'unità locale. Si tratta dell'immagine (VHD o VHDX) scaricata tramite il portale di Azure. Prendere nota della posizione in cui è stata copiata l'immagine da usare più avanti nella procedura.
2. Aprire **Server Manager**. Nell'angolo in alto a destra fare clic su **Strumenti** e scegliere **Console di gestione di Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Se si esegue Windows Server 2008 R2, aprire la Console di gestione di Hyper-V. In Server Manager fare clic su **Ruoli > Hyper-V > Console di gestione di Hyper-V**.
3. Nella **Console di gestione di Hyper-V**, nel riquadro dell'ambito, fare clic con il pulsante destro del mouse sul nodo del sistema per aprire il menu di scelta rapida e quindi fare clic su **Nuovo** > **Macchina virtuale**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Nella pagina **Before you begin** (Prima di iniziare) della Creazione guidata macchina virtuale fare clic su **Avanti**.
5. Nella pagina **Specificare nome e percorso** indicare un **Nome** da assegnare all'array virtuale. Fare clic su **Avanti**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Nella pagina **Impostazione generazione** scegliere il tipo di immagine del dispositivo e fare clic su **Avanti**. Se si usa Windows Server 2008 R2, questa pagina non verrà visualizzata.

   * Se è stata scaricata un'immagine VHDX per Windows Server 2012 o versione successiva, scegliere **Generazione 2** .
   * Se è stata scaricata un'immagine VHD per Windows Server 2008 R2 o versione successiva, scegliere **Generazione 1** .

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Nella pagina **Assegnazione memoria** specificare una **Memoria di avvio** di almeno **8192 MB**, non abilitare la memoria dinamica e quindi fare clic su **Avanti**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Nella pagina **Configurazione della rete** selezionare il commutatore virtuale connesso a Internet e quindi fare clic su **Avanti**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Nella pagina **Connessione disco rigido virtuale** scegliere **Usa disco rigido virtuale esistente**, specificare il percorso dell'immagine dell'array virtuale (formato vhdx o vhd) e quindi fare clic su **Avanti**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Esaminare la pagina **Riepilogo** e quindi fare clic su **Fine** per creare la macchina virtuale.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Per soddisfare i requisiti minimi, sono necessarie 4 memorie centrali. Per aggiungere 4 processori virtuali, selezionare il sistema host nella finestra **Console di gestione di Hyper-V**. Nel riquadro di destra sotto l'elenco di **macchine virtuali** individuare la macchina virtuale appena creata. Selezionare e fare clic con il pulsante destro del mouse sul nome della macchina, quindi selezionare **Impostazioni**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Nella pagina **Impostazioni**, nel riquadro di sinistra, fare clic su **Processore**. Nel riquadro di destra, impostare **Numero di processori virtuali** su 4 (o oltre). Fare clic su **Apply**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Per soddisfare i requisiti minimi, è necessario anche aggiungere un disco dati virtuale da 500 GB. Nella pagina **Impostazioni** :

    1. Nel riquadro di sinistra selezionare **Controller SCSI**.
    2. Nel riquadro di destra selezionare **Disco rigido** e fare clic su **Aggiungi**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Nella pagina **Disco rigido** selezionare l'opzione **Disco rigido virtuale** e fare clic su **Nuovo**. Viene avviata la **Creazione guidata disco rigido virtuale**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Nella pagina **Before you begin** (Prima di iniziare) della Creazione guidata disco rigido virtuale fare clic su **Avanti**.
16. Nella pagina **Selezione formato disco** accettare l'opzione di formato predefinita **VHDX**. Fare clic su **Next**. Se si esegue Windows Server 2008 R2, questa schermata non viene visualizzata.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Nella pagina **Selezione tipo di disco** impostare il tipo di disco rigido virtuale su **A espansione dinamica** (consigliato). Il funzionamento di un disco **A dimensione fissa** è garantito, ma può comportare tempi lunghi di attesa. Si consiglia di non usare l'opzione **Differenze** . Fare clic su **Avanti**. In Windows Server 2012 R2 e Windows Server 2012 l'opzione predefinita è **A espansione dinamica**, mentre in Windows Server 2008 R2 il valore predefinito è **A dimensione fissa**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Nella pagina **Impostazione nome e percorso** fornire un **nome** e un **percorso** (anche accedendovi) da assegnare al disco dati. Fare clic su **Avanti**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Nella pagina **Configurazione disco** selezionare l'opzione **Crea un nuovo disco rigido virtuale vuoto** e specificare la dimensione di **500 GB** (o superiore). Fare clic su **Avanti**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Nella pagina **Riepilogo** esaminare i dettagli del disco dati virtuale e, se conformi alle proprie esigenze, fare clic su **Fine** per creare il disco. La procedura guidata viene chiusa e un disco rigido virtuale viene aggiunto al computer.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Viene nuovamente visualizzata la pagina **Impostazioni**. Fare clic su **OK** per chiudere la pagina **Impostazioni** e tornare alla finestra Console di gestione di Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Passaggio 3: Avviare l'array virtuale e ottenere l'indirizzo IP
Eseguire la procedura seguente per avviare l'array virtuale a cui connettersi.

#### <a name="to-start-the-virtual-array"></a>Per avviare l'array virtuale
1. Avviare l'array virtuale.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Quando il dispositivo è in esecuzione, selezionarlo, fare clic con il pulsante destro del mouse e selezionare **Connetti**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. È necessario attendere 5-10 minuti prima che il dispositivo sia pronto. Un messaggio di stato viene visualizzato nella console per indicare l'avanzamento. Quando il dispositivo è pronto, passare ad **Azione**. Premere `Ctrl + Alt + Delete` per accedere all'array virtuale. L'utente e la password predefiniti sono rispettivamente *StorSimpleAdmin* e *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Per motivi di sicurezza, la password amministratore del dispositivo scade al primo accesso. Viene richiesto di modificarla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Immettere una password contenente almeno 8 caratteri. La password deve soddisfare almeno 3 dei seguenti 4 requisiti: caratteri maiuscoli, minuscoli, numerici e speciali. Immettere nuovamente la password per confermarla. Si riceve una notifica in cui si comunica che la password è stata modificata.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Dopo aver modificato correttamente la password, l'array virtuale potrebbe essere riavviato. Attendere l'avvio del dispositivo.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    La console di Windows PowerShell del dispositivo viene visualizzata con un indicatore di stato.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. I passaggi da 6 a 8 si applicano solo all'avvio in un ambiente non DHCP. In presenza di un ambiente DHCP, ignorare questi passaggi e andare al passaggio 9. Se il dispositivo è stato avviato in un ambiente non DHCP, viene visualizzata la schermata seguente.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Successivamente configurare la rete.
7. Usare il comando `Get-HcsIpAddress` per elencare le interfacce di rete abilitate nell'array virtuale. Se il dispositivo dispone di una singola interfaccia di rete abilitata, il nome predefinito assegnato a questa interfaccia è `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Usare il cmdlet `Set-HcsIpAddress` per configurare la rete. Vedere l'esempio seguente:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Dopo aver completato l'installazione iniziale e avviato il dispositivo, viene visualizzato il relativo testo intestazione. Prendere nota dell'indirizzo IP e dell'URL visualizzati nel testo intestazione per gestire il dispositivo. Usare questo indirizzo IP per connettersi all'interfaccia utente Web dell'array virtuale e completare l'installazione locale e la registrazione.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Facoltativo) Eseguire questo passaggio solo se si distribuisce il dispositivo nel cloud per enti pubblici. Si abilita quindi la modalità FIPS (Federal Information Processing Standard per gli Stati Uniti) sul dispositivo. Lo standard FIPS 140 definisce gli algoritmi di crittografia approvati per l'uso da parte dei sistemi del governo federale degli Stati Uniti per la protezione dei dati sensibili.

    1. Per abilitare la modalità FIPS, eseguire il cmdlet seguente:

        `Enable-HcsFIPSMode`
    2. Riavviare il dispositivo dopo aver abilitato la modalità FIPS in modo che le convalide di crittografia abbiano effetto.

       > [!NOTE]
       > È possibile abilitare o disabilitare la modalità FIPS sul dispositivo. Il dispositivo non supporta l'alternanza tra la modalità FIPS e una modalità diversa.
       >
       >

Se il dispositivo non soddisfa i requisiti minimi di configurazione, viene visualizzato un errore nel testo intestazione (riportato sotto). Modificare la configurazione del dispositivo per garantire la presenza di risorse adeguate a soddisfare i requisiti minimi della macchina. È quindi possibile riavviare il dispositivo a cui connettersi. Vedere i requisiti minimi di configurazione in [Passaggio 1: Verificare che il sistema host soddisfi i requisiti minimi dell'array virtuale](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

In presenza di altri errori durante la configurazione iniziale con l'interfaccia utente Web locale, vedere i flussi di lavoro seguenti:

* Eseguire i test diagnostici per [risolvere i problemi di installazione dell'interfaccia utente Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generare un pacchetto di log e visualizzare i file di log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passaggi successivi
* [Configurare StorSimple Virtual Array come file server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurare StorSimple Virtual Array come server iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)

