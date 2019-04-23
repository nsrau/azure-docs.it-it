---
title: Integrare funzioni di Azure con una rete virtuale di Azure
description: Un'esercitazione dettagliata che illustra come connettersi a una funzione a una rete virtuale di Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002793"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrare un'app per le funzioni con una rete virtuale di Azure

Questa esercitazione illustra come usare funzioni di Azure per connettersi alle risorse in una rete virtuale di Azure.

Per questa esercitazione, che verrà distribuito un sito WordPress in una macchina virtuale in una rete virtuale che non è accessibile da internet. Che verrà quindi distribuito una funzione con accesso a internet e la rete virtuale. Si userà tale funzione per accedere alle risorse dal sito WordPress distribuito all'interno della rete virtuale.

Per altre informazioni sul funzionamento del sistema, la risoluzione dei problemi e configurazione avanzata, vedere [integrare un'app in una rete virtuale di Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Funzioni di Azure nel piano Premium hanno le stesse funzionalità di hosting di App web, in modo che tutte le funzionalità e limitazioni nell'articolo si applicano alle funzioni.

## <a name="topology"></a>Topologia

 ![Interfaccia utente per l'integrazione rete virtuale][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>Creare una macchina virtuale all'interno di una rete virtuale

Per iniziare, si creerà una macchina virtuale preconfigurata che esegue WordPress in una rete virtuale. 

Abbiamo scelto WordPress in una macchina virtuale perché è una delle risorse meno costose che possono essere distribuite in una rete virtuale. Si noti che questo scenario può inoltre funzionare con qualsiasi risorsa in una rete virtuale, ad esempio le API REST, gli ambienti del servizio App e altri servizi di Azure.

1. Accedere al portale di Azure.
2. Aggiungere una nuova risorsa, aprire il **crea una risorsa** pannello.
3. Cercare "[WordPress LEMP7 Max Performance in CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" e aprire il pannello di creazione. 
4. Nel **nozioni di base** , configurare la macchina virtuale con le informazioni seguenti:
    1. Creare un nuovo gruppo di risorse per questa macchina virtuale per rendere più facile la pulizia delle risorse alla fine dell'esercitazione. In questo caso, usiamo "Funzione-VNET-Tutorial" come esempio.
    1. Assegnare un nome univoco alla macchina virtuale. Utilizziamo "Rete virtuale-Wordpress" come esempio.
    1. Selezionare l'area più vicina all'utente.
    1. Selezionare le dimensioni come B1s (1 vCPU, 1 GB di memoria).
    1. Per l'account amministratore, scegliere l'autenticazione della password e immettere un nome utente e una password univoci. Per questa esercitazione, non dovrai accedere alla macchina virtuale a meno che non sia necessario risolvere.
    
        ![Scheda elementi di base per la creazione di una macchina virtuale](./media/functions-create-vnet/create-vm-1.png)

1. Spostare il **Networking** scheda e immettere le informazioni seguenti:
    1.  Creare una nuova rete virtuale.
    1.  Immettere un intervallo di indirizzi privato e una subnet all'interno di tale intervallo di indirizzi. La dimensione della subnet determinerà quante macchine virtuali è possibile usare nel piano di servizio app. Se non ha familiarità, gli indirizzi IP e subnet: è presente una [documento che illustra le nozioni di base](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Indirizzamento IP e subnet sono importanti in questo scenario, pertanto si consiglia di leggere alcuni articoli e video in pochi online finché non ha senso. 
    
        In questo esempio è stiamo queste ultime scelgono di usare la rete 10.10.0.0/16 con una subnet di 10.10.1.0/24. Si sta ricorrere all'overprovisioning e utilizzando un /16 subnet perché è facile calcolare le subnet sono disponibili nella rete 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Riaccenderle il **Networking** scheda, impostare l'indirizzo IP pubblico **None**. Questo passaggio distribuirà la macchina virtuale con l'accesso solo alla rete virtuale.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Creare la macchina virtuale Il processo richiede circa 5 minuti.
8. Dopo aver creata la macchina virtuale, passare al relativo **Networking** scheda e annotare l'indirizzo IP privato per un uso successivo. La macchina virtuale non deve avere un indirizzo IP pubblico.

    ![14]

È ora disponibile un sito WordPress distribuito completamente all'interno della rete virtuale. Questo sito non è accessibile dall'Internet pubblico.

## <a name="create-a-function-app-in-a-premium-plan"></a>Creare un'app per le funzioni in un piano Premium

Il passaggio successivo consiste nel creare un'app per le funzioni in un piano Premium. Un piano Premium offre una scalabilità senza server con tutti i vantaggi di un piano di servizio App dedicato. App per le funzioni create tramite il piano a consumo non supportano l'integrazione della rete virtuale.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>Connettere l'app di funzione per la rete virtuale

Con un sito WordPress che ospitano i file dall'interno della rete virtuale, è possibile connettersi a questo punto l'app per le funzioni alla rete virtuale.

1.  Nel portale di app per le funzioni nel passaggio precedente, selezionare **funzionalità della piattaforma**. Quindi selezionare **Networking**.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Selezionare **fare clic qui per configurare** sotto **integrazione rete virtuale**.

    ![Stato per la configurazione di una funzionalità di rete](./media/functions-create-vnet/Networking-1.png)

1. Nella pagina di integrazione rete virtuale, selezionare **aggiunta della rete virtuale (anteprima)**.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Creare una nuova subnet per la funzione e il piano di servizio App da usare. Si noti che le dimensioni della subnet limiterà il numero totale di macchine virtuali che è possibile aggiungere al piano di servizio App. La rete virtuale instraderà automaticamente il traffico tra le subnet nella rete virtuale, in modo che non è importante che la funzione è in una subnet diversa dalla macchina virtuale. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>Creare una funzione che accede a una risorsa nella rete virtuale

L'app per le funzioni possono ora accedere nella rete virtuale con il nostro sito WordPress. Quindi, dobbiamo utilizzare la funzione per accedere a tale file e li fornirà all'utente. In questo esempio si userà un sito WordPress come un proxy e l'API della funzione chiamante perché entrambi sono facili da configurare e visualizzare. 

È possibile usare facilmente qualsiasi altra API distribuita all'interno di una rete virtuale. È anche possibile usare un'altra funzione con il codice che effettua chiamate all'API per le API distribuita all'interno della rete virtuale. Un'istanza di SQL Server distribuita all'interno della rete virtuale è un esempio perfetto.

1. Nel portale, aprire l'app per le funzioni nel passaggio precedente.
1. Creare un proxy selezionando **proxy** > **+**.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Configurare il nome del proxy e la route. In questo esempio Usa "/ prevede" come una route.
1. Compilandone IP del sito WordPress in precedenza e impostare **URL di back-end** a `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

A questo punto, se si prova a visitare l'URL del back-end direttamente da incollandoli in una nuova scheda del browser, la pagina dovrebbe raggiungere il timeout. Questo avviene perché il sito WordPress è connesso solo la rete virtuale e non a internet. Se si incolla l'URL del proxy nel browser, si noterà un'immagine di pianta (estratta dal sito WordPress) all'interno della rete virtuale. 

App per le funzioni è connesso a internet e la rete virtuale. Il proxy riceve una richiesta sulla rete Internet pubblica e agisce come un proxy HTTP semplice per inoltrare tale richiesta nella rete virtuale. Il proxy ritrasmette quindi la risposta all'utente sulla rete Internet pubblica. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Passaggi successivi

Le funzioni in esecuzione in un piano Premium condividono la stessa infrastruttura sottostante del servizio App come App web in piani PremiumV2. Tutta la documentazione per le app web si applica alle funzioni del piano Premium.

* [Altre informazioni sulle opzioni di rete nelle funzioni](./functions-networking-options.md)
* [Leggere le funzioni di domande frequenti sulla rete](./functions-networking-faq.md)
* [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
* [Abilita altre funzionalità di rete e controllo con ambienti del servizio App](../app-service/environment/intro.md)
* [Connettersi a singole risorse locali senza modifiche al firewall usando connessioni ibride](../app-service/app-service-hybrid-connections.md)
* [Altre informazioni sui proxy di funzioni](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
