---
title: Installare MySQL in una macchina virtuale OpenSUSE in Azure | Microsoft Docs
description: Informazioni su come installare MySQL in una macchina virtuale OpenSUSE Linux in Azure.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: cynthn
ms.openlocfilehash: 88bd895cb3a384f1ada0394fe2da206aca86b981
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure

[MySQL](http://www.mysql.com) è un database SQL open source molto diffuso. Questa esercitazione illustra come creare una macchina virtuale che esegue OpenSUSE Linux, poi installa MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Creare una macchina virtuale che esegue OpenSUSE Linux

Creare prima un gruppo di risorse. In questo esempio, il gruppo di risorse viene denominato *mySQSUSEResourceGroup* e creato nell'area *Stati Uniti orientali*.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Creare la macchina virtuale In questo esempio la macchina virtuale viene denominata *myVM*. Viene usata una macchina virtuale di dimensioni *Standard_D2s_v3*, ma è consigliabile scegliere le [dimensioni della VM](sizes.md) più adatte al proprio carico di lavoro.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

È anche necessario aggiungere una regola al gruppo di sicurezza di rete per consentire il traffico sulla porta 3306 per MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Connettersi alla VM

Per la connessione alla macchina virtuale si userà SSH. In questo esempio, l'indirizzo IP pubblico della macchina virtuale è *10.111.112.113*. L'indirizzo IP è visibile nell'output restituito alla creazione della VM.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Aggiornare la VM
 
Dopo la connessione alla macchina virtuale, installare aggiornamenti di sistema e patch. 
   
```bash
sudo zypper update
```

Seguire le istruzioni visualizzate per aggiornare la macchina virtuale.

## <a name="install-mysql"></a>Installare MySQL 


Installare MySQL nella macchina virtuale tramite SSH. Rispondere alle istruzioni visualizzate come appropriato.

```bash
sudo zypper install mysql
```
 
Impostare MySQL in modo che si avvii all'avvio del sistema. 

```bash
sudo systemctl enable mysql
```
Verificare che MySQL sia abilitato.

```bash
systemctl is-enabled mysql
```

Il comando dovrebbe restituire "enabled".


## <a name="mysql-password"></a>Password MySQL

Dopo l'installazione, la password radice di MySQL è vuota per impostazione predefinita. Eseguire lo script **mysql\_secure\_installation** per proteggere MySQL. Lo script richiederà di cambiare la password radice di MySQL, rimuovere gli account utente anonimi, disabilitare gli account di accesso radice remoti, rimuovere i database di test e ricaricare la tabella dei privilegi. 


```bash
mysql_secure_installation
```

## <a name="log-in-to-mysql"></a>Accedere a MySQL

Ora è possibile accedere ed entrare nel prompt di MySQL.

```bash  
mysql -u root -p
```
Questo comando consente di passare prompt di MySQL, in cui è possibile eseguire istruzioni SQL per interagire con il database.

Ora creare un nuovo utente MySQL.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Il punto e virgola (;) alla fine della riga è essenziale per la conclusione del comando.


## <a name="create-a-database"></a>Creare un database


Creare un database e concedere le autorizzazioni all'utente `mysqluser`.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
I nomi utente e le password per il database sono usati solo da script per la connessione al database.  I nomi di account utente per il database non rappresentano necessariamente account utente effettivi nel sistema.

Abilitare l'accesso da un altro computer. In questo esempio, l'indirizzo IP del computer da cui vogliamo accedere è *10.112.113.114*.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Per uscire dall'utilità di amministrazione database MySQL, digitare:

```    
quit
```


## <a name="next-steps"></a>Passaggi successivi
Per informazioni su MySQL, vedere la [documentazione di MySQL](http://dev.mysql.com/doc/index-topic.html).




