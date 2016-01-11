<properties
   pageTitle="Configurare una connessione VPN da punto a sito a una rete virtuale di Azure | Microsoft Azure"
   description="Connettersi in modo sicuro alla rete virtuale di Azure attraverso la creazione di una connessione VPN da punto a sito. Istruzioni per le reti virtuali create con il modello di distribuzione di Gestione servizi (Classico)."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/16/2015"
   ms.author="cherylmc"/>

# Configurare una connessione VPN da punto a sito a una rete virtuale


Questo articolo si applica alle connessioni Gateway VPN da punto a sito alla rete virtuale create con il modello di distribuzione classico (Gestione dei servizi). Le connessioni da punto a sito per le reti virtuali create con il modello di distribuzione di Gestione risorse di Azure sono ora disponibili attraverso le API REST e PowerShell. È in preparazione un articolo che descrive in modo dettagliato i passaggi per l'uso di PowerShell. Questa pagina verrà aggiornata con il relativo collegamento quando l'articolo sarà pronto. La pubblicazione è prevista per l'inizio di gennaio.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Informazioni sulla creazione di una connessione da punto a sito
 
I passaggi seguenti illustrano in modo dettagliato come creare una connessione da punto a sito protetta a una rete virtuale. Una connessione da punto a sito richiede più passaggi di configurazione, ma rappresenta un'ottima soluzione per avere una connessione protetta dal computer alla rete virtuale senza dover acquisire e configurare un dispositivo VPN.

La configurazione di una connessione da punto a sito è suddivisa in 3 sezioni. La **sezione 1** illustra come creare una rete virtuale e un gateway VPN, la **sezione 2** illustra come creare i certificati usati per l'autenticazione e la **sezione 3** illustra i passaggi relativi al client VPN da usare per connettersi alla rete virtuale. L'ordine in cui è possibile configurare ciascuno di essi è importante, pertanto non saltare passaggi o andare troppo avanti.

## Sezione 1: Creare una rete virtuale e un gateway VPN

Una connessione da punto a sito richiede una rete virtuale con un gateway di routing dinamico. Seguire la procedura dettagliata seguente:

Passaggio 1: Creare una rete virtuale.

Passaggio 2: Creare un gateway di routing dinamico.

### Crea rete virtuale

1. Accedere al **portale di Azure classico** (non al portale di Azure).
1. Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.
1. Nella pagina **Virtual Network Details** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.
	- **Nome**: assegnare un nome alla rete virtuale. Ad esempio "VNetEast". Questo sarà il nome che è possibile fare riferimento quando si distribuiscono macchine virtuali e istanze PaaS in questa rete virtuale.
	- **Indirizzo**: la località è direttamente correlata alla posizione fisica (regione) in cui si desidera che le risorse (macchine virtuali) devono risiedere. Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.
1. Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.
	- **Server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non crea un server DNS, ma consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale. Se si desidera utilizzare il servizio di risoluzione dei nomi predefinito di Azure, lasciare vuota questa sezione.
	- **Configura Point-To-Site VPN**: selezionare la casella di controllo.
1. Nella pagina **Connettività Point-To-Site** e specificare l'intervallo indirizzi IP dal quale i client VPN riceveranno un indirizzo IP durante la connessione. Sono previste alcune regole relative agli intervalli di indirizzi che è possibile specificare. È molto importante verificare che l'intervallo specificato non si sovrapponga ad altri intervalli presenti nella rete locale.
1. Immettere le seguenti informazioni e fare clic sulla freccia avanti.
 - **Spazio di indirizzi**: includono l'indirizzo IP iniziale e CIDR (conteggio indirizzi).
 - **Aggiungi spazio di indirizzi**: aggiungere solo se è necessario per la progettazione della rete.
1. Nella pagina **Spazi di indirizzi della rete virtuale** è possibile immettere lo spazio di indirizzi che si desidera utilizzare per la rete virtuale. Questi sono gli indirizzi IP dinamici (DIP) che verranno assegnati alle macchine virtuali e alle istanze di altri ruoli distribuiti nella rete virtuale. È particolarmente importante selezionare un intervallo che non si sovrapponga con gli intervalli utilizzati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete che potrebbe essere necessario selezionare un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale da utilizzare per la rete virtuale.
1. Immettere le seguenti informazioni, quindi fare clic sul segno di spunta per iniziare a creare la rete virtuale.
 - **Spazio degli indirizzi**: aggiungere l'intervallo di indirizzi IP interno che si desidera utilizzare per la rete virtuale, inclusi IP iniziale e conteggio. È importante selezionare un intervallo che non si sovrapponga con gli intervalli usati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete che potrebbe essere necessario selezionare un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale da utilizzare per la rete virtuale.
 - **Aggiungi subnet**: non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP statici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.
 - **Aggiungi subnet gateway**: la subnet del gateway è necessaria per una VPN da punto a sito. Fare clic per aggiungere la subnet del gateway. Verrà usata solo per il gateway della rete virtuale.
1. Al termine della creazione della rete virtuale, nella pagina delle reti del portale di Azure classico in **Stato** verrà visualizzata l'indicazione **Creato**. Dopo avere creato la rete virtuale, è possibile creare il gateway di routing dinamico.

### Creare un gateway di routing dinamico

Il tipo di gateway deve essere configurato come dinamico. I gateway con routing statico non interagiscono con questa funzionalità.

1. Nella pagina **Reti** del portale di Azure classico fare clic sulla rete virtuale appena creata e quindi passare alla pagina **Dashboard**.
1. Fare clic su **Crea Gateway** nella parte inferiore della pagina **Dashboard**. Verrà visualizzato un messaggio viene chiesto **si desidera creare un gateway per rete virtuale "rete personale"**. Fare clic su **Sì** per iniziare a creare il gateway. La creazione del gateway può richiedere circa 15 minuti.

## Sezione 2: Generare e caricare i certificati

I certificati vengono usati per autenticare client VPN per VPN da punto a sito. In precedenza, era necessario generare il proprio certificato autofirmato. Ora è possibile usare i certificati generati con una soluzione aziendale. È possibile caricare fino a 20 certificati radice in Azure.

Se si vuole usare un certificato autofirmato, la procedura seguente illustra il processo. Se si prevede di usare una soluzione aziendale per la creazione di certificati, i passaggi in ogni sezione saranno diversi, ma sarà comunque necessario seguire questa procedura:


Passaggio 1: Identificare o generare un certificato radice.

Passaggio 2: Caricare il file CER del certificato radice in Azure.

Passaggio 3: Generare un certificato client.

Passaggio 4: Esportare e installare il certificato client.


### Identificare o generare un certificato radice

Se non si usa una soluzione aziendale per la creazione di certificati, è necessario generare un certificato radice autofirmato. I passaggi seguenti funzionano in Windows 8. È in corso il processo di aggiornamento con nuovi passaggi per Windows 10.

1. Una modalità per creare un certificato X.509 consiste nell'utilizzare lo strumento di creazione certificati (makecert.exe). Per usare makecert, scaricare e installare [Microsoft Visual Studio Express](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), disponibile gratuitamente.
2. Individuare la cartella Strumenti di Visual Studio e avviare il prompt dei comandi come Amministratore.
3. Il comando nell'esempio seguente crea e installa un certificato radice nell'archivio certificati personali del computer e crea anche un file con estensione *cer* corrispondente da caricare in seguito nel portale di Azure classico.
4. Passare alla directory in cui si vuole inserire il file con estensione cer ed eseguire il comando seguente, dove *RootCertificateName* è il nome che si vuole usare per il certificato. Se si esegue l'esempio riportato di seguito senza modifiche, il risultato sarà un certificato radice e il file *RootCertificateName.cer* corrispondente.

>[AZURE.NOTE]Poiché è stato creato un certificato radice da cui verranno generati i certificati client, è possibile esportarlo, insieme alla relativa chiave privata e salvarlo in una posizione sicura dove può essere recuperato.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Caricare il file CER del certificato radice nel portale di Azure classico

È necessario caricare in Azure il file con estensione cer corrispondente per ogni certificato radice. Si possono caricare fino a 20 certificati.

1. Quando è stato generato un certificato radice nella procedura precedente, è stato creato anche un file con estensione *cer*. Tale file a questo punto verrà caricato nel portale di Azure classico. Si noti che il file con estensione CER non contiene la chiave privata del certificato radice. È possibile caricare fino a 20 certificati radice.
1. Nella pagina **Certificati** per la rete virtuale nel portale di Azure classico fare clic su **Carica certificato radice**.
1. Nella pagina **Carica certificato** cercare il certificato radice con estensione cer e quindi fare clic sul segno di spunta.

### Generazione di un certificato client

La procedura seguente consente di generare un certificato client dal certificato radice autofirmato. Se si usa una soluzione aziendale per la creazione di certificati, seguire le linee guida per la soluzione in uso.

1. Nello stesso computer utilizzato per creare il certificato radice autofirmato aprire una finestra del prompt dei comandi di Visual Studio come amministratore.
2. Passare alla posizione in cui si desidera salvare il file del certificato client. *RootCertificateName* fa riferimento al certificato radice autofirmato generato. Se si esegue l'esempio riportato di seguito (sostituendo RootCertificateName con il nome del certificato radice), il risultato sarà un certificato client denominato "ClientCertificateName" nell'archivio certificati personale.
3. Digitare il seguente comando:

    Makecert.exe - n "CN = ClientCertificateName"-pe-sky exchange -m 96 -ss My - in "RootCertificateName" - è my-sha1

4. Tutti i certificati vengono archiviati nell'archivio certificati Personale nel computer. Controllare *certmgr* per verificare. È possibile generare tutti i certificati client necessari in base a questa procedura. È consigliabile creare certificati client univoci per ogni computer che si desidera connettere alla rete virtuale.

### Esportazione e installazione del certificato client

È obbligatorio installare un certificato client in ogni computer che si vuole connettere alla rete virtuale. La procedura seguente illustra l'installazione manuale del certificato client.

1. Un certificato client deve essere installato in ogni computer che si desidera connettere alla rete virtuale. Di conseguenza, probabilmente verranno creati più certificati client e quindi sarà necessario esportarli. Per esportare i certificati client, utilizzare *certmgr. msc*. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**.
2. Esportare il *certificato client* con la chiave privata. Si tratta di un file *.pfx*. Assicurarsi di eseguire la registrazione o ricordare la password (chiave) impostata per il certificato.
3. Copia il *pfx* file nel computer client. Nel computer client, fare doppio clic sui *pfx* file per poterlo installare. Immettere la password quando richiesta. Non modificare il percorso di installazione.

## Sezione 3: Configurare il client VPN

Per connettersi alla rete virtuale, è inoltre necessario configurare il client VPN. Per la connessione del client sono necessari sia un certificato client sia la corretta configurazione del client VPN.

Per configurare il client VPN, seguire questa procedura nell'ordine:

Passaggio 1: Creare il pacchetto di configurazione del client VPN.

Passaggio 2: Installare il pacchetto di configurazione della VPN nel client e avviare la connessione.

Passaggio 3: Verificare la connessione.

### Creare il pacchetto di configurazione del client VPN

1. Nella pagina **Dashboard** per la rete virtuale nel portale di Azure classico passare al menu di riepilogo rapido nell'angolo destro e fare clic sul pacchetto VPN relativo al client da connettere alla rete virtuale.
2. 
Sono supportati i sistemi operativi client seguenti:
 - Windows 7 (a 32 e 64 bit)
 - Windows Server 2008 R2 (solo a 64 bit)
 - Windows 8 (a 32 e 64 bit)
 - Windows 8.1 (a 32 e 64 bit)
 - Windows Server 2012 (solo a 64 bit)
 - Windows Server 2012 R2 (solo a 64 bit)
 - Windows 10

1. Selezionare il pacchetto di download corrispondente al sistema operativo del client in cui verrà eseguita l'installazione:
 - Per i client a 32 bit selezionare **Scarica pacchetto VPN client a 32 bit**.
 - Per i client a 64 bit selezionare **Scarica pacchetto VPN client a 64 bit**.
1. Per la creazione del pacchetto client saranno necessari alcuni minuti. Una volta completato il pacchetto, sarà possibile scaricare il file. Il file *.exe* scaricato può essere archiviato nel computer locale in modo sicuro.
1. Dopo avere generato e scaricato il pacchetto client VPN dal portale di Azure classico, è possibile installare il pacchetto client sul computer client da cui si vuole connettersi alla rete virtuale. Se si prevede di installare il pacchetto client VPN in più computer client, assicurarsi che ognuno abbia anche un certificato client installato. Nel pacchetto client VPN sono contenute le informazioni per configurare il software client VPN incorporato in Windows. Tramite il pacchetto non viene installato del software aggiuntivo.

### Installazione del pacchetto di configurazione VPN nel client e avvio della connessione

1. Copiare il file di configurazione in locale al computer in cui si desidera connettere alla rete virtuale e fare doppio clic sul file .exe. Una volta installato il pacchetto, è possibile avviare la connessione VPN. Si noti che il pacchetto di configurazione non è firmato da Microsoft. È possibile firmare il pacchetto con il servizio di firma dell'organizzazione o firmarlo manualmente con [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). È anche possibile utilizzare il pacchetto senza firma. Tuttavia, se il pacchetto non è firmato, verrà visualizzato un avviso quando si installa il pacchetto.
2. Nel computer client passare alle connessioni VPN e individuare quella appena creata. Avrà lo stesso nome della rete virtuale. Fare clic su **Connect**.
3. Verrà visualizzato un messaggio popup utilizzato per creare un certificato autofirmato per l'endpoint del gateway. Fare clic su **Continua** utilizzare privilegi elevati.
4. Nel **connessione** pagina di stato, fare clic su **Connect** per avviare la connessione.
5. Se viene visualizzata una pagina **Seleziona certificato**, verificare che il certificato client visualizzato sia quello da usare per la connessione. In caso contrario, usare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.
6. A questo punto si è connessi alla rete virtuale e si dispone dell'accesso completo a qualsiasi servizio e macchina virtuale ospitati nella rete virtuale.

### Verificare la connessione VPN

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nell'intervallo di indirizzi di connettività da punto a sito specificato al momento della creazione della rete virtuale. I risultati dovrebbero essere simili a quanto segue:

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

È possibile aggiungere macchine virtuali nella rete virtuale. Vedere [Come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-create-custom.md).

Per altre informazioni sulle reti virtuali, vedere la pagina relativa alla [documentazione sulle reti virtuali](https://azure.microsoft.com/documentation/services/virtual-network/).

<!---HONumber=AcomDC_1223_2015-->