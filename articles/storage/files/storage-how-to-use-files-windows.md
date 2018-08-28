---
title: Usare una condivisione file di Azure con Windows | Microsoft Docs
description: Informazioni su come usare una condivisione file di Azure con Windows e Windows Server.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 96ad812aff8f6ea4f47035188940730e5dc2992c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2018
ms.locfileid: "41918075"
---
# <a name="use-an-azure-file-share-with-windows"></a>Usare una condivisione file di Azure con Windows
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere usate facilmente in Windows e Windows Server. Questo articolo illustra le considerazioni relative all'uso di una condivisione file di Azure con Windows e Windows Server.

Per usare una condivisione file di Azure al di fuori dell'area di Azure in cui è ospitata, ad esempio in locale o in un'altra area di Azure, il sistema operativo deve supportare SMB 3.0. 

È possibile usare le condivisioni file di Azure in un'installazione Windows in esecuzione in una VM di Azure o in locale. La tabella seguente illustra le versioni del sistema operativo che supportano l'accesso a condivisioni file in un determinato ambiente:

| Versione di Windows        | Versione di SMB | Montabile in una VM di Azure | Montabile in locale |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019 (anteprima)<sup>1</sup> | SMB 3.0 | Yes | Yes |
| Windows 10<sup>2</sup> | SMB 3.0 | Yes | Yes |
| Canale semestrale di Windows Server<sup>3</sup> | SMB 3.0 | Yes | Yes |
| Windows Server 2016    | SMB 3.0     | Yes                   | Yes                  |
| Windows 8.1            | SMB 3.0     | Yes                   | Yes                  |
| Windows Server 2012 R2 | SMB 3.0     | Yes                   | Yes                  |
| Windows Server 2012    | SMB 3.0     | Yes                   | Yes                  |
| Windows 7              | SMB 2.1     | Yes                   | No                    |
| Windows Server 2008 R2 | SMB 2.1     | Yes                   | No                    |

<sup>1</sup>Windows Server 2019 è disponibile in anteprima tramite il [programma Windows Server Insider](https://insider.windows.com/for-business-getting-started-server/). Anche se Windows Server 2019 non è ancora supportato per l'uso in produzione, comunicare a Microsoft se si riscontrano problemi nella connessione a condivisioni file di Azure non inclusi nella [guida alla risoluzione dei problemi per Windows](storage-troubleshoot-windows-file-connection-problems.md).  
<sup>2</sup>Windows 10 versioni 1507, 1607, 1703, 1709 e 1803.  
<sup>3</sup>Windows Server versioni 1709 e 1803.

> [!Note]  
> È sempre consigliabile seguire l'articolo della KB più recente per la propria versione di Windows.

## <a name="prerequisites"></a>Prerequisiti 
* **Nome dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessario il nome dell'account di archiviazione.

* **Chiave dell'account di archiviazione**: per montare una condivisione file di Azure, sarà necessaria la chiave dell'account primaria (o secondaria). Le chiavi di firma di accesso condiviso non sono attualmente supportate per il montaggio.

* **Assicurarsi che la porta 445 sia aperta**: il protocollo SMB richiede che la porta TCP 445 sia aperta. Se la porta 445 è bloccata, le connessioni non riusciranno. È possibile verificare se la porta 445 è bloccata dal firewall con il cmdlet `Test-NetConnection`. Ricordarsi di sostituire `your-storage-account-name` con il nome pertinente per il proprio account di archiviazione.

    ```PowerShell
    Test-NetConnection -ComputerName <your-storage-account-name>.core.windows.net -Port 445
    
    ```

    Se la connessione ha avuto esito positivo, verrà visualizzato l'output seguente:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > Il comando precedente restituisce l'indirizzo IP corrente dell'account di archiviazione. Non è garantito che tale indirizzo IP rimanga invariato. Potrebbe cambiare in qualsiasi momento. Non includere l'indirizzo IP come hardcoded in script o nella configurazione di un firewall. 

## <a name="using-an-azure-file-share-with-windows"></a>Uso di una condivisione file di Azure con Windows
Per usare una condivisione file di Azure con Windows, è necessario montare la condivisione, ossia assegnarle una lettera di unità o il percorso di un punto di montaggio, oppure accedervi tramite il relativo [percorso UNC](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx). 

A differenza di altre condivisioni SMB con cui si potrebbe aver interagito, come quelle ospitate in Windows Server, in un server Linux Samba o in un dispositivo NAS, le condivisioni file di Azure attualmente non supportano l'autenticazione Kerberos con l'identità di Active Directory (AD) o Azure Active Directory (AAD), nonostante Microsoft stia [lavorando per offrire questa funzionalità](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles). È necessario invece accedere alla condivisione file di Azure con la chiave dell'account di archiviazione contenente la condivisione. La chiave di un account di archiviazione è una chiave amministratore per l'account di archiviazione, con autorizzazioni di amministratore per tutti i file e le cartelle nella condivisione file a cui si accede, e per tutte le condivisioni file e le altre risorse di archiviazione (BLOB, code, tabelle e così via) contenute nell'account di archiviazione. Se questo non è sufficiente per lo specifico carico di lavoro, [Sincronizzazione file di Azure](storage-files-planning.md#data-access-method) può risolvere nel frattempo l'assenza dell'autenticazione Kerberos e del supporto degli elenchi di controllo di accesso finché tali funzionalità basate su AAD non saranno pubblicamente disponibili.

Un modello comune per il trasferimento in modalità lift-and-shift in Azure di applicazioni line-of-business che prevedono una condivisione file SMB consiste nell'usare una condivisione file di Azure come alternativa per eseguire un file server Windows dedicato in una VM di Azure. Una considerazione importante per eseguire correttamente la migrazione di un'applicazione line-of-business in modo da usare una condivisione file di Azure è che molte applicazioni line-of-business vengono eseguite nel contesto di un account di servizio dedicato con autorizzazioni di sistema limitate anziché con l'account amministrativo della VM. Di conseguenza, è necessario assicurarsi di eseguire il montaggio/salvare le credenziali per la condivisione file di Azure dal contesto dell'account di servizio anziché dall'account amministrativo.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Salvataggio permanente delle credenziali della condivisione file di Azure in Windows  
L'utilità [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) consente di archiviare le credenziali dell'account di archiviazione in Windows. In questo modo, quando si prova ad accedere a una condivisione file di Azure tramite il relativo percorso UNC o a montare la condivisione file di Azure, non è necessario specificare le credenziali. Per salvare le credenziali dell'account di archiviazione, eseguire i comandi di PowerShell seguenti sostituendo `<your-storage-account-name>` e `<your-resoure-group-name>` quando opportuno.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
# already logged in.
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as soverign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command "cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)"
```

È possibile verificare che l'utilità cmdkey abbia archiviato le credenziali per l'account di archiviazione usando il parametro list:

```PowerShell
cmdkey /list
```

Se le credenziali per la condivisione file di Azure sono state archiviate correttamente, l'output previsto è il seguente (l'elenco potrebbe includere altre chiavi archiviate):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Dovrebbe ora essere possibile montare la condivisione o accedervi senza specificare altre credenziali.

#### <a name="advanced-cmdkey-scenarios"></a>Scenari avanzati con cmdkey
Esistono altri due scenari da prendere in considerazione con cmdkey: l'archiviazione di credenziali per un altro utente nel computer, ad esempio un account di servizio, e l'archiviazione di credenziali in un computer remoto con la comunicazione remota di PowerShell.

Archiviare le credenziali per un altro utente nel computer è molto facile. Dopo aver eseguito l'accesso all'account, è sufficiente eseguire questo comando di PowerShell:

```PowerShell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Verrà aperta una nuova finestra di PowerShell nel contesto utente dell'account di servizio o dell'account utente. È quindi possibile usare l'utilità cmdkey come descritto [sopra](#persisting-azure-file-share-credentials-in-windows).

Archiviare le credenziali in un computer remoto con la comunicazione remota di PowerShell non è invece possibile, perché cmdkey non consente l'accesso all'archivio credenziali, anche per aggiunte, se l'utente ha eseguito l'accesso tramite la comunicazione remota di PowerShell. È consigliabile eseguire l'accesso al computer con [Desktop remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows).

### <a name="mount-the-azure-file-share-with-powershell"></a>Montare la condivisione file di Azure con PowerShell
Per montare la condivisione file di Azure, eseguire i comandi seguenti da una sessione normale, ossia non con privilegi elevati, di PowerShell. Ricordarsi di sostituire `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` e `<desired-drive-letter>` con le informazioni appropriate.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
# already logged in.
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as soverign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> L'uso dell'opzione `-Persist` nel cmdlet `New-PSDrive` consentirà di rimontare la condivisione file all'avvio solo in caso di salvataggio delle credenziali. È possibile salvare le credenziali con cmdkey come [descritto in precedenza](#persisting-azure-file-share-credentials-in-windows). 

Se si vuole, è possibile smontare la condivisione file di Azure con il cmdlet di PowerShell seguente.

```PowerShell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Montare la condivisione file di Azure con Esplora file
> [!Note]  
> Tenere presente che le istruzioni seguenti si basano su Windows 10 e potrebbero essere leggermente diverse per le versioni precedenti. 

1. Aprire Esplora file. Per eseguire questa operazione, aprire il menu Start o premere i tasti di scelta rapida Win+E.

2. Passare all'elemento **Questo PC** sul lato sinistro della finestra. Verranno cambiati i menu disponibili sulla barra multifunzione. Scegliere **Connetti unità di rete** dal menu Computer.
    
    ![Screenshot del menu a discesa "Connetti unità di rete"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Copiare il percorso UNC dal riquadro **Connetti** nel portale di Azure. 

    ![Il percorso UNC dal riquadro Connetti di File di Azure](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Selezionare la lettera di unità e immettere il percorso UNC. 
    
    ![Screenshot della finestra di dialogo "Connetti unità di rete"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Usare il nome dell'account di archiviazione preceduto da `AZURE\` come nome utente e una chiave dell'account di archiviazione come password.
    
    ![Screenshot della finestra di dialogo delle credenziali di rete](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Usare la condivisione file di Azure in base alle esigenze.
    
    ![Condivisione file di Azure montata](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Quando si è pronti per smontare la condivisione file di Azure, fare clic con il pulsante destro del mouse sulla voce relativa alla condivisione in **Percorsi di rete** in Esplora file e scegliere **Disconnetti**.

### <a name="accessing-share-snapshots-from-windows"></a>Accedere agli snapshot di condivisione da Windows
Se è stato creato uno snapshot di condivisione manualmente o automaticamente con uno script o un servizio come Backup di Azure, è possibile visualizzare le versioni precedenti di una condivisione, di una directory o di un determinato file dalla condivisione file in Windows. È possibile creare uno snapshot di condivisione con il [portale di Azure](storage-how-to-use-files-portal.md), con [Azure PowerShell](storage-how-to-use-files-powershell.md) o con l'[interfaccia della riga di comando di Azure](storage-how-to-use-files-cli.md).

#### <a name="list-previous-versions"></a>Elencare le versioni precedenti
Passare all'elemento o all'elemento padre da ripristinare. Fare doppio clic per passare alla directory desiderata. Fare clic con il pulsante destro del mouse e scegliere **Proprietà** dal menu.

![Fare clic con il pulsante destro del mouse sul menu per scegliere una directory specifica](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Selezionare **Versioni precedenti** per visualizzare l'elenco di snapshot di condivisione per questa directory. Il caricamento dell'elenco potrebbe richiedere alcuni secondi in base alla velocità della rete e al numero di snapshot di condivisione presenti nella directory.

![Scheda Versioni precedenti](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

È possibile selezionare **Apri** per aprire un determinato snapshot. 

![Snapshot aperto](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Eseguire il ripristino da una versione precedente
Selezionare **Ripristina** per copiare il contenuto dell'intera directory in modo ricorsivo al momento della creazione dello snapshot di condivisione nel percorso originale.
 ![Pulsante Ripristina nel messaggio di avviso](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Protezione di Windows/Windows Server
Per montare una condivisione file di Azure in Windows, è necessario che la porta 445 sia accessibile. Molte organizzazioni bloccano la porta 445 a causa dei rischi per la sicurezza associati a SMB 1. SMB 1, denominato anche CIFS (Common Internet File System), è un protocollo di file system legacy incluso in Windows e Windows Server. SMB 1 è un protocollo obsoleto, inefficiente e, soprattutto, non sicuro. La buona notizia è che File di Azure non supporta SMB 1 e tutte le versioni supportate di Windows e Windows Server consentono di rimuovere o disabilitare SMB 1. È sempre [consigliabile](https://aka.ms/stopusingsmb1) rimuovere o disabilitare il server e il client SMB 1 in Windows prima di usare condivisioni file di Azure nell'ambiente di produzione.

La tabella seguente offre informazioni dettagliate sullo stato di SMB 1 in ogni versione di Windows:

| Versione di Windows                           | Stato predefinito di SMB 1 | Metodo di disabilitazione/rimozione       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019 (anteprima)             | Disabled             | Rimuovere con funzionalità di Windows |
| Windows Server versione 1709 e successive            | Disabled             | Rimuovere con funzionalità di Windows |
| Windows 10 versione 1709 e successive                | Disabled             | Rimuovere con funzionalità di Windows |
| Windows Server 2016                       | Attivato              | Rimuovere con funzionalità di Windows |
| Windows 10 versioni 1507, 1607 e 1703 | Attivato              | Rimuovere con funzionalità di Windows |
| Windows Server 2012 R2                    | Attivato              | Rimuovere con funzionalità di Windows | 
| Windows 8.1                               | Attivato              | Rimuovere con funzionalità di Windows | 
| Windows Server 2012                       | Attivato              | Disabilitare con Registro di sistema       | 
| Windows Server 2008 R2                    | Attivato              | Disabilitare con Registro di sistema       |
| Windows 7                                 | Attivato              | Disabilitare con Registro di sistema       | 

### <a name="auditing-smb-1-usage"></a>Controllo dell'utilizzo di SMB 1
> Si applica a Windows Server 2019 (anteprima), canale semestrale di Windows Server (versioni 1709 e 1803), Windows Server 2016, Windows 10 (versioni 1507, 1607, 1703, 1709 e 1803), Windows Server 2012 R2 e Windows 8.1

Prima di rimuovere SMB 1 nell'ambiente, può essere opportuno controllare l'utilizzo di SMB 1 per verificare se la modifica causerà un'interruzione per eventuali client. Se vengono eseguite richieste per condivisioni SMB con SMB 1, nel log eventi verrà registrato un evento di controllo in `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Per abilitare il supporto del controllo in Windows Server 2012 R2 e Windows 8.1, installare almeno [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Per abilitare il controllo, eseguire questi cmdlet da una sessione di PowerShell con privilegi elevati:

```PowerShell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Rimozione di SMB 1 da Windows Server
> Si applica a Windows Server 2019 (anteprima), canale semestrale di Windows Server (versioni 1709 e 1803), Windows Server 2016 e Windows Server 2012 R2

Per rimuovere SMB 1 da un'istanza di Windows Server, eseguire questo cmdlet da una sessione di PowerShell con privilegi elevati:

```PowerShell
Remove-WindowsFeature -Name FS-SMB1
```

Per completare il processo di rimozione, riavviare il server. 

> [!Note]  
> A partire da Windows 10 e Windows Server versione 1709, SMB 1 non viene installato per impostazione predefinita e sono presenti funzionalità di Windows separate per il client SMB 1 e il server SMB 1. È sempre consigliabile lasciare sia il server SMB 1 (`FS-SMB1-SERVER`) che il client SMB 1 (`FS-SMB1-CLIENT`) non installati.

### <a name="removing-smb-1-from-windows-client"></a>Rimozione di SMB 1 dal client Windows
> Si applica a Windows 10 (versioni 1507, 1607, 1703, 1709 e 1803) e Windows 8.1

Per rimuovere SMB 1 dal client Windows, eseguire questo cmdlet da una sessione di PowerShell con privilegi elevati:

```PowerShell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Per completare il processo di rimozione, riavviare il PC.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Disabilitazione di SMB 1 in versioni legacy di Windows/Windows Server
> Si applica a Windows Server 2012, Windows Server 2008 R2 e Windows 7

SMB 1 non può essere completamente rimosso dalle versioni legacy di Windows/Windows Server, ma può essere disabilitato tramite il Registro di sistema. Per disabilitare SMB 1, creare una nuova chiave del Registro di sistema `SMB1` di tipo `DWORD` con valore `0` in `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Anche questa operazione può essere facilmente eseguita con questo cmdlet di PowerShell:

```PowerShell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Dopo aver creato questa chiave del Registro di sistema, è necessario riavviare il server per disabilitare SMB 1.

### <a name="smb-resources"></a>Risorse relative a SMB
- [Stop using SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/) (Interrompere l'uso di SMB 1)
- [SMB 1 Product Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/) (Prodotti che richiedono SMB 1)
- [Discover SMB 1 in your environment with DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/) (Individuare SMB 1 nell'ambiente con DSCEA)
- [Disabling SMB 1 through Group Policy](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/) (Disabilitazione di SMB 1 tramite Criteri di gruppo)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure, vedere i collegamenti seguenti:
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
* [DOMANDE FREQUENTI](../storage-files-faq.md)
* [Risoluzione dei problemi in Windows](storage-troubleshoot-windows-file-connection-problems.md)      
