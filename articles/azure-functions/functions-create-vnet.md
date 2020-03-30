---
title: Integrare le funzioni di Azure con una rete virtuale di AzureIntegrate Azure Functions with an Azure virtual network
description: Esercitazione dettagliata che illustra come connettere una funzione a una rete virtuale di AzureA step-by-step tutorial that shows you how to connect a function to an Azure virtual network
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433220"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Esercitazione: Integrare Funzioni con una rete virtuale di Azure

Questa esercitazione illustra come usare Funzioni di Azure per connettersi alle risorse in una rete virtuale di Azure.This tutorial shows you how to use Azure Functions to connect to resources in an Azure virtual network. si creerà una funzione che ha accesso sia a Internet che a una macchina virtuale che esegue WordPress nella rete virtuale.

> [!div class="checklist"]
> * Creare un'app per le funzioni nel piano PremiumCreate a function app in the Premium plan
> * Distribuire un sito WordPress in una macchina virtuale in una rete virtualeDeploy a WordPress site to VM in a virtual network
> * Connettere l'app per le funzioni alla rete virtuale
> * Creare un proxy di funzione per accedere alle risorse wordPress
> * Richiedere un file WordPress dall'interno della rete virtuale

## <a name="topology"></a>Topologia

Nel diagramma seguente viene illustrata l'architettura della soluzione creata:

 ![Interfaccia utente per l'integrazione della rete virtuale](./media/functions-create-vnet/topology.png)

Le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting delle app Web nel servizio app di Azure, che include la funzionalità di integrazione della rete virtuale. Per altre informazioni sull'integrazione della rete virtuale, inclusa la risoluzione dei problemi e la configurazione avanzata, vedere [Integrare l'app con](../app-service/web-sites-integrate-with-vnet.md)una rete virtuale di Azure.To learn more about VNet Integration, including troubleshooting and advanced configuration, see Integrate your app with an Azure virtual network .

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione è importante comprendere l'indirizzamento IP e il subnetting. È possibile iniziare con [questo articolo che illustra le nozioni di base per l'indirizzamento e](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)la subnetzione . Molti altri articoli e video sono disponibili online.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-function-app-in-a-premium-plan"></a>Creare un'app per le funzioni in un piano PremiumCreate a function app in a Premium plan

Innanzitutto, si crea un'app per le funzioni nel [piano Premium]. Questo piano fornisce scalabilità senza server e supporto dell'integrazione della rete virtuale.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

È possibile aggiungere l'app per le funzioni al dashboard selezionando l'icona a forma di puntina nell'angolo superiore destro. Il blocco semplifica la restituzione a questa app per le funzioni dopo aver creato la macchina virtuale.

## <a name="create-a-vm-inside-a-virtual-network"></a>Creare una macchina virtuale all'interno di una rete virtualeCreate a VM inside a virtual network

Successivamente, creare una macchina virtuale preconfigurata che esegue WordPress all'interno di una rete virtuale ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) di Jetware). Una macchina virtuale WordPress viene utilizzata a causa del suo basso costo e convenienza. Questo stesso scenario funziona con qualsiasi risorsa in una rete virtuale, ad esempio API REST, ambienti del servizio app e altri servizi di Azure.This same scenario works with any resource in a virtual network, such as REST APIs, App Service Environments, and other Azure services. 

1. Nel portale scegliere **Crea una risorsa** nel riquadro di spostamento `WordPress LEMP7 Max Performance`sinistro, digitare nel campo di ricerca e premere INVIO.

1. Scegliere **Wordpress LEMP Max Performance** nei risultati della ricerca. Selezionare un piano software di **Wordpress LEMP Max Performance per CentOS** come **piano software** e selezionare **Crea**.

1. Nella scheda **Nozioni di base** usare le impostazioni della macchina virtuale come specificato nella tabella seguente dell'immagine:In the Basics tab, use the VM settings as specified in the table below the image:

    ![Scheda Nozioni di base per la creazione di una macchina virtualeBasics tab for creating a VM](./media/functions-create-vnet/create-vm-1.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione con cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Scegliere `myResourceGroup`o il gruppo di risorse creato con l'app per le funzioni. L'uso dello stesso gruppo di risorse per l'app per le funzioni, WordPress VM e piano di hosting semplifica la pulizia delle risorse al termine di questa esercitazione. |
    | **Nome macchina virtuale** | VNET-Wordpress | Il nome della macchina virtuale deve essere univoco nel gruppo di risorseThe VM name needs to be unique in the resource group |
    | **[Regione](https://azure.microsoft.com/regions/)** | (Europa) Europa occidentale | Scegliere un'area vicina all'utente o vicino alle funzioni che accedono alla macchina virtuale. |
    | **Dimensione** | B1s | Scegliere **Cambia dimensione** e quindi selezionare l'immagine standard B1s, che dispone di 1 vCPU e 1 GB di memoria. |
    | **Tipo di autenticazione** | Password | Per utilizzare l'autenticazione tramite password, è inoltre necessario specificare un **nome utente**, una **password**sicura e quindi **Conferma password**. Per questa esercitazione non sarà necessario accedere alla macchina virtuale a meno che non sia necessario risolvere i problemi. |

1. Scegliere la scheda **Rete** e in Configura reti virtuali selezionare **Crea nuovo**.

1. In **Crea rete virtuale**usare le impostazioni nella tabella seguente:

    ![Scheda Rete della creazione di VM](./media/functions-create-vnet/create-vm-2.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | È possibile usare il nome predefinito generato per la rete virtuale. |
    | **Intervallo di indirizzi** | 10.10.0.0/16 | Usare un singolo intervallo di indirizzi per la rete virtuale. |
    | **Nome subnet** | Tutorial-Net | Nome della subnet. |
    | **Intervallo di indirizzi** (subnet) | 10.10.1.0/24   | La dimensione della subnet definisce il numero di interfacce che possono essere aggiunte alla subnet. Questa subnet viene utilizzata dal sito WordPress.  Una `/24` subnet fornisce 254 indirizzi host. |

1. Selezionare **OK** per creare la rete virtuale.

1. Tornando alla scheda **Rete,** scegliere **Nessuno** per **IP pubblico**.

1. Scegliere la scheda **Gestione,** quindi in **Account di archiviazione di diagnostica**scegliere l'account di archiviazione creato con l'app per le funzioni.

1. Selezionare **Revisione e creazione**. Al termine della convalida, selezionare **Crea**. Il processo di creazione della macchina virtuale richiede alcuni minuti. La macchina virtuale creata può accedere solo alla rete virtuale.

1. Dopo aver creato la macchina virtuale, scegliere **Vai alla risorsa** per visualizzare la pagina della nuova macchina virtuale, quindi scegliere **Rete** in **Impostazioni**.

1. Verificare che non vi sia alcun **IP pubblico**. Prendere nota **dell'IP privato**, usato per connettersi alla macchina virtuale dall'app per le funzioni.

    ![Impostazioni di rete nella macchina virtualeNetworking settings in the VM](./media/functions-create-vnet/vm-networking.png)

Ora hai un sito WordPress distribuito interamente all'interno della tua rete virtuale. Questo sito non è accessibile da Internet pubblico.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Connettere l'app per le funzioni alla rete virtualeConnect your function app to the virtual network

Con un sito WordPress in esecuzione in una macchina virtuale in una rete virtuale, è ora possibile connettere l'app per le funzioni a tale rete virtuale.

1. Nella nuova app per le funzioni selezionare **Funzionalità** > della piattaforma**In rete**.

    ![Scegliere la rete nell'app per le funzioni](./media/functions-create-vnet/networking-0.png)

1. In **Integrazione rete virtuale**selezionare Fare clic qui per **configurare**.

    ![Stato della configurazione di una funzionalità di rete](./media/functions-create-vnet/Networking-1.png)

1. Nella pagina di integrazione della rete virtuale selezionare **Aggiungi rete virtuale (anteprima).**

    ![Aggiungere l'anteprima dell'integrazione della rete virtualeAdd the VNet Integration preview](./media/functions-create-vnet/networking-2.png)

1. In **Stato funzionalità di rete**, utilizzare le impostazioni nella tabella sottostante l'immagine:

    ![Definire la rete virtuale dell'app per le funzioniDefine the function app virtual network](./media/functions-create-vnet/networking-3.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Rete virtuale** | MyResourceGroup-vnet | Questa rete virtuale è quella creata in precedenza. |
    | **Subnet** | Crea nuova subnet | Creare una subnet nella rete virtuale per l'app per le funzioni da usare. L'integrazione della rete virtuale deve essere configurata per l'utilizzo di una subnet vuota. Non importa che le funzioni usino una subnet diversa dalla macchina virtuale. La rete virtuale instrada automaticamente il traffico tra le due subnet. |
    | **Nome subnet** | Rete funzione | Nome della nuova subnet. |
    | **Blocco di indirizzi di rete virtuale** | 10.10.0.0/16 | Scegli lo stesso blocco di indirizzi utilizzato dal sito WordPress. È necessario avere un solo blocco di indirizzi definito. |
    | **Intervallo di indirizzi** | 10.10.2.0/24   | La dimensione della subnet limita il numero totale di istanze a cui l'app per le funzioni del piano Premium può essere scalata orizzontalmente. In questo `/24` esempio viene utilizzata una subnet con 254 indirizzi host disponibili. Il provisioning di questa subnet è eccessivo, ma facile da calcolare. |

1. Selezionare **OK** per aggiungere la subnet. Chiudere le pagine Integrazione della rete virtuale e Stato funzionalità di rete per tornare alla pagina dell'app per le funzioni.

L'applicazione funzione può ora accedere alla rete virtuale in cui è in esecuzione il sito WordPress. Successivamente, si utilizzano i proxy di Funzioni di Azure per restituire un file dal sito WordPress.Next, you use [Azure Functions Proxies](functions-proxies.md) to return a file from the WordPress site.

## <a name="create-a-proxy-to-access-vm-resources"></a>Creare un proxy per accedere alle risorse della macchina virtualeCreate a proxy to access VM resources

Con l'integrazione della rete virtuale abilitata, è possibile creare un proxy nell'app per le funzioni per inoltrare le richieste alla macchina virtuale in esecuzione nella rete virtuale.

1. Nell'app per le funzioni seleziona **Proxy** > **+**, quindi usa le impostazioni proxy nella tabella sotto l'immagine:

    ![Definire le impostazioni proxy](./media/functions-create-vnet/create-proxy.png)

    | Impostazione  | Valore consigliato  | Descrizione      |
    | -------- | ---------------- | ---------------- |
    | **Nome** | PlAnT | Il nome può essere qualsiasi valore. Viene utilizzato per identificare il proxy. |
    | **Modello di percorso** | /pianta | Eseguire il mapping a una risorsa VM. |
    | **URL back-end** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Sostituire `<YOUR_VM_IP>` con l'indirizzo IP della macchina virtuale WordPress creata in precedenza. Questo mapping restituisce un singolo file dal sito. |

1. Selezionare **Crea** per aggiungere il proxy all'app per le funzioni.

## <a name="try-it-out"></a>Provare il servizio

1. Nel browser provare ad accedere all'URL utilizzato come **URL di back-end.** Come previsto, la richiesta scade. Un timeout si verifica perché il tuo sito WordPress è collegato solo alla tua rete virtuale e non a Internet.

1. Copiare il valore **dell'URL del proxy** dal nuovo proxy e incollarlo nella barra degli indirizzi del browser. L'immagine restituita proviene dal sito WordPress in esecuzione all'interno della rete virtuale.

    ![File di immagine dell'impianto restituito dal sito WordPress](./media/functions-create-vnet/plant.png)

L'app per le funzioni è connessa sia a Internet che alla rete virtuale. Il proxy riceve una richiesta tramite Internet pubblica e quindi funge da semplice proxy HTTP per inoltrare tale richiesta alla rete virtuale connessa. Il proxy inoltra quindi la risposta pubblicamente su Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo tutorial, il sito WordPress funge da API che viene chiamata utilizzando un proxy nell'app per le funzioni. Questo scenario rende una buona esercitazione perché è facile da configurare e visualizzare. È possibile usare qualsiasi altra API distribuita all'interno di una rete virtuale. È anche possibile aver creato una funzione con codice che chiama le API distribuite all'interno della rete virtuale. Uno scenario più realistico è una funzione che utilizza le API client di dati per chiamare un'istanza di SQL Server distribuita nella rete virtuale.

Le funzioni in esecuzione in un piano Premium condividono la stessa infrastruttura del servizio app sottostante delle app Web nei piani PremiumV2.Functions running in a Premium plan share the same underlying App Service infrastructure as web apps on PremiumV2 plans. Tutta la documentazione per le app Web nel servizio app di Azure si applica alle funzioni del piano Premium.All the documentation for [web apps in Azure App Service](../app-service/overview.md) applies to your Premium plan functions.

> [!div class="nextstepaction"]
> [Ulteriori informazioni sulle opzioni di rete in Funzioni](./functions-networking-options.md)

[Piano Premium]: functions-scale.md#premium-plan
