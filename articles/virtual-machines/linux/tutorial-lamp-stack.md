---
title: 'Esercitazione: distribuire LAMP in una macchina virtuale Linux in Azure | Microsoft Docs'
description: In questa esercitazione viene descritto come installare lo stack di LAMP in una macchina virtuale Linux in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 66b7d7692d9143c8db813ad135b0b9c70b8869d2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708579"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Esercitazione: Installare un server Web LAMP in una macchina virtuale Linux in Azure

Questo articolo illustra come distribuire un server Web Apache, MySQL e PHP (lo stack LAMP) in una VM Ubuntu in Azure. Se si preferisce il server Web NGINX, vedere l'esercitazione [LEMP stack](tutorial-lemp-stack.md) (Stack LEMP). Per verificare il funzionamento del server LAMP, è facoltativamente possibile installare e configurare un sito WordPress. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una VM Ubuntu ("L" nello stack LAMP)
> * Aprire la porta 80 per il traffico Web
> * Installare Apache, MySQL e PHP
> * Verificare l'installazione e la configurazione
> * Installare WordPress nel server LAMP

Questa installazione è utilizzabile per i test rapidi o il modello di prova. Per altre informazioni sullo stack LAMP, incluse le raccomandazioni per un ambiente di produzione, vedere la [documentazione di Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Installare Apache, MySQL e PHP

Usare il comando seguente per aggiornare le origini dei pacchetti Ubuntu e installare Apache, MySQL e PHP. Si noti che alla fine del comando è presente un accento circonflesso (^) che fa parte del nome del pacchetto `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Viene chiesto di installare i pacchetti e le altre dipendenze. In questo modo vengono installate le estensioni PHP minime richieste per l'uso di PHP con MySQL.  

## <a name="verify-installation-and-configuration"></a>Verificare l'installazione e la configurazione


### <a name="verify-apache"></a>Verificare Apache

Controllare la versione di Apache con il comando seguente:
```bash
apache2 -v
```

Con Apache installato e la porta 80 aperta per la macchina virtuale, è ora possibile accedere al server Web da Internet. Per visualizzare Apache2 Ubuntu Default Page (Pagina predefinita Apache2 Ubuntu), aprire un Web browser e immettere l'indirizzo IP pubblico della VM. Usare l'indirizzo IP pubblico usato per stabilire la connessione SSH alla VM:

![Pagina predefinita di Apache][3]


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

Per eseguire altri test, creare rapidamente una pagina di informazioni PHP da visualizzare in un browser. Il comando seguente crea la pagina di informazioni di PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Ora è possibile controllare la pagina di informazioni di PHP creata. Aprire un browser e passare a `http://yourPublicIPAddress/info.php`. Sostituire l'indirizzo IP pubblico della VM. Dovrebbe avere un aspetto simile a questa immagine.

![Pagina di informazioni di PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è distribuito un server LAMP in Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una VM Ubuntu
> * Aprire la porta 80 per il traffico Web
> * Installare Apache, MySQL e PHP
> * Verificare l'installazione e la configurazione
> * Installare WordPress nel server LAMP

Passare all'esercitazione successiva per apprendere come proteggere i server Web con i certificati SSL.

> [!div class="nextstepaction"]
> [Secure web server with SSL (Proteggere il server Web con SSL)](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
