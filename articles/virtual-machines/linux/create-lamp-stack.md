---
title: Distribuire LAMP in una macchina virtuale Linux in Azure | Microsoft Docs
description: Informazioni su come installare lo stack LAMP in una macchina virtuale Linux in Azure
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
ms.devlang: azurecli
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 174490bec1aea20abf2c2bd465c7aa2c8590e3e2
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-on-azure"></a>Distribuire lo stack LAMP in Azure
Questo articolo illustra come distribuire un server Web Apache, MySQL e PHP (lo stack LAMP) in Azure. Sono necessari un account Azure ([richiedere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2). È possibile eseguire questi passaggi anche tramite l'[interfaccia della riga di comando di Azure 1.0](create-lamp-stack-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-command-summary"></a>Breve riepilogo dei comandi

1. Salvare e modificare il [file azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) nel punto desiderato sul computer locale.
2. Eseguire i due comandi seguenti per creare un gruppo di risorse e quindi distribuire il modello:

```azurecli
az group create -l westus -n myResourceGroup
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

### <a name="deploy-lamp-on-existing-vm"></a>Distribuire LAMP in una VM esistente
I comandi seguenti aggiornano i pacchetti e poi installano Apache, MySQL e PHP:

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Procedura dettagliata per distribuire LAMP in una nuova macchina virtuale

1. Creare un gruppo di risorse con [az group create](/cli/azure/group#create) per contenere la nuova macchina virtuale:

```azurecli
az group create -l westus -n myResourceGroup
```
Per creare la VM, è possibile usare un modello di Azure Resource Manager già pronto disponibile [qui in GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

2. Salvare il [file azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) nel computer locale.
3. Modificare il file **azuredeploy.parameters.json** secondo le proprie preferenze di input.
4. Distribuire il modello con [az group deployment create] che fa riferimento al file json scaricato:

```azurecli
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

L'output è simile all'esempio seguente:

```json
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Resources/deployments/azuredeploy",
"name": "azuredeploy",
"properties": {
    "correlationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "debugSetting": null,
}
...
"provisioningState": "Succeeded",
"template": null,
"templateLink": {
    "contentVersion": "1.0.0.0",
    "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json"
    },
    "timestamp": "2017-02-22T00:05:51.860411+00:00"
},
"resourceGroup": "myResourceGroup"
}
```

È stata creata una VM Linux con LAMP già installato. Se si desidera verificare l'installazione, si può passare direttamente a [Verificare l'installazione di LAMP](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Procedura dettagliata per distribuire LAMP in una macchina virtuale esistente
Per assistenza nella creazione di una macchina virtuale Linux, [qui sono disponibili informazioni su come creare una macchina virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-cli). È quindi necessario connettersi tramite SSH alla macchina virtuale Linux. Per assistenza nella creazione di una chiave SSH, [qui sono disponibili informazioni su come creare una chiave SSH in Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Se si dispone già di una chiave SSH, continuare connettendosi tramite SSH dalla riga di comando alla macchina virtuale Linux con `ssh azureuser@mypublicdns.westus.cloudapp.azure.com`.

Ora che la macchina virtuale Linux è disponibile, viene illustrata l'installazione dello stack LAMP nelle distribuzioni basate su Debian. I comandi possono essere leggermente diversi per le altre distribuzioni Linux.

#### <a name="installing-on-debianubuntu"></a>Installazione in Debian/Ubuntu
È necessario che siano installati i pacchetti seguenti: `apache2`, `mysql-server`, `php5` e `php5-mysql`. È possibile installare questi pacchetti catturandoli direttamente o usando Tasksel.
Prima dell'installazione, è necessario scaricare e aggiornare gli elenchi di pacchetti.

```bash
sudo apt-get update
```

##### <a name="individual-packages"></a>Pacchetti singoli
Uso di apt-get:

```bash
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

##### <a name="using-tasksel"></a>Uso di Tasksel
In alternativa è possibile scaricare Tasksel, uno strumento Debian/Ubuntu che installa più pacchetti correlati come "attività" coordinata nel sistema.

```bash
sudo apt-get install tasksel
sudo tasksel install lamp-server
```

Dopo l'esecuzione di una delle due opzioni precedenti, verrà richiesto di installare questi pacchetti e altre dipendenze. Per impostare una password amministrativa per MySQL, premere 'y' e poi 'INVIO' per continuare e seguire eventuali altri prompt. In questo modo vengono installate le estensioni PHP minime richieste per l'uso di PHP con MySQL. 

![][1]

Eseguire il comando seguente per visualizzare altre estensioni PHP disponibili come pacchetti:

```bash
apt-cache search php5
```

#### <a name="create-infophp-document"></a>Creare un documento info.php
Ora sarà possibile controllare la versione di Apache, MySQL e PHP disponibile digitando `apache2 -v`, `mysql -v` o `php -v` nella riga di comando.

Per eseguire altri test, è possibile creare rapidamente una pagina di informazioni PHP da visualizzare in un browser. Creare un file con l'editor di testo Nano con questo comando:

```bash
sudo nano /var/www/html/info.php
```

Nell'editor di testo GNU Nano aggiungere le righe seguenti:

```php
<?php
phpinfo();
?>
```

Quindi salvare e chiudere l'editor di testo.

Riavviare Apache con questo comando, in modo che tutte le nuove installazioni vengano applicate.

```bash
sudo service apache2 restart
```

## <a name="verify-lamp-successfully-installed"></a>Verificare l'installazione di LAMP
A questo punto è possibile aprire un browser e accedere a http://youruniqueDNS/info.php per controllare la pagina di informazioni PHP creata. Dovrebbe avere un aspetto simile a questa immagine.

![][2]

È possibile controllare l'installazione di Apache visualizzando la pagina predefinita di Apache2 Ubuntu andando su http://youruniqueDNS/. L'output è simile all'esempio seguente:

![][3]

È stato configurato uno stack LAMP nella VM di Azure.

## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione di Ubuntu sullo stack LAMP:

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png

