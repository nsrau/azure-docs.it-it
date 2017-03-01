---
title: Distribuire un&quot;applicazione Node.js in macchine virtuali Linux in Azure
description: Informazioni su come distribuire un&quot;applicazione Node.js in macchine virtuali Linux in Azure.
services: 
documentationcenter: nodejs
author: stepro
manager: dmitryr
editor: 
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: /azure
ms.assetid: 857a812d-c73e-4af7-a985-2d0baf8b6f71
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2016
ms.author: stephpr
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 0de0314902805a2bdb37ce3c6f79ec221f3aed31
ms.lasthandoff: 02/16/2017


---
# <a name="deploy-a-nodejs-application-to-linux-virtual-machines-in-azure"></a>Distribuire un'applicazione Node.js in macchine virtuali Linux in Azure
Questa esercitazione illustra come selezionare un'applicazione Node.js e distribuirla in macchine virtuali Linux eseguite in Azure. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo in grado di eseguire Node.js.

Si apprenderà come:

* Eseguire il fork e la clonazione di un repository GitHub contenente una semplice applicazione TODO;
* Creare e configurare due macchine virtuali Linux in Azure per eseguire l'applicazione;
* Iterare l'applicazione tramite il push degli aggiornamenti alla macchina virtuale front-end Web.

> [!NOTE]
> Per completare l'esercitazione, sono necessari un account GitHub e un account Microsoft Azure. È inoltre necessario poter usare GIT da un computer di sviluppo.
> 
> Se non si ha un account GitHub, è possibile iscriversi [qui](https://github.com/join).
> 
> Se non si ha un account [Microsoft Azure](https://azure.microsoft.com/), è possibile iscriversi [qui](https://azure.microsoft.com/pricing/free-trial/) per ottenere una versione di prova GRATUITA. Le istruzioni riportate consentiranno di completare il processo di iscrizione per ottenere un [account Microsoft](http://account.microsoft.com) , nel caso in cui non sia già presente. In alternativa, se si è eseguita la sottoscrizione a Visual Studio, è possibile [attivare i vantaggi di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
> 
> Se sul computer di sviluppo non è presente GIT e si usa un sistema Macintosh o Windows, è possibile installarlo da [qui](http://www.git-scm.com). Se si usa Linux, installare GIT mediante il meccanismo più adatto alle proprie esigenze, ad esempio `sudo apt-get install git`.
> 
> 

## <a name="forking-and-cloning-the-todo-application"></a>Fork e clonazione dell'applicazione TODO
L'applicazione TODO usata in questa esercitazione implementa un semplice front-end Web in un'istanza di MongoDB che tiene traccia di un elenco TODO. Dopo l'iscrizione a GitHub, accedere [qui](https://github.com/stepro/node-todo) per trovare l'applicazione ed eseguirne il fork usando il collegamento in alto a destra. In questo modo verrà creato un repository nell'account denominato *nomeaccount*/node-todo.

A questo punto, clonare il repository nel computer di sviluppo:

    git clone https://github.com/accountname/node-todo.git

Il clone locale del repository verrà usato in seguito per apportare modifiche al codice sorgente.

## <a name="creating-and-configuring-the-linux-virtual-machines"></a>Creazione e configurazione di macchine virtuali Linux
Azure supporta il calcolo non elaborato mediante macchine virtuali Linux. Questa parte dell'esercitazione illustra come è possibile attivare facilmente due macchine virtuali Linux e distribuire l'applicazione TODO in esse, eseguendo il front-end Web su di una e l'istanza di MongoDB sull'altra.

### <a name="creating-virtual-machines"></a>Creazione di macchine virtuali
Il modo più semplice per creare una nuova macchina virtuale in Azure è usare il portale di Azure. Fare clic [qui](https://portal.azure.com) per accedere e avviare il portale di Azure sul Web browser. Dopo aver caricato il portale di Azure, seguire questa procedura:

* Fare clic sul collegamento "+ Nuovo"
* Scegliere la categoria "Calcolo", quindi scegliere "Ubuntu Server 14.04 LTS"
* Selezionare il modello di distribuzione "Gestione risorse" e fare clic su "Crea"
* Fornire le informazioni di base attenendosi alle linee guida seguenti:
  * Specificare un nome facilmente identificabile in un secondo momento
  * Per questa esercitazione, scegliere Autenticazione password
  * Creare un nuovo gruppo di risorse con un nome identificabile
* Ai fini di questa esercitazione, specificare "A1 Standard" come dimensione della macchina virtuale è una scelta ragionevole
* Per le impostazioni aggiuntive, assicurarsi che il disco sia di tipo "Standard" e accettare tutte le restanti impostazioni predefinite
* Avviare la creazione nella pagina di riepilogo

Eseguire due volte il processo descritto in precedenza per creare due macchine virtuali Linux, una per il front-end Web e l'altra per l'istanza di MongoDB. Per la creazione delle macchine virtuali saranno necessari da 5 a 10 minuti.

### <a name="assigning-a-dns-entry-for-virtual-machines"></a>Assegnazione di una voce DNS per le macchine virtuali
Per impostazione predefinita, è possibile accedere alle macchine virtuali create in Azure solo mediante un indirizzo IP pubblico, ad esempio 1.2.3.4. Di seguito viene indicato come rendere le macchine più facilmente identificabili assegnando loro voci DNS.

Dopo che nel portale è stata confermata la creazione delle macchine virtuali, fare clic sul collegamento "Macchine virtuali" nella barra di spostamento sinistra e individuare le macchine in questione. Per ogni macchina virtuale:

* Individuare la scheda Informazioni di base e far clic su Indirizzo IP pubblico;
* Nella configurazione dell'indirizzo IP pubblico assegnare un'etichetta di nome DNS e salvare.

Il portale garantirà che il nome specificato sia disponibile. Dopo il salvataggio della configurazione, le macchine virtuali avranno nomi host simili a `machinename.region.cloudapp.azure.com`.

### <a name="connecting-to-the-virtual-machines"></a>Connessione alle macchine virtuali
Al momento del provisioning, le macchine virtuali sono state preconfigurate per consentire le connessioni remote su SSH. Si tratta del meccanismo che verrà usato per configurare le macchine virtuali. Se per la distribuzione si sta usando Windows e non si dispone di un client SSH, è necessario ottenerne uno. Una soluzione comune è usare PuTTY, scaricabile da [qui](http://www.chiark.greenend.org.uk/~sgtatham/putty/). I sistemi operativi Macintosh e Linux vengono forniti con una versione di SSH preinstallata.

### <a name="configuring-the-web-frontend-virtual-machine"></a>Configurazione della macchina virtuale front-end Web
Usare SSH per connettersi alla macchina front-end Web creata usando PuTTY, la riga di comando ssh o un altro strumento SSH di propria scelta. Verrà visualizzato un messaggio di benvenuto seguito da un prompt dei comandi:

Assicurarsi innanzitutto che GIT e il nodo siano entrambi installati:

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs

Poiché il front-end Web dell'applicazione si basa su alcuni moduli Node.js nativi, è necessario installare anche il set essenziale di strumenti di compilazione:

    sudo apt-get install -y build-essential

Al termine, installare un'applicazione Node.js denominata *forever*, che consentirà di eseguire le applicazioni server Node.js:

    sudo npm install -g forever

Queste sono tutte le dipendenze che devono essere presenti su questa macchina virtuale per eseguire il front-end Web dell'applicazione. A questo scopo, creare innanzitutto un clone "bare" del repository GitHub precedentemente sottoposto a fork, in modo da poter pubblicare facilmente aggiornamenti nella macchina virtuale (lo scenario di aggiornamento verrà descritto in seguito) e quindi clonare il clone "bare" per fornire una versione del repository effettivamente eseguibile.

Dalla home directory (~) eseguire i comandi seguenti sostituendo *accountname* con il nome dell'account utente GitHub:

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

Immettere la directory node-todo ed eseguire i comandi seguenti:

    npm install
    forever start server.js

Il front-end Web dell'applicazione è ora in esecuzione. Per eccedere all'applicazione da un Web browser è tuttavia necessario eseguire ancora un passaggio. La macchina virtuale creata è protetta da una risorsa di Azure denominata *gruppo di sicurezza di rete*. Tale risorsa è stata creata al momento del provisioning della macchina virtuale. Attualmente questa risorsa consente soltanto l'indirizzamento alla macchina virtuale delle richieste esterne alla porta 22. Questo permette unicamente la comunicazione di SSH con la macchina. Di conseguenza, per visualizzare l'applicazione TODO, configurata per essere eseguita sulla porta 8080, è necessario aprire anche tale porta.

Tornare al portale di Azure e seguire questa procedura:

* Fare clic su "Gruppi di risorse" nella barra di spostamento sinistra
* Selezionare il gruppo di risorse contenente la macchina virtuale
* Dall'elenco di risorse visualizzato selezionare il gruppo di sicurezza di rete (quello contrassegnato dall'icona dello scudo)
* In Proprietà scegliere "Regole di sicurezza in ingresso"
* Nella barra degli strumenti fare clic su "Aggiungi"
* Specificare un nome, ad esempio "default-allow-todo"
* Impostare il protocollo su "TCP"
* Impostare l'intervallo di porte di destinazione su "8080"
* Fare clic su OK e attendere che la regola di sicurezza venga creata

Dopo la creazione della regola di sicurezza, l'applicazione TODO è visibile pubblicamente su Internet ed è possibile accedervi usando ad esempio un URL simile al seguente:

    http://machinename.region.cloudapp.azure.com:8080

Si noterà che, anche se la macchina virtuale MongoDB non è ancora stata configurata, l'applicazione TODO rende disponibile un certo numero di funzionalità. Il motivo di questo comportamento è che il repository di origine è hardcoded per l'uso di un'istanza di MongoDB già distribuita. Dopo la configurazione della macchina virtuale MongoDB, l'esercitazione prevede la modifica del codice sorgente per consentire l'utilizzo dell'istanza di MongoDB privata.

### <a name="configuring-the-mongodb-virtual-machine"></a>Configurazione della macchina virtuale MongoDB
Usare SSH per connettersi alla seconda macchina virtuale creata usando PuTTY, la riga di comando ssh o un altro strumento SSH di propria scelta. Dopo aver visualizzato il messaggio di benvenuto e il prompt dei comandi, installare MongoDB (le istruzioni sono disponibili [qui](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)):

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

Per impostazione predefinita, MongoDB è configurato in modo da essere accessibile solo in locale. Ai fini di questa esercitazione, MongoDB verrà configurato in modo che sia possibile accedervi dalla macchina virtuale dell'applicazione. In un contesto sudo aprire il file /etc/mongod.conf e individuare la sezione `# network interfaces` . Modificare il valore di configurazione `net.bindIp` in `0.0.0.0`.

> [!NOTE]
> Questa configurazione verrà usata solo ai fini di questa esercitazione. **NON** è una procedura di sicurezza consigliata e non deve essere usata in ambienti di produzione.
> 
> 

Assicurarsi che il servizio MongoDB sia stato avviato:

    sudo service mongod restart

Per impostazione predefinita, MongoDB è attivo sulla porta 27017. Di conseguenza, come è stato necessario aprire la porta 8080 sulla macchina virtuale front-end Web, è ora necessario aprire la porta 27017 sulla macchina virtuale MongoDB.

Tornare al portale di Azure e seguire questa procedura:

* Fare clic su "Gruppi di risorse" nella barra di spostamento sinistra
* Selezionare il gruppo di risorse contenente la macchina virtuale MongoDB
* Dall'elenco di risorse visualizzato selezionare il gruppo di sicurezza di rete (quello contrassegnato dall'icona dello scudo) denominato come la macchina virtuale MongoDB
* In Proprietà scegliere "Regole di sicurezza in ingresso"
* Nella barra degli strumenti fare clic su "Aggiungi"
* Specificare un nome, ad esempio "default-allow-mongo"
* Impostare il protocollo su "TCP"
* Impostare l'intervallo di porte di destinazione su "27017"
* Fare clic su OK e attendere che la regola di sicurezza venga creata

## <a name="iterating-on-the-todo-application"></a>Iterazione dell'applicazione TODO
Nei precedenti passaggi dell'esercitazione è stato eseguito il provisioning di due macchine virtuali Linux: una esegue il front-end Web dell'applicazione, mentre l'altra esegue un'istanza di MongoDB. Si è tuttavia verificato un problema: il front-end Web non sta ancora effettivamente usando l'istanza di MongoDB sottoposta a provisioning. È possibile risolvere il problema aggiornando il codice del front-end Web in modo che usi una variabile di ambiente anziché un'istanza hardcoded.

### <a name="changing-the-todo-application"></a>Modifica dell'applicazione TODO
Nel computer di sviluppo in cui è stato clonato il repository node-todo aprire il file `node-todo/config/database.js` usando l'editor preferito e modificare l'URL da un valore hardcoded come `mongodb://...` in `process.env.MONGODB`.

Eseguire il commit e il push delle modifiche nel master GitHub:

    git commit -am "Get MongoDB instance from env"
    git push origin master

Sfortunatamente, questa operazione non determina la pubblicazione della modifica nella macchina virtuale front-end Web. Nei passaggi successivi verranno apportate altre modifiche alla macchina virtuale per consentire l'attivazione di un meccanismo semplice ma efficace. Tale meccanismo consente di pubblicare gli aggiornamenti in modo che sia possibile osservarne rapidamente gli effetti nell'ambiente di produzione.

### <a name="configuring-the-web-frontend-virtual-machine"></a>Configurazione della macchina virtuale front-end Web
Si ricordi che nei passaggi precedenti è stato creato un clone "bare" del repository node-todo sulla macchina virtuale front-end Web. Risulta che questa azione abbia creato un nuovo GIT remoto in cui è possibile eseguire il push delle modifiche. Tuttavia, il semplice push a questo GIT remoto non fornisce il modello di iterazione rapida richiesto dagli sviluppatori quando scrivono codice.

Ciò che si desidera è assicurare che, quando si esegue un push al repository sulla macchina virtuale, l'applicazione TODO in esecuzione venga automaticamente aggiornata. Fortunatamente, GIT consente di ottenere facilmente questo risultato.

GIT espone un numero di hook che vengono chiamati in determinati momenti per rispondere alle azioni eseguite sul repository. Gli hook vengono specificati usando script della shell nella cartella `hooks` del repository. L'hook più utilizzabile per lo scenario di aggiornamento automatico è l'evento `post-update` .

In una sessione SSH per la connessione alla macchina virtuale front-end Web passare alla directory `~/node-todo.git/hooks` e aggiungere il contenuto seguente al file denominato `post-update` (sostituire `machinename` e `region` con le informazioni della macchina virtuale MongoDB):

    #!/bin/bash

    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info

Verificare che il file sia eseguibile usando il comando seguente:

    chmod 755 post-update

Questo script verifica che l'applicazione server corrente venga arrestata, che il codice nel repository clonato venga aggiornato alla versione più recente, che tutte le dipendenze aggiornate vengano soddisfatte e che il server venga riavviato. Garantisce inoltre che l'ambiente sia stato configurato per il ricevimento del primo aggiornamento dell'applicazione, in modo da ottenere l'istanza di MongoDB da una variabile di ambiente.

### <a name="configuring-your-development-machine"></a>Configurazione del computer di sviluppo
Di seguito il computer di sviluppo verrà collegato alla macchina virtuale front-end Web. L'operazione è molto semplice ed è analoga all'aggiunta del repository "bare" alla macchina virtuale in remoto. A questo scopo eseguire il comando seguente, sostituendo *user* con il nome di accesso della macchina virtuale front-end Web e *machinename* e *region* con i valori appropriati:

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

Queste operazioni sono sufficienti per eseguire il push, o meglio la pubblicazione, delle modifiche nella macchina virtuale front-end Web.

### <a name="publishing-updates"></a>Pubblicazione degli aggiornamenti
Di seguito verrà pubblicata l'unica modifica apportata finora, in modo che l'applicazione usi l'istanza di MongoDB:

    git push azure master

Verrà visualizzato un output simile al seguente:

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

Dopo l'esecuzione del comando, tentare di aggiornare l'applicazione in un Web browser. Dovrebbe essere possibile vedere che l'elenco TODO qui presentato è vuoto e non più collegato all'istanza di MongoDB distribuita che è stata condivisa.

Per completare l'esercitazione, è necessario apportare un'altra modifica più evidente. Nel computer di sviluppo aprire il file node-todo/public/index.html usando l'editor preferito. Individuare l'intestazione jumbotron e modificare il titolo da "I'm a Todo-aholic" in "I'm a Todo-aholic on Azure!".

A questo punto, eseguire il commit:

    git commit -am "Azurify the title"

Questa volta la modifica verrà pubblicata in Azure prima di eseguirne il push nel repository GitHub:

    git push azure master

Dopo il completamento del comando, aggiornare la pagina Web per visualizzare le modifiche. Poiché appare corretta, eseguire il push della modifica nell'origine remota: 

    git push origin master

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha illustrato come selezionare un'applicazione Node.js e distribuirla in macchine virtuali Linux eseguite in Azure. Per altre informazioni sulle macchine virtuali Linux, vedere [Introduzione a Linux in Azure](/documentation/articles/virtual-machines-linux-introduction/).

Per altre informazioni su come sviluppare applicazioni Node.js in Azure, vedere il [centro per sviluppatori Node.js](/develop/nodejs/).


