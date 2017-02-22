---
title: Installazione del server iSCSI dell&quot;array virtuale StorSimple | Microsoft Docs
description: Viene illustrato come eseguire l&quot;installazione iniziale, registrare il server iSCSI StorSimple e completare l&quot;installazione del dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 1a854bb3-3068-4db9-87b7-9f3692ab64e4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 715720d22b58ddd3d0e5042de151219e49549c5e


---
# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Distribuire StorSimple Virtual Array: configurare il dispositivo virtuale come server iSCSI
![flusso del processo di installazione di iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Panoramica
Questa esercitazione di sviluppo si applica solo a Microsoft Azure StorSimple Virtual Array (noto anche come dispositivo virtuale locale StorSimple o dispositivo virtuale StorSimple) che esegue la versione di disponibilità generale (GA) di marzo 2016. Questa esercitazione illustra come eseguire l'installazione iniziale, registrare il server iSCSI StorSimple, completare l'installazione del dispositivo, quindi creare, montare, inizializzare e formattare i volumi nel server iSCSI del dispositivo virtuale StorSimple. Le informazioni sull'installazione di StorSimple in questo articolo si applicano solo all'array virtuale StorSimple. 

Il completamento delle procedure descritte di seguito richiede approssimativamente da 30 minuti a 1 ora. Le informazioni pubblicate in questo articolo si applicano solo all'array virtuale StorSimple.

## <a name="setup-prerequisites"></a>Prerequisiti di installazione
Prima di configurare e installare il dispositivo virtuale StorSimple, si deve:

* Eseguire il provisioning di un dispositivo virtuale e connettersi come illustrato in [Distribuire StorSimple Virtual Array: eseguire il provisioning di un array virtuale in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o in [Distribuire StorSimple Virtual Array: eseguire il provisioning di un array virtuale in VMware](storsimple-ova-deploy2-provision-vmware.md).
* Avere ottenuto la chiave di registrazione del servizio dal servizio StorSimple Manager creato per gestire i dispositivi virtuali StorSimple. Per altre informazioni, vedere **Passaggio 2: Ottenere la chiave di registrazione del servizio** in [Distribuire StorSimple Virtual Array: preparare il portale](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Se questo è il secondo dispositivo virtuale o l'ulteriore dispositivo registrato con un servizio StorSimple Manager esistente, è necessario disporre della chiave DEK del servizio. Questa chiave viene generata nel momento in cui avviene la corretta registrazione del primo dispositivo con questo servizio. Se si smarrisce la chiave, vedere **Ottenere la chiave DEK del servizio** in [Usare l'interfaccia utente Web per amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Installazione passo per passo
Seguire passo per passo le istruzioni riportate sotto per installare e configurare il dispositivo virtuale StorSimple:

* [Passaggio 1: Completare l'installazione dell'interfaccia utente Web locale e registrare il dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [Passaggio 2: Completare l'installazione del dispositivo richiesta](#step-2-complete-the-required-device-setup)
* [Passaggio 3: Aggiungere un volume](#step-3-add-a-volume)
* [Passaggio 4: Montare, inizializzare e formattare un volume](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passaggio 1: Completare l'installazione dell'interfaccia utente Web locale e registrare il dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Per completare l'installazione e registrare il dispositivo
1. Aprire una finestra del browser e connettersi all'interfaccia utente Web digitando:
   
    `https://<ip-address of network interface>`
   
    Usare l'URL di connessione indicata nel passaggio precedente. Viene visualizzato un errore in cui si notifica che si è verificato un problema con il certificato di sicurezza del sito Web. Fare clic su **Passa a questa pagina Web**.
   
    ![errore di certificato di sicurezza](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)
2. Accedere all'interfaccia utente Web del dispositivo virtuale come **StorSimpleAdmin**. Immettere la password amministratore del dispositivo modificata nel Passaggio 3: Avviare il dispositivo virtuale dell'articolo [Distribuire un array virtuale StorSimple: eseguire il provisioning di un dispositivo virtuale in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o [Distribuire un array virtuale StorSimple: eseguire il provisioning di un dispositivo virtuale in VMware](storsimple-ova-deploy2-provision-vmware.md).
   
    ![Pagina di accesso](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)
3. Si passa così alla pagina **Home** . Questa pagina illustra le varie impostazioni necessarie per configurare e registrare il dispositivo virtuale con il servizio StorSimple Manager. Notare che le **Impostazioni di rete**, le **Impostazioni proxy Web** e le **Impostazioni ora** sono facoltative. Le uniche impostazioni obbligatorie sono le **Impostazioni del dispositivo** e le **Impostazioni cloud**.
   
    ![Home page](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)
4. Nella pagina delle **Impostazioni di rete** in **Interfacce di rete** DATA 0 viene configurato automaticamente. Ogni interfaccia di rete è impostata come predefinita per ottenere un indirizzo IP automaticamente (DHCP). Quindi, un indirizzo IP, una subnet e un gateway vengono assegnati automaticamente (sia per IPv4 che per IPv6).
   
    Se si prevede di distribuire il dispositivo come server iSCSI (per il provisioning dell'archiviazione a blocchi), si consiglia di disabilitare l'opzione **Ottieni automaticamente un indirizzo IP** e configurare gli indirizzi IP statici.
   
    ![Pagina Impostazioni di rete](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)
   
    Se si aggiunge più di un'interfaccia di rete durante il provisioning del dispositivo, è possibile configurarle tutte qui. Si noti che è possibile configurare l'interfaccia di rete come solo IPv4 o come IPv4 e IPv6. Le configurazioni solo IPv6 non sono supportate.
5. I server DNS sono necessari perché vengono usati quando il dispositivo tenta di comunicare con i provider del servizio di archiviazione cloud o per risolvere il dispositivo in base al nome se è configurato come file server. Nella pagina **Impostazioni di rete** in **Server DNS**:
   
   1. Un server DNS primario e secondario viene configurato automaticamente. Se si sceglie di configurare gli indirizzi IP statici, è possibile specificare i server DNS. Per una disponibilità elevata, si consiglia di configurare un server DNS primario e uno secondario.
   2. Fare clic su **Apply**. Si applicano e convalidano così le impostazioni di rete.
6. Nella pagina **Impostazioni del dispositivo** :
   
   1. Assegnare un **Nome** univoco al dispositivo. Questo nome può avere da 1 a 15 caratteri e contenere lettere, numeri e trattini.
   2. Fare clic sull'icona **Server iSCSI** ![icona server iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) per il **Tipo** di dispositivo che si sta creando. Un server iSCSI consente di eseguire il provisioning dell'archiviazione a blocchi.
   3. Specificare se si desidera che il dispositivo sia aggiunto al dominio. Se il dispositivo è un server iSCSI, l'aggiunta del dominio è facoltativa. Se si decide di non aggiungere il server iSCSI a un dominio, fare clic su **Applica**, attendere l'applicazione delle impostazioni, quindi andare al passaggio successivo.
      
       Se si desidera aggiungere il dispositivo a un dominio. Immettere un **nome di dominio**, quindi fare clic su **Applica**.
      
      > [!NOTE]
      > Se si aggiunge il server iSCSI a un dominio, assicurarsi che l'array virtuale si trovi nella propria unità organizzativa (OU) per Microsoft Azure Active Directory e che ad esso non siano applicati oggetti Criteri di gruppo (GPO).
      > 
      > 
   4. Viene visualizzata una finestra di dialogo. Immettere le credenziali di dominio nel formato specificato. Fare clic sull’icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Le credenziali di dominio vengono verificate. Se le credenziali non sono valide, viene visualizzato un messaggio di errore.
      
       ![credentials](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)
   5. Fare clic su **Apply**. Si applicano e convalidano così le impostazioni del dispositivo.
7. Configurare il server proxy Web (facoltativo). Sebbene la configurazione del proxy Web sia facoltativa, tenere presente che se si utilizza un proxy Web, è possibile configurarlo solo qui.
   
    ![configurare il proxy Web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)
   
    Nella pagina **Proxy Web** :
   
   1. Fornire l'**URL proxy Web** in questo formato: *http://indirizzo IP-host* o *FDQN:Numero della porta*. Notare che gli URL HTTPS non sono supportati.
   2. Specificare **Autenticazione** come **Basic** o **Nessuna**.
   3. Se si usa l'autenticazione, è necessario fornire anche **Nome utente** e **Password**.
   4. Fare clic su **Apply**. Le impostazioni proxy Web configurate vengono così convalidate e applicate.
8. Configurare le impostazioni ora per il dispositivo, ad esempio il fuso orario e i server NTP primari e secondari (facoltativo). I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.
   
    ![Impostazioni ora](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)
   
    Nella pagina **Impostazioni ora** :
   
   1. Nell'elenco a discesa, selezionare il **Fuso orario** in base alla posizione geografica in cui viene distribuito il dispositivo. Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.
   2. Specificare un **Server NTP primario** per il dispositivo o accettare il valore predefinito di time.windows.com. Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.
   3. Facoltativamente, specificare un **Server NTP secondario** per il dispositivo.
   4. Fare clic su **Apply**. Le impostazioni ora configurate vengono così convalidate e applicate.
9. Configurare le impostazioni del cloud per il dispositivo. In questo passaggio, viene completata la configurazione del dispositivo locale, quindi si registra il dispositivo con il servizio StorSimple Manager.
   
   1. Immettere la **chiave di registrazione del servizio** ottenuta al **Passaggio 2: Ottenere la chiave di registrazione del servizio** in [Distribuire l'array virtuale StorSimple: preparare il portale](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Se questo non è il primo dispositivo che si registra con questo servizio, è necessario fornire la **Chiave DEK del servizio**. Questa chiave viene richiesta con la chiave di registrazione del servizio per registrare altri dispositivi con il servizio StorSimple Manager. Per altre informazioni, fare riferimento a come ottenere la [chiave DEK del servizio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) nell'interfaccia utente Web locale.
   3. Fare clic su **Register**. Il dispositivo viene così riavviato. È necessario attendere 2-3 minuti per la corretta registrazione del dispositivo. Dopo avere riavviato il dispositivo, si passa alla pagina di accesso.
      
      ![Registrare il dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)
10. Tornare al portale di Azure classico. Nella pagina **Dispositivi** , verificare che il dispositivo sia connesso correttamente al servizio controllando lo stato. Lo stato del dispositivo deve essere **Attivo**.
    
    ![Pagina Dispositivi](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Passaggio 2: Completare l'installazione del dispositivo richiesta
Per completare la configurazione dispositivo del dispositivo StorSimple, è necessario:

* Selezionare un account di archiviazione da associare al dispositivo.
* Scegliere le impostazioni di crittografia per i dati inviati al cloud.

Eseguire i passaggi seguenti nel portale di Azure classico per completare l'installazione richiesta del dispositivo.

#### <a name="to-complete-the-minimum-device-setup"></a>Per completare l'installazione minima del dispositivo
1. Nella pagina **Dispositivi** selezionare il dispositivo appena creato. Il dispositivo viene visualizzato come **Attivo**. Fare clic sulla freccia accanto al nome del dispositivo e quindi fare clic su **Avvio rapido**.
   
    ![Pagina Dispositivi](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)
2. Fare clic su **installazione dispositivo completata** per avviare la configurazione guidata del dispositivo.
   
    ![Configurazione guidata del dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)
3. Nella configurazione guidata del dispositivo, nella pagina **Impostazioni di base** eseguire le seguenti operazioni:
   
   1. Specificare un account di archiviazione da usare con il dispositivo. In questa sottoscrizione, è possibile selezionare un account di archiviazione esistente nell'elenco a discesa o specificare **Aggiungi elementi** per scegliere un account da una sottoscrizione diversa.
   2. Definire le impostazioni di crittografia per tutti i dati inattivi da inviare al cloud. (StorSimple usa la crittografia AES-256.) Per crittografare i dati, selezionare la casella di controllo **Abilitare la crittografia per l'archiviazione cloud**. Immettere una crittografia di archiviazione cloud di 32 caratteri. Immettere nuovamente la chiave per confermarla.
   3. Fare clic sull’icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).
      
      ![Impostazioni di base](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)
      
      Le impostazioni vengono così aggiornate. Se le impostazioni sono state aggiornate correttamente, il pulsante di installazione completa del dispositivo non è disponibile. Si ritornerà alla pagina **Avvio rapido** del dispositivo.                                                        

> [!NOTE]
> È possibile modificare tutte le altre impostazioni del dispositivo in qualsiasi momento effettuando l’accesso alla pagina **Configura** .
> 
> 

## <a name="step-3-add-a-volume"></a>Passaggio 3: Aggiungere un volume
Eseguire i passaggi seguenti nel portale di Azure classico per creare un volume.

#### <a name="to-create-a-volume"></a>Per creare un volume
1. Nella pagina **Avvio rapido** del dispositivo fare clic su **Aggiungi un volume**. Verrà avviata la procedura guidata Aggiungi volume.
2. Nella procedura guidata Aggiungi un volume, in **Impostazioni di base**, procedere come segue:
   
   1. Fornire un nome univoco per il volume. Il nome deve essere una stringa contenente da 3 a 127 caratteri.
   2. Fornire una descrizione per il volume. La descrizione consente di identificare i proprietari del volume.
   3. Selezionare un tipo di utilizzo per il volume. Il tipo di utilizzo è disponibile come **Volume a livelli** o **Volume aggiunto in locale**. L'impostazione predefinita è **Volume a livelli**. Per carichi di lavoro che richiedono garanzie locali, latenze basse e prestazioni di livello superiore, selezionare **Volume** **aggiunto in locale**. Per tutti gli altri dati, selezionare **Volume** **a livelli**.
      
       Per un volume aggiunto in locale viene eseguito il thick provisioning per garantire che i dati primari nel volume rimangano nel dispositivo e non si spostino sul cloud. Se si crea un volume aggiunto in locale, il dispositivo cercherà lo spazio disponibile nei livelli locali per il provisioning di un volume delle dimensioni richieste. La creazione di un volume aggiunto in locale può comportare la distribuzione dei dati esistenti dal dispositivo al cloud, aumentando il tempo necessario per la creazione del volume. Il tempo totale dipende dalle dimensioni del volume di cui è stato eseguito il provisioning, dalla larghezza di banda di rete disponibile e dai dati sul dispositivo.
      
       Al contrario, un volume a livelli viene sottoposto a thin provisioning e può essere creato molto velocemente. Quando si crea un volume a livelli, viene eseguito il provisioning di circa il 10% dello spazio a livello locale e del 90% dello spazio nel cloud. Ad esempio, se si esegue il provisioning di un volume da 1 TB, 100 GB si trovano nello spazio locale e 900 GB vengono usati nel cloud quando i dati sono disposti a livelli. Questo implica che, se si esaurisce tutto lo spazio locale nel dispositivo, non è possibile eseguire il provisioning di una condivisione a livelli (perché il 10% non è disponibile).
   4. Specificare la capacità sottoposta a provisioning per il volume. Notare che la capacità specificata deve essere inferiore rispetto alla capacità disponibile. Se si sta creando un volume a livelli, la dimensione deve essere compresa tra 500 GB e 5 TB. Per un volume aggiunto in locale, specificare una dimensione di volume tra 50 GB e 500 GB. Usare la capacità disponibile come guida per il provisioning di un volume. Se la capacità locale disponibile è di 0 GB, non è consentito eseguire il provisioning di un volume aggiunto in locale o a livelli.
      
       ![Impostazioni di base](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)
   5. Fare clic sull'icona freccia  ![Icona freccia](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) per passare alla pagina successiva.
3. Nella pagina **Impostazioni aggiuntive** , aggiungere un nuovo record di controllo di accesso (ACR):
   
   1. Fornire un **Nome** per l'ACR.
   2. In **Nome iniziatore iSCSI**, fornire il nome qualificato iSCSI (IQN) dell'host di Windows. Se non è disponibile un IQN, passare all' [Appendice A: Ottenere il nome qualificato iSCSI di un host di Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   3. È consigliabile abilitare un backup predefinito tramite la selezione della casella di controllo **Abilita un criterio di backup predefinito per questo volume** . Il backup predefinito creerà un criterio eseguito alle 22:30 di ogni giorno (ora del dispositivo) e creerà uno spapshot nel cloud del volume.
      
       ![Impostazioni aggiuntive](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)
   4. Fare clic sull’icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Si avvia così il processo di creazione del volume. Viene visualizzato un messaggio di stato simile al seguente.
      
       ![messaggio di stato](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)
      
       Verrà creato un volume con le impostazioni specificate. Per impostazione predefinita, il monitoraggio e il backup vengono abilitati per il volume.
   5. Per verificare la creazione del volume, passare alla pagina **Volumi** . Il volume deve essere visualizzato nell'elenco.
      
       ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Passaggio 4: Montare, inizializzare e formattare un volume
Eseguire i passaggi seguenti per montare, inizializzare e formattare i volumi StorSimple in un host Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Per montare, avviare e formattare un volume
1. Avviare l'iniziatore iSCSI di Microsoft.
2. Nella scheda **Individuazione** della finestra delle **proprietà dell'iniziatore iSCSI** fare clic su **Individua portale**.
   
    ![Individua portale](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)
3. Nella finestra di dialogo **Individua portale destinazione** specificare l'indirizzo IP dell'interfaccia di rete abilitata per iSCSI e quindi fare clic su **OK**.
   
    ![Indirizzo IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)
4. Nella scheda **Destinazioni** della finestra delle **proprietà dell'iniziatore iSCSI** individuare **Destinazioni individuate**. (Ogni volume è una destinazione individuata.) Lo stato del dispositivo deve apparire come **Inattivo**.
   
    ![destinazioni individuate](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)
5. Selezionare un dispositivo di destinazione e quindi fare clic su **Connetti**. Dopo aver connesso il dispositivo, lo stato deve essere modificato in **Connesso**. Per altre informazioni sull'uso dell'iniziatore iSCSI Microsoft, vedere [Installing and Configuring Microsoft iSCSI Initiator][1] (Installazione e configurazione dell'iniziatore iSCSI Microsoft).
   
    ![selezionare il dispositivo di destinazione](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)
6. Nell'host di Windows, premere il tasto Logo Windows + X, quindi fare clic su **Esegui**.
7. Nella finestra di dialogo **Esegui** digitare **Diskmgmt.msc**. Fare clic su **OK**. Verrà visualizzata la finestra di dialogo **Gestione disco**. Nel riquadro destro vengono visualizzati i volumi sul proprio host.
8. Nella finestra **Gestione disco** , i volumi montati verranno visualizzati come indicato nella figura seguente. Fare clic con il pulsante destro del mouse sul volume individuato (fare clic sul nome del disco) e fare clic su **Online**.
   
    ![Gestione disco](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)
9. Fare clic con il pulsante destro del mouse e selezionare **Inizializza disco**.
   
    ![inizializzare disco 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)
10. Nella finestra di dialogo selezionare i dischi da inizializzare e quindi fare clic su **OK**.
    
    ![inizializzare disco 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)
11. Si avvia così la creazione guidata nuovo volume semplice. Selezionare una dimensione disco e quindi fare clic su **Avanti**.
    
    ![procedura guidata nuovo volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)
12. Assegnare una lettera di unità al volume e quindi fare clic su **Avanti**.
    
    ![procedura guidata nuovo volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)
13. Immettere i parametri per formattare il volume. **In Windows Server, è supportato solo NTFS.** Impostare le unità di allocazione su 64K. Specificare un'etichetta per il volume. È una procedura consigliata che questo nome sia identico al nome del volume specificato nel dispositivo virtuale StorSimple. Fare clic su **Avanti**.
    
    ![procedura guidata nuovo volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)
14. Controllare i valori del volume e quindi fare clic su **Fine**.
    
    ![procedura guidata nuovo volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)
    
    I volumi vengono visualizzati come **Online** on the **Gestione disco** .
    
    ![volumi online](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come usare l'interfaccia utente Web locale per amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Appendice A: Ottenere il nome qualificato iSCSI di un host di Windows Server
Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI (IQN) di un host di Windows che esegue Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Per ottenere il nome qualificato iSCSI di un host di Windows
1. Avviare l'iniziatore iSCSI di Microsoft sull’host di Windows.
2. Nella scheda **Configurazione** della finestra delle **proprietà dell'iniziatore iSCSI** selezionare e copiare la stringa dal campo **Nome iniziatore**.
   
    ![Proprietà dell'iniziatore iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)
3. Salvare la stringa.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx






<!--HONumber=Dec16_HO2-->


