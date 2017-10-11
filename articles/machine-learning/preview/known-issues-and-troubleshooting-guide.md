---
title: Guida alla risoluzione dei problemi e problemi noti | Microsoft Docs
description: Elenco di problemi noti e guida alla risoluzione
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - Guida alla risoluzione dei problemi e problemi noti 
Questo articolo consente di trovare e correggere errori o guasti riscontrati durante l'uso dell'applicazione Azure Machine Learning Workbench. 

> [!NOTE]
> Quando si comunica con il team di supporto tramite messaggi di posta elettronica o post di forum, è utile conoscere il numero di build. Per trovare il numero di build dell'app, fare clic sul menu **?**. Fare clic sul numero di build per copiarlo negli Appunti. È possibile incollarlo in messaggi di posta elettronica o nei forum del supporto per segnalare problemi.

![Controllare il numero di versione](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows e Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>Macchina virtuale di data science di Azure basata su CentOS 
* L'invio di processi a una macchina virtuale di data science (DSVM, Data Science Virtual Machine) di Azure basata su CentOS non è supportato. È possibile specificare come destinazione una [DSVM basata su Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

### <a name="docker-error"></a>Errore di Docker 
* Durante l'esecuzione in un contenitore Docker locale, se viene visualizzato l'errore seguente, è possibile correggerlo modificando il server DNS da Automatic a Fixed 8.8.8.8. 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![Image](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Password di una VM Linux 
* Se si modifica la password dal portale di Azure in una VM Ubuntu Linux, è possibile che venga visualizzato l'errore seguente quando si esegue un processo:
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  La soluzione alternativa prevede di aggiungere un file (ad esempio, _myDockerUsers_) in _/etc/sudoers.d_ con il contenuto seguente:
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>Chiavi SSH 
* Le chiavi SSH non sono supportate quando ci si connette a un computer remoto o a un cluster Spark tramite SSH. È supportata solo la modalità nome utente/password.

## <a name="windows-only"></a>Solo Windows 
### <a name="sharing-c-drive-in-docker"></a>Condivisione dell'unità C in Docker 
* Controllare la condivisione nell'unità C usando Esplora file
* Aprire le impostazioni della scheda di rete e disinstallare/reinstallare "Condivisione file e stampanti per reti Microsoft" per vEthernet
* Aprire le impostazioni di Docker e condividere l'unità C dalle impostazioni di Docker
* Le modifiche alla password di Windows influiscono sulla condivisione. Aprire Esplora file, condividere di nuovo l'unità C e immettere la nuova password.
* Potrebbe anche verificarsi un problema di firewall quando si prova a condividere l'unità C con Docker. Questo [post di Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) può essere utile.
* Quando si condivide l'unità C usando le credenziali di dominio, la condivisione potrebbe smettere di funzionare nelle reti in cui il controller di dominio non è raggiungibile (ad esempio, rete domestica, Wi-Fi pubblico e così via). Per altre informazioni, vedere [questo post](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).



## <a name="mac-only"></a>Solo Mac 
* Le trasformazioni clustering del testo non sono supportate in Mac.
* La libreria RevoScalePy non è supportata in Mac.

## <a name="azure-machine-learning-service-limits"></a>Limiti dei servizi di Azure Machine Learning

- Dimensioni massime consentite per la cartella del progetto: 25 MB
    >[!Note]
    >Questo limite non si applica alle cartelle `.git`, `docs` e `outputs`. Questi nomi di cartella distinguono tra maiuscole e minuscole.

- Tempo di esecuzione massimo consentito per l'esperimento: 7 giorni
- Dimensioni massime di un file tracciato nella cartella `outputs` dopo un'esecuzione: 512 MB 

>[!NOTE]
>Se si usano file di grandi dimensioni, vedere [Persisting Changes and Deal with Large Files](how-to-read-write-files.md) (Salvataggio permanente delle modifiche e uso di file di grandi dimensioni).

## <a name="other-issues"></a>Altri problemi
Se si è a conoscenza di altri problemi non documentati, Inviare commenti e suggerimenti tramite _Invia smile/faccia imbronciata_. 




