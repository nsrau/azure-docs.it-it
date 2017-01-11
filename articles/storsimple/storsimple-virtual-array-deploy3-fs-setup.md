---
title: Distribuire un array virtuale Microsoft Azure StorSimple - Eseguire la configurazione come file server | Documentazione Microsoft
description: Questa terza esercitazione sulla distribuzione di StorSimple Virtual Array illustra come configurare un dispositivo virtuale come file server.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: ae076d52b532f5181868b2ab24ee8f2ffa887af7
ms.openlocfilehash: 5d250042d47b4dfdb6d6e95b93384b1acb323816

---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Distribuire StorSimple Virtual Array: configurare come file server
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduzione
Questo articolo illustra come eseguire l'installazione iniziale, registrare il file server StorSimple, completare l'installazione del dispositivo, quindi creare condivisioni SMB a cui connettersi. Questo è l'ultimo articolo della serie di esercitazioni per la distribuzione necessarie per la distribuzione completa dell'array virtuale come file server o server iSCSI.

Il completamento del processo di installazione e configurazione può richiedere circa 10 minuti. Le informazioni in questo articolo si applicano solo alla distribuzione dell'array virtuale StorSimple. Per la distribuzione di dispositivi StorSimple serie 8000, vedere [Distribuire un dispositivo StorSimple serie 8000 con Aggiornamento 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Prerequisiti di installazione
Prima di configurare e installare l'array virtuale StorSimple, si deve:

* Eseguire il provisioning di un array virtuale e connettersi come illustrato nell'articolo relativo al [provisioning dell'array virtuale StorSimple in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) o al [provisioning dell'array virtuale StorSimple in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Avere ottenuto la chiave di registrazione del servizio dal servizio Gestione dispositivi StorSimple creato per gestire gli array virtuali StorSimple. Per altre informazioni, vedere [Passaggio 2: Ottenere la chiave di registrazione del servizio](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) per l'array virtuale StorSimple.
* Se questo è il secondo o l'ulteriore array virtuale registrato con un servizio Gestione dispositivi StorSimple esistente, è necessario disporre della chiave di crittografia del servizio. Questa chiave viene generata nel momento in cui avviene la corretta registrazione del primo dispositivo con questo servizio. Se si smarrisce la chiave, vedere come [ottenere la chiave DEK del servizio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) per StorSimple Virtual Array.

## <a name="step-by-step-setup"></a>Installazione passo per passo
Seguire passo per passo le istruzioni riportate sotto per installare e configurare l'array virtuale StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passaggio 1: Completare l'installazione dell'interfaccia utente Web locale e registrare il dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Per completare l'installazione e registrare il dispositivo
1. Aprire una finestra del browser e connettersi all'interfaccia utente Web locale. Digitare:
   
   `https://<ip-address of network interface>`
   
   Usare l'URL di connessione indicata nel passaggio precedente. Viene visualizzato un errore in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. Fare clic su **Passa a questa pagina Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Accedere all'interfaccia utente Web dell'array virtuale come **StorSimpleAdmin**. Immettere la password amministratore del dispositivo modificata in Passaggio 3: Avviare l'array virtuale nell'articolo relativo all'[esecuzione del provisioning di un array virtuale StorSimple in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) o all'[esecuzione del provisioning di un array virtuale StorSimple in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Si passa così alla **pagina iniziale**. Questa pagina illustra le varie impostazioni necessarie per configurare e registrare l'array virtuale con il servizio Gestione dispositivi StorSimple. Le **Impostazioni di rete**, le **Impostazioni proxy Web** e le **Impostazioni ora** sono facoltative. Le uniche impostazioni obbligatorie sono le **Impostazioni del dispositivo** e le **Impostazioni cloud**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Nella pagina delle **Impostazioni di rete** in **Interfacce di rete** DATA 0 viene configurato automaticamente per l'utente. Ogni interfaccia di rete è impostata come predefinita per ottenere l'indirizzo IP automaticamente (DHCP). Di conseguenza, un indirizzo IP, una subnet e un gateway vengono assegnati automaticamente (sia per IPv4 che per IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Se si aggiunge più di un'interfaccia di rete durante il provisioning del dispositivo, è possibile configurarle tutte qui. Si noti che è possibile configurare l'interfaccia di rete come solo IPv4 o come IPv4 e IPv6. Le configurazioni solo IPv6 non sono supportate.
5. I server DNS sono necessari perché vengono usati quando il dispositivo tenta di comunicare con i provider del servizio di archiviazione cloud o per risolvere il dispositivo in base al nome quando configurato come file server. Nella pagina **Impostazioni di rete** in **Server DNS**:
   
   1. Un server DNS primario e uno secondario vengono configurati automaticamente. Se si sceglie di configurare gli indirizzi IP statici, è possibile specificare i server DNS. Per una disponibilità elevata, si consiglia di configurare un server DNS primario e uno secondario.
   2. Fare clic su **Applica** per applicare e convalidare così le impostazioni di rete.
6. Nella pagina **Impostazioni del dispositivo** :
   
   1. Assegnare un **Nome** univoco al dispositivo. Questo nome può avere da 1 a 15 caratteri e contenere lettere, numeri e trattini.
   2. Fare clic sull'icona ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png)**File server** per il **Tipo** di dispositivo che si sta creando. Un file server consente la creazione di cartelle condivise.
   3. Il dispositivo è un file server, quindi è necessario aggiungerlo a un dominio. Immettere un **Nome di dominio**.
   4. Fare clic su **Apply**.
7. Viene visualizzata una finestra di dialogo. Immettere le credenziali di dominio nel formato specificato. Fare clic sull'icona del segno di spunta Le credenziali di dominio vengono verificate. Se le credenziali non sono valide, viene visualizzato un messaggio di errore.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Fare clic su **Apply**. Si applicano e convalidano così le impostazioni del dispositivo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Assicurarsi che l'array virtuale sia nella propria unità organizzativa (OU) per Active Directory e che ad esso non siano applicati o ereditati oggetti Criteri di gruppo (GPO). Tramite Criteri di gruppo è possibile installare applicazioni quali software antivirus in StorSimple Virtual Array. L'installazione di software aggiuntivo non è supportata e può causare il danneggiamento dei dati. 
   > 
   > 
9. Configurare il server proxy Web (facoltativo). Sebbene la configurazione del proxy Web sia facoltativa, tenere presente che se si utilizza un proxy Web, è possibile configurarlo solo qui.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Nella pagina **Proxy Web** :
   
   1. Fornire l'**URL proxy Web** in questo formato: *http://&lt;indirizzo IP-host o FDQN&gt;:Numero della porta*. Notare che gli URL HTTPS non sono supportati.
   2. Specificare **Autenticazione** come **Basic** o **Nessuna**.
   3. Se si usa l'autenticazione, è necessario anche fornire **Nome utente** e **Password**.
   4. Fare clic su **Apply**. Le impostazioni proxy Web configurate vengono così convalidate e applicate.
10. Configurare le impostazioni ora per il dispositivo, ad esempio il fuso orario e i server NTP primari e secondari (facoltativo). I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Nella pagina **Impostazioni ora** :
    
    1. Nell'elenco a discesa, selezionare il **Fuso orario** in base alla posizione geografica in cui viene distribuito il dispositivo. Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.
    2. Specificare un **Server NTP primario** per il dispositivo o accettare il valore predefinito di time.windows.com. Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.
    3. Facoltativamente, specificare un **Server NTP secondario** per il dispositivo.
    4. Fare clic su **Apply**. Le impostazioni ora configurate vengono così convalidate e applicate.
11. Configurare le impostazioni del cloud per il dispositivo. In questo passaggio viene completata la configurazione del dispositivo locale, quindi si registra il dispositivo con il servizio Gestione dispositivi StorSimple.
    
    1. Immettere la **Chiave di registrazione del servizio** ottenuta in [Passaggio 2: Ottenere la chiave di registrazione del servizio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) per l'array virtuale StorSimple.
    2. Se questo non è il primo dispositivo che si registra con questo servizio, è necessario fornire la **chiave di crittografia dati del servizio**. Copiare questo codice e salvarlo in un luogo sicuro. Questa chiave viene richiesta con la chiave di registrazione del servizio per registrare altri dispositivi con il servizio Gestione dispositivi StorSimple. 
       
       Se questo non è il primo dispositivo che si registra con questo servizio, è necessario fornire la chiave di crittografia dati del servizio. Per altre informazioni, fare riferimento a come ottenere la [chiave DEK del servizio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) nell'interfaccia utente Web locale.
    3. Fare clic su **Register**. Il dispositivo viene così riavviato. È necessario attendere 2-3 minuti per la corretta registrazione del dispositivo. Dopo avere riavviato il dispositivo, si passa alla pagina di accesso.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Tornare al portale di Azure. Passare a **Tutte le risorse**, cercare il servizio Gestione dispositivi StorSimple.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Nell'elenco filtrato selezionare il servizio Gestione dispositivi StorSimple e quindi passare a **Gestione > Dispositivi**. Nel pannello **Dispositivi** verificare che il dispositivo sia connesso correttamente al servizio controllando che lo stato sia **Pronto per la configurazione**.
    
    ![Configurare un file server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Passaggio 2: Configurare il dispositivo come file server
Eseguire la procedura seguente nel [portale di Azure](https://portal.azure.com/) per completare l'installazione richiesta del dispositivo.

#### <a name="to-configure-the-device-as-file-server"></a>Per configurare il dispositivo come file server
1. Passare al servizio Gestione dispositivi StorSimple, quindi a **Gestione > Dispositivi**. Nel pannello **Dispositivi** selezionare il dispositivo appena creato. Il dispositivo viene visualizzato come **Pronto per la configurazione**.
   
   ![Configurare un file server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Fare clic sul dispositivo e verrà visualizzato un messaggio di intestazione che indica che il dispositivo è pronto per la configurazione.
   
    ![Configurare un file server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Fare clic su **Configura** sulla barra dei comandi. Verrà visualizzato il pannello **Configura**. Nel pannello **Configura** eseguire le operazioni seguenti:
   
    1. Il nome del file server viene inserito automaticamente.
    
    2. Assicurarsi che la crittografia per l'archiviazione cloud sia impostata su **Abilitata**. In questo modo verranno crittografati tutti i dati inviati al cloud. 
    
    3. Una chiave AES a 256 bit viene usata con la chiave definita dall'utente per la crittografia. Specificare una chiave di 32 caratteri e quindi immetterla nuovamente per confermarla. Registrare la chiave in un'app di gestione delle chiavi per riferimento futuro.
    
    4. Fare clic su **Configura le impostazioni necessarie** per specificare le credenziali dell'account di archiviazione da usare con il dispositivo. Se non è configurata alcuna credenziale, fare clic su **Aggiungi nuova**.
   
    ![Configurare un file server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Nel pannello **Aggiungi credenziali dell'account di archiviazione** eseguire le operazioni seguenti: 

    1. Scegliere la sottoscrizione corrente se l'account di archiviazione è nella stessa sottoscrizione del servizio. Specificare altro se l'account di archiviazione è esterno alla sottoscrizione del servizio. 
    
    2. Nell'elenco a discesa scegliere un account di archiviazione esistente. 
    
    3. Il percorso verrà popolato automaticamente in base all'account di archiviazione specificato. 
    
    4. Abilitare SSL per garantire un canale di comunicazione di rete sicuro tra il dispositivo e il cloud.
    
    5. Fare clic su **Aggiungi** per aggiungere questa credenziale dell'account di archiviazione. 
   
        ![Configurare un file server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Dopo aver creato correttamente la credenziale dell'account di archiviazione, il pannello **Configura** verrà aggiornato per visualizzare le credenziali dell'account di archiviazione specificato. Fare clic su **Configura**.
   
   ![Configurare un file server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Viene avviata la creazione di un file server. Dopo aver creato correttamente il file server, si riceverà la relativa notifica.
   
   ![Configurare un file server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Anche lo stato del dispositivo verrà modificato in **Online**.
   
   ![Configurare un file server](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   È possibile procedere all'aggiunta di una condivisione.

## <a name="step-3-add-a-share"></a>Passaggio 3: Aggiungere una condivisione
Eseguire i passaggi seguenti nel [portale di Azure](https://portal.azure.com/) per creare una condivisione.

#### <a name="to-create-a-share"></a>Per creare una condivisione
1. Selezionare il file server configurato nel passaggio precedente e fare clic su **...** (o sul pulsante destro del mouse). Nel menu di scelta rapida selezionare **Aggiungi condivisione**. In alternativa, è possibile fare clic su **+ Aggiungi condivisione** sulla barra dei comandi del dispositivo.
   
   ![Aggiungere una condivisione](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Specificare le impostazioni di condivisione seguenti:

    1. Un nome univoco per la condivisione. Il nome deve essere una stringa contenente da 3 a 127 caratteri.
    
    2. Una **Descrizione** facoltativa per la condivisione. La descrizione consente di identificare i proprietari della condivisione.
    
    3. Un **Tipo** per la condivisione. Il tipo è disponibile come **Condivisione a livelli** o **Aggiunto in locale** dove quella a livelli è l'impostazione predefinita. Per carichi di lavoro che richiedono garanzie locali, latenze basse e prestazioni di livello superiore, selezionare la condivisione **Aggiunto in locale** . Per tutti gli altri dati, selezionare una **Condivisione a livelli** .
    Una condivisione aggiunta in locale viene sottoposta a thick provisioning per garantire che i dati primari nella condivisione rimangano a livello locale nel dispositivo e non a livello cloud. D'altra parte, una condivisione a livelli viene sottoposta a thin provisioning. Quando si crea una condivisione a livelli, viene eseguito il provisioning del 10% dello spazio a livello locale e del 90% dello spazio nel cloud. Ad esempio, se si esegue il provisioning di un volume di 1 TB, 100 GB si trovano nello spazio locale e 900 GB vengono usati nel cloud quando i dati sono suddivisi in livelli. Questo implica che, se si esaurisce tutto lo spazio locale nel dispositivo, non è possibile eseguire il provisioning di una condivisione a livelli.
   
    4. Nel campo **Set default full permissions to** (Imposta le autorizzazioni complete predefinite su) assegnare le autorizzazioni all'utente o al gruppo che avrà accesso a questa condivisione. Specificare il nome dell'utente o del gruppo di utenti nel formato *john@contoso.com*. Si consiglia di usare un gruppo di utenti (anziché un singolo utente) per consentire ai privilegi amministratore di accedere a queste condivisioni. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificarle con Esplora file.
   
    5. Fare clic su **Aggiungi** per creare la condivisione. 
    
        ![Aggiungere una condivisione](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        Viene ricevuta una notifica che indica che la creazione della condivisione è in corso.
   
        ![Aggiungere una condivisione](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    Dopo aver creato la condivisione con le impostazioni specificate, il pannello **Condivisioni** verrà aggiornato per riflettere la nuova condivisione. Per impostazione predefinita, il monitoraggio e il backup vengono abilitati per la condivisione.
   
    ![Aggiungere una condivisione](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Passaggio 4: Connettersi alla condivisione
A questo punto, è necessario connettersi a una o più condivisioni create nel passaggio precedente. Eseguire questi passaggi nell'host di Windows Server connesso all'array virtuale StorSimple.

#### <a name="to-connect-to-the-share"></a>Per connettersi alla condivisione
1. Premere ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. Nella finestra Esegui, specificare *&#92;&#92;&lt;nome file server&gt;* come percorso, sostituendo il *nome file server* con il nome del dispositivo che è stato assegnato al file server. Fare clic su **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Si apre Esplora file. Ora devono essere visualizzate le condivisioni create come cartelle. Selezionare e fare doppio clic su una condivisione (cartella) per visualizzarne il contenuto.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. È ora possibile aggiungere file a queste condivisioni ed eseguire un backup.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come usare l'interfaccia utente Web locale per [amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO4-->


