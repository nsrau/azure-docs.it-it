---
title: Usare l&quot;estensione CustomScript in una macchina virtuale Linux | Microsoft Docs
description: Informazioni su come utilizzare l&quot;estensione CustomScript per distribuire le applicazioni in macchine virtuali Linux in Azure create utilizzando il modello di distribuzione classica.
editor: tysonn
manager: timlt
documentationcenter: 
services: virtual-machines-linux
author: gbowerman
tags: azure-service-management
ms.assetid: e535241d-feca-4412-b07a-67c936ba88a0
ms.service: virtual-machines-linux
ms.workload: multiple
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f63649cc3b6c4acad6e39356f0210d3726548b17
ms.lasthandoff: 03/21/2017


---
# <a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Distribuire un'applicazione LAMP utilizzando l'estensione CustomScript di Azure per Linux
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per informazioni sulla distribuzione di uno stack LAMP con il modello di Resource Manager, vedere [qui](virtual-machines-linux-create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

L'estensione CustomScript di Microsoft Azure per Linux fornisce un modo per personalizzare le macchine virtuali (VM) tramite l'esecuzione di codice arbitrario scritto in un linguaggio supportato dalla macchina virtuale (ad esempio, Python e Bash). In tal modo è possibile automatizzare la distribuzione di applicazioni a più computer in un modo molto flessibile.

È possibile distribuire l'estensione CustomScript tramite il portale di Azure classico, Windows PowerShell o l'interfaccia della riga di comando di Azure (CLI di Azure).

In questo articolo utilizzeremo la CLI di Azure per distribuire una semplice applicazione LAMP a una macchina virtuale di Ubuntu creata utilizzando il modello di distribuzione classica.

## <a name="prerequisites"></a>Prerequisiti
Per questo esempio, creare innanzitutto due macchine virtuali di Azure che eseguano Ubuntu 14.04 o la versione successiva. Le macchine virtuali vengono chiamate *script-vm* e *lamp-vm*. Quando si creano macchine virtuali, utilizzare sempre nomi univoci. Uno viene utilizzato per eseguire i comandi dell’interfaccia della riga di comando e uno viene utilizzato per distribuire l'applicazione LAMP.

Sono inoltre necessari un account di archiviazione di Azure e una chiave di accesso (che è possibile ottenere nel portale di Azure classico).

Per informazioni sulla creazione delle macchine virtuali Linux in Azure, fare riferimento a [Creare una macchina virtuale che esegue Linux](virtual-machines-linux-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

I comandi di installazione presuppongono Ubuntu, ma è possibile adattare l'installazione per qualsiasi distro Linux supportata.

La macchina virtuale script-vm richiede l'interfaccia della riga di comando di Azure installata e una connessione ad Azure attiva. Per informazioni, fare riferimento a [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](../cli-install-nodejs.md).

## <a name="upload-a-script"></a>Caricamento di uno script
Si utilizzerà l'estensione CustomScript per eseguire uno script in una macchina virtuale remota per installare lo stack LAMP e creare una pagina PHP. Per accedere ovunque ci si trovi, lo script viene caricato come blob di Azure.

### <a name="script-overview"></a>Panoramica di script
Lo script consente di installare uno stack LAMP in Ubuntu (inclusa l'impostazione di un'installazione invisibile all'utente di MySQL), di scrivere un semplice file PHP e di avviare Apache:

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

### <a name="upload-script"></a>Caricamento script
Salvare lo script come file di testo, ad esempio *install_lamp.sh* e caricarlo nell'archiviazione di Azure. È possibile eseguire facilmente questa operazione con l’interfaccia della riga di comando di Azure. Nell'esempio seguente il file viene caricato in un contenitore di archiviazione denominato "scripts". se il contenitore non esiste, è necessario prima crearlo.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Creare inoltre un file JSON che descrive la procedura di download dello script dall'archiviazione di Azure. Salvare il file come *public_config.json* (sostituendo "mystorage" con il nome dell'account di archiviazione in uso):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Distribuzione dell'estensione
A questo punto, è possibile utilizzare il seguente comando per distribuire l'estensione CustomScript di Linux alla macchina virtuale remota utilizzando l'interfaccia della riga di comando di Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

In tal modo verrà scaricato ed eseguito lo script *install_lamp.sh* in una macchina virtuale denominata *lamp-vm*.

Poiché l'applicazione include un server Web, ricordarsi di aprire una porta di ascolto HTTP nella macchina virtuale remota con il seguente comando:

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi
È possibile controllare l'avanzamento dell'esecuzione dello script personalizzato esaminando il file di log nella macchina virtuale remota. SSH per *lamp-vm* e tail per il file di log con il seguente comando:

    cd /var/log/azure/customscript
    tail -f handler.log

Dopo aver eseguito l'estensione CustomScript, è possibile esplorare la pagina PHP creata per le informazioni. La pagina PHP per l'esempio in questo articolo è *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile utilizzare gli stessi passaggi di base per distribuire applicazioni più complesse. In questo esempio lo script di installazione è stato salvato come blob pubblico in Archiviazione di Azure. Un'opzione più sicura sarebbe archiviare lo script di installazione come blob protetto con una [firma di accesso protetto](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Di seguito sono riportate alcune risorse aggiuntive per l’interfaccia della riga di comando di Azure, Linux e l'estensione CustomScript:

[Automatizzare le attività di personalizzazione delle macchine virtuali Linux utilizzando l'estensione CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Estensioni per Linux di Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Computing Linux e open source in Azure](virtual-machines-linux-opensource-links.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


