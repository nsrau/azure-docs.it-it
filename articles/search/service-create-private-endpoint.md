---
title: Creare un endpoint privato per una connessione protetta
titleSuffix: Azure Cognitive Search
description: Configurare un endpoint privato in una rete virtuale per una connessione sicura a un servizio ricerca cognitiva di Azure
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945831"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Creare un endpoint privato per una connessione sicura al ricerca cognitiva di Azure (anteprima)

In questo articolo viene usato il portale per creare una nuova istanza del servizio ricerca cognitiva di Azure a cui non è possibile accedere tramite un indirizzo IP pubblico. Configurare quindi una macchina virtuale di Azure nella stessa rete virtuale e usarla per accedere al servizio di ricerca tramite un endpoint privato.

> [!Important]
> Il supporto per endpoint privati per Azure ricerca cognitiva è disponibile [su richiesta](https://aka.ms/SearchPrivateLinkRequestAccess) come anteprima con accesso limitato. Le funzionalità di anteprima vengono fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Una volta ottenuto l'accesso all'anteprima, sarà possibile configurare endpoint privati per il servizio usando il portale di Azure o l' [API REST di gestione versione 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Perché usare endpoint privato per l'accesso sicuro?

Gli [endpoint privati](../private-link/private-endpoint-overview.md) per Azure ricerca cognitiva consentono a un client in una rete virtuale di accedere in modo sicuro ai dati in un indice di ricerca tramite un [collegamento privato](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello [spazio di indirizzi della rete virtuale](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) per il servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica. Per un elenco di altri servizi PaaS che supportano il collegamento privato, vedere la sezione relativa alla [disponibilità](../private-link/private-link-overview.md#availability) nella documentazione del prodotto.

Gli endpoint privati per il servizio di ricerca consentono di:

- Blocca tutte le connessioni nell'endpoint pubblico per il servizio di ricerca.
- Aumentare la sicurezza per la rete virtuale, consentendo di bloccare exfiltration di dati dalla rete virtuale.
- Connettersi in modo sicuro al servizio di ricerca da reti locali che si connettono alla rete virtuale tramite [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [delle expressroute](../expressroute/expressroute-locations.md) con peering privato.

> [!NOTE]
> Esistono attualmente alcune limitazioni nell'anteprima che è necessario conoscere:
> * Disponibile solo per i servizi di ricerca per il livello **Basic** . 
> * Disponibile nelle aree Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti centro-meridionali, Australia orientale e Australia sudorientale.
> * Quando l'endpoint del servizio è privato, alcune funzionalità del portale sono disabilitate. Sarà possibile visualizzare e gestire le informazioni sul livello di servizio, ma l'accesso al portale per i dati di indicizzazione e i vari componenti del servizio, ad esempio le definizioni di indice, indicizzatore e competenze, è limitato per motivi di sicurezza.
> * Quando l'endpoint del servizio è privato, è necessario usare l' [API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/) per caricare i documenti nell'indice.
> * È necessario usare il collegamento seguente per visualizzare l'opzione di supporto dell'endpoint privato nella portale di Azure: https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Richiedere l'accesso 

Fare clic su [Richiedi accesso](https://aka.ms/SearchPrivateLinkRequestAccess) per iscriversi a questa funzionalità in anteprima. Il modulo richiede le informazioni relative all'utente, alla società e alla topologia di rete generale. Una volta esaminata la richiesta, si riceverà un messaggio di posta elettronica di conferma con istruzioni aggiuntive.

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si creeranno una rete virtuale e una subnet per ospitare la macchina virtuale che verrà usata per accedere all'endpoint privato del servizio di ricerca.

1. Nella scheda Home portale di Azure selezionare **Crea una risorsa** ** > rete** **rete virtuale** > .

1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere *MyVirtualNetwork* |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16* |
    | Sottoscrizione | Selezionare la propria sottoscrizione|
    | Gruppo di risorse | Selezionare **Crea nuovo**, immettere *myResourceGroup*, quindi fare clic su **OK** . |
    | Percorso | Selezionare **Stati Uniti occidentali** o qualsiasi area in uso|
    | Subnet - Nome | Immettere la *subnet* |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24* |
    |||

1. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Creare un servizio di ricerca con un endpoint privato

In questa sezione si creerà un nuovo servizio ricerca cognitiva di Azure con un endpoint privato. 

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** ** >  > ** **Azure ricerca cognitiva**.

1. In **New servizio di ricerca-nozioni di base**immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **DETTAGLI DELL'ISTANZA** |  |
    | URL | Immettere un nome univoco. |
    | Percorso | Consente di selezionare l'area specificata durante la richiesta di accesso a questa funzionalità in anteprima. |
    | Piano tariffario | Selezionare **modifica piano tariffario** e scegliere di **base**. Questo livello è obbligatorio per l'anteprima. |
    |||
  
1. Selezionare **Next: scale**.

1. Lasciare i valori predefiniti e selezionare **Avanti: rete**.

1. In **nuovo servizio di ricerca-rete**selezionare **privato** per **connettività endpoint (dati)** .

1. In **nuovo servizio di ricerca-rete**selezionare **+ Aggiungi** in **endpoint privato**. 

1. In **Crea endpoint privato**immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | Percorso | Selezionare **Stati Uniti occidentali**.|
    | Nome | Immettere *myPrivateEndpoint*.  |
    | Sottorisorsa di destinazione | Lasciare il valore predefinito **SearchService**. |
    | **RETE** |  |
    | Rete virtuale  | Selezionare *MyVirtualNetwork* dal gruppo di risorse *myResourceGroup*. |
    | Subnet | Selezionare  *mySubnet*. |
    | **INTEGRAZIONE DNS PRIVATO** |  |
    | Integra con la zona DNS privato  | Lasciare l'impostazione predefinita **Sì**. |
    | Zona DNS privato  | Lasciare il valore predefinito * * (nuovo) privatelink.search.windows.net * *. |
    |||

1. Selezionare **OK**. 

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**. 

1. Al termine del provisioning del nuovo servizio, passare alla risorsa appena creata.

1. Selezionare **chiavi** dal menu contenuto a sinistra.

1. Copiare la **chiave di amministrazione primaria** per un momento successivo, quando ci si connette al servizio.

## <a name="create-a-virtual-machine"></a>Crea una macchina virtuale

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa** > **calcolo** > **macchina virtuale**.

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Area | Selezionare **Stati Uniti occidentali** o qualsiasi area in uso. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensioni | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare l'impostazione predefinita **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Lasciare il **protocollo RDP predefinito (3389)** . |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: dischi**.

1. In **creare una macchina virtuale-dischi**lasciare le impostazioni predefinite e selezionare **Avanti: rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Rete virtuale | Lasciare l'impostazione predefinita **MyVirtualNetwork**.  |
    | Spazio degli indirizzi | Lasciare l'impostazione predefinita **10.1.0.0/24**.|
    | Subnet | Lasciare l'impostazione predefinita **mySubnet (10.1.0.0/24)** .|
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**.|
    ||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**. 


## <a name="connect-to-the-vm"></a>Connettersi alla VM

Scaricare e connettersi alla macchina virtuale *myVm* come segue:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione rdp scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  


## <a name="test-connections"></a>Testare le connessioni

In questa sezione si verificherà l'accesso alla rete privata al servizio di ricerca e si effettuerà la connessione privata a usando l'endpoint privato.

Tenere presente che tutte le interazioni con il servizio di ricerca richiedono l' [API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/). Il portale e .NET SDK non sono supportati in questa versione di anteprima.

1. Nel desktop remoto di  *myVM* aprire PowerShell.

1. Immettere ' nslookup [nome servizio di ricerca]. search. Windows. NET '

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Dalla macchina virtuale connettersi al servizio di ricerca e creare un indice. È possibile seguire questa [Guida introduttiva](search-get-started-postman.md) per creare un nuovo indice di ricerca nel servizio in un post con l'API REST. Per la configurazione delle richieste da posting è necessario l'endpoint del servizio di ricerca (https://[nome del servizio di ricerca]. search. Windows. NET) e la chiave API di amministrazione copiata in un passaggio precedente.

1. Per completare la Guida introduttiva dalla macchina virtuale, è consigliabile confermare che il servizio è completamente operativo.

1. Chiudere la connessione Desktop remoto a *myVM*. 

1. Per verificare che il servizio non sia accessibile in un endpoint pubblico, aprire il post nella workstation locale e provare le prime attività nella Guida introduttiva. Se viene visualizzato un errore che segnala che il server remoto non esiste, è stato configurato correttamente un endpoint privato per il servizio di ricerca.

## <a name="clean-up-resources"></a>Pulire le risorse 
Al termine dell'operazione, eliminare il gruppo di risorse e tutte le risorse in esso contenute usando l'endpoint privato, il servizio di ricerca e la macchina virtuale:
1. Immettere *myResourceGroup* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca. 
1. Selezionare **Elimina gruppo di risorse**. 
1. Immettere *myResourceGroup* per **digitare il nome del gruppo di risorse** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata una VM in una rete virtuale e un servizio di ricerca con un endpoint privato. È stata effettuata la connessione alla macchina virtuale da Internet e la comunicazione protetta con il servizio di ricerca usando un collegamento privato. Per altre informazioni sull'endpoint privato, vedere [che cos'è endpoint privato di Azure?](../private-link/private-endpoint-overview.md).
