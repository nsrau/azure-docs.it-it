---
title: Panoramica dell'agente di VM Linux di Azure | Documentazione Microsoft
description: Informazioni su come installare e configurare l'agente Linux (waagent) per gestire l'interazione della macchina virtuale con il controller di infrastruttura di Azure.
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: szark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59266c6d6452eeff56b05e60389ac14f0b2c3f1f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Informazioni e uso dell'agente Linux di Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introduzione
L'agente Linux di Microsoft Azure (waagent) gestisce il provisioning di Linux e FreeBSD e l'interazione della macchina virtuale con il controller di infrastruttura di Azure. Per le distribuzioni IaaS di Linux e FreeBSD, sono disponibili le funzionalità seguenti:

> [!NOTE]
> Vedere il file [LEGGIMI](https://github.com/Azure/WALinuxAgent/blob/master/README.md) dell'agente Linux di Azure per informazioni dettagliate.
> 
> 

* **Provisioning dell'immagine**
  
  * Creazione di un account utente
  * Configurazione dei tipi di autenticazione SSH
  * Distribuzione di coppie di chiavi e chiavi pubbliche SSH
  * Impostazione del nome host
  * Pubblicazione del nome host nel DNS della piattaforma
  * Segnalazione dell'ID digitale della chiave dell'host SSH alla piattaforma
  * Gestione del disco risorse
  * Formattazione e montaggio del disco risorse
  * Configurazione dell'area di swap
* **Rete**
  
  * Gestisce i percorsi per migliorare la compatibilità con i server DHCP della piattaforma.
  * Garantisce la stabilità del nome dell'interfaccia di rete
* **Kernel**
  
  * Configura un NUMA virtuale (disabilitare per kernel <2.6.37)
  * Usa l'entropia Hyper-V per /dev/random
  * Configura i timeout SCSI per il dispositivo radice (che può essere remoto)
* **Diagnostica**
  
  * Reindirizzamento della console alla porta seriale
* **Distribuzioni SCVMM**
  
  * Rileva e avvia l'agente VMM per Linux durante l'esecuzione in un ambiente System Center Virtual Machine Manager 2012 R2
* **Estensione VM**
  
  * Inserire il componente creato da Microsoft e partner in VM Linux (IaaS) per attivare il software e l'automazione di configurazione
  * Implementazione di riferimento di estensione VM in [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Comunicazione
Il flusso di informazioni dalla piattaforma all'agente avviene tramite due canali:

* Un DVD collegato in fase di avvio per le distribuzioni IaaS. Nel DVD è incluso un file di configurazione conforme a OVF che include tutte le informazioni di provisioning diverse dalle coppie di chiavi SSH effettive.
* Un endpoint TCP che espone un'API REST usata per ottenere la configurazione della distribuzione e della topologia.

## <a name="requirements"></a>Requisiti
I sistemi seguenti sono stati testati e funzionano con l'agente Linux di Azure:

> [!NOTE]
> È possibile che questo elenco sia diverso da quello ufficiale dei sistemi supportati sulla piattaforma Microsoft Azure, come descritto di seguito: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Altri sistemi supportati:

* FreeBSD 10+ (agente Linux di Azure v2.0.10+)

Per il corretto funzionamento dell'agente Linux sono necessari alcuni package di sistema:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Utilità file system: sfdisk, fdisk, mkfs, separate
* Strumenti password: chpasswd, sudo
* Strumenti di elaborazione testo: sed, grep
* Strumenti di rete: ip-route
* Supporto di kernel per l'installazione di file system UDF.

## <a name="installation"></a>Installazione
Il metodo preferito per l'installazione e l'aggiornamento dell'agente Linux di Azure prevede l'installazione tramite un pacchetto RPM o DEB dal repository di pacchetti della distribuzione. Tutti i [provider di distribuzione supportati](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository.

Leggere la documentazione nel [repository dell'agente Linux di Azure su GitHub](https://github.com/Azure/WALinuxAgent) per conoscere le opzioni di installazione avanzate, ad esempio l'installazione da origine o da percorsi personalizzati o prefissi.

## <a name="command-line-options"></a>Opzioni da riga di comando
### <a name="flags"></a>Flag
* verbose: aumenta il livello di dettaglio del comando specificato
* force: ignora la conferma interattiva per determinati comandi

### <a name="commands"></a>Comandi:
* help: elenca i flag e i comandi supportati.
* deprovision: tenta di pulire il sistema per renderlo idoneo per un nuovo provisioning. Questa operazione comporta l'eliminazione di quanto segue:
  
  * Tutte le chiavi host (se Provisioning.RegenerateSshHostKeyPair è 'y' nel file di configurazione)
  * Configurazione NameServer in /etc/resolv.conf
  * Password radice da /etc/shadow (se Provisioning.DeleteRootPassword è 'y' nel file di configurazione)
  * Lease client DHCP memorizzati nella cache
  * Ripristina il nome host su localhost.localdomain

> [!WARNING]
> Il deprovisioning non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili e che l'immagine sia adatta per la ridistribuzione.
> 
> 

* deprovision+user: esegue tutte le operazioni in -deprovision (sopra) ed elimina l'ultimo account utente sottoposto a provisioning (ottenuto da /var/lib/waagent) e i dati associati. Questo parametro viene usato per il deprovisioning di un'immagine precedentemente sottoposta a provisioning in Azure in modo che possa essere acquisita e riutilizzata.
* version: visualizza la versione di waagent
* serialconsole: configura GRUB affinché contrassegni ttyS0 (la prima porta seriale) come console di avvio. Questo garantisce che i log di avvio del kernel vengano inviati alla porta seriale e resi disponibili per il debug.
* daemon: esegue waagent come daemon per gestire l'interazione con la piattaforma. Questo argomento è specificato per waagent nello script di inizializzazione di waagent.
* start: esegue waagent come processo in background

## <a name="configuration"></a>Configurazione
Un file di configurazione (/etc/waagent.conf) controlla le azioni dell'agente waagent. Di seguito è riportato un file di configurazione di esempio:

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y

Di seguito sono descritte le opzioni di configurazione disponibili in modo dettagliato. Le opzioni di configurazione sono di tre tipi: Boolean, String o Integer. Le opzioni di configurazione booleane possono essere specificate come "y" o "n". È possibile usare la parola chiave speciale "None" per alcune voci di configurazione di tipo stringa, come indicato di seguito.

**Provisioning.Enabled:**  
Tipo: booleano  
Predefinito: y

Consente all'utente di attivare o disattivare la funzionalità di provisioning nell'agente. I valori validi sono "y" o "n". Se il provisioning è disabilitato, le chiavi utente e host SSH nell'immagine vengono mantenute e qualsiasi configurazione specificata nell'API di provisioning di Azure viene ignorata.

> [!NOTE]
> Per impostazione predefinita il parametro `Provisioning.Enabled` è impostato su "n" nelle immagini Ubuntu Cloud che usano cloud-init per il provisioning.
> 
> 

**Provisioning.DeleteRootPassword:**  
Tipo: booleano  
Predefinito: n

Se questa voce è impostata, la password radice nel file /etc/shadow viene cancellata durante il processo di provisioning.

**Provisioning.RegenerateSshHostKeyPair:**  
Tipo: booleano  
Predefinito: y

Se questa voce è impostata, tutte le coppie di chiavi host SSH (ecdsa, dsa e rsa) vengono eliminate da /etc/ssh/ durante il processo di provisioning e viene generata un'unica coppia di chiavi aggiornata.

Il tipo di crittografia per la coppia di chiavi aggiornata è configurabile dalla voce Provisioning.SshHostKeyPairType. Si noti che alcune distribuzioni ricreeranno le coppie di chiavi SSH per qualsiasi tipo di crittografia mancante al riavvio del daemon SSH, ad esempio in caso di riavvio.

**Provisioning.SshHostKeyPairType:**  
Tipo: String  
Predefinito: rsa

È possibile impostare questa voce su un tipo di algoritmo di crittografia supportato dal daemon SSH nella macchina virtuale. I valori supportati sono in genere "rsa", "dsa" e "ecdsa". Si noti che "putty.exe" in Windows non supporta il tipo "ecdsa". Se pertanto si intende usare putty.exe in Windows per connettersi a una distribuzione Linux, usare "rsa" o "dsa".

**Provisioning.MonitorHostName:**  
Tipo: booleano  
Predefinito: y

Se questa voce è impostata, waagent monitorerà la macchina virtuale Linux per rilevare modifiche del nome host (come restituite dal comando "hostname") e aggiornerà automaticamente la configurazione di rete nell'immagine per riflettere la modifica. Per effettuare il push della modifica del nome ai server DNS, la funzionalità di rete nella macchina virtuale verrà riavviata, causando una breve interruzione nella connettività Internet.

**Provisioning.DecodeCustomData**  
Tipo: booleano  
Predefinito: n

Se impostato, waagent decodificherà CustomData da Base64.

**Provisioning.ExecuteCustomData**  
Tipo: booleano  
Predefinito: n

Se impostato, waagent eseguirà CustomData dopo il provisioning.

**Provisioning.AllowResetSysUser** Tipo: Boolean Impostazione predefinita: n

Questa opzione consente la reimpostazione della password per l'utente sys. L'impostazione predefinita corrisponde alla disabilitazione della funzionalità.

**Provisioning.PasswordCryptId**  
Tipo: String  
Impostazione predefinita: 6

Algoritmo usato dalla crittografia durante la generazione di hash della password.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
Tipo: String  
Valore predefinito: 10

Lunghezza di salt casuale usata durante la generazione di hash della password.

**ResourceDisk.Format:**  
Tipo: booleano  
Predefinito: y

Se questa voce è impostata, il disco risorse fornito dalla piattaforma verrà formattato e montato da waagent se il tipo di file system richiesto dall'utente in "ResourceDisk.Filesystem" è diverso da "ntfs". Una singola partizione di tipo Linux (83) verrà resa disponibile nel disco. Si noti che la partizione non verrà formattata se è possibile montarla correttamente.

**ResourceDisk.Filesystem:**  
Tipo: String  
Predefinito: ext4

Specifica il tipo di file system per il disco risorse. I valori supportati variano in base alla distribuzione Linux. Se la stringa è X, è necessario che mkfs.X sia presente nell'immagine Linux. Le immagini SLES 11 in genere utilizzano il valore 'ext3'. Le immagini FreeBSD in questo caso devono usare il valore 'ufs2'.

**ResourceDisk.MountPoint:**  
Tipo: String  
Predefinito: /mnt/resource 

Specifica il percorso in cui è montato il disco risorse. Si noti che il disco risorse è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale.

**ResourceDisk.MountOptions**  
Tipo: String  
Predefinito: nessuno

Specifica le opzioni di montaggio del disco da passare al comando. È un elenco di valori delimitato da virgole, ad esempio nodev,nosuid. Vedere montaggio(8) per informazioni dettagliate.

**ResourceDisk.EnableSwap:**  
Tipo: booleano  
Predefinito: n

Se questa voce è impostata, viene creato un file di scambio (/swapfile) nel disco risorse e aggiunto all'area di swap del sistema.

**ResourceDisk.SwapSizeMB:**  
Tipo: numero intero  
Predefinito: 0

Dimensione del file di scambio in megabyte.

**Logs.Verbose:**  
Tipo: booleano  
Predefinito: n

Se questa voce è impostata, viene incrementato il livello di dettaglio del log. Waagent registra in /var/log/waagent.log e usufruisce della funzionalità logrotate del sistema per la rotazione dei log.

**OS.EnableRDMA**  
Tipo: booleano  
Predefinito: n

Se impostato, l'agente tenterà di installare e caricare un driver del kernel RDMA adatto alla versione del firmware nell'hardware sottostante.

**OS.RootDeviceScsiTimeout:**  
Tipo: numero intero  
Predefinito: 300

Consente di configurare il timeout SCSI in secondi nel disco del sistema operativo e nelle unità dati. Se questa voce non è impostata, vengono usate le impostazioni predefinite del sistema.

**OS.OpensslPath:**  
Tipo: String  
Predefinito: nessuno

È possibile aggiungere questa voce per specificare un percorso alternativo per il file binario openssl da usare per le operazioni di crittografia.

**HttpProxy.Host, HttpProxy.Port**  
Tipo: String  
Predefinito: nessuno

Se impostato, l'agente userà il server proxy per accedere a Internet. 

**AutoUpdate.Enabled** Tipo: Boolean Impostazione predefinita: y

Consente di abilitare o disabilitare l'aggiornamento automatico per l'elaborazione dello stato degli obiettivi. L'impostazione predefinita corrisponde all'abilitazione della funzionalità.



## <a name="ubuntu-cloud-images"></a>Immagini di Ubuntu Cloud
Si noti che le immagini di Ubuntu Cloud utilizzano [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) per eseguire molte attività di configurazione che verrebbero altrimenti gestite dall’agente Linux di Azure.  Tenere presenti le differenze seguenti:

* **Provisioning.Enabled** è "n" nelle immagini di Ubuntu Cloud che utilizzano cloud-init per eseguire attività di provisioning.
* I seguenti parametri di configurazione non hanno alcun effetto sulle immagini di Ubuntu Cloud che utilizzano cloud-init per gestire disco risorse e spazio di scambio:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**
* Vedere le risorse seguenti per configurare il punto di montaggio del disco di risorsa e scambiare spazio nelle immagini di Ubuntu Cloud durante il provisioning:
  
  * [Ubuntu Wiki: Configurare partizioni di scambio](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Inserimento di dati personalizzati in una macchina virtuale di Azure](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

