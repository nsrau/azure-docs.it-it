<properties
	pageTitle="Distribuire LAMP in una macchina virtuale Linux | Microsoft Azure"
	description="Informazioni su come installare lo stack LAMP in una VM Linux"
	services="virtual-machines-linux"
	documentationCenter="virtual-machines"
	authors="jluk"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="jluk"/>

# Distribuire lo stack LAMP in Azure
Questo articolo illustra come distribuire un server Web Apache, MySQL e PHP (lo stack LAMP) in Azure. Saranno necessari un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) [connessa all'account Azure](../xplat-cli-connect.md).

Questo articolo illustra i due metodi per installare LAMP:

## Breve riepilogo del comando

1) Distribuire LAMP in una nuova VM

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Distribuire LAMP in una VM esistente

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## Procedura dettagliata per distribuire LAMP in una nuova VM

È possibile iniziare creando un nuovo [gruppo di risorse](../resource-group-overview.md) che conterrà la VM:

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

È stata creata una VM Linux con LAMP già installato. Per verificare l'installazione, se necessario, vedere [Verificare l'installazione di LAMP].

## Procedura dettagliata per distribuire LAMP in una VM esistente

Per assistenza nella creazione di una VM Linux, è possibile vedere [qui per informazioni su come creare una VM Linux](./virtual-machines-linux-quick-create-cli.md). Sarà quindi necessario connettersi tramite SSH alla VM Linux. Per assistenza nella creazione di una chiave SSH, è possibile vedere [qui per informazioni su come creare una chiave SSH in Linux/Mac](./virtual-machines-linux-mac-create-ssh-keys.md). Se si ha già una chiave SSH, continuare connettendosi tramite SSH alla VM Linux con `ssh username@uniqueDNS`.

Ora che la VM Linux è disponibile, verrà illustrata l'installazione dello stack LAMP nelle distribuzioni basate su Debian. I comandi possono essere leggermente diversi per le altre distribuzioni Linux.

#### Installazione in Debian/Ubuntu

È necessario che siano installati i pacchetti seguenti: `apache2`, `mysql-server`, `php5` e `php5-mysql`. È possibile installarli catturandoli direttamente o usando Tasksel. Di seguito sono fornite le istruzioni per entrambi le opzioni. Prima dell'installazione, sarà necessario scaricare e aggiornare gli elenchi di pacchetti.

    user@ubuntu$ sudo apt-get update
    
##### Pacchetti singoli
Uso di apt-get:

	user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### Uso di Tasksel
In alternativa è possibile scaricare Tasksel, uno strumento Debian/Ubuntu che installa più pacchetti correlati come "attività" coordinata nel sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Dopo l'esecuzione di una delle due opzioni riportate sopra verrà chiesto di installare questi pacchetti e altre dipendenze. Premere y e quindi INVIO per continuare e seguire eventuali altri comandi per impostare una password amministrativa per MySQL. Verranno così installate le estensioni PHP minime obbligatorie per l'uso di PHP con MySQL.

![][1]

Eseguire il comando seguente per visualizzare altre estensioni PHP disponibili come pacchetti:

	user@ubuntu$ apt-cache search php5


#### Creare un documento info.php

Ora sarà possibile controllare la versione di Apache, MySQL e PHP disponibile digitando `apache2 -v`, `mysql -v` o `php -v` nella riga di comando.

Per eseguire altri test, è possibile creare rapidamente una pagina di informazioni PHP da visualizzare in un browser. Creare un nuovo file con l'editor di testo Nano con questo comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

Nell'editor di testo GNU Nano aggiungere le righe seguenti:

    <?php
    phpinfo();
    ?>

Quindi salvare e chiudere l'editor di testo.

Riavviare Apache con questo comando per applicare tutte le nuove installazioni.

    user@ubuntu$ sudo service apache2 restart

## Verificare l'installazione di LAMP

Ora è possibile controllare la pagina di informazioni PHP appena creata nel browser, andando a http://youruniqueDNS/info.php. Dovrebbe essere simile alla seguente.

![][2]

È possibile controllare l'installazione di Apache visualizzando la pagina predefinita di Apache2 Ubuntu andando a http://youruniqueDNS/. Dovrebbe essere visualizzata una schermata analoga alla seguente.

![][3]

È stato configurato uno stack LAMP nella VM di Azure.

## Passaggi successivi

Vedere la documentazione di Ubuntu sullo stack LAMP:

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png

<!---HONumber=AcomDC_0824_2016-->