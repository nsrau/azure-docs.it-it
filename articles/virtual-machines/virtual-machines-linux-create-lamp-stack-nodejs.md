---
title: Distribuire LAMP in una macchina virtuale Linux con l&quot;interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Informazioni su come installare lo stack LAMP in una VM Linux in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: NA
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: 59af3469a5b2d5cca68bf18dca1aa1e3ab684adb
ms.openlocfilehash: 0675b6471e37e89e426df85e2fb696fcff2927fd
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-lamp-stack-with-the-azure-cli-10"></a>Distribuire lo stack LAMP con l'interfaccia della riga di comando di Azure 1.0
Questo articolo illustra come distribuire un server Web Apache, MySQL e PHP (lo stack LAMP) in Azure. Sono necessari un account Azure ([richiedere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) [connessa all'account Azure](../xplat-cli-connect.md).

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0]: interfaccia della riga di comando per i modelli di distribuzione classica e di Gestione risorsa (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machines-linux-create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

* Distribuire LAMP in una VM esistente

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Procedura dettagliata per distribuire LAMP in una nuova VM
È possibile iniziare creando un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) che conterrà la nuova VM:

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Per creare la VM, è possibile usare un modello di Azure Resource Manager già pronto disponibile [qui in GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Verrà visualizzata una risposta che chiede alcuni input aggiuntivi:

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

È stata creata una VM Linux con LAMP già installato. Se si desidera verificare l'installazione si può passare direttamente a [Verificare l'installazione di LAMP](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Procedura dettagliata per distribuire LAMP in una macchina virtuale esistente
Per assistenza nella creazione di una VM Linux, è possibile vedere [qui per informazioni su come creare una VM Linux](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È quindi necessario connettersi tramite SSH alla macchina virtuale Linux. Per assistenza nella creazione di una chiave SSH è possibile dirigersi [qui per informazioni su come creare una chiave SSH in Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Se si dispone già di una chiave SSH, continuare connettendosi tramite SSH alla VM Linux con `ssh exampleUsername@exampleDNS` dalla riga di comando.

Ora che la macchina virtuale Linux è disponibile è possibile illustrare l'installazione dello stack LAMP nelle distribuzioni basate su Debian. I comandi possono essere leggermente diversi per le altre distribuzioni Linux.

#### <a name="installing-on-debianubuntu"></a>Installazione in Debian/Ubuntu
È necessario che siano installati i pacchetti seguenti: `apache2`, `mysql-server`, `php5` e `php5-mysql`. È possibile installare questi pacchetti catturandoli direttamente o usando Tasksel. Di seguito sono fornite le istruzioni per entrambi le opzioni.
Prima dell'installazione, è necessario scaricare e aggiornare gli elenchi di pacchetti.

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>Pacchetti singoli
Uso di apt-get:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Uso di tasksel
In alternativa è possibile scaricare Tasksel, uno strumento Debian/Ubuntu che installa più pacchetti correlati come "attività" coordinata nel sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Dopo l'esecuzione di una delle due opzioni precedenti, verrà richiesto di installare questi pacchetti e altre dipendenze. Premere y e quindi INVIO per continuare e seguire eventuali altri comandi per impostare una password amministrativa per MySQL. In questo modo vengono installate le estensioni PHP minime obbligatorie per l'uso di PHP con MySQL. 

![][1]

Eseguire il comando seguente per visualizzare altre estensioni PHP disponibili come pacchetti:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Creare un documento info.php
Ora sarà possibile controllare la versione di Apache, MySQL e PHP disponibile digitando `apache2 -v`, `mysql -v` o `php -v` nella riga di comando.

Per eseguire altri test, è possibile creare rapidamente una pagina di informazioni PHP da visualizzare in un browser. Creare un file con l'editor di testo Nano con questo comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

Nell'editor di testo GNU Nano aggiungere le righe seguenti:

    <?php
    phpinfo();
    ?>

Quindi salvare e chiudere l'editor di testo.

Riavviare Apache con questo comando per applicare tutte le nuove installazioni.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Verificare l'installazione di LAMP
A questo punto è possibile controllare la pagina di informazioni PHP creata aprendo un browser e andando a http://youruniqueDNS/info.php. Dovrebbe essere simile a questa immagine.

![][2]

È possibile controllare l'installazione di Apache visualizzando la pagina predefinita di Apache2 Ubuntu andando su http://youruniqueDNS/. Dovrebbe essere visualizzata una schermata simile a questa immagine.

![][3]

È stato configurato uno stack LAMP nella VM di Azure.

## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione di Ubuntu sullo stack LAMP:

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png
