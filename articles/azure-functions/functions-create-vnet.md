---
title: Integrare funzioni di Azure con una rete virtuale di Azure
description: Esercitazione dettagliata che illustra come connettere una funzione a una rete virtuale di Azure
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: bc6c87a28078d25a212a681206258d6d369f2867
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575534"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Esercitazione: integrare funzioni con una rete virtuale di Azure

Questa esercitazione illustra come usare funzioni di Azure per connettersi alle risorse in una rete virtuale di Azure. verrà creata una funzione che ha accesso sia a Internet che a una macchina virtuale che esegue WordPress nella rete virtuale.

> [!div class="checklist"]
> * Creare un'app per le funzioni nel piano Premium
> * Distribuire un sito WordPress in una macchina virtuale in una rete virtuale
> * Connettere l'app per le funzioni alla rete virtuale
> * Creare un proxy di funzione per accedere alle risorse di WordPress
> * Richiedere un file WordPress dall'interno della rete virtuale

## <a name="topology"></a>Topologia

Il diagramma seguente illustra l'architettura della soluzione creata:

 ![Interfaccia utente per l'integrazione della rete virtuale](./media/functions-create-vnet/topology.png)

Le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di app Web nel servizio app Azure, che include la funzionalità di integrazione VNet. Per altre informazioni sull'integrazione di VNet, incluse la risoluzione dei problemi e la configurazione avanzata, vedere [integrare l'app con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione, è importante comprendere gli indirizzi IP e la suddivisione in subnet. È possibile iniziare con [questo articolo che illustra le nozioni di base sull'indirizzamento e la suddivisione in subnet](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Molti altri articoli e video sono disponibili online.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-function-app-in-a-premium-plan"></a>Creare un'app per le funzioni in un piano Premium

In primo luogo, si crea un'app per le funzioni nel [piano Premium]. Questo piano offre una scalabilità senza server per supportare l'integrazione della rete virtuale.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

È possibile aggiungere l'app per le funzioni al dashboard selezionando l'icona Aggiungi nell'angolo in alto a destra. Il blocco rende più semplice tornare a questa app per le funzioni dopo aver creato la macchina virtuale.

## <a name="create-a-vm-inside-a-virtual-network"></a>Creare una VM all'interno di una rete virtuale

Successivamente, creare una macchina virtuale preconfigurata che esegue WordPress all'interno di una rete virtuale ([Wordpress LEMP7 max performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). Viene usata una macchina virtuale WordPress a causa del costo e della convenienza ridotti. Questo stesso scenario funziona con tutte le risorse in una rete virtuale, ad esempio le API REST, gli ambienti del servizio app e altri servizi di Azure. 

1. Nel portale fare clic su **+ Crea una risorsa** nel riquadro di spostamento a sinistra, nel campo di ricerca digitare `WordPress LEMP7 Max Performance`e premere INVIO.

1. Scegliere **Wordpress Lemp max performance** nei risultati della ricerca. Selezionare un piano software di **Wordpress Lemp max performance per CentOS** come **piano software** e selezionare **Crea**.

1. Nella scheda **nozioni di base** usare le impostazioni della macchina virtuale come specificato nella tabella sotto l'immagine:

    ![Scheda nozioni di base per la creazione di una macchina virtuale](./media/functions-create-vnet/create-vm-1.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Scegliere `myResourceGroup`o il gruppo di risorse creato con l'app per le funzioni. L'uso dello stesso gruppo di risorse per l'app per le funzioni, la macchina virtuale WordPress e il piano di hosting rende più semplice la pulizia delle risorse al termine dell'esercitazione. |
    | **Nome macchina virtuale** | VNET-Wordpress | Il nome della macchina virtuale deve essere univoco nel gruppo di risorse |
    | **[Area](https://azure.microsoft.com/regions/)** | (Europa) Europa occidentale | Scegliere un'area vicina o vicina alle funzioni che accedono alla macchina virtuale. |
    | **Dimensione** | B1S | Scegliere **modifica dimensioni** , quindi selezionare l'immagine standard B1S, che include 1 vCPU e 1 GB di memoria. |
    | **Tipo di autenticazione** | Password | Per usare l'autenticazione della password, è necessario specificare anche un **nome utente**, una **password**sicura e quindi **confermare la password**. Per questa esercitazione non è necessario accedere alla macchina virtuale, a meno che non sia necessario risolvere il problema. |

1. Scegliere la scheda **rete** e in Configura reti virtuali selezionare **Crea nuovo**.

1. In **Crea rete virtuale**usare le impostazioni nella tabella sotto l'immagine:

    ![Scheda rete di Crea macchina virtuale](./media/functions-create-vnet/create-vm-2.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | MyResourceGroup-VNET | È possibile usare il nome predefinito generato per la rete virtuale. |
    | **Intervallo di indirizzi** | 10.10.0.0/16 | Usare un singolo intervallo di indirizzi per la rete virtuale. |
    | **Nome della subnet** | Esercitazione-NET | Nome della subnet. |
    | **Intervallo di indirizzi** (subnet) | 10.10.1.0/24   | Le dimensioni della subnet definiscono il numero di interfacce che è possibile aggiungere alla subnet. Questa subnet viene usata dal sito WordPress.  Una subnet `/24` fornisce 254 indirizzi host. |

1. Selezionare **OK** per creare la rete virtuale.

1. Tornare alla scheda **rete** e scegliere **nessuno** per l' **indirizzo IP pubblico**.

1. Scegliere la scheda **gestione** , quindi in **account di archiviazione di diagnostica**scegliere l'account di archiviazione creato con l'app per le funzioni.

1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea**. Il processo di creazione della macchina virtuale richiede alcuni minuti. La VM creata può accedere solo alla rete virtuale.

1. Dopo aver creato la VM, scegliere **Vai alla risorsa** per visualizzare la pagina per la nuova macchina virtuale, quindi scegliere **rete** in **Impostazioni**.

1. Verificare che non sia presente alcun **indirizzo IP pubblico**. Prendere nota dell' **indirizzo IP privato**, che viene usato per connettersi alla macchina virtuale dall'app per le funzioni.

    ![Impostazioni di rete nella macchina virtuale](./media/functions-create-vnet/vm-networking.png)

A questo punto si dispone di un sito WordPress distribuito interamente nella rete virtuale. Il sito non è accessibile dalla rete Internet pubblica.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Connettere l'app per le funzioni alla rete virtuale

Con un sito WordPress in esecuzione in una macchina virtuale in una rete virtuale, è ora possibile connettere l'app per le funzioni a tale rete virtuale.

1. Nella nuova app per le funzioni selezionare **funzionalità della piattaforma** > **rete**.

    ![Scegliere la rete nell'app per le funzioni](./media/functions-create-vnet/networking-0.png)

1. In **integrazione VNet**selezionare **fare clic qui per configurare**.

    ![Stato per la configurazione di una funzionalità di rete](./media/functions-create-vnet/Networking-1.png)

1. Nella pagina di integrazione della rete virtuale selezionare **Aggiungi VNet (anteprima)** .

    ![Aggiungere l'anteprima dell'integrazione VNet](./media/functions-create-vnet/networking-2.png)

1. In **stato funzionalità di rete**usare le impostazioni nella tabella sotto l'immagine:

    ![Definire la rete virtuale dell'app per le funzioni](./media/functions-create-vnet/networking-3.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Rete virtuale** | MyResourceGroup-VNET | Questa rete virtuale è quella creata in precedenza. |
    | **Subnet** | Crea nuova subnet | Creare una subnet nella rete virtuale da usare per l'app per le funzioni. L'integrazione di VNet deve essere configurata per l'uso di una subnet vuota. Non è importante che le funzioni usino una subnet diversa da quella della macchina virtuale. La rete virtuale instrada automaticamente il traffico tra le due subnet. |
    | **Nome della subnet** | Funzione-NET | Nome della nuova subnet. |
    | **Blocco di indirizzi della rete virtuale** | 10.10.0.0/16 | Scegliere lo stesso blocco di indirizzi usato dal sito WordPress. È necessario definire un solo blocco di indirizzi. |
    | **Intervallo di indirizzi** | 10.10.2.0/24   | Le dimensioni della subnet limitano il numero totale di istanze per le quali l'app per le funzioni del piano Premium può essere scalata. Questo esempio usa una subnet `/24` con 254 indirizzi host disponibili. Questa subnet viene sottoposta a provisioning eccessivo, ma facile da calcolare. |

1. Selezionare **OK** per aggiungere la subnet. Chiudere le pagine di integrazione VNet e di stato della funzionalità di rete per tornare alla pagina dell'app per le funzioni.

L'app per le funzioni può ora accedere alla rete virtuale in cui è in esecuzione il sito WordPress. Usare quindi [proxy di funzioni di Azure](functions-proxies.md) per restituire un file dal sito WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Creare un proxy per accedere alle risorse della macchina virtuale

Con l'integrazione di VNet abilitata, è possibile creare un proxy nell'app per le funzioni per inviare le richieste alla macchina virtuale in esecuzione nella rete virtuale.

1. Nell'app per le funzioni selezionare **proxy** >  **+** , quindi usare le impostazioni proxy nella tabella sotto l'immagine:

    ![Definire le impostazioni del proxy](./media/functions-create-vnet/create-proxy.png)

    | Impostazione  | Valore consigliato  | Descrizione      |
    | -------- | ---------------- | ---------------- |
    | **Nome** | Impianto | Il nome può essere qualsiasi valore. Viene usato per identificare il proxy. |
    | **Modello di route** | /plant | Route mappata a una risorsa VM. |
    | **URL back-end** | http://< YOUR_VM_IP >/wp-content/themes/twentyseventeen/assets/images/header.jpg | Sostituire `<YOUR_VM_IP>` con l'indirizzo IP della macchina virtuale WordPress creata in precedenza. Questo mapping restituisce un singolo file dal sito. |

1. Selezionare **Crea** per aggiungere il proxy all'app per le funzioni.

## <a name="try-it-out"></a>Prova

1. Nel browser provare ad accedere all'URL usato come **URL back-end**. Come previsto, si verifica il timeout della richiesta. Si verifica un timeout perché il sito WordPress è connesso solo alla rete virtuale e non a Internet.

1. Copiare il valore di **URL proxy** dal nuovo proxy e incollarlo nella barra degli indirizzi del browser. L'immagine restituita si trova nel sito WordPress in esecuzione all'interno della rete virtuale.

    ![File di immagine impianto restituito dal sito WordPress](./media/functions-create-vnet/plant.png)

L'app per le funzioni è connessa sia a Internet che alla rete virtuale. Il proxy riceve una richiesta attraverso la rete Internet pubblica e quindi funge da proxy HTTP semplice per l'inoltro della richiesta alla rete virtuale connessa. Il proxy inoltra quindi la risposta all'utente tramite Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione il sito WordPress funge da API che viene chiamata usando un proxy nell'app per le funzioni. Questo scenario costituisce un'esercitazione efficace perché è facile da configurare e visualizzare. È possibile usare qualsiasi altra API distribuita in una rete virtuale. È anche possibile creare una funzione con il codice che chiama le API distribuite nella rete virtuale. Uno scenario più realistico è una funzione che usa le API client dei dati per chiamare un'istanza di SQL Server distribuita nella rete virtuale.

Le funzioni in esecuzione in un piano Premium condividono la stessa infrastruttura del servizio app sottostante come app Web nei piani PremiumV2. Tutta la documentazione per le [app Web nel servizio app Azure](../app-service/overview.md) si applica alle funzioni del piano Premium.

> [!div class="nextstepaction"]
> [Altre informazioni sulle opzioni di rete in funzioni](./functions-networking-options.md)

[Piano Premium]: functions-scale.md#premium-plan
