---
title: 'Esercitazione: distribuire LEMP in una macchina virtuale Linux in Azure | Microsoft Docs'
description: In questa esercitazione viene descritto come installare lo stack di LEMP in una macchina virtuale Linux in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 06a009978d85f2ba0f10030aeb1344a1b84bf3c3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299381"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>Esercitazione: Installare un server Web LEMP in una macchina virtuale Linux in Azure

Questo articolo illustra come distribuire un server Web NGINX, MySQL e PHP (lo stack LEMP) in una VM Ubuntu in Azure. Lo stack LEMP è un alternativa al popolare [stack LAMP](tutorial-lamp-stack.md), che è anche possibile installare in Azure. Per verificare il funzionamento del server LEMP, è facoltativamente possibile installare e configurare un sito WordPress. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una VM Ubuntu ("L" nello stack LEMP)
> * Aprire la porta 80 per il traffico Web
> * Installare NGINX, MySQL e PHP
> * Verificare l'installazione e la configurazione
> * Installare WordPress nel server LEMP

Questa installazione è utilizzabile per i test rapidi o il modello di prova.

Questa esercitazione usa l'interfaccia della riga di comando all'interno di [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), che viene costantemente aggiornato alla versione più recente. Per aprire Cloud Shell, selezionare **Prova** nella parte superiore di qualsiasi blocco di codice.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Installare NGINX, MySQL e PHP

Usare il comando seguente per aggiornare le origini dei pacchetti Ubuntu e installare NGINX, MySQL e PHP. 

```bash
sudo apt update && sudo apt install nginx && sudo apt install mysql-server php-mysql php-fpm
```

Viene chiesto di installare i pacchetti e le altre dipendenze. In questo modo vengono installate le estensioni PHP minime richieste per l'uso di PHP con MySQL.  

## <a name="verify-installation-and-configuration"></a>Verificare l'installazione e la configurazione


### <a name="verify-nginx"></a>Verificare NGINX

Controllare la versione di NGINX con il comando seguente:
```bash
nginx -v
```

Con NGINX installato e la porta 80 aperta per la macchina virtuale, è ora possibile accedere al server Web da Internet. Per visualizzare la home page di NGINX, aprire un Web browser e immettere l'indirizzo IP pubblico della VM. Usare l'indirizzo IP pubblico usato per stabilire la connessione SSH alla VM:

![Pagina NGINX predefinita][3]


### <a name="verify-and-secure-mysql"></a>Verificare e proteggere MySQL

Controllare la versione di MySQL con il comando seguente. Si noti il parametro `V` in maiuscolo:

```bash
mysql -V
```

Per proteggere l'installazione di MySQL, impostando anche una password radice, eseguire lo script `mysql_secure_installation`. 

```bash
sudo mysql_secure_installation
```

Facoltativamente, è possibile configurare il plug-in di convalida password (scelta consigliata). Impostare quindi una password per l'utente ROOT MySQL e configurare le restanti impostazioni di sicurezza per l'ambiente. È consigliabile rispondere "Y" (sì) a tutte le domande.

Per provare le funzionalità di MySQL, ovvero creare un database MySQL, aggiungere utenti o modificare le impostazioni di configurazione, accedere a MySQL. Questo passaggio non è obbligatorio per completare l'esercitazione. 


```bash
sudo mysql -u root -p
```

Al termine, chiudere il prompt mysql digitando `\q`.

### <a name="verify-php"></a>Verificare PHP

Controllare la versione di PHP con il comando seguente:

```bash
php -v
```

Configurare NGINX per usare PHP FastCGI Process Manager (PHP-FPM). Usare i comandi seguenti per eseguire un backup del file config del blocco del server NGINX originale e quindi modificare il file originale in un editor di propria scelta:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

Nell'editor sostituire il contenuto di `/etc/nginx/sites-available/default` con il codice seguente. Vedere i commenti per la spiegazione delle impostazioni. Sostituire *yourPublicIPAddress* con l'indirizzo IP pubblico della VM, confermare la versione di PHP in `fastcgi_pass` e mantenere le altre impostazioni. Salvare quindi il file.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
}
```

Cercare errori di sintassi nella configurazione NGINX:

```bash
sudo nginx -t
```

Se la sintassi è corretta, riavviare NGINX con i comandi seguenti:

```bash
sudo service nginx restart
```

Per eseguire altri test, creare rapidamente una pagina di informazioni PHP da visualizzare in un browser. Il comando seguente crea la pagina di informazioni di PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Ora è possibile controllare la pagina di informazioni di PHP creata. Aprire un browser e passare a `http://yourPublicIPAddress/info.php`. Sostituire l'indirizzo IP pubblico della VM. Dovrebbe avere un aspetto simile a questa immagine.

![Pagina di informazioni di PHP][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è distribuito un server LEMP in Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una VM Ubuntu
> * Aprire la porta 80 per il traffico Web
> * Installare NGINX, MySQL e PHP
> * Verificare l'installazione e la configurazione
> * Installare WordPress nello stack LEMP

Passare all'esercitazione successiva per apprendere come proteggere i server Web con i certificati SSL.

> [!div class="nextstepaction"]
> [Secure web server with SSL (Proteggere il server Web con SSL)](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
