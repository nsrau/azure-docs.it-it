---
title: Integrare funzioni di Azure con una rete virtuale di Azure
description: Un'esercitazione dettagliata che illustra come connettersi a una funzione a una rete virtuale di Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 07c7d7fb682708bf813820440d9c790c28b1f3e5
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834651"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Esercitazione: integrazione di funzioni con una rete virtuale di Azure

Questa esercitazione illustra come usare funzioni di Azure per connettersi alle risorse in una rete virtuale di Azure. verrà creata una funzione che dispone dell'accesso a entrambi internet e a una macchina virtuale che esegue WordPress nella rete virtuale.

> [!div class="checklist"]
> * Creare un'app per le funzioni nel piano Premium
> * Distribuire un sito WordPress in macchina virtuale in una rete virtuale
> * Connettere l'app per le funzioni per la rete virtuale
> * Creare un proxy di funzioni per accedere alle risorse di WordPress
> * Richiede un file di WordPress all'interno della rete virtuale

> [!NOTE]  
> Questa esercitazione crea un'app per le funzioni nel piano Premium. Questo piano di hosting è attualmente in anteprima. Per altre informazioni, vedere [Piano Premium].

## <a name="topology"></a>Topologia

Il diagramma seguente illustra l'architettura della soluzione creato:

 ![Interfaccia utente per l'integrazione rete virtuale](./media/functions-create-vnet/topology.png)

Le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di App web nel servizio App di Azure, che include la funzionalità integrazione rete virtuale. Per altre informazioni sull'integrazione della rete virtuale, inclusa la configurazione avanzata e sulla risoluzione dei problemi, vedere [integrare un'app in una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione, è importante comprendere gli indirizzi IP e subnet:. È possibile iniziare con [questo articolo che tratta i fondamenti della suddivisione in subnet e indirizzamento](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Molti che più articoli e video sono disponibili online.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-function-app-in-a-premium-plan"></a>Creare un'app per le funzioni in un piano Premium

In primo luogo, si crea un'app per le funzioni nel [Piano Premium]. Questo piano offre scalabilità senza server, supportando l'integrazione della rete virtuale.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

È possibile aggiungere app per le funzioni al dashboard selezionando l'icona della puntina nell'angolo superiore destro. L'aggiunta, è facile tornare a questa app di funzione, dopo aver creato la macchina virtuale.

## <a name="create-a-vm-inside-a-virtual-network"></a>Creare una macchina virtuale all'interno di una rete virtuale

Successivamente, creare una macchina virtuale preconfigurata che esegue WordPress in una rete virtuale ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) da Jetware). Una VM WordPress viene usata a causa di relativi costi ridotti e convenience. Questo stesso scenario funziona con qualsiasi risorsa in una rete virtuale, ad esempio le API REST, gli ambienti del servizio App e altri servizi di Azure. 

1. Nel portale, scegliere **+ crea una risorsa** nel riquadro di spostamento a sinistra, nel campo di ricerca digitare `WordPress LEMP7 Max Performance`, quindi premere INVIO.

1. Scegli **Wordpress LEMP Max Performance** nei risultati della ricerca. Selezionare un piano software della **Wordpress LEMP Max Performance per CentOS** come la **piano Software** e selezionare **Create**.

1. Nel **nozioni di base** scheda, usare le impostazioni della macchina virtuale come indicato nella tabella sotto l'immagine:

    ![Scheda elementi di base per la creazione di una macchina virtuale](./media/functions-create-vnet/create-vm-1.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione | La sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Scegliere `myResourceGroup`, o il gruppo di risorse è stato creato con l'app per le funzioni. Uso dello stesso gruppo di risorse per l'app per le funzioni, VM, WordPress e piano di hosting rende più semplice pulire le risorse al termine di questa esercitazione. |
    | **Nome macchina virtuale** | VNET-Wordpress | Nome della macchina virtuale deve essere univoco nel gruppo di risorse |
    | **[Region](https://azure.microsoft.com/regions/)** | (Europa) Europa occidentale | Scegliere un'area nelle vicinanze o vicino le funzioni di accesso alla macchina virtuale. |
    | **Dimensione** | B1s | Scegli **modificare le dimensioni** e quindi selezionare l'immagine B1s standard, che include 1 vCPU e 1 GB di memoria. |
    | **Tipo di autenticazione** | Password | Per usare l'autenticazione della password, è necessario specificare anche un **nomeutente**, sicuro **Password**e quindi **Conferma password**. Per questa esercitazione, non dovrai accedere alla macchina virtuale a meno che non sia necessario risolvere. |

1. Scegliere il **Networking** scheda e nella sezione Configura reti virtuali selezionare **Crea nuovo**.

1. Nelle **crea rete virtuale**, usare le impostazioni nella tabella sotto l'immagine:

    ![Scheda di rete di creare una VM](./media/functions-create-vnet/create-vm-2.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | È possibile usare il nome predefinito generato per la rete virtuale. |
    | **Intervallo di indirizzi** | 10.10.0.0/16 | Usare un intervallo di indirizzi singolo per la rete virtuale. |
    | **Nome della subnet** | Esercitazione-Net | Nome della subnet. |
    | **Intervallo di indirizzi** (subnet) | 10.10.1.0/24   | Le dimensioni della subnet definisce quante interfacce possono essere aggiunti alla subnet. Questa subnet viene usata dal sito WordPress.  Oggetto `/24` subnet fornisce gli indirizzi di 254 host. |

1. Selezionare **OK** per creare la rete virtuale.

1. Nel **Networking** scheda, scegliere **None** per **indirizzo IP pubblico**.

1. Scegliere il **Management** scheda, quindi nella **account di archiviazione di diagnostica**, scegliere l'account di archiviazione è stato creato con l'app per le funzioni.

1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Create**. La macchina virtuale creare processo richiede alcuni minuti. La macchina virtuale creata è possibile accedere solo la rete virtuale.

1. Dopo aver creata la macchina virtuale, scegliere **Vai alla risorsa** per visualizzare la pagina per la nuova macchina virtuale, quindi scegliere **Networking** sotto **impostazioni**.

1. Verificare che sia presente alcun **indirizzo IP pubblico**. Prendere nota di **IP privato**, utilizzabile per connettersi alla macchina virtuale da app per le funzioni.

    ![Impostazioni di rete nella macchina virtuale](./media/functions-create-vnet/vm-networking.png)

È ora disponibile un sito WordPress distribuito completamente all'interno della rete virtuale. Questo sito non è accessibile dalla rete internet pubblica.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Connettere l'app di funzione per la rete virtuale

Con un sito WordPress in esecuzione in una macchina virtuale in una rete virtuale, è ora possibile connettersi le app per le funzioni a tale rete virtuale.

1. Nuova funzione nell'app, selezionare **funzionalità della piattaforma** > **Networking**.

    ![Scegliere la rete in app per le funzioni](./media/functions-create-vnet/networking-0.png)

1. Sotto **integrazione rete virtuale**, selezionare **fare clic qui per configurare**.

    ![Stato per la configurazione di una funzionalità di rete](./media/functions-create-vnet/Networking-1.png)

1. Nella pagina di integrazione rete virtuale, selezionare **aggiunta della rete virtuale (anteprima)**.

    ![Aggiungere l'anteprima di integrazione rete virtuale](./media/functions-create-vnet/networking-2.png)

1. Nelle **lo stato della funzionalità di rete**, usare le impostazioni nella tabella sotto l'immagine:

    ![Definire la rete virtuale di app (funzione)](./media/functions-create-vnet/networking-3.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Rete virtuale** | MyResourceGroup-vnet | Questa rete virtuale è quello creato in precedenza. |
    | **Subnet** | Crea nuova subnet | Creare una subnet nella rete virtuale per l'app per le funzioni da usare. Integrazione rete virtuale deve essere configurato per usare una subnet vuota. Non è importante che le funzioni usano una subnet diversa da macchina virtuale. La rete virtuale instrada automaticamente il traffico tra le due subnet. |
    | **Nome della subnet** | Funzione-Net | Nome della nuova subnet. |
    | **Blocco di indirizzi di rete virtuale** | 10.10.0.0/16 | Scegliere lo stesso blocco di indirizzi usato dal sito WordPress. È consigliabile utilizzare solo un blocco di indirizzi definito. |
    | **Intervallo di indirizzi** | 10.10.2.0/24   | Le dimensioni della subnet limitano il numero totale di istanze che l'app per le funzioni piano Premium grado di scalare orizzontalmente. Questo esempio viene usato un `/24` subnet con indirizzi di 254 host disponibili. Questa subnet è con provisioning eccessivo, ma facile da calcolare. |

1. Selezionare **OK** per aggiungere la subnet. Chiudere la pagina di integrazione rete virtuale e lo stato della funzionalità di rete per tornare alla pagina dell'app di funzione.

L'app per le funzioni possono ora accedere la rete virtuale in cui viene eseguito il sito WordPress. Successivamente, utilizziamo [proxy di funzioni di Azure](functions-proxies.md) per restituire un file dal sito WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Creare un proxy per accedere alle risorse della macchina virtuale

Integrazione di reti virtuali abilitate, è possibile creare un proxy nell'app per le funzioni per inoltrare le richieste per la macchina virtuale in esecuzione nella rete virtuale.

1. Nell'app per le funzioni, selezionare **proxy** > **+**, quindi usare le impostazioni del proxy nella tabella sotto l'immagine:

    ![Definire le impostazioni del proxy](./media/functions-create-vnet/create-proxy.png)

    | Impostazione  | Valore consigliato  | Descrizione      |
    | -------- | ---------------- | ---------------- |
    | **Nome** | Plant | Il nome può essere qualsiasi valore. Viene utilizzato per identificare il proxy. |
    | **Modello di route** | /plant | Route che esegue il mapping a una risorsa di macchina virtuale. |
    | **URL back-end** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Sostituire `<YOUR_VM_IP>` con l'indirizzo IP della VM WordPress creato in precedenza. Questo mapping restituisce un singolo file dal sito. |

1. Selezionare **Create** per aggiungere il proxy app per le funzioni.

## <a name="try-it-out"></a>Prova

1. Nel browser, prova ad accedere all'URL è utilizzato come il **URL di back-end**. Come previsto, la richiesta scade. Poiché il sito WordPress è connesso solo alla propria rete virtuale e non a internet, si verifica un timeout.

1. Copia il **URL del Proxy** valore il nuovo proxy e incollarlo nella barra degli indirizzi del browser. L'immagine restituita proviene dal sito WordPress in esecuzione all'interno della rete virtuale.

    ![File di immagine impianto restituito dal sito WordPress](./media/functions-create-vnet/plant.png)

App per le funzioni è connesso a internet e la rete virtuale. Il proxy è riceve una richiesta tramite la rete internet pubblica e quindi funge da un proxy HTTP semplice per inoltrare la richiesta per la rete virtuale connessa. Il proxy quindi inoltra la risposta al cliente pubblicamente tramite internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, il sito WordPress funge da un'API che viene chiamata con un proxy in app per le funzioni. Questo scenario esegue una buona esercitazione perché è facile da configurare e visualizzare. È possibile usare qualsiasi altra API distribuita all'interno di una rete virtuale. È possibile inoltre stata creata una funzione con il codice che chiama le API distribuite all'interno della rete virtuale. Uno scenario più realistico è una funzione che usa le API client di dati per chiamare un'istanza di SQL Server distribuita nella rete virtuale.

Le funzioni in esecuzione in un piano Premium condividono la stessa infrastruttura sottostante del servizio App come App web in piani PremiumV2. Tutta la documentazione per [App web nel servizio App di Azure](../app-service/overview.md) si applica alle funzioni del piano Premium.

> [!div class="nextstepaction"]
> [Altre informazioni sulle opzioni di rete nelle funzioni](./functions-networking-options.md)

[Piano Premium]: functions-scale.md#premium-plan-public-preview
