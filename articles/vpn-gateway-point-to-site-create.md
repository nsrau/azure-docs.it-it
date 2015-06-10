<properties
   pageTitle="Configurare una connessione VPN Point-to-Site alla rete virtuale di Azure"
   description="Connettersi per la protezione della rete virtuale tramite la creazione di una connessione point-to-site vpn."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/12/2015"
   ms.author="cherylmc"/>

# Configurare una connessione VPN Point-to-Site alla rete virtuale di Azure

Configurazione di una connessione point-to-site richiede più passaggi, ma è consigliato per avere una connessione protetta dal computer alla rete virtuale senza acquisire e configurare un dispositivo VPN. Esistono 3 componenti principali per configurare una VPN point-to-site: la rete virtuale e il gateway, i certificati utilizzati per l'autenticazione e il client VPN che viene utilizzato per connettersi alla rete virtuale. L'ordine in cui è possibile configurare ciascuno di essi è importante, pertanto non saltare passaggi o andare troppo avanti.

1. [Configurare una rete virtuale e un gateway di routing dinamico](#configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Creare i certificati](#create-your-certificates)
3. [Configurare il client VPN](#configure-your-VPN-client)

## Configurare una rete virtuale e un gateway di routing dinamico

Una connessione point-to-site richiede una rete virtuale con un gateway con routing dinamico. La procedura seguente verrà illustrata la creazione di entrambi.

### Crea rete virtuale

1. Accedere al **portale di gestione**.
1. Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.
1. Nella pagina **Virtual Network Details** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra. Per ulteriori informazioni sulle impostazioni nella pagina dei dettagli, vedere il [pagina dettagli rete virtuale](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails).
	- **Nome**: assegnare un nome alla rete virtuale. Ad esempio "VNetEast". Questo sarà il nome che è possibile fare riferimento quando si distribuiscono macchine virtuali e istanze PaaS in questa rete virtuale.
	- **Indirizzo**: la località è direttamente correlata alla posizione fisica (regione) in cui si desidera che le risorse (macchine virtuali) devono risiedere. Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.
1. Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra. Per ulteriori informazioni, vedere il [pagina server DNS e connettività VPN](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS).
	- **Server DNS**:immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dall'elenco a discesa. Questa impostazione non crea un server DNS, consente di specificare i server DNS che si desidera utilizzare per la risoluzione dei nomi per la rete virtuale. Se si desidera utilizzare il servizio di risoluzione dei nomi predefinito di Azure, lasciare vuota questa sezione.
	- **Configura Point-To-Site VPN**: selezionare la casella di controllo.
1. Nella pagina **Connettività Point-To-Site** e specificare l'intervallo indirizzi IP dal quale i client VPN riceveranno un indirizzo IP durante la connessione. Esistono diverse regole riguardano gli intervalli di indirizzi che è possibile specificare. È molto importante verificare che l'intervallo specificato non si sovrapponga ad altri intervalli presenti nella rete locale. Per altre informazioni, vedere la pagina [Configurare la connettività Point-to-Site](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT).
1. Immettere le informazioni seguenti e quindi fare clic sulla freccia avanti.
 - **Spazio di indirizzi**: includono l'indirizzo IP iniziale e CIDR (conteggio indirizzi).
 - **Aggiungi spazio di indirizzi**: aggiungere solo se è necessario per la progettazione della rete.
1. Nella pagina **Spazi di indirizzi della rete virtuale** è possibile immettere lo spazio di indirizzi che si desidera utilizzare per la rete virtuale. Si tratta di indirizzi IP dinamici (DIP) che verranno assegnati per le macchine virtuali e altre istanze del ruolo da distribuire a questa rete virtuale. Esistono diverse regole relative allo spazio degli indirizzi della rete virtuale; è possibile fare riferimento alla pagina Web dedicata per ulteriori informazioni. È particolarmente importante selezionare un intervallo che non si sovrapponga con gli intervalli utilizzati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete che potrebbe essere necessario selezionare un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale da utilizzare per la rete virtuale.
1. Immettere le informazioni seguenti e quindi fare clic sul segno di spunta per iniziare a creare la rete virtuale.
 - **Spazio degli indirizzi**: aggiungere l'intervallo di indirizzi IP interno che si desidera utilizzare per la rete virtuale, inclusi IP iniziale e conteggio. Esistono diverse regole relative allo spazio degli indirizzi della rete virtuale; è possibile fare riferimento alla pagina [Web dedicata](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS)  per ulteriori informazioni. È particolarmente importante selezionare un intervallo che non si sovrapponga con gli intervalli utilizzati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete che potrebbe essere necessario selezionare un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale da utilizzare per la rete virtuale.
 - **Aggiungi subnet**: non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP statici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.
 - **Aggiungi subnet gateway**: la subnet del gateway è necessaria per una VPN point-to-site. Fare clic per aggiungere la subnet del gateway. La subnet del gateway viene utilizzata solo per il gateway di rete virtuale.
1. Al termine della creazione della rete virtuale, in **Status** verrà visualizzata la dicitura **Created** nella pagina relativa alle reti del portale di gestione. Dopo aver creata la rete virtuale, è possibile creare il gateway con routing dinamico.

### Creare un gateway di routing dinamico

1. Nel **portale di gestione**, nella pagina **Reti**, fare clic sulla rete virtuale appena creata e passare alla pagina **Dashboard**.
1. Nella parte inferiore della pagina Dashboard fare clic su **Crea Gateway**. Verrà visualizzato un messaggio viene chiesto **si desidera creare un gateway per rete virtuale "rete personale"**. Fare clic su **Sì** per iniziare a creare il gateway. Può richiedere circa 15 minuti per la creazione del gateway.

## Creare i certificati

I certificati vengono utilizzati per autenticare i client VPN per le connessioni VPN point-to-site. Questa procedura prevede diversi passaggi. Utilizzare i collegamenti seguenti per completare ogni passaggio, in ordine.

1. [Generare un certificato radice autofirmato](#generate-a-self-signed-root-certificate) -sono supportati solo i certificati radice autofirmati in questo momento
2. [Caricare il file del certificato radice nel portale di gestione](#upload-the-root-certificate-file-to-the-Management-Portal)
3. [Generare un certificato client](#generate-a-client-certificate)
4. [Esportazione e installare il certificato client](#export-and-install-the-client-certificate)

### Generare un certificato radice autofirmato

1. Un modo per creare un certificato x. 509 consiste nell'utilizzare lo strumento di creazione certificati (makecert.exe). Per utilizzare makecert, scaricare e installare [Microsoft Visual Studio Express 2013 per Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), che è disponibile gratuitamente.
2. Individuare il **Visual Studio Tools** cartella e avviare il prompt dei comandi come amministratore.
3. Il comando nell'esempio seguente consente di creare e installare un certificato radice nell'archivio certificati personale nel computer e creare anche un file corrispondente con estensione *.cer* da caricare in seguito nel portale di gestione.
4. Passare alla directory in cui si vuole inserire il file con estensione cer, dove *RootCertificateName* è il nome che si vuole usare per il certificato. Se si esegue l'esempio riportato di seguito senza modifiche, il risultato sarà un certificato radice e il corrispondente file *RootCertificateName.cer*.

>[AZURE.NOTE]Poiché è stato creato un certificato radice da cui verranno generati i certificati client, è possibile esportarlo, insieme alla relativa chiave privata e salvarlo in una posizione sicura dove può essere recuperato.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Caricare il file del certificato radice nel portale di gestione

1. Quando è stato generato un certificato radice autofirmato nella procedura precedente, è stato creato anche un *. cer* file. Tale file a questo punto verrà caricato nel portale di gestione. Si noti che il file con estensione CER non contiene la chiave privata del certificato radice.
1. Nel portale di gestione sul **certificati** per la rete virtuale fare clic su **caricare un certificato radice**.
1. Nel **Carica certificato** pagina, cercare il certificato radice con estensione cer e quindi fare clic sul segno di spunta.

### Generare un certificato client

1. Nello stesso computer utilizzato per creare il certificato radice autofirmato aprire una finestra del prompt dei comandi di Visual Studio come amministratore.
2. Passare alla directory nella posizione in cui si desidera salvare il client file di certificato. *RootCertificateName* fa riferimento al certificato radice autofirmato generato. Se si esegue l'esempio riportato di seguito (sostituendo RootCertificateName con il nome del certificato radice), il risultato sarà un certificato client denominato "ClientCertificateName" nell'archivio certificati personale.
3. Digitare il seguente comando:

    Makecert.exe - n "CN = ClientCertificateName"-pe-sky exchange -m 96 -ss My - in "RootCertificateName" - è my-sha1

4. Tutti i certificati vengono archiviati nell'archivio certificati personali del computer. Controllare *certmgr* per verificare. È possibile generare l'esempio tutti i certificati client necessari in base a questa procedura. Si consiglia di creare client univoci certificati per ogni computer che si desidera connettersi alla rete virtuale.

### Esportazione e installare il certificato client

1. Un certificato client deve essere installato in ogni computer che si connette alla rete virtuale. Ciò significa che saranno stati creati probabilmente creati più certificati client, pertanto è necessario esportarli. Per esportare i certificati client, utilizzare *certmgr. msc*. Fare clic con il certificato client che si desidera esportare, fare clic su **tutte le attività**, quindi fare clic su **esportare**.
2. Esportare il *certificato client* con la chiave privata. Si tratta di un file *.pfx*. Assicurarsi di registrare o ricordare la password (chiave) impostata per il certificato.
3. Copia il *pfx* file nel computer client. Nel computer client, fare doppio clic sui *pfx* file per poterlo installare. Immettere la password quando richiesto. Non modificare il percorso di installazione.

## Configurare il client VPN

Per connettersi alla rete virtuale, è inoltre necessario configurare il client VPN. Il client richiede un certificato client e la corretta configurazione del client VPN per connettersi.

### Creare il pacchetto di configurazione del client VPN

1. Nel portale di gestione, nella pagina del Dashboard per la rete virtuale, passare alla rapida **menu immediatamente** nell'angolo destro e fare clic sul pacchetto VPN pertinente al client che si desidera connettere alla rete virtuale. Sono supportati i seguenti sistemi operativi client:
 - Windows 7 (32 bit e a 64 bit)
 - Windows Server 2008 R2 (solo 64 bit)
 - Windows 8 (32 bit e a 64 bit)
 - Windows 8.1 (32 bit e a 64 bit)
 - Windows Server 2012 (solo 64 bit)
 - Windows Server 2012 R2 (solo 64 bit)

1. Selezionare il pacchetto di download che corrisponde al sistema operativo client in cui verrà installato:
 - Per i client a 32 bit, selezionare **scaricare il pacchetto di VPN Client a 32 bit**
 - Per i client a 64 bit, selezionare **scaricare il pacchetto di VPN Client a 64 bit**
1. Richiederà alcuni minuti per creare il pacchetto client. Una volta completato il pacchetto, sarà possibile scaricare il file. Il file *.exe* scaricato può essere archiviato nel computer locale in modo sicuro.
1. Dopo avere generato e scaricare il pacchetto client VPN dal portale di gestione, è possibile installare il pacchetto client sul computer client da cui si desidera connettersi alla rete virtuale. Se si prevede di installare il pacchetto client VPN in più computer client, assicurarsi che ogni dispongono anche di un certificato client installato. Il pacchetto client VPN contiene informazioni di configurazione per configurare il software client VPN incorporato in Windows. Il pacchetto non viene installato il software aggiuntivo.

### Installare il pacchetto di configurazione VPN nel client e avvio della connessione

1. Copiare il file di configurazione in locale al computer in cui si desidera connettere alla rete virtuale e fare doppio clic sul file .exe. Una volta installato il pacchetto, è possibile avviare la connessione VPN. Si noti che il pacchetto di configurazione non è firmato da Microsoft. È possibile firmare il pacchetto utilizzando il servizio di firma dell'organizzazione o firmarlo manualmente utilizzando [SignTool](https://msdn.microsoft.com/library/windows/desktop/aa387764(v=vs.85).aspx). È anche possibile utilizzare il pacchetto senza firma. Tuttavia, se il pacchetto non è firmato, verrà visualizzato un avviso quando si installa il pacchetto. 
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



## Vedere anche


Ulteriori informazioni sulla connettività di rete virtuale cross-premise in questo articolo: [su virtuale sicura sedi connettività di rete](https://msdn.microsoft.com/library/azure/dn133798.aspx)

Se si desidera configurare una connessione VPN da sito a sito, vedere [configurare una connessione VPN da sito](vpn-gateway-site-to-site-create.md)

È possibile aggiungere macchine virtuali nella rete virtuale. Vedere [Come creare una macchina virtuale personalizzata](virtual-machines-create-custom.md)

Se si desidera configurare una connessione di rete virtuale mediante RRAS, vedere [configurare una VPN da sito a sito utilizzando Windows Server 2012 Routing e accesso remoto (RRAS)](https://msdn.microsoft.com/library/dn636917.aspx)

<!---HONumber=58-->