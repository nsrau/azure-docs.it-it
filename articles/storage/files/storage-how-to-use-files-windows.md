---
title: Usare una condivisione file di Azure con Windows | Microsoft Docs
description: Informazioni su come usare le condivisioni file di Azure con Windows e Windows Server. Usare condivisioni file di Azure con SMB 3,0 nelle installazioni di Windows in esecuzione in locale o in macchine virtuali di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c8a1d1c0f8de742bdafa130cce6927a472efd8f7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329347"
---
# <a name="use-an-azure-file-share-with-windows"></a>Usare una condivisione file di Azure con Windows
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere usate facilmente in Windows e Windows Server. Questo articolo illustra le considerazioni relative all'uso di una condivisione file di Azure con Windows e Windows Server.

Per usare una condivisione file di Azure al di fuori dell'area di Azure in cui è ospitata, ad esempio in locale o in un'altra area di Azure, il sistema operativo deve supportare SMB 3.0. 

È possibile usare le condivisioni file di Azure in un'installazione Windows in esecuzione in una VM di Azure o in locale. La tabella seguente illustra le versioni del sistema operativo che supportano l'accesso a condivisioni file in un determinato ambiente:

| Versione di Windows        | Versione di SMB | Montabile in una VM di Azure | Montabile in locale |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Sì | Sì |
| Windows 10<sup>1</sup> | SMB 3.0 | Sì | Sì |
| Canale semestrale di Windows Server<sup>2</sup> | SMB 3.0 | Sì | Sì |
| Windows Server 2016 | SMB 3.0 | Sì | Sì |
| Windows 8.1 | SMB 3.0 | Sì | Sì |
| Windows Server 2012 R2 | SMB 3.0 | Sì | Sì |
| Windows Server 2012 | SMB 3.0 | Sì | Sì |
| Windows 7<sup>3</sup> | SMB 2.1 | Sì | No |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Sì | No |

<sup>1</sup> Windows 10, versioni 1507, 1607, 1803, 1809, 1903, 1909 e 2004.  
<sup>2</sup> Windows Server, versioni 1809, 1903, 1909, 2004.  
<sup>3</sup>Il normale supporto tecnico Microsoft per Windows 7 e Windows Server 2008 R2 è terminato. È possibile acquistare supporto tecnico aggiuntivo per gli aggiornamenti della sicurezza solo tramite il programma di [aggiornamenti della sicurezza estesa (ESU)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). È fortemente consigliabile eseguire la migrazione di questi sistemi operativi.

> [!Note]  
> È sempre consigliabile seguire l'articolo della KB più recente per la propria versione di Windows.

## <a name="prerequisites"></a>Prerequisiti 

Assicurarsi che la porta 445 sia aperta: il protocollo SMB richiede che la porta TCP 445 sia aperta. Se la porta 445 è bloccata, le connessioni non riusciranno. È possibile verificare se il firewall sta bloccando la porta 445 con il `Test-NetConnection` cmdlet. Per informazioni sui modi per aggirare una porta 445 bloccata, vedere la sezione [cause 1: porta 445 è bloccata](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) nella Guida alla risoluzione dei problemi di Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Uso di una condivisione file di Azure con Windows
Per usare una condivisione file di Azure con Windows, è necessario montare la condivisione, ossia assegnarle una lettera di unità o il percorso di un punto di montaggio, oppure accedervi tramite il relativo [percorso UNC](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx). 

Questo articolo usa la chiave dell'account di archiviazione per accedere alla condivisione file. La chiave di un account di archiviazione è una chiave amministratore per l'account di archiviazione, con autorizzazioni di amministratore per tutti i file e le cartelle nella condivisione file a cui si accede, e per tutte le condivisioni file e le altre risorse di archiviazione (BLOB, code, tabelle e così via) contenute nell'account di archiviazione. Se questa operazione non è sufficiente per il carico di lavoro, è possibile usare [Sincronizzazione file di Azure](storage-sync-files-planning.md) oppure è possibile usare l'[autenticazione basata su identità tramite SMB](storage-files-active-directory-overview.md).

Un modello comune per il trasferimento in modalità lift-and-shift in Azure di applicazioni line-of-business che prevedono una condivisione file SMB consiste nell'usare una condivisione file di Azure come alternativa per eseguire un file server Windows dedicato in una VM di Azure. Una considerazione importante per eseguire correttamente la migrazione di un'applicazione line-of-business in modo da usare una condivisione file di Azure è che molte applicazioni line-of-business vengono eseguite nel contesto di un account di servizio dedicato con autorizzazioni di sistema limitate anziché con l'account amministrativo della VM. Di conseguenza, è necessario assicurarsi di eseguire il montaggio/salvare le credenziali per la condivisione file di Azure dal contesto dell'account di servizio anziché dall'account amministrativo.

### <a name="mount-the-azure-file-share"></a>Montare la condivisione file di Azure

Il portale di Azure fornisce uno script che è possibile usare per montare la condivisione file direttamente in un host. Si consiglia di usare questo script specificato.

Per ottenere lo script seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare all'account di archiviazione che contiene la condivisione file che si vuole montare.
1. Selezionare **Condivisioni file**.
1. Selezionare la condivisione file che si desidera montare.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="esempio":::

1. Selezionare **Connetti**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Screenshot dell'icona Connetti per la condivisione file.":::

1. Selezionare la lettera di unità in cui montare la condivisione.
1. Copiare lo script specificato.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Testo di esempio":::

1. Incollare lo script in una shell nell'host in cui si vuole montare la condivisione file ed eseguirlo.

A questo punto è stata montata la condivisione file di Azure.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Montare la condivisione file di Azure con Esplora file
> [!Note]  
> Tenere presente che le istruzioni seguenti si basano su Windows 10 e potrebbero essere leggermente diverse per le versioni precedenti. 

1. Aprire Esplora file. Per eseguire questa operazione, aprire il menu Start o premere i tasti di scelta rapida Win+E.

1. Passare a **questo PC** sul lato sinistro della finestra. Verranno cambiati i menu disponibili sulla barra multifunzione. Scegliere **Connetti unità di rete** dal menu Computer.
    
    ![Screenshot del menu a discesa "Connetti unità di rete"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Selezionare la lettera di unità e immettere il percorso UNC. Il formato del percorso UNC è `\\<storageAccountName>.file.core.windows.net\<fileShareName>`. Ad esempio: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Screenshot della finestra di dialogo "Connetti unità di rete"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Usare il nome dell'account di archiviazione preceduto da `AZURE\` come nome utente e una chiave dell'account di archiviazione come password.
    
    ![Screenshot della finestra di dialogo delle credenziali di rete](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Usare la condivisione file di Azure in base alle esigenze.
    
    ![Condivisione file di Azure montata](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Quando si è pronti per smontare la condivisione file di Azure, fare clic con il pulsante destro del mouse sulla voce relativa alla condivisione in **Percorsi di rete** in Esplora file e scegliere **Disconnetti**.

### <a name="accessing-share-snapshots-from-windows"></a>Accedere agli snapshot di condivisione da Windows
Se è stato creato uno snapshot di condivisione manualmente o automaticamente con uno script o un servizio come Backup di Azure, è possibile visualizzare le versioni precedenti di una condivisione, di una directory o di un determinato file dalla condivisione file in Windows. È possibile creare uno snapshot di condivisione usando [Azure PowerShell](storage-how-to-use-files-powershell.md), l'interfaccia della riga di comando di [Azure](storage-how-to-use-files-cli.md)o l' [portale di Azure](storage-how-to-use-files-portal.md).

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
| Windows Server 2019                       | Disabled             | Rimuovere con funzionalità di Windows |
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
> Si applica a Windows Server 2019, canale semestrale di Windows Server (versioni 1709 e 1803), Windows Server 2016, Windows 10 (versioni 1507, 1607, 1703, 1709 e 1803), Windows Server 2012 R2 e Windows 8.1

Prima di rimuovere SMB 1 nell'ambiente, può essere opportuno controllare l'utilizzo di SMB 1 per verificare se la modifica causerà un'interruzione per eventuali client. Se vengono eseguite richieste per condivisioni SMB con SMB 1, nel log eventi verrà registrato un evento di controllo in `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Per abilitare il supporto del controllo in Windows Server 2012 R2 e Windows 8.1, installare almeno [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Per abilitare il controllo, eseguire questi cmdlet da una sessione di PowerShell con privilegi elevati:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Rimozione di SMB 1 da Windows Server
> Si applica a Windows Server 2019, canale semestrale di Windows Server (versioni 1709 e 1803), Windows Server 2016 e Windows Server 2012 R2

Per rimuovere SMB 1 da un'istanza di Windows Server, eseguire questo cmdlet da una sessione di PowerShell con privilegi elevati:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Per completare il processo di rimozione, riavviare il server. 

> [!Note]  
> A partire da Windows 10 e Windows Server versione 1709, SMB 1 non viene installato per impostazione predefinita e sono presenti funzionalità di Windows separate per il client SMB 1 e il server SMB 1. È sempre consigliabile lasciare sia il server SMB 1 (`FS-SMB1-SERVER`) che il client SMB 1 (`FS-SMB1-CLIENT`) non installati.

### <a name="removing-smb-1-from-windows-client"></a>Rimozione di SMB 1 dal client Windows
> Si applica a Windows 10 (versioni 1507, 1607, 1703, 1709 e 1803) e Windows 8.1

Per rimuovere SMB 1 dal client Windows, eseguire questo cmdlet da una sessione di PowerShell con privilegi elevati:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Per completare il processo di rimozione, riavviare il PC.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Disabilitazione di SMB 1 in versioni legacy di Windows/Windows Server
> Si applica a Windows Server 2012, Windows Server 2008 R2 e Windows 7

SMB 1 non può essere completamente rimosso dalle versioni legacy di Windows/Windows Server, ma può essere disabilitato tramite il Registro di sistema. Per disabilitare SMB 1, creare una nuova chiave del Registro di sistema `SMB1` di tipo `DWORD` con valore `0` in `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Anche questa operazione può essere facilmente eseguita con questo cmdlet di PowerShell:

```powershell
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
- [Domande frequenti](../storage-files-faq.md)
- [Risoluzione dei problemi in Windows](storage-troubleshoot-windows-file-connection-problems.md)      
