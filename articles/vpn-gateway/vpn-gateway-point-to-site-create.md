---
title: Configurare una connessione gateway VPN da punto a sito a una rete virtuale di Azure con il portale classico | Microsoft Docs
description: Connettersi in modo sicuro alla rete virtuale di Azure attraverso la creazione di una connessione gateway VPN da punto a sito.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 4f5668a5-9b3d-4d60-88bb-5d16524068e0
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 3ff2dcba568ed7ff83154cb6e1f1861ffb32a0d2
ms.lasthandoff: 03/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Configurare una connessione da punto a sito a una rete virtuale usando il portale classico
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Classica - Portale di Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [Classica - Portale classico](vpn-gateway-point-to-site-create.md)
> 
> 

Una configurazione da punto a sito (P2S) permette di creare una connessione protetta da un singolo computer client a una rete virtuale. Una soluzione P2S è utile quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale.

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare. Una connessione VPN viene stabilita avviando la connessione dal computer client. Per altre informazioni sulla connettività da punto a sito, consultare le [Domande frequenti sulla connettività da punto a sito](#faq) alla fine di questo articolo.

Questo articolo illustra la creazione di una rete virtuale con una connessione da punto a sito nel modello di distribuzione classica usando il portale classico.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Metodi e modelli di distribuzione per connessioni P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La tabella seguente descrive i due modelli di distribuzione e i metodi di distribuzione disponibili per le configurazioni P2S. Quando è disponibile un articolo contenente la procedura di configurazione, nella tabella è presente un collegamento diretto.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flusso di lavoro di base
![Diagramma da punto a sito](./media/vpn-gateway-point-to-site-create/p2sclassic.png "Da punto a sito")

I passaggi seguenti illustrano in dettaglio come creare una connessione sicura da punto a sito a una rete virtuale. 

La configurazione di una connessione da punto a sito è suddivisa in quattro sezioni. È importante rispettare l'ordine in cui si configura ognuna di queste sezioni. Non saltare passaggi o andare troppo avanti.

* **Sezione 1** : Creare una rete virtuale e un gateway VPN.
* **Sezione 2** : Creare i certificati usati per l'autenticazione e caricarli.
* **Passaggio 3** : Esportare e installare i certificati client.
* **Sezione 4** : Configurare il client VPN.

## <a name="vnetvpn"></a>Sezione 1: Creare una rete virtuale e un gateway VPN
### <a name="part-1-create-a-virtual-network"></a>Parte 1: Creare una rete virtuale
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com). Queste procedure usano il portale classico, non il portale di Azure. Attualmente non è possibile creare una connessione P2S con il portale di Azure.
2. Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo**. Nel riquadro di spostamento fare clic su **Servizi di rete** e quindi su **Rete virtuale**. Fare clic su **Creazione personalizzata** per avviare la configurazione guidata.
3. Nella pagina **Virtual Network Details** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.
   
   * **Nome**: assegnare un nome alla rete virtuale. Ad esempio, "VNet1". Questo è il nome a cui sarà possibile fare riferimento quando si distribuiscono macchine virtuali a questa rete virtuale.
   * **Indirizzo**: la località è direttamente correlata alla località fisica (area) in cui si vuole che risiedano le risorse (macchine virtuali). Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.
4. Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.
   
   * **Server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non comporta la creazione di un server DNS. Consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale. Se si desidera utilizzare il servizio di risoluzione dei nomi predefinito di Azure, lasciare vuota questa sezione.
   * **Configura Point-To-Site VPN**: selezionare la casella di controllo.
5. Nella pagina **Connettività Point-To-Site** e specificare l'intervallo indirizzi IP dal quale i client VPN riceveranno un indirizzo IP durante la connessione. Sono previste alcune regole relative agli intervalli di indirizzi che è possibile specificare. È importante verificare che l'intervallo specificato non si sovrapponga ad altri intervalli presenti nella rete locale.
6. Immettere le seguenti informazioni e fare clic sulla freccia avanti.
   
   * **Spazio di indirizzi**: includono l'indirizzo IP iniziale e CIDR (conteggio indirizzi).
   * **Aggiungi spazio di indirizzi**: aggiungere lo spazio indirizzi solo se è necessario per la progettazione della rete.
7. Nella pagina **Spazi di indirizzi della rete virtuale** è possibile immettere lo spazio di indirizzi che si desidera utilizzare per la rete virtuale. Questi sono gli indirizzi IP dinamici che verranno assegnati alle macchine virtuali e ad altre istanze del ruolo distribuite in questa rete virtuale.<br><br>È particolarmente importante selezionare un intervallo che non si sovrapponga agli intervalli usati per la rete locale. È necessario coordinarsi con l'amministratore di rete che potrebbe dover creare un intervallo di indirizzi IP dallo spazio indirizzi della rete locale da usare per la rete virtuale.
8. Immettere le seguenti informazioni, quindi fare clic sul segno di spunta per iniziare a creare la rete virtuale.
   
   * **Spazio degli indirizzi**: aggiungere l'intervallo di indirizzi IP interno che si desidera utilizzare per la rete virtuale, inclusi IP iniziale e conteggio. È importante selezionare un intervallo che non si sovrapponga con gli intervalli usati per la rete locale. 
   * **Aggiungi subnet**: non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP statici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.
   * **Aggiungi subnet gateway**: la subnet del gateway è necessaria per una VPN da punto a sito. Fare clic per aggiungere la subnet del gateway. Verrà usata solo per il gateway della rete virtuale.
9. Al termine della creazione della rete virtuale, nella pagina Reti del portale di Azure classico verrà visualizzato **Creato** in **Stato**. Dopo avere creato la rete virtuale, è possibile creare il gateway di routing dinamico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Parte 2: Creare un gateway di routing dinamico
Il tipo di gateway deve essere configurato come dinamico. I gateway con routing statico non funzionano con questa funzionalità.

1. Nella pagina **Reti** del portale di Azure classico fare clic sulla rete virtuale creata e passare alla pagina **Dashboard**.
2. Alla fine della pagina del **Dashboard** fare clic su **Crea gateway**. Verrà visualizzato un messaggio che richiede **Creare un gateway per la rete virtuale "VNet1"?**. Fare clic su **Sì** per iniziare a creare il gateway. La creazione del gateway può richiedere fino a 45 minuti.

## <a name="generate"></a>Sezione 2: Generare e caricare i certificati
I certificati vengono usati per autenticare client VPN per VPN da punto a sito. Si può usare un certificato radice generato da una soluzione aziendale per la creazione di certificati oppure un certificato autofirmato. È possibile caricare fino a 20 certificati radice in Azure. Una volta caricato il file CER, Azure può usare le informazioni contenute nel file per autenticare i client in cui è installato un certificato client. Il certificato client deve essere generato dallo stesso certificato rappresentato dal file CER.

In questa sezione si dovranno eseguire queste operazioni:

* Ottenere il file CER per un certificato radice. Può trattarsi di un certificato autofirmato oppure si può usare il sistema aziendale per la creazione di certificati.
* Caricare il file CER in Azure.
* Generare certificati client.

### <a name="root"></a>Parte 1: Ottenere il file CER per il certificato radice
Se si tratta di una soluzione aziendale, è possibile usare la catena di certificati esistente. Ottenere il file con estensione cer per il certificato radice che si vuole usare.

Se non si usa una soluzione aziendale per la creazione di certificati, è necessario creare un certificato radice autofirmato. Per creare un certificato autofirmato contenente i campi necessari per l'autenticazione da punto a sito, usare makecert. L'articolo [Usare i certificati autofirmati per le connessioni da punto a sito](vpn-gateway-certificates-point-to-site.md) illustra i passaggi per creare un certificato radice autofirmato. Benché deprecata, makecert in questo momento è la soluzione supportata.

>[!NOTE]
>Anche se è possibile usare PowerShell per creare certificati autofirmati, il certificato generato con PowerShell non contiene i campi necessari per le connessioni da punto a sito.
>


#### <a name="to-obtain-the-cer-file-from-a-self-signed-root-certificate"></a>Per ottenere il file con estensione cer da un certificato radice autofirmato

1. Per ottenere un file con estensione cer da un certificato radice autofirmato, aprire **certmgr.msc** e individuare il certificato radice creato. Il certificato si trova in genere in "Certificati-Utente corrente/ Personale/Certificati" e ha il nome che gli è stato assegnato al momento della sua creazione. Fare clic con il pulsante destro del mouse sul certificato radice autofirmato, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.
2. Nella procedura guidata fare clic su **Avanti**, selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.
3. Nella pagina **Formato file di esportazione** selezionare **Codificato Base&64; X.509 (.CER)**. Quindi fare clic su **Avanti**.
4. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.
5. Fare clic su **Fine** per esportare il certificato.

### <a name="upload"></a>Parte 2: Caricare il file CER del certificato radice nel portale di Azure classico
Aggiungere un certificato attendibile in Azure. L'aggiunta di un file con codifica Base64 X.509 (estensione CER) consente ad Azure di considerare attendibile il certificato radice che il file rappresenta.

1. Nella pagina **Certificati** della rete virtuale nel portale di Azure classico fare clic su **Carica certificato radice**.
2. Nella pagina **Carica certificato** cercare il certificato radice con estensione cer e quindi fare clic sul segno di spunta.

### <a name="createclientcert"></a>Parte 3: Generare un certificato client
Procedere quindi alla generazione dei certificati client. È possibile generare un certificato univoco per ogni client che stabilirà la connessione oppure usare lo stesso certificato in più client. Il vantaggio della generazione di certificati client univoci è dato dalla possibilità di revocare un certificato, se necessario. Diversamente, se tutti gli utenti usano lo stesso certificato client ed è necessario revocarlo per un client, si dovranno generare e installare nuovi certificati per tutti i client che usano il certificato per l'autenticazione.

####<a name="enterprise-certificate"></a>Certificato aziendale
- Se si usa una soluzione aziendale per la creazione di certificati, generare un certificato client con il valore di nome comune nel formato "name@yourdomain.com", invece che nel formato "nome dominio\nome utente".
- Assicurarsi che il certificato client che si emette sia basato sul modello di certificato "Utente" con "Autenticazione client" come primo elemento nell'elenco d'uso, invece di Accesso smart card e così via. È possibile controllare il certificato facendo doppio clic sul certificato client e aprendo **Dettagli > Utilizzo chiavi avanzato**.

####<a name="self-signed-certificate"></a>Certificato autofirmato 
Se si usa un certificato autofirmato, per generare un certificato client vedere [Usare i certificati radice autofirmati per le configurazioni da punto a sito](vpn-gateway-certificates-point-to-site.md) .

## <a name="installclientcert"></a>Sezione 3: Esportare e installare il certificato client
Installare un certificato client in ogni computer che si vuole connettere alla rete virtuale. Il certificato client è necessario per l'autenticazione. È possibile automatizzare l'installazione del certificato client oppure installarlo manualmente. La procedura seguente illustra come esportare e installare manualmente il certificato client.

1. Per esportare un certificato client, è possibile usare *certmgr.msc*. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**.
2. Esportare il certificato client con la chiave privata. Si tratta di un file con estensione *prx* . Assicurarsi di eseguire la registrazione o ricordare la password (chiave) impostata per il certificato.
3. Copia il *pfx* file nel computer client. Nel computer client fare doppio clic sul file con estensione *pfx* per installarlo. Immettere la password quando richiesta. Non modificare il percorso di installazione.

## <a name="vpnclientconfig"></a>Sezione 4: Configurare il client VPN
Per connettersi alla rete virtuale, è necessario configurare anche un client VPN. Per la connessione del client sono necessari sia un certificato client sia la corretta configurazione del client VPN. Per configurare un client VPN, seguire questa procedura nell'ordine indicato.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: Creare il pacchetto di configurazione del client VPN
1. Nella pagina **Dashboard** della rete virtuale nel portale di Azure classico passare al menu di riepilogo rapido nell'angolo destro. Nel pacchetto client VPN sono contenute le informazioni per configurare il software client VPN incorporato in Windows. Tramite il pacchetto non viene installato del software aggiuntivo. Le impostazioni sono specifiche per la rete virtuale a cui ci si vuole connettere. Per l'elenco dei sistemi operativi client supportati, vedere [Domande frequenti sulla connettività da punto a sito](#faq) alla fine di questo articolo.<br><br>Selezionare il pacchetto di download che corrisponde al sistema operativo client in cui verrà installato:
   
   * Per i client a 32 bit, selezionare **Scarica pacchetto VPN client a 32 bit**.
   * Per i client a 64 bit, selezionare **Scarica pacchetto VPN client a 64 bit**.
2. La creazione del pacchetto client richiede alcuni minuti. Una volta completato il pacchetto, si potrà scaricare il file. Il file *.exe* scaricato può essere archiviato nel computer locale in modo sicuro.
3. Dopo avere generato e scaricato il pacchetto client VPN dal portale di Azure classico, è possibile installare il pacchetto client nel computer client da cui ci si vuole connettere alla rete virtuale. Se si prevede di installare il pacchetto client VPN in più computer client, assicurarsi che ognuno abbia anche un certificato client installato.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Passaggio 2: Installare il pacchetto di configurazione della VPN nel client
1. Copiare il file di configurazione in locale nel computer che si vuole connettere alla rete virtuale e fare doppio clic sul file EXE. 
2. Una volta installato il pacchetto, è possibile avviare la connessione VPN. Il pacchetto di configurazione non è firmato da Microsoft. È possibile firmare il pacchetto con il servizio di firma dell'organizzazione oppure manualmente con [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). È anche possibile utilizzare il pacchetto senza firma. Tuttavia, se il pacchetto non è firmato, verrà visualizzato un avviso quando lo si installa.
3. Nel computer client passare a **Impostazioni di rete** e fare clic su **VPN**. La connessione verrà visualizzata in un elenco, insieme al nome della rete virtuale a cui si connette e avrà un aspetto simile al seguente: 
   
    ![Client VPN](./media/vpn-gateway-point-to-site-create/vpn.png "Client VPN")

### <a name="part-3-connect-to-azure"></a>Parte 3: Connettersi ad Azure
1. Per connettersi alla rete virtuale, nel computer client passare alle connessioni VPN e individuare quella creata, che ha lo stesso nome della rete virtuale locale. Fare clic su **Connetti**. È possibile che venga visualizzato un messaggio popup che fa riferimento all'uso del certificato. In questo caso, fare clic su **Continua** per usare privilegi elevati. 
2. Nella pagina **Stato connessione** fare clic su **Connetti** per avviare la connessione. Se viene visualizzato un **Seleziona certificato** dello schermo, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. In caso contrario, usare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.
   
    ![Client VPN 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "Connessione del client VPN")
3. Verrà stabilita la connessione.
   
    ![Client VPN 3](./media/vpn-gateway-point-to-site-create/connected.png "Connessione del client VPN 2")

> [!NOTE]
> Se si usa un certificato che è stato rilasciato tramite una soluzione CA globale e si riscontrano problemi durante l'autenticazione, controllare l'ordine di autenticazione del certificato client. È possibile controllare l'ordine dell'elenco di autenticazione facendo doppio clic sul certificato client e andando in **Dettagli > Utilizzo chiavi avanzato**. Assicurarsi che l'elenco mostri "Autenticazione client" come primo elemento. In caso contrario, è necessario emettere un certificato client in base al modello di utente con l'autenticazione client come primo elemento nell'elenco. 
>
>

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: Verificare la connessione VPN
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

## <a name="faq"></a>Domande frequenti sulla connettività da punto a sito

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

Per altre informazioni sulle reti virtuali, vedere la pagina relativa alla [documentazione sulle reti virtuali](/azure/virtual-network) .


