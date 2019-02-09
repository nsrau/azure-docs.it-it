---
title: Convalidare un backup di Azure Stack tramite il ASDK | Microsoft Docs
description: Come convalidare un backup di sistemi integrati di Azure Stack tramite il ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 02ecb3cdec9ddb07bf48dfe77d1ed5fbf07975e0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965325"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Usare il ASDK per convalidare un backup di Azure Stack
Dopo la distribuzione di Azure Stack e il provisioning di risorse utente, ad esempio offerte, piani, le quote e le sottoscrizioni, è consigliabile [abilitare il backup di Azure Stack dell'infrastruttura](../azure-stack-backup-enable-backup-console.md). Pianificazione e l'esecuzione dei backup regolari infrastruttura garantisce che i dati di gestione dell'infrastruttura non vengono persi se è presente un grave errore hardware o servizio.

> [!TIP]
> È consigliabile che si [eseguire un backup su richiesta](../azure-stack-backup-back-up-azure-stack.md) prima di iniziare questa procedura per verificare di disporre di una copia dei dati dell'infrastruttura più recenti disponibili. Assicurarsi di acquisire l'ID di backup dopo il completamento del backup. Questo ID sarà necessario durante il ripristino di cloud. 

I backup di Azure Stack dell'infrastruttura contengono dati importanti sul cloud che può essere ripristinato durante la ridistribuzione di Azure Stack. È possibile usare il ASDK per convalidare i backup senza conseguenze per il cloud di produzione. 

Convalida i backup in ASDK è supportata per gli scenari seguenti:

|Scenario|Scopo|
|-----|-----|
|Convalidare i backup di infrastruttura da una soluzione integrata.|Convalida di durata brevi che i dati nel backup sono validi.|
|Descrive il flusso di lavoro di ripristino end-to-end.|Usare ASDK per convalidare l'intero backup e ripristinare l'esperienza.|
|     |     |

Lo scenario seguente **non è** supportate durante la convalida di backup nel ASDK:

|Scenario|Scopo|
|-----|-----|
|Compilazione ASDK per compilare il backup e ripristino.|Ripristinare i dati di backup da una versione precedente del ASDK a una versione più recente.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Distribuzione di ripristino di cloud
È possibile convalidare i backup Infrastructure dalla distribuzione di sistemi integrati eseguendo una distribuzione di ripristino di cloud del ASDK. In questo tipo di distribuzione, i dati del servizio specifico viene ripristinati da un backup dopo il ASDK è installato nel computer host.



### <a name="prereqs"></a>Prerequisiti di ripristino di cloud
Prima di iniziare una distribuzione di ripristino di cloud del ASDK, assicurarsi di avere le informazioni seguenti:

|Prerequisito|DESCRIZIONE|
|-----|-----|
|Percorso di condivisione di backup.|Il percorso di condivisione file UNC del backup più recente di Azure Stack che verrà usato per recuperare informazioni sull'infrastruttura di Azure Stack. Questa condivisione locale verrà creata durante il processo di distribuzione cloud di ripristino.|
|Chiave di crittografia dei backup.|La chiave di crittografia che è stata usata per pianificare il backup dell'infrastruttura per l'esecuzione usando il portale di amministrazione di Azure Stack.|
|ID di backup da ripristinare.|L'ID di backup, nel formato alfanumerico "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", che identifica il backup da ripristinare durante il ripristino di cloud.|
|IP del server di tempo.|Un indirizzo IP server ora valida, ad esempio 132.163.97.2, è necessario per la distribuzione di Azure Stack.|
|Password certificato esterno.|La password per il certificato esterno usato da Azure Stack. Il backup della CA contiene certificati esterni che devono essere ripristinati con la password.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Preparare il computer host 
Come in una distribuzione ASDK normale, è necessario preparare l'ambiente del sistema host ASDK per l'installazione. Quando il computer host kit di sviluppo è stato preparato, eseguirà l'avvio dal disco rigido CloudBuilder.vhdx macchina virtuale per iniziare la distribuzione ASDK.

Nel computer host ASDK, scaricare un nuovo cloudbuilder.vhdx corrispondente alla stessa versione di Azure Stack che è stato eseguito il backup e seguire le istruzioni per [Prepara il computer host ASDK](asdk-prepare-host.md).

Dopo che il server host viene riavviato dal cloudbuilder.vhdx, è necessario creare una condivisione file e copiare i dati di backup in. La condivisione file deve essere accessibile per l'account che esegue l'installazione; Amministratore in questi comandi di PowerShell di esempio: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Successivamente, copiare i file di backup di Azure Stack più recenti nella condivisione appena creato. La struttura di cartelle all'interno della condivisione deve essere: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Distribuire il ASDK in modalità di ripristino di cloud
Il **InstallAzureStackPOC.ps1** script viene usato per avviare il ripristino del cloud. 

> [!IMPORTANT]
> Installazione ASDK supporta esattamente una scheda di interfaccia di rete (NIC) per la rete. Se si dispone di più schede di rete, assicurarsi che solo uno è attivato (e tutti gli altri sono disabilitati) prima di eseguire lo script di distribuzione.

### <a name="use-the-installer-to-deploy-the-asdk-in-recovery-mode"></a>Usare il programma di installazione per distribuire il ASDK in modalità di ripristino
I passaggi descritti in questa sezione mostrano come distribuire il ASDK usando un'interfaccia utente grafica (GUI) fornita scaricando ed eseguendo il **asdk installer.ps1** script di PowerShell.

> [!NOTE]
> L'interfaccia utente di programma di installazione per Azure Stack Development Kit è un script open source basato su WCF e PowerShell.

1. Dopo che il computer host viene avviato correttamente nell'immagine CloudBuilder.vhdx, accedere con le credenziali di amministratore specificato quando si [preparato il computer host kit di sviluppo](asdk-prepare-host.md) per l'installazione ASDK. Deve trattarsi di quello utilizzato per le credenziali di amministratore locale host kit di sviluppo.
2. Aprire una console di PowerShell con privilegi elevata ed eseguire la  **&lt;lettera unità > \AzureStack_Installer\asdk-installer.ps1** script di PowerShell. Lo script potrebbe essere in un'unità diversa da quella C:\ nell'immagine CloudBuilder.vhdx. Fare clic su **Ripristina**.

    ![Script di installazione di ASDK](media/asdk-validate-backup/1.PNG) 

3. Immettere le informazioni di directory di Azure AD (facoltative) e la password di amministratore locale per il computer host ASDK nella pagina delle credenziali e provider di identità. Fare clic su **Avanti**.

    ![Pagina credenziali e identità](media/asdk-validate-backup/2.PNG) 

4. Selezionare la scheda di rete da utilizzare per il computer host ASDK e fare clic su **successivo**. Tutte le altre interfacce di rete verranno disabilitate durante l'installazione ASDK. 

    ![Scheda di rete](media/asdk-validate-backup/3.PNG) 

5. Nella pagina di configurazione di rete, specificare server ora valido e gli indirizzi IP di server d'inoltro DNS. Fare clic su **Avanti**.

    ![Pagina Configurazione di rete](media/asdk-validate-backup/4.PNG) 

6. Proprietà delle schede di interfaccia di rete sono stati verificati, fare clic su **successivo**. 

    ![Verifica le impostazioni della rete](media/asdk-validate-backup/5.PNG) 

7. Fornire le informazioni necessarie, descritte nella sezione precedente [sezione Prerequisiti](#prereqs) nella pagina delle impostazioni di Backup e il nome utente e password da utilizzare per accedere alla condivisione. Fare clic su **successivo**: 

   ![Pagina Impostazioni di backup](media/asdk-validate-backup/6.PNG) 

8. Esaminare lo script di distribuzione da utilizzare per la distribuzione di ASDK nella pagina di riepilogo. Fare clic su **Distribuisci** per iniziare la distribuzione. 

    ![Pagina Riepilogo](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>Usare PowerShell per distribuire il ASDK in modalità di ripristino
Modificare i seguenti comandi di PowerShell per l'ambiente ed eseguire in modo da distribuire ASDK in modalità di ripristino di cloud:

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>Ripristinare i dati di infrastruttura da backup
Dopo una distribuzione di ripristino di cloud di successo, è necessario completare il ripristino tramite il **Restore-AzureStack** cmdlet. 

Dopo l'accesso come l'operatore di Azure Stack [installare PowerShell per Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) e quindi sostituendo l'ID di Backup per il `Name` parametro, eseguire il comando seguente:

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
Attendere 60 minuti dopo la chiamata a questo cmdlet per avviare la verifica dei dati di backup nel cloud di ripristino ASDK.

## <a name="next-steps"></a>Passaggi successivi
[Registrare Azure Stack](asdk-register.md)

