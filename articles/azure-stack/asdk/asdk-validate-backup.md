---
title: Convalidare un backup di Azure Stack tramite il ASDK | Microsoft Docs
description: Come convalidare un backup di sistemi integrati di Azure Stack tramite il ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 12d5c2448bc6a5241f6aa4e283acdf9c5a0f602a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265652"
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



### <a name="cloud-recovery-prerequisites"></a>Prerequisiti di ripristino di cloud
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

