---
title: Creare un endpoint privato per una connessione sicuraCreate a Private Endpoint for a secure connection
titleSuffix: Azure Cognitive Search
description: Configurare un endpoint privato in una rete virtuale per una connessione sicura a un servizio Ricerca cognitiva di AzureSet up a private endpoint in a virtual network for a secure connection to an Azure Cognitive Search service
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945831"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Creare un endpoint privato per una connessione sicura a Ricerca cognitiva di Azure (anteprima)Create a Private Endpoint for a secure connection to Azure Cognitive Search (Preview)

In questo articolo usare il portale per creare una nuova istanza del servizio Ricerca cognitiva di Azure a cui non è possibile accedere tramite un indirizzo IP pubblico. Configurare quindi una macchina virtuale di Azure nella stessa rete virtuale e usarla per accedere al servizio di ricerca tramite un endpoint privato.

> [!Important]
> Il supporto dell'endpoint privato per Ricerca cognitiva di Azure è disponibile [su richiesta](https://aka.ms/SearchPrivateLinkRequestAccess) come anteprima ad accesso limitato. Le funzionalità di anteprima vengono fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Dopo aver concesso l'accesso all'anteprima, sarà possibile configurare gli endpoint privati per il servizio usando il portale di Azure o la [versione 2019-10-06-Preview dell'API REST](https://docs.microsoft.com/rest/api/searchmanagement/)di gestione.
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Perché usare l'endpoint privato per l'accesso sicuro?

[Gli endpoint privati](../private-link/private-endpoint-overview.md) per Ricerca cognitiva di Azure consentono a un client in una rete virtuale di accedere in modo sicuro ai dati in un indice di ricerca tramite un [collegamento privato.](../private-link/private-link-overview.md) L'endpoint privato usa un indirizzo IP dello [spazio di indirizzi](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) della rete virtuale per il servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato nella rete backbone Microsoft, eliminando l'esposizione da Internet pubblico. Per un elenco degli altri servizi PaaS che supportano Private Link, consultare la [sezione relativa](../private-link/private-link-overview.md#availability) alla disponibilità nella documentazione del prodotto.

Gli endpoint privati per il servizio di ricerca consentono di:Private endpoints for your search service enables you to:

- Bloccare tutte le connessioni nell'endpoint pubblico per il servizio di ricerca.
- Aumentare la sicurezza per la rete virtuale, consentendo di bloccare l'esfiltrazione dei dati dalla rete virtuale.
- Connettersi in modo sicuro al servizio di ricerca da reti locali che si connettono alla rete virtuale tramite [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoutes](../expressroute/expressroute-locations.md) con peering privato.

> [!NOTE]
> Ci sono attualmente alcune limitazioni nell'anteprima che si dovrebbe essere a conoscenza di:
> * Disponibile solo per i servizi di ricerca nel livello **Basic.** 
> * Disponibile nelle regioni Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti centro-meridionali, Australia orientale e Australia sud-orientale.
> * Quando l'endpoint del servizio è privato, alcune funzionalità del portale sono disabilitate. Sarà possibile visualizzare e gestire le informazioni sul livello di servizio, ma l'accesso al portale ai dati dell'indice e ai vari componenti del servizio, ad esempio l'indice, l'indicizzatore e le definizioni del set di competenze, è limitato per motivi di sicurezza.
> * Quando l'endpoint del servizio è privato, è necessario usare [l'API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/) per caricare documenti nell'indice.
> * È necessario usare il collegamento seguente per visualizzare l'opzione di supporto dell'endpoint privato nel portale di Azure:You must use the following link to see the private endpoint support option in the Azure portal:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Richiedere l'accesso 

Fai clic su [Richiedi accesso](https://aka.ms/SearchPrivateLinkRequestAccess) per iscriverti a questa funzione di anteprima. Il modulo richiede informazioni sull'utente, sulla società e sulla topologia generale della rete. Una volta esaminata la tua richiesta, riceverai un'email di conferma con ulteriori istruzioni.

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione verranno create una rete virtuale e una subnet per ospitare la macchina virtuale che verrà usata per accedere all'endpoint privato del servizio di ricerca.

1. Nella scheda Home page del portale di Azure selezionare **Crea una risorsa** > **Rete virtuale**di**rete** > .

1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere *MyVirtualNetwork* |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16* |
    | Subscription | Selezionare la propria sottoscrizione|
    | Resource group | Selezionare **Crea nuovo**, *immettere myResourceGroup*, quindi scegliere **OK** |
    | Location | Selezionare **Stati Uniti occidentali** o qualsiasi regione in uso|
    | Subnet - Nome | Inserisci *mySubnet* |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24* |
    |||

1. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Creare un servizio di ricerca con un endpoint privatoCreate a search service with a private endpoint

In questa sezione verrà creato un nuovo servizio Ricerca cognitiva di Azure con un endpoint privato. 

1. Nella parte superiore sinistra dello schermo nel portale di Azure selezionare **Crea una risorsa** > **Ricerca cognitiva**di**Azure Web** > .

1. In **Nuovo servizio di ricerca - Nozioni di base**immettere o selezionare le informazioni seguente:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **DETTAGLI ISTANZA** |  |
    | URL | Immettere un nome univoco. |
    | Location | Selezionare l'area specificata quando si richiede l'accesso a questa funzionalità di anteprima. |
    | Piano tariffario | Selezionare **Cambia livello prezzi** e scegliere Di **base**. Questo livello è necessario per l'anteprima. |
    |||
  
1. Selezionare **Avanti: Scala**.

1. Lasciare i valori come predefiniti e selezionare **Avanti: Rete**.

1. In **Nuovo servizio di ricerca - Rete**selezionare **Privato** per **Connettività endpoint (dati).**

1. In **Nuovo servizio di ricerca - Rete**, selezionare **Aggiungi** in Endpoint **privato**. 

1. In Crea endpoint privato immettere o selezionare le informazioni seguente:In **Create Private Endpoint**, enter or select this information:

    | Impostazione | valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | Location | selezionare **Stati Uniti occidentali**.|
    | Nome | Immettere *myPrivateEndpoint*.  |
    | Sottorisorsa di destinazione | Lasciare il valore predefinito **searchService**. |
    | **Networking** |  |
    | Rete virtuale  | Selezionare *MyVirtualNetwork* dal gruppo di risorse *myResourceGroup*. |
    | Subnet | Selezionare  *mySubnet*. |
    | **INTEGRAZIONE DNS PRIVATO** |  |
    | Integra con la zona DNS privato  | Lasciare il valore predefinito **Sì**. |
    | Zona DNS privato  | Lasciare il valore predefinito di privatelink.search.windows.net . |
    |||

1. Selezionare **OK**. 

1. Selezionare **Revisione e creazione**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 

1. Quando viene visualizzato il messaggio **Convalida superata** , selezionare **Crea**. 

1. Al termine del provisioning del nuovo servizio, passare alla risorsa appena creata.

1. Selezionare **Tasti** dal menu del contenuto a sinistra.

1. Copiare la **chiave di amministrazione principale** per un secondo momento, quando ci si connette al servizio.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Nella parte superiore sinistra dello schermo nel portale di Azure selezionare Crea una**macchina virtuale**di**calcolo** >  **delle risorse** > .

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **Stati Uniti occidentali** o qualsiasi regione in uso. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **REGOLE DELLE PORTE IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare l'impostazione predefinita **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Lasciare il valore RDP predefinito **(3389)**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: Dischi**.

1. In **Create a virtual machine - Disks**, lasciare le impostazioni predefinite e selezionare **Next: Networking**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Rete virtuale | Lasciare il valore predefinito **MyVirtualNetwork**.  |
    | Spazio degli indirizzi | Lasciare l'impostazione predefinita **10.1.0.0/24**.|
    | Subnet | Lasciare l'impostazione predefinita **mySubnet (10.1.0.0/24)**.|
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**.|
    ||

1. Selezionare **Revisione e creazione**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata** , selezionare **Crea**. 


## <a name="connect-to-the-vm"></a>Connettersi alla VM

Scaricare e quindi connettersi alla macchina virtuale myVm come segue:Download and then connect to the VM *myVm* as follows:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione rdp scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un account diverso**per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  


## <a name="test-connections"></a>Testare le connessioni

In questa sezione verrà verificato l'accesso alla rete privata al servizio di ricerca e si connetterà privatamente all'endpoint privato.

Ricordare dall'introduzione che tutte le interazioni con il servizio di ricerca richiedono [l'API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/). Il portale e .NET SDK non sono supportati in questa anteprima.

1. Nel desktop remoto di  *myVM* aprire PowerShell.

1. Immettere 'nslookup [nome servizio di ricerca].search.windows.net'

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Dalla macchina virtuale connettersi al servizio di ricerca e creare un indice. È possibile seguire questa [guida introduttiva](search-get-started-postman.md) per creare un nuovo indice di ricerca nel servizio in Postman usando l'API REST. La configurazione delle richieste da Postman richiede l'endpoint del servizio di ricerca (https://[nome servizio di ricerca].search.windows.net) e l'API-key di amministrazione copiata in un passaggio precedente.

1. Completare la guida introduttiva dalla macchina virtuale è la conferma che il servizio è completamente operativo.

1. Chiudere la connessione desktop remoto a *myVM*. 

1. Per verificare che il servizio non sia accessibile in un endpoint pubblico, aprire Postman sulla workstation locale e tentare le prime attività nella guida introduttiva. Se viene visualizzato un errore che indica che il server remoto non esiste, è stato configurato correttamente un endpoint privato per il servizio di ricerca.

## <a name="clean-up-resources"></a>Pulire le risorse 
Al termine dell'uso dell'endpoint privato, del servizio di ricerca e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse in esso contenute:When you're done using the Private Endpoint, search service, and the VM, delete the resource group and all of the resources it contains:
1. Immettere *myResourceGroup* nella casella **di ricerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca. 
1. Selezionare **Elimina gruppo di risorse**. 
1. Immettere *myResourceGroup* per **TYPE THE RESOURCE GROUP NAME** e selezionare **Delete**.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata una macchina virtuale in una rete virtuale e un servizio di ricerca con un endpoint privato. Si è connessi alla macchina virtuale da Internet e si è comunicati in modo sicuro al servizio di ricerca tramite Private Link. Per altre informazioni sull'endpoint privato, vedere [Che cos'è l'endpoint privato di Azure?.](../private-link/private-endpoint-overview.md)
