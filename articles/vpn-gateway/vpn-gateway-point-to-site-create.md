<properties
   pageTitle="Configurare una connessione VPN da punto a sito a una rete virtuale di Azure con il portale classico | Microsoft Azure"
   description="Connettersi in modo sicuro alla rete virtuale di Azure attraverso la creazione di una connessione VPN da punto a sito."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>  

# Configurare una connessione VPN da punto a sito a una rete virtuale tramite il portale classico

> [AZURE.SELECTOR]
- [PowerShell - Gestione risorse](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portale - Classica](vpn-gateway-point-to-site-create.md)

Una configurazione da punto a sito (P2S) consente di creare una connessione sicura da un singolo computer client a una rete virtuale. Una soluzione P2S è utile quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale.

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare. Una connessione VPN viene stabilita avviando la connessione dal computer client. Per altre informazioni sulle connessioni da punto a sito, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Pianificazione e progettazione per il gateway VPN](vpn-gateway-plan-design.md).

Questo articolo si applica alle connessioni da punto a sito a una rete virtuale creata con il modello di distribuzione classica. La procedura descritta in questo articolo usa il portale classico. Attualmente non è possibile creare questa configurazione con il portale di Azure.

**Strumenti e modelli di distribuzione per le connessioni da punto a sito**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

![Diagramma da punto a sito](./media/vpn-gateway-point-to-site-create/point2site.png "da punto a sito")

## Informazioni sulla creazione di una connessione da punto a sito
 
I passaggi seguenti illustrano in dettaglio come creare una connessione sicura da punto a sito a una rete virtuale.

La configurazione di una connessione da punto a sito è suddivisa in quattro sezioni. È importante rispettare l'ordine in cui si configura ognuna di queste sezioni. Non saltare passaggi o andare troppo avanti.

- **Sezione 1**: Creare una rete virtuale e un gateway VPN.
- **Sezione 2**: Creare i certificati usati per l'autenticazione e caricarli.
- **Passaggio 3**: Esportare e installare i certificati client.
- **Sezione 4**: Configurare il client VPN.

## <a name="vnetvpn"></a>Sezione 1: Creare una rete virtuale e un gateway VPN

### Parte 1: Creare una rete virtuale

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/). Queste procedure usano il portale classico, non il portale di Azure. Attualmente non è possibile creare una connessione P2S con il portale di Azure.

2. Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo**. Nel riquadro di spostamento fare clic su **Servizi di rete** e quindi su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.

3. Nella pagina **Virtual Network Details** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.
	- **Nome**: assegnare un nome alla rete virtuale. Ad esempio, "VNet1". Questo è il nome a cui sarà possibile fare riferimento quando si distribuiscono macchine virtuali a questa rete virtuale.
	- **Indirizzo**: la località è direttamente correlata alla località fisica (area) in cui si vuole che risiedano le risorse (macchine virtuali). Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.

4. Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.
	- **Server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non comporta la creazione di un server DNS. Consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale. Se si desidera utilizzare il servizio di risoluzione dei nomi predefinito di Azure, lasciare vuota questa sezione.
	- **Configura Point-To-Site VPN**: selezionare la casella di controllo.

5. Nella pagina **Connettività Point-To-Site** e specificare l'intervallo indirizzi IP dal quale i client VPN riceveranno un indirizzo IP durante la connessione. Sono previste alcune regole relative agli intervalli di indirizzi che è possibile specificare. È importante verificare che l'intervallo specificato non si sovrapponga ad altri intervalli presenti nella rete locale.

6. Immettere le seguenti informazioni e fare clic sulla freccia avanti.
 - **Spazio di indirizzi**: includono l'indirizzo IP iniziale e CIDR (conteggio indirizzi).
 - **Aggiungi spazio di indirizzi**: aggiungere lo spazio indirizzi solo se è necessario per la progettazione della rete.

7. Nella pagina **Spazi di indirizzi della rete virtuale** è possibile immettere lo spazio di indirizzi che si desidera utilizzare per la rete virtuale. Questi sono gli indirizzi IP dinamici che verranno assegnati alle macchine virtuali e ad altre istanze del ruolo distribuite in questa rete virtuale.<br><br>È particolarmente importante selezionare un intervallo che non si sovrapponga agli intervalli usati per la rete locale. È necessario coordinarsi con l'amministratore di rete che potrebbe dover creare un intervallo di indirizzi IP dallo spazio indirizzi della rete locale da usare per la rete virtuale.

8. Immettere le seguenti informazioni, quindi fare clic sul segno di spunta per iniziare a creare la rete virtuale.
 - **Spazio degli indirizzi**: aggiungere l'intervallo di indirizzi IP interno che si desidera utilizzare per la rete virtuale, inclusi IP iniziale e conteggio. È importante selezionare un intervallo che non si sovrapponga con gli intervalli usati per la rete locale.
 - **Aggiungi subnet**: non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP statici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.
 - **Aggiungi subnet gateway**: la subnet del gateway è necessaria per una VPN da punto a sito. Fare clic per aggiungere la subnet del gateway. Verrà usata solo per il gateway della rete virtuale.

9. Una volta completata la creazione della rete virtuale, nella pagina Reti del portale di Azure classico in **Stato** verrà visualizzato **Creato**. Dopo avere creato la rete virtuale, è possibile creare il gateway di routing dinamico.

### Parte 2: Creare un gateway di routing dinamico

Il tipo di gateway deve essere configurato come dinamico. I gateway con routing statico non funzionano con questa funzionalità.

1. Nella pagina **Reti** del portale di Azure classico fare clic sulla rete virtuale appena creata e quindi passare alla pagina **Dashboard**.

2. Fare clic su **Crea Gateway** nella parte inferiore della pagina **Dashboard**. Verrà visualizzato un messaggio che richiede **Creare un gateway per la rete virtuale "VNet1"?**. Fare clic su **Sì** per iniziare a creare il gateway. La creazione del gateway può richiedere circa 15 minuti.

## <a name="generate"></a>Sezione 2: Generare e caricare i certificati

I certificati vengono usati per autenticare client VPN per VPN da punto a sito. Si può usare un certificato radice generato da una soluzione aziendale per la creazione di certificati oppure un certificato autofirmato. È possibile caricare fino a 20 certificati radice in Azure. Una volta caricato il file CER, Azure può usare le informazioni contenute nel file per autenticare i client in cui è installato un certificato client. Il certificato client deve essere generato dallo stesso certificato rappresentato dal file CER.

In questa sezione si dovranno eseguire queste operazioni:

- Ottenere il file CER per un certificato radice. Può trattarsi di un certificato autofirmato oppure si può usare il sistema aziendale per la creazione di certificati.
- Caricare il file CER in Azure.
- Generare certificati client.

### <a name="root"></a>Parte 1: Ottenere il file CER per il certificato radice

Se si usa un sistema aziendale per la creazione di certificati, ottenere il file CER per il certificato radice che si vuole usare. Nella [Parte 3](#createclientcert) verranno generati i certificati client dal certificato radice.

Se non si usa una soluzione aziendale per la creazione di certificati, è necessario generare un certificato radice autofirmato. Per i passaggi relativi a Windows 10, è possibile vedere [Usare i certificati radice autofirmati per le configurazioni da punto a sito](vpn-gateway-certificates-point-to-site.md). L'articolo illustra l'uso makecert per generare un certificato autofirmato e quindi esportare il file CER.

### <a name="upload"></a>Parte 2: Caricare il file CER del certificato radice nel portale di Azure classico

Aggiungere un certificato attendibile in Azure. L'aggiunta di un file con codifica Base64 X.509 (estensione CER) consente ad Azure di considerare attendibile il certificato radice che il file rappresenta.

1. Nella pagina **Certificati** della rete virtuale nel portale di Azure classico fare clic su **Carica certificato radice**.

2. Nella pagina **Carica certificato** cercare il certificato radice con estensione cer e quindi fare clic sul segno di spunta.

### <a name="createclientcert"></a>Parte 3: Generare un certificato client

Procedere quindi alla generazione dei certificati client. È possibile generare un certificato univoco per ogni client che stabilirà la connessione oppure usare lo stesso certificato in più client. Il vantaggio della generazione di certificati client univoci è dato dalla possibilità di revocare un certificato, se necessario. Diversamente, se tutti gli utenti usano lo stesso certificato client ed è necessario revocarlo per un client, si dovranno generare e installare nuovi certificati per tutti i client che usano il certificato per l'autenticazione.

- Se si usa una soluzione aziendale per la creazione di certificati, generare un certificato client con il valore di nome comune nel formato 'nome@dominio.com', invece che nel formato NetBIOS 'DOMINIO\\nomeutente'.

- Se si usa un certificato autofirmato, per generare un certificato client vedere [Usare i certificati radice autofirmati per le configurazioni da punto a sito](vpn-gateway-certificates-point-to-site.md).

## <a name="installclientcert"></a>Sezione 3: Esportare e installare il certificato client

Installare un certificato client in ogni computer che si vuole connettere alla rete virtuale. Il certificato client è necessario per l'autenticazione. È possibile automatizzare l'installazione del certificato client oppure installarlo manualmente. La procedura seguente illustra come esportare e installare manualmente il certificato client.

1. Per esportare un certificato client, è possibile usare *certmgr.msc*. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**.
2. Esportare il certificato client con la chiave privata. Si tratta di un file con estensione *prx*. Assicurarsi di eseguire la registrazione o ricordare la password (chiave) impostata per il certificato.
3. Copia il *pfx* file nel computer client. Nel computer client fare doppio clic sul file con estensione *pfx* per installarlo. Immettere la password quando richiesta. Non modificare il percorso di installazione.

## <a name="vpnclientconfig"></a>Sezione 4: Configurare il client VPN

Per connettersi alla rete virtuale, è necessario configurare anche un client VPN. Per la connessione del client sono necessari sia un certificato client sia la corretta configurazione del client VPN. Per configurare un client VPN, seguire questa procedura nell'ordine indicato.

### Parte 1: Creare il pacchetto di configurazione del client VPN

1. Nella pagina **Dashboard** della rete virtuale nel portale di Azure classico passare al menu di riepilogo rapido nell'angolo destro. Per l'elenco dei sistemi operativi client supportati, vedere la sezione [Connessioni da punto a sito](vpn-gateway-vpn-faq.md#point-to-site-connections) dell'articolo Domande frequenti sul gateway VPN.<br><br>Selezionare il pacchetto di download che corrisponde al sistema operativo client in cui verrà installato:
 - Per i client a 32 bit, selezionare **Scarica pacchetto VPN client a 32 bit**.
 - Per i client a 64 bit, selezionare **Scarica pacchetto VPN client a 64 bit**.

2. La creazione del pacchetto client richiede alcuni minuti. Una volta completato il pacchetto, si potrà scaricare il file. Il file *.exe* scaricato può essere archiviato nel computer locale in modo sicuro.

3. Dopo avere generato e scaricato il pacchetto client VPN dal portale di Azure classico, è possibile installare il pacchetto client nel computer client da cui ci si vuole connettere alla rete virtuale. Se si prevede di installare il pacchetto client VPN in più computer client, assicurarsi che ognuno abbia anche un certificato client installato. Nel pacchetto client VPN sono contenute le informazioni per configurare il software client VPN incorporato in Windows. Tramite il pacchetto non viene installato del software aggiuntivo.

### Passaggio 2: Installare il pacchetto di configurazione della VPN nel client e avviare la connessione.

1. Copiare il file di configurazione in locale nel computer che si vuole connettere alla rete virtuale e fare doppio clic sul file EXE. Una volta installato il pacchetto, è possibile avviare la connessione VPN. Il pacchetto di configurazione non è firmato da Microsoft. È possibile firmare il pacchetto con il servizio di firma dell'organizzazione o firmarlo manualmente con [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). È anche possibile utilizzare il pacchetto senza firma. Tuttavia, se il pacchetto non è firmato, verrà visualizzato un avviso quando lo si installa.
2. Nel computer client passare alle connessioni VPN e individuare quella creata. Avrà lo stesso nome della rete virtuale. Fare clic su **Connect**.
3. Verrà visualizzato un messaggio popup usato per creare un certificato autofirmato per l'endpoint del gateway. Fare clic su **Continua** utilizzare privilegi elevati.
4. Nella pagina **Stato connessione** fare clic su **Connetti** per avviare la connessione.
5. Se viene visualizzato un **Seleziona certificato** dello schermo, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. In caso contrario, usare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.
6. A questo punto si è connessi alla rete virtuale e si dispone dell'accesso completo a qualsiasi servizio e macchina virtuale ospitati nella rete virtuale.

### Parte 3: Verificare la connessione VPN

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nell'intervallo di indirizzi di connettività da punto a sito specificato al momento della creazione della rete virtuale. I risultati dovrebbero essere simili a quanto segue:

Esempio:



    PPP adapter VNet1:
		Connection-specific DNS Suffix .:
		Description.....................: VNet1
		Physical Address................:
		DHCP Enabled....................: No
		Autoconfiguration Enabled.......: Yes
		IPv4 Address....................: 192.168.130.2(Preferred)
		Subnet Mask.....................: 255.255.255.255
		Default Gateway.................:
		NetBIOS over Tcpip..............: Enabled

## Passaggi successivi

È possibile aggiungere macchine virtuali nella rete virtuale. Vedere [Come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Per altre informazioni sulle reti virtuali, vedere la pagina relativa alla [documentazione sulle reti virtuali](https://azure.microsoft.com/documentation/services/virtual-network/).

<!---HONumber=AcomDC_0817_2016-->