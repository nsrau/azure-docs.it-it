---
title: Integrare Funzioni di Azure con una rete virtuale di Microsoft Azure
description: Un'esercitazione dettagliata che illustra come collegare una funzione a una rete virtuale di Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f8ddcbcb25dc45ee71304ffa1bc5c0c4d3751b61
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523733"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrare un'app per le funzioni con una rete virtuale di Azure

Questa esercitazione dettagliata illustra come usare Funzioni di Azure per collegarsi alle risorse in una rete virtuale di Azure.

Per questa esercitazione si distribuirà un sito WordPress in una VM in una privata, non internet accessibile, rete virtuale. Verrà quindi distribuita una funzione con accesso sia a Internet sia alla rete virtuale. Si userà tale funzione per accedere alle risorse dal sito WordPress distribuito all'interno della rete virtuale.

Per altre informazioni sul funzionamento del sistema, la risoluzione dei problemi e la configurazione avanzata, vedere il documento [Integrare l'app con una rete virtuale di Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Funzioni di Azure nel piano Premium hanno le stesse funzionalità di hosting di App web, in modo che tutte le funzionalità e le limitazioni in tale documento si applicano anche alle funzioni.

## <a name="topology"></a>Topologia

 ![Interfaccia utente di Integrazione rete virtuale][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Creazione di una macchina virtuale all'interno di una rete virtuale

Per iniziare, si creerà una macchina virtuale preconfigurata che esegue Wordpress all'interno di una rete virtuale. 

WordPress in una macchina virtuale è stato scelto perché è una delle risorse meno costose che possono essere distribuite all'interno di una rete virtuale. Si noti che questo scenario può inoltre funzionare con qualsiasi risorsa in una rete virtuale, ad esempio le API REST, ambienti del servizio App e altri servizi di Azure.

1. Accedere al portale di Azure
2. Aggiungere una nuova risorsa aprendo il pannello "Crea una risorsa"
3. Cercare "[WordPress LEMP7 Max Performance in CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" e aprire il pannello di creazione 
4. Nel pannello di creazione configurare la macchina virtuale con le informazioni seguenti:
    1. Creare un nuovo gruppo di risorse per questa macchina virtuale per rendere più facile la pulizia delle risorse alla fine dell'esercitazione. Il gruppo di risorse è stato denominato "Funzione-VNET-Tutorial"
    1. Assegnare un nome univoco alla macchina virtuale. È stato denominato "VNET-Wordpress"
    1. Selezionare l'area più vicina.
    1. Selezionare le dimensioni come B1s (1 CPU virtuale, 1 GB di memoria)
    1. Per l'account amministratore, scegliere l'autenticazione della password e immettere un nome utente e una password univoci. Per questa esercitazione, non è necessario accedere alla macchina virtuale a meno che non sia necessario risolvere i problemi.
    
        ![Crea scheda Basic della macchina virtuale](./media/functions-create-vnet/create-vm-1.png)

1. Passare alla scheda di rete e immettere le informazioni seguenti:
    1.  Creare una nuova rete virtuale
    1.  Immettere l'intervallo di indirizzi privato desiderato e la subnet all'interno di tale intervallo di indirizzi. La dimensione della subnet determinerà quante macchine virtuali è possibile usare nel piano di servizio app. Se gli indirizzi IP e le subnet sono nozioni nuove, esiste un [documento che illustra le nozioni di base](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Indirizzi IP e subnet sono importanti in questo scenario, pertanto si consiglia di leggere alcuni articoli e guardare alcuni video online finché queste nozioni non risultano comprensibili. 
        1. Per questo esempio, è stato scelto di usare la rete 10.10.0.0/16 con una subnet di 10.10.1.0/24. È stato scelto di effettuare un provisioning maggiore e usare una subnet /16 perché è facile calcolare le subnet disponibili nella rete 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Tornare nella scheda rete e impostare l'indirizzo IP pubblico su "Nessuno". Ciò distribuirà la macchina virtuale con accesso solo alla rete virtuale.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Creare la macchina virtuale Questo richiederà circa 5 minuti.
8. Dopo aver creata la macchina virtuale, visitare la scheda di rete e prendere nota dell'indirizzo IP privato per un uso successivo. La macchina virtuale non deve avere un indirizzo IP pubblico.

    ![14]

A questo punto si dispone di un sito wordpress distribuito completamente all'interno della rete virtuale. Questo sito non è accessibile dall'Internet pubblico.

## <a name="create-a-premium-plan-function-app"></a>Creare un piano Premium App per le funzioni

Il passaggio successivo consiste nel creare un'app per le funzioni in un piano premium. Il piano premium è una nuova offerta che offre scalabilità senza server con tutti i vantaggi di un piano di servizio App dedicato. Utilizzo delle App per le funzioni piano non supportano l'integrazione rete virtuale.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-vnet"></a>Connettere l'app per le funzioni alla rete virtuale

Con un sito WordPress file dall'interno di una rete virtuale di cui viene ospitata, è ora possibile connettersi le app per le funzioni per la rete virtuale.

1.  Nel portale per l'app per le funzioni del passaggio precedente selezionare **Funzionalità della piattaforma**, quindi selezionare **Rete**

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Selezionare **Fare clic per configurare** in Integrazione rete virtuale

    ![Configurare lo stato della funzionalità di rete](./media/functions-create-vnet/Networking-1.png)

1. Nella pagina di integrazione rete virtuale, selezionare **Aggiungi rete virtuale (anteprima)**

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Creare una nuova subnet per il piano di servizio app e di funzione da usare. Si noti che la dimensione della subnet limiterà il numero totale di macchine virtuali che è possibile aggiungere al piano di servizio app. La rete virtuale indirizzerà automaticamente il traffico tra le subnet della rete virtuale; in questo modo non importa se la funzione è in una subnet diversa dalla macchina virtuale. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Creare una funzione che accede a una risorsa nella rete virtuale

L'app per le funzioni ora può accedere alla rete virtuale con il sito wordpress, quindi verrà usata la funzione per accedere a tale file e restituirlo all'utente. In questo esempio verranno usati un sito wordpress come API e un proxy di funzione come funzioni di chiamata perché sono entrambi facili da configurare e visualizzare. È possibile usare facilmente qualsiasi altra API distribuita all'interno di una rete virtuale e un'altra funzione con chiamate API di creazione del codice per tale API distribuita all'interno della rete virtuale. Un server SQL distribuito all'interno della rete virtuale è un esempio perfetto.

1. Nel portale, aprire l'app per le funzioni del passaggio precedente
1. Creare un proxy selezionando **Proxy** > **+**

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Configurare il nome e la route del Proxy. /pianta è stata scelta come route.
1. Compilare l'IP del sito wordpress dei passaggi precedenti e impostare l'URL di back-end su`http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

A questo punto, se si prova a visitare l'URL di back-end direttamente incollandolo in una nuova scheda del browser, la pagina dovrebbe raggiungere il timeout. Ciò è prevedibile poiché il sito wordpress è connesso solo alla rete virtuale e non a Internet. Se si incolla l'URL del proxy nel browser, dovrebbe essere visualizzata l'immagine di una bella pianta, della quale Wordpress ha eseguito il pull all'interno della rete virtuale. 

La app per le funzioni è connessa sia a Internet sia alla rete virtuale. Il proxy riceve una richiesta sulla rete Internet pubblica e agisce come un proxy HTTP semplice per inoltrare tale richiesta nella rete virtuale. Il proxy ritrasmette quindi la risposta all'utente sulla rete Internet pubblica. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Fasi successive

Le funzioni in esecuzione in un piano Premium condividono la stessa infrastruttura sottostante di servizio App come App Web in PV2 piani. Ciò significa che tutta la documentazione per le app Web si applica alle funzioni del piano Premium.

1. [Altre informazioni sulle opzioni di rete in questo caso le funzioni](./functions-networking-options.md)
1. [Leggere le funzioni di rete domande frequenti qui](./functions-networking-faq.md)
1. [Altre informazioni sulle reti virtuali in Azure](../virtual-network/virtual-networks-overview.md)
1. [Abilita altre funzionalità di rete e controllo con ambienti del servizio App](../app-service/environment/intro.md)
1. [Connettersi a singole risorse locali senza modifiche al firewall usando connessioni ibride](../app-service/app-service-hybrid-connections.md)
1. [Altre informazioni sui proxy di funzione](./functions-proxies.md)

<!--Image references-->
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
