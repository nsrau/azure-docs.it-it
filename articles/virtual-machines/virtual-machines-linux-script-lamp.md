<properties
	pageTitle="Distribuire un'applicazione Linux utilizzando l'estensione CustomScript di Azure"
	description="Informazioni su come utilizzare l'estensione CustomScript di Azure per distribuire le applicazioni in macchine virtuali Linux."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines"
	authors="gbowerman"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Distribuire un'applicazione LAMP utilizzando l'estensione CustomScript di Azure per Linux#

L'estensione CustomScript di Azure per Linux fornisce un modo per personalizzare le macchine virtuali \(VM\) tramite l'esecuzione di codice arbitrario scritto in un linguaggio supportato dalla macchina virtuale \(ad esempio, Python, Bash e così via\). In tal modo è possibile automatizzare la distribuzione di applicazioni a più computer in un modo molto flessibile.

È possibile distribuire l'estensione CustomScript tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure \(CLI di Azure\).

In questo esempio verrà distribuita una semplice applicazione LAMP su Ubuntu utilizzando l'interfaccia della riga di comando di Azure.

## Prerequisiti

Per questa procedura dettagliata, creare due macchine virtuali di Azure che eseguono Ubuntu 14.04. Le macchine virtuali vengono definite qui *script-vm* e *lamp vm*. Quando si esegue questa procedura, utilizzare sempre nomi univoci. In una macchina virtuale vengono eseguiti i comandi CLI e nell'altra viene distribuita l'applicazione LAMP.

Sono inoltre necessari un account di archiviazione di Azure e una chiave di accesso che è possibile ottenere nel portale di Azure.

Per informazioni sulla creazione delle macchine virtuali Linux in Azure, fare riferimento a [Creare una macchina virtuale che esegue Linux](virtual-machines-linux-tutorial.md).

Sebbene i comandi di installazione specifici presuppongono l'utilizzo di Ubuntu, è possibile adattare i passaggi generali per qualsiasi distribuzione supportata.

La macchina virtuale *script-vm* richiede l'interfaccia della riga di comando di Azure installata e una connessione ad Azure attiva. Per informazioni, fare riferimento a [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli.md).

## Caricamento di uno script

In questo esempio l'estensione CustomScript esegue uno script in una macchina virtuale remota per installare lo stack LAMP e creare una pagina PHP. Per accedere ovunque ci si trovi, lo script viene caricato come blob di Azure.

**Lo script**

Questo script consente di installare uno stack LAMP in Ubuntu \(inclusa l'impostazione di un'installazione invisibile all'utente di MySQL\), di scrivere un semplice file PHP e di avviare Apache:

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
	echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

**Caricamento**

Salvare lo script come file di testo, ad esempio *lamp\_install.sh*, e quindi caricarlo nell'archiviazione di Azure. È possibile eseguire facilmente questa operazione con l’interfaccia della riga di comando di Azure. Nell'esempio seguente il file viene caricato in un contenitore di archiviazione denominato "scripts". Nota: se il contenitore non esiste, è necessario prima crearlo.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Creare inoltre un file JSON che descrive la procedura di download dello script dall'archiviazione di Azure. Salvare il file come *public\_config.json* \(sostituendo "mystorage" con il nome dell'account di archiviazione in uso\):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Distribuzione dell'estensione

A questo punto, è possibile distribuire l'estensione CustomScript di Linux alla macchina virtuale remota utilizzando l'interfaccia della riga di comando di Azure:

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

In tal modo verrà scaricato ed eseguito lo script *lamp\_install.sh* su una macchina virtuale denominata *lamp-vm*.

Poiché l'applicazione include un server Web, ricordarsi di aprire una porta di ascolto HTTP nella macchina virtuale remota:

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Monitoraggio e risoluzione dei problemi

È possibile controllare l'avanzamento dell'esecuzione dello script personalizzato esaminando il file di log nella macchina virtuale remota. SSH per *lamp-vm* e tail per il file di log:

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Al termine dell'esecuzione dell'estensione CustomScript è possibile passare alla pagina PHP creata \(in questo esempio: *http://lamp-vm.cloudapp.net/phpinfo.php*.\).

## Risorse aggiuntive

È possibile utilizzare gli stessi passaggi di base per distribuire applicazioni più complesse. In questo esempio lo script di installazione è stato salvato come blob pubblico in Archiviazione di Azure. Un'opzione più sicura sarebbe archiviare lo script di installazione come blob protetto con una [firma di accesso protetto](https://msdn.microsoft.com/library/azure/ee395415.aspx) \(SAS\).

Di seguito sono riportate alcune risorse aggiuntive per l’interfaccia della riga di comando di Azure, Linux e l'estensione CustomScript:

[Automatizzare le attività di personalizzazione delle macchine virtuali Linux utilizzando l'estensione CustomScript](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Estensioni per Linux di Azure \(GitHub\)](https://github.com/Azure/azure-linux-extensions)

[Computing Linux e open source in Azure](virtual-machines-linux-opensource.md)
 

<!---HONumber=58_postMigration-->