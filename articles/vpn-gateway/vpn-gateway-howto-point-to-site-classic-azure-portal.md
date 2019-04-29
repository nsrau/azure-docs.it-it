---
title: "Connettere un computer a una rete virtuale da punto a sito con l'autenticazione del certificato: Connessione classica nel portale di Azure | Microsoft Docs"
description: Creare una connessione classica al gateway VPN da punto a sito tramite il portale di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 74940f3b89237233acd575aa5df441163e00d178
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845602"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Configurare una connessione da punto a sito usando l'autenticazione del certificato (versione classica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Questo articolo illustra come creare una rete virtuale con una connessione da punto a sito. Si crea questa rete virtuale con il modello di distribuzione classica tramite il portale di Azure. Questa configurazione usa i certificati autofirmati o rilasciati da un'autorità di certificazione per autenticare il client di connessione. È anche possibile creare questa configurazione con un diverso modello o strumento di distribuzione usando le opzioni descritte negli articoli seguenti:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Si usa un gateway VPN da punto a sito (P2S) per creare una connessione sicura alla rete virtuale da un singolo computer client. Le connessioni VPN da punto a sito sono utili per connettersi alla rete virtuale da una posizione remota. Una VPN P2S è una soluzione utile da usare al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale. Una connessione VPN P2S viene stabilita avviandola dal computer client.

> [!IMPORTANT]
> Il modello di distribuzione classica supporta solo i client VPN Windows e usa SSTP (Secure Socket Tunneling Protocol), un protocollo VPN basato su SSL. Per supportare i client VPN non Windows, è necessario creare la rete virtuale con il modello di distribuzione Resource Manager. Il modello di distribuzione Resource Manager supporta VPN IKEv2, oltre a SSTP. Per altre informazioni, vedere [Informazioni sulle connessioni da punto a sito](point-to-site-about.md).
>
>

![Diagramma da punto a sito](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Prerequisiti

Le connessioni da punto a sito con l'autenticazione del certificato richiedono i prerequisiti seguenti:

* Un gateway VPN dinamico.
* La chiave pubblica (file CER) per un certificato radice, caricato in Azure. Questa chiave viene considerata un certificato attendibile e viene usata per l'autenticazione.
* Un certificato client generato dal certificato radice e installato in ogni computer client che effettuerà la connessione. Questo certificato viene usato per l'autenticazione client.
* Un pacchetto di configurazione client VPN deve essere generato e installato in ogni computer client che effettua la connessione. Il pacchetto di configurazione client configura il client VPN nativo già disponibile nel sistema operativo con le informazioni necessarie per la connessione alla rete virtuale.

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico locale. La connessione VPN viene creata tramite SSTP (Secure Sockets Tunneling Protocol). Sul lato server sono supportate le versioni 1.0, 1.1 e 1.2 di SSTP. Il client decide quale versione usare. Per Windows 8.1 e versioni successive, SSTP usa per impostazione predefinita la versione 1.2. 

Per altre informazioni sulle connessioni da punto a sito, vedere [Domande frequenti sulle connessioni da punto a sito](#point-to-site-faq).

### <a name="example-settings"></a>Impostazioni di esempio

Usare i valori seguenti per creare un ambiente di test o fare riferimento a questi valori per comprendere meglio gli esempi di questo articolo:

- **Impostazioni per creare una rete virtuale (versione classica)**
   - **Nome**: Immettere *VNet1*.
   - **Spazio degli indirizzi**: Immettere *192.168.0.0/16*. Per questo esempio, viene usato un solo spazio indirizzi. È possibile avere più di uno spazio degli indirizzi per la rete virtuale, come mostrato nel diagramma.
   - **Nome della subnet**: Immettere *FrontEnd*.
   - **Intervallo di indirizzi subnet**: Immettere *192.168.1.0/24*.
   - **Sottoscrizione** Selezionare una sottoscrizione dall'elenco delle sottoscrizioni disponibili.
   - **Gruppo di risorse**: Immettere *TestRG*. Selezionare **Crea nuovo** se il gruppo di risorse non esiste.
   - **Località**: Selezionare **Stati Uniti orientali** dall'elenco.

  - **Impostazioni di connessione VPN**
    - **Tipo di connessione**: Selezionare **Da punto a sito**.
    - **Spazio indirizzi client**: Immettere *172.16.201.0/24*. I client VPN che si connettono alla rete virtuale con questa connessione da punto a sito ricevono un indirizzo IP dal pool specificato.

- **Impostazioni della subnet per la configurazione del gateway**
   - **Nome**: Completato automaticamente con *GatewaySubnet*.
   - **Intervallo di indirizzi**: Immettere *192.168.200.0/24*. 

- **Impostazioni per la configurazione del gateway**
   - **Dimensione**: Selezionare lo SKU del gateway da usare.
   - **Tipo di routing**: Selezionare **Dinamico**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Creare una rete virtuale e un gateway VPN

Prima di iniziare, verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Parte 1: Crea rete virtuale

Se non si ha una rete virtuale, crearne una. Gli screenshot sono forniti come esempio. Assicurarsi di sostituire i valori con i propri. Per creare una rete virtuale usando il portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**. Verrà visualizzata la pagina **Nuova**. 

2. Nel campo **Cerca nel Marketplace** immettere *rete virtuale* e quindi selezionare **Rete virtuale** nell'elenco restituito. Verrà visualizzata la pagina **Rete virtuale**.

3. Dall'elenco **Selezionare un modello di distribuzione** scegliere **Versione classica** e quindi **Crea**. Verrà visualizzata la pagina **Crea rete virtuale**.

4. Nella pagina **Crea rete virtuale** configurare le impostazioni della rete virtuale. Aggiungere a questa pagina il primo spazio di indirizzi e l'intervallo di indirizzi di una subnet singola. Dopo aver creato la rete virtuale, è possibile tornare indietro e aggiungere altre subnet e altri spazi di indirizzi.

   ![Pagina Crea rete virtuale](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Selezionare la **Sottoscrizione** da usare nell'elenco a discesa.

6. Selezionare un **Gruppo di risorse** esistente. In alternativa, creare un nuovo gruppo di risorse selezionando **Crea nuovo** e specificando un nome. Se si crea un nuovo gruppo di risorse, denominare il gruppo in base ai valori di configurazione pianificati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

7. Selezionare una **Posizione** per la rete virtuale. Questa impostazione determina la posizione geografica delle risorse distribuite in questa rete virtuale.

8. Selezionare **Crea** per creare la rete virtuale. Nella pagina **Notifiche** si noterà il messaggio **Distribuzione in corso**.

8. Una volta creata la rete virtuale, il messaggio nella pagina **Notifiche** cambia in **Distribuzione riuscita**. Per trovare facilmente la rete virtuale nel dashboard, selezionare **Aggiungi al dashboard**. 

10. Aggiungere un server DNS (facoltativo). Dopo aver creato la rete virtuale, è possibile aggiungere l'indirizzo IP di un server DNS per la risoluzione dei nomi. L'indirizzo IP del server DNS specificato deve essere l'indirizzo di un server DNS in grado di risolvere i nomi per le risorse della rete virtuale.

    Per aggiungere un server DNS, selezionare **Server DNS** dalla pagina della rete virtuale. Immettere quindi l'indirizzo IP del server DNS da usare per la risoluzione dei nomi e selezionare **Salva**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Parte 2: Creare una subnet del gateway e un gateway di routing dinamico

In questo passaggio vengono creati una subnet del gateway e un gateway di routing dinamico. Nel portale di Azure per il modello di distribuzione classica è possibile creare la subnet del gateway e il gateway usando le stesse pagine di configurazione. Usare la subnet del gateway solo per i servizi gateway. Non distribuire mai nulla direttamente nella subnet del gateway, ad esempio VM o altri servizi.

1. Nel portale di Azure passare alla rete virtuale per cui si vuole creare un gateway.

2. Selezionare **Panoramica** nella pagina della rete virtuale e, nella sezione **Connessioni VPN**, selezionare **Gateway**.

   ![Selezionare per creare un gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Nella pagina **Nuova connessione VPN** selezionare **Da punto a sito**.

   ![Tipo di connessione da punto a sito](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Pe **Spazio indirizzi client**, aggiungere l'intervallo di indirizzi IP da cui i client VPN ricevono un indirizzo IP al momento della connessione. Usare un intervallo di indirizzi IP privati che non si sovrapponga con la posizione locale da cui viene effettuata la connessione o con la rete virtuale a cui ci si connette. È possibile sovrascrivere l'intervallo compilato automaticamente con l'intervallo di indirizzi IP privati da usare. Questo esempio illustra l'intervallo compilato automaticamente. 

   ![Spazio indirizzi client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Selezionare **Crea gateway immediatamente** e selezionare **Configurazione gateway facoltativa** per aprire la pagina **Configurazione gateway**.

   ![Selezionare Configurazione gateway facoltativa](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Nella pagina **Configurazione gateway** selezionare **Subnet** per aggiungere la subnet del gateway. È possibile creare una subnet del gateway con dimensioni minime di /29. Tuttavia, è consigliabile crearne una più grande che includa più indirizzi selezionando almeno /28 o /27. In questo modo saranno disponibili indirizzi sufficienti per supportare in futuro le possibili configurazioni aggiuntive desiderate. Quando si usano le subnet del gateway, evitare di associare un gruppo di sicurezza di rete (NSG) alla subnet del gateway. Se si associa un gruppo di sicurezza di rete a tale subnet, il gateway VPN potrebbe non funzionare come previsto. Selezionare **OK** per salvare questa impostazione.

   ![Aggiungere GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Selezionare le **dimensioni** del gateway, che rappresentano lo SKU di gateway per il gateway di rete virtuale. Nel portale di Azure, lo SKU predefinito è **Predefinito**. Per altre informazioni sugli SKU di gateway, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Dimensioni del gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Selezionare il **tipo di routing** per il gateway. Le configurazioni P2S richiedono il tipo di routing **Dinamico**. Al termine della configurazione della pagina, selezionare **OK**.

   ![Configurare il tipo di routing](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Nella pagina **Nuova connessione VPN** selezionare **OK** nella parte inferiore per iniziare a creare il gateway di rete virtuale. Per il completamento di un gateway VPN possono essere necessari fino a 45 minuti, in base allo SKU selezionato per il gateway.
 
## <a name="generatecerts"></a>Creare i certificati

Azure usa i certificati per autenticare client VPN per VPN da punto a sito. È necessario caricare le informazioni della chiave pubblica del certificato radice in Azure. La chiave pubblica viene quindi considerata *attendibile*. I certificati client devono essere generati dal certificato radice attendibile e quindi installati in ogni computer client nell'archivio certificati Certificati - Utente corrente\Personale\Certificati. Il certificato viene usato per l'autenticazione del client alla connessione alla rete virtuale. 

Se usati, i certificati autofirmati devono essere creati con parametri specifici. È possibile creare un certificato autofirmato seguendo le istruzioni per [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). È importante seguire i passaggi di queste istruzioni quando si usano i certificati radice autofirmati e si generano certificati client dal certificato radice autofirmato. In caso contrario, i certificati creati non saranno compatibili con le connessioni P2S e si riceverà un errore di connessione.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Ottenere il file di chiave pubblica (con estensione cer) per il certificato radice

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Generazione di un certificato client

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Caricare il file CER del certificato radice

Dopo la creazione del gateway, caricare il file con estensione cer, che contiene le informazioni sulla chiave pubblica, per un certificato radice attendibile nel server di Azure. Non caricare la chiave privata per il certificato radice. Una volta caricato un certificato, Azure lo usa per autenticare i client che hanno installato un certificato client generato dal certificato radice attendibile. È possibile caricare in seguito fino a 20 file di certificato radice attendibile aggiuntivi, se necessario.  

1. Nella sezione **Connessioni VPN** della pagina della rete virtuale selezionare l'icona client per aprire la pagina **Connessione VPN da punto a sito**.

   ![Client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Nella pagina **Connessione VPN da punto a sito** selezionare **Gestisci certificato** per aprire la pagina **Certificati**.

   ![Pagina Certificati](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Nella pagina **Certificati** selezionare **Carica** per aprire la pagina **Carica certificato**.

    ![Pagina Carica certificato](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Selezionare l'icona della cartella per cercare il file CER. Selezionare il file e quindi selezionare **OK**. Il certificato caricato viene visualizzato nella pagina **Certificati**.

   ![Caricamento del certificato](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Configurare il client

Per connettersi a una rete virtuale tramite VPN da punto a sito, ogni client deve installare un pacchetto per configurare il client VPN di Windows nativo. Il pacchetto di configurazione configura il client VPN nativo di Windows con le impostazioni necessarie per la connessione alla rete virtuale.

È possibile usare lo stesso pacchetto di configurazione del client VPN in ogni computer client, a condizione che la versione corrisponda all'architettura del client. Per l'elenco dei sistemi operativi client supportati, vedere la sezione [Domande frequenti sulle connessioni da punto a sito](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generare e installare un pacchetto di configurazione del client VPN

1. Nella pagina **Panoramica** della rete virtuale nel portale di Azure selezionare l'icona relativa ai client in **Connessioni VPN** per aprire la pagina **Connessione VPN da punto a sito**.

2. Nella pagina **Connessione VPN da punto a sito** selezionare il pacchetto di download corrispondente al sistema operativo client in cui è installato:

   * Per client a 64 bit, selezionare **Client VPN (64 bit)**.
   * Per client a 32 bit, selezionare **Client VPN (32 bit)**.

   ![Scaricare il pacchetto di configurazione del client VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Dopo che è stato generato, scaricare e installare il pacchetto nel computer client. Se viene visualizzato un popup SmartScreen, selezionare **Altre informazioni** e quindi su **Esegui comunque**. È anche possibile salvare il pacchetto per l'installazione su altri computer client.

### <a name="install-a-client-certificate"></a>Installare un certificato client

Per creare una connessione da punto a sito da un computer client diverso da quello usato per generare i certificati client, installare un certificato client. Quando si installa un certificato client, è necessaria la password che è stata creata durante l'esportazione del certificato client. In genere, è possibile installare il certificato semplicemente facendo doppio clic su di esso. Per altre informazioni, vedere [Installare un certificato client esportato](vpn-gateway-certificates-point-to-site.md#install).


## <a name="connect-to-your-vnet"></a>Connettersi alla rete virtuale

>[!NOTE]
>È necessario avere diritti di amministratore per il computer client da cui ci si connette.
>
>

1. Per connettersi alla rete virtuale, nel computer client passare alle **connessioni VPN** nel portale di Azure e individuare quella creata, che ha lo stesso nome della rete virtuale. Selezionare **Connessione**. Se viene visualizzato un messaggio popup sul certificato, selezionare **Continua** per usare privilegi elevati.

2. Nella pagina di stato **Connessione** fare clic su **Connetti** per avviare la connessione. Se viene visualizzata la schermata **Seleziona certificato**, verificare che il certificato client visualizzato sia quello corretto. In caso contrario, selezionare il certificato corretto nell'elenco a discesa e quindi selezionare **OK**.

3. Se la connessione ha esito positivo, si riceverà una notifica **Connesso**.


### <a name="troubleshooting-p2s-connections"></a>Risoluzione dei problemi delle connessioni P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verificare la connessione VPN

1. Verificare che la connessione VPN sia attiva. Aprire un prompt dei comandi con privilegi elevati nel computer client ed eseguire **ipconfig/all**.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nell'intervallo di indirizzi di connettività da punto a sito specificato al momento della creazione della rete virtuale. I risultati dovrebbero essere simili all'esempio seguente:

   ```
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
   ```

## <a name="connect-to-a-virtual-machine"></a>Connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Aggiungere o rimuovere certificati radice attendibili

È possibile aggiungere e rimuovere certificati radice attendibili da Azure. Quando si rimuove un certificato radice, i client con un certificato generato da tale radice non potranno più eseguire l'autenticazione e connettersi. Per consentire ai client di eseguire l'autenticazione e connettersi nuovamente, è necessario installare un nuovo certificato client generato da un certificato radice considerato attendibile in Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Per aggiungere un certificato radice attendibile

In Azure è possibile aggiungere fino a 20 file CER di certificato radice trusted. Per istruzioni, vedere Caricare il file CER del certificato radice.

### <a name="to-remove-a-trusted-root-certificate"></a>Per rimuovere un certificato radice attendibile

1. Nella sezione **Connessioni VPN** della pagina della rete virtuale selezionare l'icona client per aprire la pagina **Connessione VPN da punto a sito**.

   ![Client](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Nella pagina **Connessione VPN da punto a sito** selezionare **Gestisci certificato** per aprire la pagina **Certificati**.

   ![Pagina Certificati](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Nella pagina **Certificati** selezionare i puntini di sospensione accanto al certificato da rimuovere e quindi fare clic su **Elimina**.

   ![Eliminare un certificato radice](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Revocare un certificato client

Se necessario, è possibile revocare un certificato client. L'elenco di revoche di certificati consente di negare in modo selettivo la connettività da punto a sito basata sui singoli certificati client. Questo metodo è diverso rispetto alla rimozione di un certificato radice attendibile. Se si rimuove un file con estensione cer del certificato radice attendibile da Azure, viene revocato l'accesso per tutti i certificati client generati o firmati dal certificato radice revocato. La revoca di un certificato client, anziché del certificato radice, consente di continuare a usare gli altri certificati generati dal certificato radice per l'autenticazione per la connessione da punto a sito.

La regola generale è quella di usare il certificato radice per gestire l'accesso a livello di team o organizzazione, usando i certificati client revocati per il controllo di accesso con granularità fine su singoli utenti.

### <a name="to-revoke-a-client-certificate"></a>Per revocare un certificato client

È possibile revocare un certificato client aggiungendo l'identificazione personale all'elenco di revoche di certificati.

1. Ottenere l'identificazione personale del certificato client. Per ulteriori informazioni, consultare [Come recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copiare le informazioni in un editor di testo e rimuovere tutti gli spazi in modo da avere una stringa continua.
3. Passare alla rete virtuale classica. Selezionare **Connessione VPN da punto a sito**, quindi selezionare **Gestisci certificato** per aprire la pagina **Certificati**.
4. Selezionare **Elenco di revoche** per aprire la pagina **Elenco di revoche**. 
5. Selezionare **Aggiungi certificato** per aprire la pagina **Aggiungi certificato all'elenco di revoche**.
6. Nella pagina **Identificazione personale** incollare l'identificazione personale del certificato come una riga di testo continua, senza spazi. Selezionare **OK** per terminare.

Dopo aver completato l'aggiornamento, il certificato non può più essere usato per la connessione. Ai client che provano a connettersi con questo certificato verrà visualizzato un messaggio che informa che il certificato non è più valido.

## <a name="point-to-site-faq"></a>Domande frequenti sulla connettività da punto a sito

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/). 

- Per altre informazioni sulla rete e sulle macchine virtuali Linux, vedere [Panoramica di rete delle macchine virtuali Linux e Azure](../virtual-machines/linux/network-overview.md).

- Per informazioni sulla risoluzione dei problemi della connessione da punto a sito, vedere [Risolvere i problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
