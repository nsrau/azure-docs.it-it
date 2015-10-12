<properties
	pageTitle="Come usare l'archiviazione file di Azure con Linux | Microsoft Azure"
        description="Creare una condivisione file nel cloud e montarla da una VM di Azure o da un'applicazione locale in esecuzione su Linux."
        services="storage"
        documentationCenter="na"
        authors="jutang"
        manager="jahogg"
        editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="09/28/2015"
      ms.author="jutang;tamram" />


# Come usare l'archiviazione file di Azure con Linux 

## Panoramica

L’archiviazione file di Azure offre condivisioni di file nel cloud utilizzando il protocollo standard SMB. L’archiviazione file è ora disponibile al pubblico e supporta sia SMB 3.0 che SMB 2.1.

È possibile creare condivisioni di file di Azure tramite il portale di anteprima di Azure, i cmdlet PowerShell di Archiviazione di Azure, le librerie client di Archiviazione di Azure o l'API REST di Archiviazione di Azure. Inoltre, poiché le condivisioni di file sono condivisioni SMB, è possibile accedervi tramite API del file system standard e familiari.

Le applicazioni in esecuzione in Azure possono montare facilmente le condivisioni di file dalle macchine virtuali di Azure. Con la versione più recente dell’archiviazione di file, è inoltre possibile montare una condivisione di file da un'applicazione locale che supporta SMB 3.0.

Si noti che poiché il client SMB Linux non supporta ancora la crittografia, il montaggio di una condivisione di file da Linux richiede ancora che il client si trovi nella stessa area di Azure della condivisione di file. Tuttavia, il supporto della crittografia per Linux fa paret del programma degli sviluppatori Linux responsabili della funzionalità di SMB. In futuro, le distribuzioni di Linux che supportano la crittografia saranno in grado di montare una condivisione di file di Azure da qualsiasi luogo.

## Distribuzione Linux da utilizzare ##

Quando si crea una macchina virtuale Linux in Azure, è possibile specificare un'immagine Linux che supporta SMB 2.1 o versione successiva dalla raccolta di immagini di Azure. Di seguito si trova un elenco di immagini Linux consigliate:

- Ubuntu Server 14.04	
- Ubuntu Server 15.04	
- CentOS 7.1	
- Open SUSE 13.2	
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Premium Image)

## Montare la condivisione file ##

Per montare la condivisione file da una macchina virtuale che esegue Linux, è necessario installare un client SMB/CIFS se la distribuzione in uso non dispone di un client integrato. Questo è il comando da Ubuntu per installare una scelta di cifs-utils:

    sudo apt-get install cifs-utils

Successivamente, è necessario creare un punto di montaggio (mkdir mymountpoint) e quindi inviare il comando di montaggio, che è simile al seguente:

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

È inoltre possibile aggiungere le impostazioni a /etc/fstab per montare la condivisione.

Si noti che qui 0777 rappresenta un codice di autorizzazione file/directory che concede autorizzazioni di esecuzione/lettura/scrittura a tutti gli utenti. È possibile sostituirlo con un altro codice di autorizzazione file seguendo il documento di autorizzazione dei file Linux.
 
Inoltre, per mantenere una condivisione di file montata dopo il riavvio, è possibile aggiungere un'impostazione come indicato di seguito a /etc/fstab:

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Per essere più specifici, ecco un esempio.

Se è stata creata una VM di Azure utilizzando l'immagine Linux di Ubuntu Server 15.04, che è disponibile in Azure Marketplace, è possibile montare il file come indicato di seguito:

    azureuser@azureconubuntu:~$ sudo apt-get install apt-file
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Se si utilizza CentOS 7.1, è possibile montare il file come indicato di seguito:

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Se si utilizza Open SUSE 13.2, è possibile montare il file come indicato di seguito:

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint


## Passaggi successivi

Vedere i collegamenti seguenti per ulteriori informazioni sull'archiviazione file di Azure.

### Articoli concettuali

- [Come usare l'archiviazione file di Azure con Windows](storage-dotnet-how-to-use-files.md)

### Strumenti di supporto per l'archiviazione file

- [Come usare AzCopy con Archiviazione di Microsoft Azure](storage-use-azcopy.md)
- [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](storage-azure-cli.md#create-and-manage-file-shares)

### Riferimento

- [Riferimento API REST del servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Post di BLOG

- [Archiviazione file di Azure è ora disponibile al pubblico](http://go.microsoft.com/fwlink/?LinkID=626728&clcid=0x409)
- [Analisi approfondita dell'archiviazione file di Azure](http://go.microsoft.com/fwlink/?LinkID=626729&clcid=0x409) 
- [Introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Mantenimento delle connessioni ai file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=Oct15_HO1-->