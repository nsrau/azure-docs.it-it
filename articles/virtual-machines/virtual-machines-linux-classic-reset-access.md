<properties
        pageTitle="Reimpostare la password e la chiave SSH di VM Linux dall'interfaccia della riga di comando | Microsoft Azure"
        description="Come usare l'estensione VMAccess dall'interfaccia della riga di comando di Azure per reimpostare la password o la chiave SSH di una VM Linux, correggere la configurazione SSH e verificare la coerenza dei dischi"
        services="virtual-machines-linux"
        documentationCenter=""
        authors="cynthn"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines-linux"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# Come reimpostare la password o la chiave SSH di una VM Linux, correggere la configurazione SSH e verificare la coerenza dei dischi che utilizzano l'estensione VMAccess


Se non è possibile connettersi a una macchina virtuale Linux su Azure perché si è dimenticata la password o una chiave SSH (Secure Shell) non è valida o per un problema di configurazione di SSH, usare l'estensione VMAccessForLinux con l'interfaccia della riga di comando di Azure per reimpostare la password o la chiave SSH, correggere la configurazione SSH e verificare la coerenza del disco.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Con l'interfaccia della riga di comando di Azure, per accedere ai comandi si usa il comando **azure vm extension set** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi). Per informazioni dettagliate sull'utilizzo dell'estensione, eseguire **azure help vm extension set**.

Con l’interfaccia della riga di comando di Azure è possibile eseguire le attività seguenti:

+ [Reimpostare la password](#pwresetcli)
+ [Reimpostare la chiave SSH](#sshkeyresetcli)
+ [Reimpostare la password e la chiave SSH](#resetbothcli)
+ [Creare un nuovo account utente sudo](#createnewsudocli)
+ [Reimpostare la configurazione SSH](#sshconfigresetcli)
+ [Eliminare un utente](#deletecli)
+ [Visualizzare lo stato dell'estensione VMAccess](#statuscli)
+ [Verificare la coerenza dei dischi aggiunti](#checkdisk)
+ [Ripristinare i dischi aggiunti nella VM Linux](#repairdisk)


## Prerequisiti

Sarà necessario eseguire le operazioni seguenti:

- Per usare le risorse di Azure associate all'account, è necessario [installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e [connettersi alla sottoscrizione](../xplat-cli-connect.md).
- Impostare la modalità corretta per il modello di distribuzione classico digitando quanto segue al prompt dei comandi:
        
        azure config mode asm
        
- Procurarsi una nuova password o un set di chiavi SSH, se si desidera reimpostare l'una o l'altro. Queste non saranno necessarie se si vuole reimpostare la configurazione di SSH.


## <a name="pwresetcli"></a>Reimpostare la password

1. Creare un file denominato PrivateConf.json con queste righe. Sostituire le parentesi e i valori & #60;segnaposto& #62; con le informazioni personalizzate.

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in &#60;nome-vm&#62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>Reimpostare la chiave SSH

1. Creare un file denominato PrivateConf.json con questo contenuto. Sostituire le parentesi e i valori & #60;segnaposto& #62; con le informazioni personalizzate.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in &#60;nome-vm&#62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Reimpostare sia la password sia la chiave SSH

1. Creare un file denominato PrivateConf.json con questo contenuto. Sostituire le parentesi e i valori & #60;segnaposto& #62; con le informazioni personalizzate.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in &#60;nome-vm&#62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>Creare un nuovo account utente sudo

Se si dimentica il nome utente, è possibile usare VMAccess per crearne uno nuovo con privilegi sudo. In questo caso, il nome utente e la password esistenti non verranno modificati.

Per creare un nuovo utente sudo con accesso tramite password, usare lo script in [Reimpostare la password](#pwresetcli) e specificare il nuovo nome utente.

Per creare un nuovo utente sudo con accesso tramite chiave SSH, usare lo script in [Reimpostare la chiave SSH](#sshkeyresetcli) e specificare il nuovo nome utente.

È inoltre possibile usare [Reimpostare la password e la chiave SSH](#resetbothcli) per creare un nuovo utente con accesso tramite password e chiave SSH.

## <a name="sshconfigresetcli"></a>Reimpostare la configurazione SSH

Se la configurazione SSH è in uno stato indesiderato, si potrebbe perdere anche l'accesso alla macchina virtuale. È possibile usare l'estensione VMAccess per reimpostare la configurazione allo stato predefinito. A tale scopo, è sufficiente impostare la chiave "reset\_ssh" su "True". L'estensione riavvia il server SSH, apre la porta SSH nella VM e ripristina la configurazione SSH predefinita. L'account utente (nome, password o chiavi SSH) non verrà modificato.

> [AZURE.NOTE] Il file di configurazione SSH che viene reimpostato si trova in /etc/ssh/sshd\_config.

1. Creare un file denominato PrivateConf.json con questo contenuto.

        {
        "reset_ssh":"True"
        }

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in &#60;nome-vm&#62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>Eliminare un utente

Se si desidera eliminare un account utente senza accedere alla macchina virtuale direttamente, è possibile usare questo script.

1. Creare un file denominato PrivateConf json con questo contenuto, sostituendo il nome utente da rimuovere in &#60;usernametoremove&#62;. 

        {
        "remove_user":"<usernametoremove>"
        }

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in &#60;nome-vm&#62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>Visualizzare lo stato dell'estensione VMAccess

Per visualizzare lo stato dell'estensione VMAccess, eseguire questo comando.

        azure vm extension get

## <a name='checkdisk'<</a>Verificare la coerenza dei dischi aggiunti

Per eseguire fsck su tutti i dischi nella macchina virtuale Linux, è necessario eseguire le operazioni seguenti:

1. Creare un file denominato PublicConf.json con questo contenuto. Il controllo del disco accetta un valore booleano che indica se controllare o meno i dischi collegati alla macchina virtuale. 

        {   
        "check_disk": "true"
        }

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in &#60;nome-vm&#62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>Riparare i dischi 

Per ripristinare i dischi che presentano problemi di montaggio o errori di configurazione di montaggio, usare l'estensione VMAccess per reimpostare la configurazione di montaggio nella macchina virtuale Linux. Sostituendo il nome del disco in & #60; disco& #62;.

1. Creare un file denominato PublicConf.json con questo contenuto. 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in &#60;nome-vm&#62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## Passaggi successivi

* Se per reimpostare la password o la chiave SSH, correggere la configurazione SSH e verificare la coerenza dei dischi si vogliono usare cmdlet Azure PowerShell o modelli di Azure Resource Manager, vedere la [documentazione dell'estensione VMAccess in GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Per reimpostare la password o la chiave SSH di una VM Linux distribuita con il modello di distribuzione classica è anche possibile usare il [portale di Azure](https://portal.azure.com). Non è attualmente possibile usare il portale per eseguire queste operazioni per una VM Linux distribuita con il modello di distribuzione Resource Manager.

* Per altre informazioni sull'uso di estensioni VM per macchine virtuali di Azure vedere [Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](virtual-machines-linux-extensions-features.md).

<!---HONumber=AcomDC_0622_2016-->