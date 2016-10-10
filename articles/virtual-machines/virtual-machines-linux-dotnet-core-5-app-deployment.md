<properties
   pageTitle="Automazione della distribuzione di applicazioni con le estensioni delle macchine virtuali | Microsoft Azure"
   description="Macchine virtuali di Azure - Esercitazione DotNet Core"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# Distribuzione di applicazioni con i modelli di Azure Resource Manager

Dopo che tutti i requisiti dell'infrastruttura di Azure sono stati identificati e convertiti in un modello di distribuzione, è necessario occuparsi della distribuzione effettiva delle applicazioni. Per distribuzione delle applicazioni si intende l'installazione effettiva dei file binari delle applicazioni nelle risorse di Azure. Per l'esempio Music Store, è necessario installare e configurare .NET Core, NGINX e Supervisor in ogni macchina virtuale, nonché installare i file binari di Music Store nella macchina virtuale e creare il database di Music Store.

Questo documento descrive in che modo le estensioni delle macchine virtuali possono automatizzare la distribuzione e la configurazione di applicazioni nelle macchine virtuali di Azure. Tutte le dipendenze e le configurazioni univoche sono evidenziate. Per ottenere risultati ottimali, pre-distribuire un'istanza della soluzione alla propria sottoscrizione di Azure ed esercitarsi con il modello di Azure Resource Manager. Il modello completo è disponibile in [Music Store Deployment on Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux) (Distribuzione di Music Store in Ubuntu).

## Script di configurazione

Le estensioni delle macchine virtuali sono programmi specializzati che vengono eseguiti sulle macchine virtuali per automatizzare le attività di configurazione. Le estensioni sono disponibili per molti scopi specifici, ad esempio un programma antivirus, la configurazione della registrazione e la configurazione di Docker. Un'estensione script personalizzata può essere usata per eseguire uno script su una macchina virtuale. Nel caso dell'esempio Music Store, l'estensione script personalizzata viene usata per configurare le macchine virtuali Ubuntu e installare l'applicazione Music Store.

Prima di vedere in che modo le estensioni delle macchine virtuali sono dichiarate in un modello di Azure Resource Manager, esaminare lo script che viene eseguito. Questo script configura la macchina virtuale Ubuntu in modo da ospitare l'applicazione Music Store. Durante l'esecuzione, lo script installa tutto il software necessario, installa l'applicazione Music Store dal controllo del codice sorgente e prepara il database.

Per altre informazioni sull'hosting di un'applicazione .NET Core su Linux, vedere [Publish to a Linux production environment](https://docs.asp.net/en/latest/publishing/linuxproduction.html) (Pubblicare in un ambiente di produzione Linux).


```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## Estensione script della macchina virtuale

Le estensioni delle macchine virtuali possono essere eseguite su una macchina virtuale in fase di compilazione includendo la risorsa dell'estensione nel modello di Azure Resource Manager. È possibile aggiungere l'estensione usando la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo una risorsa JSON valida nel modello. La risorsa dell'estensione script è annidata all'interno della risorsa della macchina virtuale, come illustrato nell'esempio seguente.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Estensione script della macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359).

Nel codice JSON seguente è possibile notare che lo script è archiviato in GitHub. Questo script può essere incluso anche nell'archiviazione BLOB di Azure. Inoltre, i modelli di Azure Resource Manager consentono di creare la stringa di esecuzione dello script in modo che i valori dei parametri del modello siano utilizzabili come parametri per l'esecuzione dello script. In questo caso, i dati vengono forniti quando si distribuiscono i modelli e questi valori possono quindi essere usati durante l'esecuzione dello script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Per altre informazioni sull'estensione script personalizzata, vedere [Custom script extensions with Resource Manager templates](./virtual-machines-linux-extensions-customscript.md) (Estensioni script personalizzate con i modelli di Resource Manager).

## Passaggio successivo

<hr>

[Explore More Azure Resource Manager Templates](https://github.com/Azure/azure-quickstart-templates) (Esplorare altri modelli di Azure Resource Manager)

<!---HONumber=AcomDC_0928_2016-->