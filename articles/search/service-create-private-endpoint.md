---
title: Creare un endpoint privato per una connessione protetta
titleSuffix: Azure Cognitive Search
description: Configurare un endpoint privato in una rete virtuale per una connessione sicura a un servizio ricerca cognitiva di Azure
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 0cfa7b63d1ce9dd4d9b40cd0eedac247f9c56437
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935756"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Creare un endpoint privato per una connessione sicura ad Azure ricerca cognitiva

In questo articolo si userà il portale di Azure per creare una nuova istanza del servizio ricerca cognitiva di Azure a cui non è possibile accedere tramite Internet. Si configurerà quindi una macchina virtuale di Azure nella stessa rete virtuale e la si userà per accedere al servizio di ricerca tramite un endpoint privato.

Gli endpoint privati vengono forniti dal [collegamento privato di Azure](../private-link/private-link-overview.md)come servizio separato. Per ulteriori informazioni sui costi, vedere la [pagina](https://azure.microsoft.com/pricing/details/private-link/)relativa ai prezzi.

> [!Important]
> Il supporto per gli endpoint privati per ricerca cognitiva di Azure può essere configurato usando il portale di Azure o l' [API REST di gestione versione 2020-03-13](/rest/api/searchmanagement/). Quando l'endpoint del servizio è privato, alcune funzionalità del portale sono disabilitate. Sarà possibile visualizzare e gestire le informazioni sul livello di servizio, ma l'accesso al portale per i dati di indicizzazione e i vari componenti del servizio, ad esempio le definizioni di indice, indicizzatore e competenze, è limitato per motivi di sicurezza.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Perché usare un endpoint privato per l'accesso sicuro?

Gli [endpoint privati](../private-link/private-endpoint-overview.md) per Azure ricerca cognitiva consentono a un client in una rete virtuale di accedere in modo sicuro ai dati in un indice di ricerca tramite un [collegamento privato](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello [spazio di indirizzi della rete virtuale](../virtual-network/private-ip-addresses.md) per il servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica. Per un elenco di altri servizi PaaS che supportano il collegamento privato, vedere la sezione relativa alla [disponibilità](../private-link/private-link-overview.md#availability) nella documentazione del prodotto.

Gli endpoint privati per il servizio di ricerca consentono di:

- Blocca tutte le connessioni nell'endpoint pubblico per il servizio di ricerca.
- Aumentare la sicurezza per la rete virtuale, consentendo di bloccare exfiltration di dati dalla rete virtuale.
- Connettersi in modo sicuro al servizio di ricerca da reti locali che si connettono alla rete virtuale tramite [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [delle expressroute](../expressroute/expressroute-locations.md) con peering privato.

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si creeranno una rete virtuale e una subnet per ospitare la macchina virtuale che verrà usata per accedere all'endpoint privato del servizio di ricerca.

1. Nella scheda Home portale di Azure selezionare **Crea una risorsa**  >  **Networking**  >  **rete rete virtuale**.

1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Sottoscrizione | Selezionare la propria sottoscrizione|
    | Gruppo di risorse | Selezionare **Crea nuovo**, immettere *myResourceGroup*, quindi fare clic su **OK** . |
    | Nome | Immettere *MyVirtualNetwork* |
    | Region | Selezionare l'area geografica desiderata |
    |||

1. Lasciare le impostazioni predefinite per le altre impostazioni. Fare clic su **Verifica + crea** e quindi su **Crea**

## <a name="create-a-search-service-with-a-private-endpoint"></a>Creare un servizio di ricerca con un endpoint privato

In questa sezione si creerà un nuovo servizio ricerca cognitiva di Azure con un endpoint privato. 

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa**  >  **Web**  >  **Azure ricerca cognitiva**.

1. In **New servizio di ricerca-nozioni di base**immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **DETTAGLI DELL'ISTANZA** |  |
    | URL | Immettere un nome univoco. |
    | Posizione | Selezionare l'area geografica desiderata. |
    | Piano tariffario | Selezionare **Cambia piano tariffario** e scegliere il livello di servizio desiderato. (Non supporta il livello **gratuito** . Deve essere di **base** o superiore. |
    |||
  
1. Selezionare **Next: scale**.

1. Lasciare i valori predefiniti e selezionare **Avanti: rete**.

1. In **nuovo servizio di ricerca-rete**selezionare **privato** per **connettività endpoint (dati)**.

1. In **nuovo servizio di ricerca-rete**selezionare **+ Aggiungi** in **endpoint privato**. 

1. In **Crea endpoint privato**immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | Posizione | selezionare **Stati Uniti occidentali**.|
    | Nome | Immettere *myPrivateEndpoint*.  |
    | Sottorisorsa di destinazione | Lasciare il valore predefinito **SearchService**. |
    | **RETE** |  |
    | Rete virtuale  | Selezionare *MyVirtualNetwork* dal gruppo di risorse *myResourceGroup*. |
    | Subnet | Selezionare *mySubnet*. |
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

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa**  >  **calcolo**  >  **macchina virtuale**.

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **Stati Uniti occidentali** o qualsiasi area in uso. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare l'impostazione predefinita **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Lasciare il **protocollo RDP predefinito (3389)**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: Dischi**.

1. In **Crea macchina virtuale - Dischi** mantenere le impostazioni predefinite e selezionare **Avanti: Rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | valore |
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

Quando l'endpoint del servizio di ricerca è privato, alcune funzionalità del portale sono disabilitate. Sarà possibile visualizzare e gestire le impostazioni del livello di servizio, ma l'accesso al portale per i dati di indicizzazione e vari altri componenti nel servizio, ad esempio le definizioni di indice, indicizzatore e competenze, è limitato per motivi di sicurezza.

1. Nel Desktop remoto di *myVm1* aprire PowerShell.

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

1. Chiudere la connessione Desktop remoto a *myVM*. 

1. Per verificare che il servizio non sia accessibile in un endpoint pubblico, aprire il post nella workstation locale e provare le prime attività nella Guida introduttiva. Se viene visualizzato un errore che segnala che il server remoto non esiste, è stato configurato correttamente un endpoint privato per il servizio di ricerca.

## <a name="clean-up-resources"></a>Pulire le risorse 
Al termine dell'operazione, eliminare il gruppo di risorse e tutte le risorse in esso contenute usando l'endpoint privato, il servizio di ricerca e la macchina virtuale:
1. Immettere *myResourceGroup*   nella casella di **ricerca** nella parte superiore del portale e selezionare *myResourceGroup*nei   Risultati della ricerca. 
1. Selezionare **Elimina gruppo di risorse**. 
1. Immettere *myResourceGroup*   per **digitare il nome del gruppo di risorse** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata una VM in una rete virtuale e un servizio di ricerca con un endpoint privato. È stata effettuata la connessione alla macchina virtuale da Internet e la comunicazione protetta con il servizio di ricerca usando un collegamento privato. Per altre informazioni sull'endpoint privato, vedere [che cos'è endpoint privato di Azure?](../private-link/private-endpoint-overview.md).