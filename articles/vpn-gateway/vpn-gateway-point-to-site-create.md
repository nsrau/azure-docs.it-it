<properties
   pageTitle="Configurare una connessione VPN Point-to-Site a una rete virtuale di Azure | Microsoft Azure"
   description="Connettersi in modo sicuro alla rete virtuale di Azure mediante la creazione di una connessione VPN Point-to-Site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2015"
   ms.author="cherylmc"/>

# Configurare una connessione VPN Point-to-Site a una rete virtuale di Azure


>[AZURE.NOTE]Questo articolo si applica alle connessioni point-to-site per le reti virtuali create in modalità di distribuzione classica. In questo momento non sono supportate connessioni point-to-site a una rete virtuale creata in modalità di distribuzione di gestione risorse di Azure.

La procedura seguente illustrerà la procedura per creare una connessione point-to-site sicura a una rete virtuale. Sebbene la configurazione di una connessione point-to-site richieda più passaggi, è consigliata per avere una connessione protetta dal computer alla rete virtuale senza acquisire e configurare un dispositivo VPN. Esistono tre componenti principali per configurare una VPN Point-to-Site: la rete virtuale e il gateway VPN, i certificati usati per l'autenticazione e il client VPN usato per connettersi alla rete virtuale. L'ordine in cui è possibile configurare ciascuno di essi è importante, pertanto non saltare passaggi o andare troppo avanti.


1. [Creare una rete virtuale e un gateway VPN](#create-a-virtual-network-and-a-vpn-gateway).
2. [Creare i certificati](#create-your-certificates).
3. [Configurare il client VPN](#configure-your-vpn-client).

## Creare una rete virtuale e un gateway VPN



Una connessione point-to-site richiede una rete virtuale con un gateway con routing dinamico. La procedura seguente illustra la creazione di entrambi.

### Crea rete virtuale

1. Accedere al **Portale di Azure**.
1. Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.
1. Nella pagina **Virtual Network Details** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.
	- **Nome**: assegnare un nome alla rete virtuale. Ad esempio "VNetEast". Questo sarà il nome che è possibile fare riferimento quando si distribuiscono macchine virtuali e istanze PaaS in questa rete virtuale.
	- **Indirizzo**: la località è direttamente correlata alla posizione fisica (regione) in cui si desidera che le risorse (macchine virtuali) devono risiedere. Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.
1. Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.
	- **Server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non crea un server DNS, consente di specificare i server DNS che si desidera usare per la risoluzione dei nomi per la rete virtuale. Se si desidera utilizzare il servizio di risoluzione dei nomi predefinito di Azure, lasciare vuota questa sezione.
	- **Configura Point-To-Site VPN**: selezionare la casella di controllo.
1. Nella pagina **Connettività Point-To-Site** e specificare l'intervallo indirizzi IP dal quale i client VPN riceveranno un indirizzo IP durante la connessione. Esistono diverse regole riguardano gli intervalli di indirizzi che è possibile specificare. È molto importante verificare che l'intervallo specificato non si sovrapponga ad altri intervalli presenti nella rete locale.
1. Immettere le informazioni seguenti e quindi fare clic sulla freccia avanti.
 - **Spazio di indirizzi**: includono l'indirizzo IP iniziale e CIDR (conteggio indirizzi).
 - **Aggiungi spazio di indirizzi**: aggiungere solo se è necessario per la progettazione della rete.
1. Nella pagina **Spazi di indirizzi della rete virtuale** è possibile immettere lo spazio di indirizzi che si desidera utilizzare per la rete virtuale. Si tratta di indirizzi IP dinamici (DIP) che verranno assegnati per le macchine virtuali e altre istanze del ruolo da distribuire a questa rete virtuale. È particolarmente importante selezionare un intervallo che non si sovrapponga con gli intervalli utilizzati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete che potrebbe essere necessario selezionare un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale da utilizzare per la rete virtuale.
1. Immettere le informazioni seguenti e quindi fare clic sul segno di spunta per iniziare a creare la rete virtuale.
 - **Spazio degli indirizzi**: aggiungere l'intervallo di indirizzi IP interno che si desidera utilizzare per la rete virtuale, inclusi IP iniziale e conteggio. È importante selezionare un intervallo che non si sovrapponga con gli intervalli usati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete che potrebbe essere necessario selezionare un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale da utilizzare per la rete virtuale.
 - **Aggiungi subnet**: non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP statici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.
 - **Aggiungi subnet gateway**: la subnet del gateway è necessaria per una VPN point-to-site. Fare clic per aggiungere la subnet del gateway. La subnet del gateway viene utilizzata solo per il gateway di rete virtuale.
1. Al termine della creazione della rete virtuale, verrà visualizzato**Creato** in **Stato** nella pagina delle reti del portale di Azure. Dopo aver creata la rete virtuale, è possibile creare il gateway con routing dinamico.

### Creare un gateway di routing dinamico

1. Nella pagina relativa alle **Reti** del portale di Azure, fare clic sulla rete virtuale appena creata, quindi andare alla pagina **Dashboard**.
1. Fare clic su **Crea Gateway**nella parte inferiore della pagina **Dashboard**. Verrà visualizzato un messaggio viene chiesto **si desidera creare un gateway per rete virtuale "rete personale"**. Fare clic su **Sì** per iniziare a creare il gateway. Può richiedere circa 15 minuti per la creazione del gateway.

## Creare i certificati

I certificati vengono utilizzati per autenticare i client VPN per le connessioni VPN point-to-site. Questa procedura prevede diversi passaggi. Utilizzare i collegamenti seguenti per completare ogni passaggio, in ordine.

1. [Generare un certificato radice autofirmato](#generate-a-self-signed-root-certificate) -sono supportati solo i certificati radice autofirmati in questo momento.
2. [Caricare il file del certificato radice nel portale di Azure](#upload-the-root-certificate-file-to-the-management-portal).
3. [Generare un certificato client](#generate-a-client-certificate).
4. [Esportazione e installazione del certificato client](#export-and-install-the-client-certificate).

### Generare un certificato radice autofirmato

1. Un modo per creare un certificato x. 509 consiste nell'utilizzare lo strumento di creazione certificati (makecert.exe). Per utilizzare makecert, scaricare e installare [Microsoft Visual Studio Express 2013 per Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), che è disponibile gratuitamente.
2. Individuare la cartella Strumenti di Visual Studio e avviare il prompt dei comandi come Amministratore.
3. Il comando nell'esempio seguente consente di creare e installare un certificato radice nell'archivio certificati personali nel computer e creare anche un file corrispondente con estensione *.cer* da caricare in seguito nel portale di Azure.
4. Passare alla directory in cui si vuole inserire il file con estensione .cer, e eseguire il seguente comando dove *RootCertificateName* è il nome che si vuole usare per il certificato. Se si esegue l'esempio riportato di seguito senza modifiche, il risultato sarà un certificato radice e il corrispondente file *RootCertificateName.cer*.

>[AZURE.NOTE]Poiché è stato creato un certificato radice da cui verranno generati i certificati client, è possibile esportarlo, insieme alla relativa chiave privata e salvarlo in una posizione sicura dove può essere recuperato.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Caricare il file del certificato radice nel portale di Azure

1. Quando è stato generato un certificato radice autofirmato nella procedura precedente, è stato creato anche un *. cer* file. Tale file a questo punto verrà caricato nel portale di Azure. Si noti che il file con estensione CER non contiene la chiave privata del certificato radice.
1. Nel portale di Azure sulla pagina **Certificati** per la rete virtuale fare clic su **Carica un certificato radice**.
1. Nel **Carica certificato** pagina, cercare il certificato radice con estensione cer e quindi fare clic sul segno di spunta.

### Generare un certificato client

1. Nello stesso computer utilizzato per creare il certificato radice autofirmato aprire una finestra del prompt dei comandi di Visual Studio come amministratore.
2. Passare alla directory nella posizione in cui si desidera salvare il client file di certificato. *RootCertificateName* fa riferimento al certificato radice autofirmato generato. Se si esegue l'esempio riportato di seguito (sostituendo RootCertificateName con il nome del certificato radice), il risultato sarà un certificato client denominato "ClientCertificateName" nell'archivio certificati personale.
3. Digitare il seguente comando:

    Makecert.exe - n "CN = ClientCertificateName"-pe-sky exchange -m 96 -ss My - in "RootCertificateName" - è my-sha1

4. Tutti i certificati vengono archiviati nell'archivio certificati personali del computer. Controllare *certmgr* per verificare. È possibile generare l'esempio tutti i certificati client necessari in base a questa procedura. Si consiglia di creare client univoci certificati per ogni computer che si desidera connettersi alla rete virtuale.

### Esportazione e installare il certificato client

1. Un certificato client deve essere installato in ogni computer che si connette alla rete virtuale. Ciò significa che saranno stati creati probabilmente creati più certificati client, pertanto è necessario esportarli. Per esportare i certificati client, utilizzare *certmgr. msc*. Fare clic con il pulsante destro del mouse sul certificato client che si desidera esportare, fare clic su **Tutte le attività**, quindi fare clic su **Esporta**.
2. Esportare il *certificato client* con la chiave privata. Si tratta di un file *.pfx*. Assicurarsi di registrare o ricordare la password (chiave) impostata per il certificato.
3. Copia il *pfx* file nel computer client. Nel computer client, fare doppio clic sui *pfx* file per poterlo installare. Immettere la password quando richiesto. Non modificare il percorso di installazione.

## Configurare il client VPN

Per connettersi alla rete virtuale, è inoltre necessario configurare il client VPN. Il client richiede un certificato client e la corretta configurazione del client VPN per connettersi.

### Creare il pacchetto di configurazione del client VPN

1. Nel portale di Azure, nella pagina del **Dashboard** per la rete virtuale, passare al menu rapido nell'angolo destro e fare clic sul pacchetto VPN pertinente al client che si desidera connettere alla rete virtuale. Sono supportati i seguenti sistemi operativi client:
 - Windows 7 (32 bit e a 64 bit)
 - Windows Server 2008 R2 (solo 64 bit)
 - Windows 8 (32 bit e a 64 bit)
 - Windows 8.1 (32 bit e a 64 bit)
 - Windows Server 2012 (solo 64 bit)
 - Windows Server 2012 R2 (solo 64 bit)

1. Selezionare il pacchetto di download che corrisponde al sistema operativo client in cui verrà installato:
 - Per i client a 32 bit, selezionare **Scaricare il pacchetto di VPN Client a 32 bit**.
 - Per i client a 64 bit, selezionare **Scaricare il pacchetto di VPN Client a 64 bit**.
1. Richiederà alcuni minuti per creare il pacchetto client. Una volta completato il pacchetto, sarà possibile scaricare il file. Il file *.exe* scaricato può essere archiviato nel computer locale in modo sicuro.
1. Dopo avere generato e scaricare il pacchetto client VPN dal portale di Azure, è possibile installare il pacchetto client sul computer client da cui si desidera connettersi alla rete virtuale. Se si prevede di installare il pacchetto client VPN in più computer client, assicurarsi che ognuno abbia anche un certificato client installato. Il pacchetto client VPN contiene informazioni di configurazione per configurare il software client VPN incorporato in Windows. Il pacchetto non viene installato il software aggiuntivo.

### Installare il pacchetto di configurazione VPN nel client e avvio della connessione

1. Copiare il file di configurazione in locale al computer in cui si desidera connettere alla rete virtuale e fare doppio clic sul file .exe. Una volta installato il pacchetto, è possibile avviare la connessione VPN. Si noti che il pacchetto di configurazione non è firmato da Microsoft. È possibile firmare il pacchetto con il servizio di firma dell'organizzazione o firmarlo manualmente usando [SignTool](https://msdn.microsoft.com/library/windows/desktop/aa387764(v=vs.85).aspx). È anche possibile utilizzare il pacchetto senza firma. Tuttavia, se il pacchetto non è firmato, verrà visualizzato un avviso quando si installa il pacchetto.
2. Nel computer client passare alle connessioni VPN e individuare la connessione VPN appena creato. Verrà denominato lo stesso nome di rete virtuale. Fare clic su **Connect**.
3. Verrà visualizzato un messaggio popup in che consente di creare un certificato autofirmato per l'endpoint del Gateway. Fare clic su **Continua** utilizzare privilegi elevati.
4. Nel **connessione** pagina di stato, fare clic su **Connect** per avviare la connessione.
5. Se viene visualizzato un **Seleziona certificato** dello schermo, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. Se non lo è, utilizzare la freccia a discesa per selezionare il certificato corretto, quindi fare clic su **OK**.
6. Si è connessi alla rete virtuale e hanno accesso completo a qualsiasi servizio e le macchine virtuali ospitate nella rete virtuale.

### Controllare la connessione VPN

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi all'interno dell'intervallo di indirizzi di connettività point-to-site specificato durante la creazione della rete virtuale. I risultati dovrebbero essere simili al seguente:

Esempio:



    PPP adapter VNetEast:
		Connection-specific DNS Suffix .:
		Description.....................: VNetEast
		Physical Address................:
		DHCP Enabled....................: No
		Autoconfiguration Enabled.......: Yes
		IPv4 Address....................: 192.168.130.2(Preferred)
		Subnet Mask.....................: 255.255.255.255
		Default Gateway.................:
		NetBIOS over Tcpip..............: Enabled



## Passaggi successivi


Per altre informazioni sulla connettività cross-premise di rete virtuale, vedere l'articolo [Informazioni sulla connettività cross-premise protetta della rete virtuale](vpn-gateway-cross-premises-options.md).

Se si vuole configurare una connessione VPN da sito a sito, vedere[Configurare una rete virtuale con una connessione di gateway VPN da sito a sito](vpn-gateway-site-to-site-create.md).

È possibile aggiungere macchine virtuali nella rete virtuale. Vedere [Come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-create-custom.md).

<!---HONumber=August15_HO7-->