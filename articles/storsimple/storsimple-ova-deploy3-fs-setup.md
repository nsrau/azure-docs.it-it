<properties
   pageTitle="Distribuire StorSimple Virtual Array 3: configurare il dispositivo virtuale come file server"
   description="Questa terza esercitazione sulla distribuzione di StorSimple Virtual Array illustra come configurare un dispositivo virtuale come file server."
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
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# Distribuire StorSimple Virtual Array: configurare come file server

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## Introduzione 

Le informazioni contenute in questo articolo si applicano solo a Microsoft Azure StorSimple Virtual Array (noto anche come dispositivo virtuale locale StorSimple o dispositivo virtuale StorSimple) che esegue la versione di disponibilità generale (GA) di marzo 2016. Questo articolo illustra come eseguire l'installazione iniziale, registrare il file server StorSimple, completare l'installazione del dispositivo, quindi creare condivisioni SMB a cui connettersi. Questo è l'ultimo articolo della serie di esercitazioni per la distribuzione necessarie per la distribuzione completa dell'array virtuale come file server o server iSCSI.

Il completamento del processo di installazione e configurazione può richiedere circa 10 minuti.


## Prerequisiti di installazione

Prima di configurare e installare il dispositivo virtuale StorSimple, si deve:

-   Eseguire il provisioning di un dispositivo virtuale e connettersi come illustrato nell'articolo relativo al [provisioning di StorSimple Virtual Array in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o al [provisioning di StorSimple Virtual Array in VMware](storsimple-ova-deploy2-provision-vmware.md).

-   Avere ottenuto la chiave di registrazione del servizio dal servizio StorSimple Manager creato per gestire i dispositivi virtuali StorSimple. Per altre informazioni, vedere [Passaggio 2: Ottenere la chiave di registrazione del servizio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) per StorSimple Virtual Array.

-   Se questo è il secondo dispositivo virtuale o l'ulteriore dispositivo registrato con un servizio StorSimple Manager esistente, è necessario disporre della chiave DEK del servizio. Questa chiave viene generata nel momento in cui avviene la corretta registrazione del primo dispositivo con questo servizio. Se si smarrisce la chiave, vedere come [ottenere la chiave DEK del servizio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) per StorSimple Virtual Array.

## Installazione passo per passo

Seguire passo per passo le istruzioni riportate sotto per installare e configurare il dispositivo virtuale StorSimple.

## Passaggio 1: Completare l'installazione dell'interfaccia utente Web locale e registrare il dispositivo 


#### Per completare l'installazione e registrare il dispositivo

1.  Aprire una finestra del browser e connettersi all'interfaccia utente Web locale. Digitare:	

    `https://<ip-address of network interface>`

	Usare l'URL di connessione indicata nel passaggio precedente. Viene visualizzato un errore in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. Fare clic su **Passa a questa pagina Web**.

	![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Accedere all'interfaccia utente Web del dispositivo virtuale come **StorSimpleAdmin**. Immettere la password amministratore del dispositivo modificata in Passaggio 3: Avviare il dispositivo virtuale nell'articolo relativo all'[esecuzione del provisioning di StorSimple Virtual Array in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o all'[esecuzione del provisioning di StorSimple Virtual Array in VMware](storsimple-ova-deploy2-provision-vmware.md).

	![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Si passa così alla pagina **Home**. Questa pagina illustra le varie impostazioni necessarie per configurare e registrare il dispositivo virtuale con il servizio StorSimple Manager. Notare che le **Impostazioni di rete**, le **Impostazioni proxy Web** e le **Impostazioni ora** sono facoltative. Le uniche impostazioni obbligatorie sono le **Impostazioni del dispositivo** e le **Impostazioni cloud**.

	![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  Nella pagina delle **Impostazioni di rete** in **Interfacce di rete** DATA 0 viene configurato automaticamente per l'utente. Ogni interfaccia di rete è impostata come predefinita per ottenere l'indirizzo IP automaticamente (DHCP). Di conseguenza, un indirizzo IP, una subnet e un gateway vengono assegnati automaticamente (sia per IPv4 che per IPv6).

	![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

	Se si aggiunge più di un'interfaccia di rete durante il provisioning del dispositivo, è possibile configurarle tutte qui. Si noti che è possibile configurare l'interfaccia di rete come solo IPv4 o come IPv4 e IPv6. Le configurazioni solo IPv6 non sono supportate.

1.  I server DNS sono necessari perché vengono usati quando il dispositivo tenta di comunicare con i provider del servizio di archiviazione cloud o per risolvere il dispositivo in base al nome quando configurato come file server. Nella pagina **Impostazioni di rete** in **Server DNS**:

    1.  Un server DNS primario e secondario viene configurato automaticamente. Se si sceglie di configurare gli indirizzi IP statici, è possibile specificare i server DNS. Per una disponibilità elevata, si consiglia di configurare un server DNS primario e uno secondario.

    2.  Fare clic su **Apply**. Si applicano e convalidano così le impostazioni di rete.

2.  Nella pagina **Impostazioni del dispositivo**:

    1.  Assegnare un **Nome** univoco al dispositivo. Questo nome può avere da 1 a 15 caratteri e contenere lettere, numeri e trattini.

    2.  Fare clic sull'icona **File server** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) per il **Tipo** di dispositivo che si sta creando. Un file server consente la creazione di cartelle condivise.

    3.  Il dispositivo è un file server, quindi è necessario aggiungerlo a un dominio. Immettere un **Nome di dominio**.

	1.  Fare clic su **Apply**.

2.  Viene visualizzata una finestra di dialogo. Immettere le credenziali di dominio nel formato specificato. Fare clic sull'icona del segno di spunta Le credenziali di dominio vengono verificate. Se le credenziali non sono valide, viene visualizzato un messaggio di errore.

	![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Fare clic su **Apply**. Si applicano e convalidano così le impostazioni del dispositivo.

	![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

	> [AZURE.NOTE]
	> 
	> Assicurarsi che l'array virtuale sia nella propria unità organizzativa (OU) per Active Directory e che ad esso non siano applicati o ereditati oggetti Criteri di gruppo (GPO). Tramite Criteri di gruppo è possibile installare applicazioni quali software antivirus in StorSimple Virtual Array. L'installazione di software aggiuntivo non è supportata e può causare il danneggiamento dei dati.

1.  Configurare il server proxy Web (facoltativo). Sebbene la configurazione del proxy Web sia facoltativa, tenere presente che se si utilizza un proxy Web, è possibile configurarlo solo qui.

	![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

	Nella pagina **Proxy Web**:

	1.  Fornire l'**URL proxy Web** in questo formato: *http://&lt;host-IP address or FDQN&gt;:Port number*. Notare che gli URL HTTPS non sono supportati.

	2.  Specificare **Autenticazione** come **Basic** o **Nessuna**.

	3.  Se si usa l'autenticazione, è necessario anche fornire **Nome utente** e **Password**.

	4.  Fare clic su **Apply**. Le impostazioni proxy Web configurate vengono così convalidate e applicate.

1.  Configurare le impostazioni ora per il dispositivo, ad esempio il fuso orario e i server NTP primari e secondari (facoltativo). I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.

	![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

	Nella pagina **Impostazioni ora**:

	1.  Nell'elenco a discesa, selezionare il **Fuso orario** in base alla posizione geografica in cui viene distribuito il dispositivo. Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.

	2.  Specificare un **Primary NTP server** (Server NTP primario) per il dispositivo o accettare il valore predefinito di time.windows.com. Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.

	3.  Facoltativamente, specificare un **Secondary NTP server** (Server NTP secondario) per il dispositivo.

	4.  Fare clic su **Apply**. Le impostazioni ora configurate vengono così convalidate e applicate.

1.  Configurare le impostazioni del cloud per il dispositivo. In questo passaggio, viene completata la configurazione del dispositivo locale, quindi si registra il dispositivo con il servizio StorSimple Manager.

    1.  Immettere la **Chiave di registrazione del servizio** ottenuta in [Passaggio 2: Ottenere la chiave di registrazione del servizio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) per l'array virtuale StorSimple.

    2.  Se questo è il primo dispositivo che si registra con questo servizio, ignorare questo passaggio e andare al passaggio successivo. Se questo non è il primo dispositivo che si registra con questo servizio, è necessario fornire la **Chiave DEK del servizio**. Questa chiave viene richiesta con la chiave di registrazione del servizio per registrare altri dispositivi con il servizio StorSimple Manager. Per altre informazioni, fare riferimento a come ottenere la [chiave DEK del servizio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) nell'interfaccia utente Web locale.

    3.  Fare clic su **Register**. Il dispositivo viene così riavviato. È necessario attendere 2-3 minuti per la corretta registrazione del dispositivo. Dopo avere riavviato il dispositivo, si passa alla pagina di accesso.

		![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
	

1.  Tornare al portale di Azure classico. Nella pagina **Dispositivi**, verificare che il dispositivo sia connesso correttamente al servizio controllando lo stato. Lo stato del dispositivo deve essere **Attivo**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## Passaggio 2: Completare l'installazione del dispositivo richiesta

Per completare la configurazione dispositivo del dispositivo StorSimple, è necessario:

-   Selezionare un account di archiviazione da associare al dispositivo.

-   Scegliere le impostazioni di crittografia per i dati inviati al cloud.

Eseguire la procedura seguente nel [portale di Azure classico](https://manage.windowsazure.com/) per completare l'installazione richiesta del dispositivo.

#### Per completare l'installazione minima del dispositivo

1.  Nella pagina **Dispositivi**, selezionare il dispositivo appena creato. Il dispositivo viene visualizzato come **Attivo**. Fare clic sulla freccia in base al nome del dispositivo, quindi fare clic su **Avvio rapido**.

2.  Fare clic su **installazione dispositivo completata** per avviare la configurazione guidata del dispositivo.

3.  Nella configurazione guidata del dispositivo, nella pagina **Impostazioni di base** eseguire le seguenti operazioni:

	1.  Specificare un account di archiviazione da usare con il dispositivo. È possibile selezionare un account di archiviazione esistente in questa sottoscrizione nell'elenco a discesa o specificare **Aggiungi elementi** per scegliere un account da una sottoscrizione diversa.

	2.  Definire le impostazioni di crittografia per tutti i dati inattivi (crittografia AES) da inviare al cloud. Per crittografare i dati, selezionare la casella combinata per **abilitare la chiave di crittografia per l'archiviazione cloud**. Immettere una crittografia di archiviazione cloud di 32 caratteri. Immettere nuovamente la chiave per confermarla. Una chiave AES a 256 bit viene usata con la chiave definita dall'utente per la crittografia.

	3.  Fare clic sull’icona del segno di spunta ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

		![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

Le impostazioni vengono così aggiornate. Se le impostazioni sono state aggiornate correttamente, il pulsante di installazione completa del dispositivo viene disabilitato. Si ritornerà alla pagina **Avvio rapido** del dispositivo.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> È possibile modificare tutte le altre impostazioni del dispositivo in qualsiasi momento effettuando l’accesso alla pagina **Configura**.

## Passaggio 3: Aggiungere una condivisione

Eseguire la procedura seguente nel [portale di Azure classico](https://manage.windowsazure.com/) per creare una condivisione.

#### Per creare una condivisione

1.  Nella pagina **Avvio rapido** del dispositivo, fare clic su **Aggiungi una condivisione**. Si avvia così la procedura guidata per l'aggiunta della condivisione.

	![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  Nella pagina**Impostazioni di base**, eseguire le operazioni seguenti:

    1.  Specificare un nome univoco per la condivisione. Il nome deve essere una stringa contenente da 3 a 127 caratteri.

    2.  (Facoltativo) Fornire una descrizione per la condivisione. La descrizione consente di identificare i proprietari della condivisione.

    3.  Selezionare un tipo di utilizzo per la condivisione. Il tipo di utilizzo è disponibile come **Condivisione a livelli** o **Aggiunto in locale** dove quella a livelli è l'impostazione predefinita. Per carichi di lavoro che richiedono garanzie locali, latenze basse e prestazioni di livello superiore, selezionare la condivisione **Aggiunto in locale**. Per tutti gli altri dati, selezionare una **Condivisione a livelli**.

	Una condivisione aggiunta in locale viene sottoposta a thick provisioning per garantire che i dati primari nella condivisione rimangano a livello locale nel dispositivo e non a livello cloud. D'altra parte, una condivisione a livelli viene sottoposta a thin provisioning. Quando si crea una condivisione a livelli, viene eseguito il provisioning del 10% dello spazio a livello locale e del 90% dello spazio nel cloud. Ad esempio, se si esegue il provisioning di un volume di 1 TB, 100 GB si trovano nello spazio locale e 900 GB vengono usati nel cloud quando i dati sono suddivisi in livelli. Questo implica che, se si esaurisce tutto lo spazio locale nel dispositivo, non è possibile eseguire il provisioning di una condivisione a livelli.

1.  Specificare la capacità sottoposta a provisioning per la condivisione. Notare che la capacità specificata deve essere inferiore rispetto alla capacità disponibile. Se si usa una condivisione a livelli, le dimensioni della condivisione devono essere comprese tra 500 GB e 20 TB. Per una condivisione aggiunta in locale, specificare una dimensione di condivisione tra 50 GB e 2 TB. Usare la capacità disponibile come guida per il provisioning di una condivisione. Se la capacità locale disponibile è di 0 GB, non è consentito eseguire il provisioning delle condivisioni locali o a livelli.

	![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Fare clic sull'icona freccia ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) per passare alla pagina successiva.

1.  Nella pagina **Impostazioni aggiuntive**, assegnare le autorizzazioni all'utente o al gruppo che avrà accesso a questa condivisione. Specificare il nome dell'utente o del gruppo di utenti nel formato *<john@contoso.com>*. Si consiglia di usare un gruppo di utenti (anziché un singolo utente) per consentire ai privilegi amministratore di accedere a queste condivisioni. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificare le autorizzazioni con Esplora risorse.

	![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Fare clic sull’icona del segno di spunta ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Viene creata una condivisione con le impostazioni specificate. Per impostazione predefinita, il monitoraggio e il backup vengono abilitati per la condivisione.

## Passaggio 4: Connettersi alla condivisione

A questo punto, è necessario connettersi alla condivisione o alle condivisioni create nel passaggio precedente. Eseguire questi passaggi nell'host di Windows Server.

#### Per connettersi alla condivisione

1.  Premere ![](./media/storsimple-ova-deploy3-fs-setup/image22.png)+R. Nella finestra Esegui, specificare *\<file server name>* come percorso, sostituendo il *nome del file server* con il nome del dispositivo che è stato assegnato al file server. Fare clic su **OK**.

	![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  Si apre così Esplora risorse. Ora devono essere visualizzate le condivisioni create come cartelle. Selezionare e fare doppio clic su una condivisione (cartella) per visualizzarne il contenuto.

	![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  È ora possibile aggiungere file a queste condivisioni ed eseguire un backup.

![icona video](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **Video disponibile**

Guardare il video per scoprire in che modo configurare e registrare StorSimple Virtual Array come file server.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## Passaggi successivi

Informazioni su come usare l'interfaccia utente Web locale per [amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0601_2016-->