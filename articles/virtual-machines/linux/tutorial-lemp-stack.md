---
title: Distribuire LEMP in una macchina virtuale Linux in Azure | Microsoft Docs
description: 'Esercitazione: installare lo stack LEMP in una macchina virtuale Linux in Azure'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.openlocfilehash: 87d60ae51aaa33b709d272605419fd85eeb5d93d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-lemp-web-server-on-an-azure-vm"></a>Installare un server Web LEMP in una macchina virtuale di Azure
Questo articolo illustra come distribuire un server Web NGINX, MySQL e PHP (lo stack LEMP) in una VM Ubuntu in Azure. Lo stack LEMP è un alternativa al popolare [stack LAMP](tutorial-lamp-stack.md), che è anche possibile installare in Azure. Per verificare il funzionamento del server LEMP, è facoltativamente possibile installare e configurare un sito WordPress. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una VM Ubuntu ("L" nello stack LEMP)
> * Aprire la porta 80 per il traffico Web
> * Installare NGINX, MySQL e PHP
> * Verificare l'installazione e la configurazione
> * Installare WordPress nel server LEMP


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Installare NGINX, MySQL e PHP

Usare il comando seguente per aggiornare le origini dei pacchetti Ubuntu e installare NGINX, MySQL e PHP. 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

Viene chiesto di installare i pacchetti e le altre dipendenze. Quando viene richiesto, impostare una password radice per MySQL e quindi premere [INVIO] per continuare. Seguire i prompt rimanenti. In questo modo vengono installate le estensioni PHP minime richieste per l'uso di PHP con MySQL. 

![Pagina della password radice per MySQL][1]

## <a name="verify-installation-and-configuration"></a>Verificare l'installazione e la configurazione


### <a name="nginx"></a>NGINX

Controllare la versione di NGINX con il comando seguente:
```bash
nginx -v
```

Con NGINX installato e la porta 80 aperta per la macchina virtuale, è ora possibile accedere al server Web da Internet. Per visualizzare la home page di NGINX, aprire un Web browser e immettere l'indirizzo IP pubblico della VM. Usare l'indirizzo IP pubblico usato per stabilire la connessione SSH alla VM:

![Pagina NGINX predefinita][3]


### <a name="mysql"></a>MySQL

Controllare la versione di MySQL con il comando seguente. Si noti il parametro `V` in maiuscolo:

```bash
mysql -V
```

È consigliabile eseguire lo script seguente per proteggere l'installazione di MySQL:

```bash
mysql_secure_installation
```

Immettere la password radice per MySQL e configurare le impostazioni di sicurezza per l'ambiente.

Per creare un database MySQL, aggiungere utenti o modificare le impostazioni di configurazione, accedere a MySQL:

```bash
mysql -u root -p
```

Al termine, chiudere il prompt mysql digitando `\q`.

### <a name="php"></a>PHP

Controllare la versione di PHP con il comando seguente:

```bash
php -v
```

Configurare NGINX per usare PHP FastCGI Process Manager (PHP-FPM). Usare i comandi seguenti per eseguire un backup del file config del blocco del server NGINX originale e quindi modificare il file originale in un editor di propria scelta:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

Nell'editor sostituire il contenuto di `/etc/nginx/sites-available/default` con il codice seguente. Vedere i commenti per la spiegazione delle impostazioni. Sostituire *yourPublicIPAddress* con l'indirizzo IP pubblico della VM e mantenere le altre impostazioni. Salvare quindi il file.

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
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
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

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
