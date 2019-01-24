---
title: Integrare Funzioni di Azure con una rete virtuale di Microsoft Azure
description: Un'esercitazione dettagliata che illustra come collegare una funzione a una rete virtuale di Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 12/03/2018
ms.author: alkarche
ms.openlocfilehash: 1140c23a0aa5344119c35434316ec73cc3918f90
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198372"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrare un'app per le funzioni con una rete virtuale di Azure
Questa esercitazione dettagliata illustra come usare Funzioni di Azure per collegarsi alle risorse in una rete virtuale di Azure. 

In questa esercitazione si distribuirà un sito wordpress di una macchina virtuale in una rete virtuale privata e non accessibile via Internet. Verrà quindi distribuita una funzione con accesso sia a Internet sia alla rete virtuale. Si userà tale funzione per accedere alle risorse dal sito wordpress distribuito all'interno della rete virtuale.

Per altre informazioni sul funzionamento del sistema, la risoluzione dei problemi e la configurazione avanzata, vedere il documento [Integrare l'app con una rete virtuale di Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Funzioni di Azure nel piano dedicato ha le stesse funzionalità di hosting delle app web, in modo che tutte le funzionalità e le limitazioni di tale documento si applichino anche alle funzioni.

## <a name="topology"></a>Topologia
 ![Interfaccia utente di Integrazione rete virtuale][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Creazione di una macchina virtuale all'interno di una rete virtuale

Per iniziare, si creerà una macchina virtuale preconfigurata che esegue Wordpress all'interno di una rete virtuale. 

WordPress in una macchina virtuale è stato scelto perché è una delle risorse meno costose che possono essere distribuite all'interno di una rete virtuale. Si noti che questo scenario può inoltre funzionare con qualsiasi risorsa in una rete virtuale, incluse le API REST, altri servizi di Azure, gli ambienti del servizio app e così via.

1.  Accedere al portale di Azure
2.  Aggiungere una nuova risorsa aprendo il pannello "Crea una risorsa"
3.  Cercare "[Wordpress LEMP7 Max Performance in CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" e aprire il pannello di creazione 
4.  Nel pannello di creazione configurare la macchina virtuale con le informazioni seguenti:
    1.  Creare un nuovo gruppo di risorse per questa macchina virtuale per rendere più facile la pulizia delle risorse alla fine dell'esercitazione. Il gruppo di risorse è stato denominato "Funzione-VNET-Tutorial"
    1.  Assegnare un nome univoco alla macchina virtuale. È stato denominato "VNET-Wordpress"
    1.  Selezionare l'area più vicina.
    1.  Selezionare le dimensioni come B1s (1 CPU virtuale, 1 GB di memoria)
    1.  Per l'account amministratore, scegliere l'autenticazione della password e immettere un nome utente e una password univoci. Per questa esercitazione, non è necessario accedere alla macchina virtuale a meno che non sia necessario risolvere i problemi.
    
        <img src="./media/functions-create-vnet/create-VM-1.png" width="700">

1. Passare alla scheda di rete e immettere le informazioni seguenti:
    1.  Creare una nuova rete virtuale
    1.  Immettere l'intervallo di indirizzi privato desiderato e la subnet all'interno di tale intervallo di indirizzi. La dimensione della subnet determinerà quante macchine virtuali è possibile usare nel piano di servizio app. Se gli indirizzi IP e le subnet sono nozioni nuove, esiste un [documento che illustra le nozioni di base](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Indirizzi IP e subnet sono importanti in questo scenario, pertanto si consiglia di leggere alcuni articoli e guardare alcuni video online finché queste nozioni non risultano comprensibili. 
        1. Per questo esempio, è stato scelto di usare la rete 10.10.0.0/16 con una subnet di 10.10.1.0/24. È stato scelto di effettuare un provisioning maggiore e usare una subnet /16 perché è facile calcolare le subnet disponibili nella rete 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-VM-2.png" width="700">

1. Tornare nella scheda rete e impostare l'indirizzo IP pubblico su "Nessuno". Ciò distribuirà la macchina virtuale con accesso solo alla rete virtuale.
       
    <img src="./media/functions-create-vnet/create-VM-2.1.png" width="700">

7. Creare la macchina virtuale Questo richiederà circa 5 minuti.
8. Dopo aver creata la macchina virtuale, visitare la scheda di rete e prendere nota dell'indirizzo IP privato per un uso successivo. La macchina virtuale non deve avere un indirizzo IP pubblico.

    ![14]

A questo punto si dispone di un sito wordpress distribuito completamente all'interno della rete virtuale. Questo sito non è accessibile dall'Internet pubblico.

## <a name="create-a-dedicated-function-app"></a>Creare un'app per le funzioni dedicata

Il passaggio successivo consiste nel creare un'app per le funzioni all'interno di un piano di servizio app standard o superiore. Si noti che le app per le funzioni del piano di consumo non supportano l'integrazione della rete virtuale.

1. Accedere al portale di Azure
2. Aggiungere una nuova risorsa aprendo il pannello "Crea una risorsa"
3. Selezionare "App per le funzioni senza server"
4. Immettere tutte le informazioni normali nel pannello di creazione, con una sola eccezione:
    1. selezionare un livello standard o superiore del piano di servizio app.

        <img src="./media/functions-create-vnet/Create-App-Service-Plan.PNG" width="300">
    
1. Il pannello di creazione completato era simile al seguente.

    <img src="./media/functions-create-vnet/Create-Function-App.png" width="300">


## <a name="connect-your-function-app-to-your-vnet"></a>Connettere l'app per le funzioni alla rete virtuale

A questo punto si dispone di un sito wordpress che ospita molti file dall'interno di una rete virtuale e ora sarà necessario connettere l'app per le funzioni a tale rete virtuale.

1.  Nel portale per l'app per le funzioni del passaggio precedente selezionare **Funzionalità della piattaforma**, quindi selezionare **Rete**
1.  Selezionare **Fare clic per configurare** in Integrazione rete virtuale

    <img src="./media/functions-create-vnet/Networking-1.png" width="450">

1. Nella pagina di integrazione rete virtuale, selezionare **Aggiungi rete virtuale (anteprima)**

    <img src="./media/functions-create-vnet/Networking-2.png" width="600"> 
    
1.  Creare una nuova subnet per il piano di servizio app e di funzione da usare. Si noti che la dimensione della subnet limiterà il numero totale di macchine virtuali che è possibile aggiungere al piano di servizio app. La rete virtuale indirizzerà automaticamente il traffico tra le subnet della rete virtuale; in questo modo non importa se la funzione è in una subnet diversa dalla macchina virtuale. 
    
    <img src="./media/functions-create-vnet/Networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Creare una funzione che accede a una risorsa nella rete virtuale

L'app per le funzioni ora può accedere alla rete virtuale con il sito wordpress, quindi verrà usata la funzione per accedere a tale file e restituirlo all'utente. In questo esempio verranno usati un sito wordpress come API e un proxy di funzione come funzioni di chiamata perché sono entrambi facili da configurare e visualizzare. È possibile usare facilmente qualsiasi altra API distribuita all'interno di una rete virtuale e un'altra funzione con chiamate API di creazione del codice per tale API distribuita all'interno della rete virtuale. Un server SQL distribuito all'interno della rete virtuale è un esempio perfetto.

1. Nel portale, aprire l'app per le funzioni del passaggio precedente
1. Creare un proxy selezionando **Proxy** > **+**

    <img src="./media/functions-create-vnet/New-Proxy.png" width="250">

1. Configurare il nome e la route del Proxy. /pianta è stata scelta come route.
1. Compilare l'IP del sito wordpress dei passaggi precedenti e impostare l'URL di back-end su`http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/Create-Proxy.png" width="900">

A questo punto, se si prova a visitare l'URL di back-end direttamente incollandolo in una nuova scheda del browser, la pagina dovrebbe raggiungere il timeout. Ciò è prevedibile poiché il sito wordpress è connesso solo alla rete virtuale e non a Internet. Se si incolla l'URL del proxy nel browser, dovrebbe essere visualizzata l'immagine di una bella pianta, della quale Wordpress ha eseguito il pull all'interno della rete virtuale. 

La app per le funzioni è connessa sia a Internet sia alla rete virtuale. Il proxy riceve una richiesta sulla rete Internet pubblica e agisce come un proxy HTTP semplice per inoltrare tale richiesta nella rete virtuale. Il proxy ritrasmette quindi la risposta all'utente sulla rete Internet pubblica. 

<img src="./media/functions-create-vnet/Plant.png" width="900">

## <a name="next-steps"></a>Passaggi successivi

Funzioni di Azure in esecuzione nel piani di servizio app vengono eseguite sullo stesso servizio delle app Web, quindi tutta la documentazione per le app Web verrà applicata alle funzioni dedicate.

1. [Altre informazioni sull'integrazione della rete virtuale con il servizio/le funzioni app qui](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
1. [Altre informazioni sulle reti virtuali in Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/)
1. [Abilitare le funzionalità e il controllo di rete con gli ambienti di servizio app](https://docs.microsoft.com/azure/app-service/environment/intro)
1. [Connettersi a singole risorse locali senza modifiche al firewall usando connessioni ibride](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)
1. [Altre informazioni sui proxy di funzione](https://review.docs.microsoft.com/azure/azure-functions/functions-proxies)

<!--Image references-->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/Create-Function-App.png
[3]: ./media/functions-create-vnet/Create-App-Service-Plan.PNG
[4]: ./media/functions-create-vnet/configure-VNET.png
[5]: ./media/functions-create-vnet/create-VM-1.png
[6]: ./media/functions-create-vnet/create-VM-2.png
[7]: ./media/functions-create-vnet/create-VM-2.1.png
[8]: ./media/functions-create-vnet/Networking-1.png
[9]: ./media/functions-create-vnet/Networking-2.png
[10]: ./media/functions-create-vnet/Networking-3.png
[11]: ./media/functions-create-vnet/New-Proxy.png
[12]: ./media/functions-create-vnet/Create-Proxy.png
[14]: ./media/functions-create-vnet/VM-Networking.png
