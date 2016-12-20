---
title: Distribuire un cluster Deis a 3 nodi | Microsoft Docs
description: In questo articolo viene descritto come creare un cluster Deis a 3 nodi utilizzando un modello di Gestione risorse di Azure.
services: virtual-machines-linux
documentationcenter: 
author: HaishiBai
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5eb67eb7-95d4-461d-8eac-44925224ba5f
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/24/2015
ms.author: hbai
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 1c45d0ad8431b5cfff7859817fe57c3168f2dc7f


---
# <a name="deploy-a-3-node-deis-cluster"></a>Distribuzione di un cluster Deis a 3 nodi
In questo articolo viene illustrato il provisioning di un cluster [Deis](http://deis.io/) su Azure. Vengono descritti tutti i passaggi dalla creazione dei certificati necessari per la distribuzione e la scalabilità di un’applicazione **Go** di esempio sul cluster di cui è stato appena eseguito il provisioning.

Nel diagramma seguente viene mostrata l'architettura del sistema distribuito. Un amministratore di sistema gestisce il cluster usando strumenti Deis come **deis** e **deisctl**. Le connessioni vengono stabilite tramite un servizio di bilanciamento del carico di Azure, che inoltra le connessioni a uno dei nodi membri sul cluster. Anche i client effettuano l’accesso alle applicazioni distribuite tramite il servizio di bilanciamento del carico. In questo caso, il servizio di bilanciamento del carico inoltra il traffico a un router mesh Deis, che indirizza ulteriormente il traffico ai contenitori Docker corrispondenti ospitati sul cluster.

  ![Diagramma dell'architettura del cluster Deis distribuito](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

Per eseguire i passaggi seguenti, saranno necessari:

* Una sottoscrizione di Azure attiva. Se non si dispone di una sottoscrizione, è possibile ottenere una versione di valutazione gratuita su [azure.com](https://azure.microsoft.com/).
* Un ID di lavoro o di scuola per utilizzare i gruppi di risorse di Azure. Se si dispone di un account personale e si accede con un ID di Microsoft, è necessario [creare un ID di lavoro da quello personale](virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* A seconda del sistema operativo del client, [Azure PowerShell](../powershell-install-configure.md) o l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli-install.md).
* [OpenSSL](https://www.openssl.org/). OpenSSL viene utilizzato per generare i certificati necessari.
* Un client Git, come [Git Bash](https://git-scm.com/).
* Per eseguire un test sull'applicazione di esempio, sarà necessario anche un server DNS. È possibile utilizzare tutti i server o i servizi DNS che supportano i record A con carattere jolly.
* Un computer per eseguire gli strumenti client Deis. È possibile utilizzare una macchina locale o una macchina virtuale. È possibile eseguire questi strumenti in qualsiasi distribuzione Linux, ma nelle istruzioni seguenti viene utilizzato Ubuntu.

## <a name="provision-the-cluster"></a>Provisioning del database
In questa sezione, verrà usato un modello [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) dal repository open source [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Innanzitutto, verrà copiato il modello. Quindi, verrà creata una nuova coppia di chiavi SSH per l'autenticazione e verrà configurato un nuovo identificatore per il cluster. Infine, verrà utilizzato lo script Shell o lo script PowerShell per eseguire il provisioning del cluster.

1. Clonare il repository: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
   
        git clone https://github.com/Azure/azure-quickstart-templates
2. Andare alla cartella del modello:
   
        cd azure-quickstart-templates\deis-cluster-coreos
3. Creare una nuova coppia di chiavi SSH utilizzando ssh-keygen:
   
        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"
4. Generare un certificato utilizzando la chiave privata riportata in precedenza:
   
        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]
5. Andare a [https://discovery.etcd.io/new](https://discovery.etcd.io/new) per generare un nuovo token del cluster, simile a:
   
        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
   <br />
    Ciascun cluster CoreOS deve disporre di un token univoco da questo servizio gratuito. Vedere [Documentazione CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) per ulteriori dettagli.
6. Modificare il file **cloud-config.yaml** per sostituire il token **discovery** esistente con il nuovo token:
   
        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...
7. Modificare il file **azuredeploy-parameters.json** : aprire il certificato creato nel passaggio 4 in un editor di testo. Copiare tutto il testo tra `----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----` nel parametro **sshKeyData** (sarà necessario rimuovere tutti i caratteri di nuova riga).
8. Modificare il parametro **newStorageAccountName** . Si tratta dell'account di archiviazione per i dischi del sistema operativo della macchina virtuale. Questo nome account deve essere univoco a livello globale.
9. Modificare il parametro **publicDomainName**. che diventerà parte del nome DNS associato all’IP pubblico del servizio di bilanciamento del carico. L'FQDN finale avrà il formato di *[valore di questo parametro]*.*[area geografica]*.cloudapp.azure.com. Ad esempio, se il nome viene specificato come deishbai32 e il gruppo di risorse viene distribuito all’area geografica Stati Uniti Occidentali, l’FQDN finale per il servizio di bilanciamento del carico sarà deishbai32.westus.cloudapp.azure.com.
10. Salvare il file del parametro. Quindi è possibile eseguire il provisioning del cluster utilizzando Azure PowerShell:
    
        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml
    
    o l’interfaccia della riga di comando di Azure:
    
        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  
11. Una volta che viene eseguito il provisioning del gruppo di risorse, è possibile visualizzare tutte le risorse nel gruppo sul portale di Azure classico. Come illustrato nella schermata seguente, il gruppo di risorse contiene una rete virtuale con tre macchine virtuali, che hanno effettuato l’accesso allo stesso set di disponibilità. Il gruppo contiene inoltre un servizio di bilanciamento del carico, che dispone di un IP pubblico associato.
    
    ![Il gruppo di risorse di cui è stato eseguito il provisioning nel portale di Azure classico](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>Installazione del client
Per controllare il cluster Deis, è necessario **deisctl** . Anche se deisctl viene installato automaticamente in tutti i nodi del cluster, si consiglia di utilizzare deisctl su una macchina amministrativa separata. Inoltre, poiché tutti i nodi vengono configurati con soli indirizzi IP privati, sarà necessario utilizzare il tunneling SSH tramite il servizio di bilanciamento del carico, che dispone di un indirizzo IP pubblico per eseguire la connessione alle macchine dei nodi. Di seguito vengono riportati i passaggi per la configurazione di deisctl su una macchina fisica o virtuale Ubuntu separata.

1. Installare deisctl:mkdir deis
   
        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl
2. Aggiungere la chiave privata all’agente SSH:
   
        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]
3. Configurare deisctl:
   
        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

Il modello definisce le regole NAT in entrata che eseguono il mapping 2223 all’istanza 1, 2224 all’istanza 2 e 2225 all’istanza 3. Ciò fornisce ridondanza per utilizzare lo strumento deisctl. È possibile esaminare queste regole sul portale di Azure classico:

![Regole NAT sul servizio di bilanciamento del carico](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [!NOTE]
> Il modello supporta attualmente solo cluster a 3 nodi. Ciò a causa di un limite nella definizione delle regole NAT del modello di Gestione risorse di Azure, che non supporta la sintassi del ciclo.
> 
> 

## <a name="install-and-start-the-deis-platform"></a>Installazione e avvio della piattaforma Deis
Ora è possibile utilizzare deisctl per installare e avviare la piattaforma Deis:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [!NOTE]
> L’avvio della piattaforma richiede alcuni minuti (fino a 10). In particolare, l'avvio del servizio generatore può richiedere molto tempo. E talvolta può richiedere alcuni tentativi per avere esito positivo: se l'operazione sembra bloccarsi, provare a digitare `ctrl+c` per interrompere l'esecuzione del comando e riprovare.
> 
> 

È possibile utilizzare `deisctl list` per verificare se tutti i servizi sono in esecuzione:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Congratulazioni. Ora si dispone di un cluster Deis in esecuzione su Azure. Successivamente, si procederà alla distribuzione di un’applicazione Go di esempio per vedere il cluster in azione.

## <a name="deploy-and-scale-a-hello-world-application"></a>Distribuzione e scalabilità di un'applicazione Hello World
Nei passaggi seguenti viene illustrato come distribuire un’applicazione Go "Hello World" al cluster. I passaggi si basano sulla [documentazione Deis](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Affinché il router mesh funzioni correttamente, sarà necessario disporre di un record A con carattere jolly per il dominio che punti all'indirizzo IP pubblico del servizio di bilanciamento del carico. Nella schermata seguente viene mostrato il record A per la registrazione di un dominio di esempio su GoDaddy:
   
    ![Record A GoDaddy](media/virtual-machines-linux-deis-cluster/go-daddy.png)
   
   <p />
2. Per installare deis:
   
        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
3. Creare una nuova chiave SSH, quindi aggiungere la chiave pubblica su GitHub (ovviamente, è possibile riutilizzare le chiavi esistenti). Per creare una nuova coppia di chiavi SSH, utilizzare:
   
        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)
4. Aggiungere id_rsa.pub o la chiave pubblica di propria scelta su GitHub. È possibile eseguire questa operazione utilizzando il pulsante Aggiungi chiave SSH nella schermata di configurazione delle chiavi SSH:
   
   ![Chiave GitHub](media/virtual-machines-linux-deis-cluster/github-key.png)
   
   <p />
5. Per registrare un nuovo utente:
   
        deis register http://deis.[your domain]
   <p />
6. Per aggiungere la chiave SSH:
   
        deis keys:add [path to your SSH public key]
   <p />      
7. Creare un'applicazione.
   
        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
   <p />
8. Il git push attiverà le immagini Docker per essere compilato e distribuito, operazione che potrebbe richiedere alcuni minuti. In base alla mia esperienza, in alcuni casi l’esecuzione del passaggio 10 (push dell’immagine sul repository privato) potrebbe bloccarsi. In questo caso, è possibile arrestare il processo, rimuovere l'applicazione usando 'deis apps:destroy –a <application name>` to remove the application and try again. You can use `deis apps:list' per individuare il nome dell'applicazione. Se tutto funziona, si otterrà un risultato simile al seguente alla fine dell'output del comando:
   
        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
   <p />
9. Verificare se l'applicazione funziona:
   
        curl -S http://[your application name].[your domain]
   Dovrebbe essere visualizzato:
   
        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
   <p />
10. Scalabilità dell'applicazione a 3 istanze:
    
        deis scale cmd=3
    <p />
11. Facoltativamente, è possibile utilizzare deis info per esaminare i dettagli dell'applicazione. Gli output seguenti provengono dalla distribuzione della mia applicazione:
    
        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }
    
        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)
    
        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>Passaggi successivi
In questo articolo vengono illustrati tutti i passaggi per eseguire il provisioning di un nuovo cluster Deis su Azure utilizzando un modello di Gestione risorse di Azure. Il modello supporta la ridondanza nelle connessioni degli strumenti, nonché il bilanciamento del carico per le applicazioni distribuite. Il modello consente anche di evitare l'utilizzo di IP pubblici nei nodi membri. Ciò consente di risparmiare preziose risorse IP pubbliche e di fornire un ambiente più protetto per ospitare le applicazioni. Per altre informazioni, vedere gli articoli seguenti:

[Panoramica di Azure Resource Manager][resource-group-overview]  
[Come usare l'interfaccia della riga di comando di Azure][azure-command-line-tools]  
[Uso di Azure PowerShell con Azure Resource Manager][powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md



<!--HONumber=Nov16_HO3-->


