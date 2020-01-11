---
title: Creare un endpoint privato per le connessioni protette
titleSuffix: Azure Cognitive Search
description: Attualmente in anteprima, è possibile limitare l'accesso a un endpoint del servizio di ricerca usando un endpoint privato e una connessione VNet sicura.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865012"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>Limitare l'accesso ad Azure ricerca cognitiva usando un endpoint privato e una connessione di rete virtuale.

> [!IMPORTANT] 
> Il supporto per l'endpoint privato è attualmente disponibile come anteprima con accesso limitato. Questa anteprima è disponibile solo per i servizi di ricerca per il **livello Basic**.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità è disponibile nell' [API REST versione 2019-10-01-Preview](search-api-preview.md) . Al momento non è disponibile alcun supporto per il portale o .NET SDK.

In questo articolo viene illustrato come creare un nuovo servizio di ricerca accessibile tramite connessioni sicure, senza accesso da indirizzi IP pubblici. Le connessioni client sono consentite da macchine virtuali di Azure distribuite nella stessa rete virtuale del servizio.

## <a name="about-private-endpoint-support"></a>Informazioni sul supporto per endpoint privati

Gli [endpoint privati](../private-link/private-endpoint-overview.md) per Azure ricerca cognitiva consentono a un client in una rete virtuale di accedere in modo sicuro ai dati tramite un [collegamento privato](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello [spazio di indirizzi della rete virtuale](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) per il servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione alla rete Internet pubblica. Per un elenco dei servizi PaaS che supportano il collegamento privato, vedere la sezione relativa alla [disponibilità](../private-link/private-link-overview.md#availability) nella documentazione del prodotto.

Un endpoint privato per il servizio di ricerca consente di:

+ Blocca tutte le connessioni nell'endpoint pubblico per il servizio di ricerca.
+ Aumentare la sicurezza per la rete virtuale consentendo di bloccare exfiltration di dati dalla rete virtuale.
+ Connettersi in modo sicuro al servizio di ricerca da reti locali che si connettono alla rete virtuale tramite [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [delle expressroute](../expressroute/expressroute-locations.md) con peering privato.

> [!NOTE]
> Quando l'endpoint del servizio è privato, alcune funzionalità del portale sono disabilitate. Sarà possibile visualizzare e gestire le informazioni sul livello di servizio, ma l'accesso al portale per i dati di indicizzazione e i vari componenti del servizio, ad esempio le definizioni di indice, indicizzatore e competenze, è limitato per motivi di sicurezza.

## <a name="request-access"></a>Richiedere l'accesso 

Fare clic su [Richiedi accesso](https://aka.ms/SearchPrivateLinkRequestAccess) per iscriversi a questa funzionalità in anteprima. Il modulo richiede le informazioni relative all'utente, alla società e alla topologia di rete generale. Una volta esaminata la richiesta, si riceverà un messaggio di posta elettronica di conferma con istruzioni aggiuntive.

## <a name="create-a-vm"></a>Creare una VM
In questa sezione si creeranno una rete virtuale e una subnet per ospitare la macchina virtuale che verrà usata per accedere all'endpoint privato del servizio di ricerca.

### <a name="set-up-the-virtual-network"></a>Configurare la rete virtuale
1. Accedere al [portale di Azure](https://portal.azure.com).
1. In alto a sinistra selezionare **Crea una risorsa** ** > rete** > **rete virtuale**.
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


### <a name="create-a-virtual-machine"></a>Crea una macchina virtuale

1. Nella parte superiore sinistra della pagina principale del portale selezionare **Crea una risorsa** > **calcolo** > **macchina virtuale**.

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
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**. |
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


## <a name="create-your-search-service-with-a-private-endpoint"></a>Creare il servizio di ricerca con un endpoint privato

In questa sezione si creerà un nuovo servizio ricerca cognitiva di Azure con un endpoint privato. 

1. Nella parte superiore sinistra della pagina principale del portale selezionare **Crea una risorsa** > **Web** > **Azure ricerca cognitiva**.

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
1. Una volta creato il servizio, passare alla risorsa appena creata.
1. Selezionare **chiavi** dal menu contenuto a sinistra.
1. Copiare la **chiave di amministrazione primaria** da usare nel passaggio successivo.

 
## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione rdp scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. È possibile che venga visualizzato un avviso di certificato al momento dell'accesso. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  


## <a name="access-the-search-service-privately-from-the-vm"></a>Accedere al servizio di ricerca privatamente dalla macchina virtuale

In questa sezione si verificherà l'accesso alla rete privata al servizio di ricerca e si effettuerà la connessione privata all'account di archiviazione usando l'endpoint privato.

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
1. Seguire questa [Guida introduttiva](search-get-started-postman.md) dalla macchina virtuale per creare un nuovo indice di ricerca nel servizio in un post usando l'API REST.

1. Provare diverse richieste in Posting nella workstation locale.

1. Se è possibile completare la Guida introduttiva dalla VM, ma viene visualizzato un errore che segnala che il server remoto non esiste nella workstation locale, è stato configurato correttamente un endpoint privato per il servizio di ricerca.

1. Chiudere la connessione Desktop remoto a *myVM*. 

## <a name="clean-up-resources"></a>Pulire le risorse 

Al termine, usare l'endpoint privato, l'account del servizio di ricerca e la macchina virtuale, eliminare il gruppo di risorse e tutte le risorse in esso contenute: 

1. Immettere *myResourceGroup* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca. 
1. Selezionare **Elimina gruppo di risorse**. 
1. Immettere *myResourceGroup* per **digitare il nome del gruppo di risorse** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una VM in una rete virtuale e un servizio di ricerca con un endpoint privato. È stata effettuata la connessione alla macchina virtuale da Internet e la comunicazione protetta con il servizio di ricerca usando un collegamento privato. 

> [!div class="nextstepaction"]
> [Che cos'è l'endpoint privato di Azure?](../private-link/private-endpoint-overview.md)
