---
title: Abilitare il backup per Azure Stack dal portale di amministrazione | Microsoft Docs
description: Abilitare il servizio di infrastruttura di Backup tramite il portale di amministrazione in modo che Azure Stack può essere ripristinato se si verifica un errore.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 98f793b7d94cd554d426a0eec30d8bb4553d3d81
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105404"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Abilitare il backup per Azure Stack dal portale di amministrazione
Abilitare il servizio di infrastruttura di Backup tramite il portale di amministrazione in modo che Azure Stack può generare backup dell'infrastruttura. I partner hardware possono usare questi backup per ripristinare l'ambiente cloud di ripristino in caso di utilizzo [un errore irreversibile](./azure-stack-backup-recover-data.md). Lo scopo di ripristino di cloud è garantire che gli operatori e gli utenti possono accedere di nuovo il portale al termine del ripristino completo. Gli utenti avranno gli abbonamenti ripristinati tra cui le autorizzazioni di accesso basato sui ruoli e i ruoli, originali piani, offerte e definita in precedenza calcolo, archiviazione, le quote di rete e i segreti di Key Vault.

Tuttavia, il servizio di infrastruttura di Backup di non eseguire il backup delle macchine virtuali IaaS, configurazioni di rete e le risorse di archiviazione, ad esempio gli account di archiviazione BLOB, tabelle e così via, in modo che gli utenti che accedono al termine del ripristino di cloud non verranno visualizzato uno dei relativi già esistente risorse. Piattaforma come servizio (PaaS), risorse e i dati anche senza il backup dal servizio. 

Gli amministratori e gli utenti sono tenuti a backup e ripristino delle risorse IaaS e PaaS separatamente dai processi di backup dell'infrastruttura. Per informazioni sul backup delle risorse IaaS e PaaS, vedere i collegamenti seguenti:

- [Macchine virtuali](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [Servizio app](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Abilitare o configurare nuovamente il backup

1. Aprire il [portale di amministrazione di Azure Stack](azure-stack-manage-portals.md).
2. Selezionare **tutti i servizi**e quindi sotto il **amministrazione** categoria, selezionare **backup dell'infrastruttura**. Scegli **Configuration** nel **backup dell'infrastruttura** pannello.
3. Digitare il percorso per il **percorso di archiviazione di Backup**. Usare una stringa di Universal Naming Convention (UNC) per il percorso a una condivisione file ospitata su un dispositivo separato. Una stringa UNC specifica il percorso delle risorse, ad esempio i file condivisi o i dispositivi. Per il servizio, è possibile usare un indirizzo IP. Per garantire la disponibilità dei dati di backup dopo un'emergenza, il dispositivo deve trovarsi in una posizione separata.

    > [!Note]  
    > Se l'ambiente supporta la risoluzione dei nomi dalla rete dell'infrastruttura di Azure Stack per l'ambiente dell'organizzazione, è possibile usare un nome di dominio completo anziché l'indirizzo IP.

4. Tipo di **Username** usando il nome utente e il dominio con diritti di accesso sufficienti per leggere e scrivere file. Ad esempio: `Contoso\backupshareuser`.
5. Tipo di **Password** per l'utente.
6. Digitare di nuovo la password **Conferma Password**.
7. Il **frequenza nelle ore** determina ogni quanto vengono creati i backup. Il valore predefinito è 12. Utilità di pianificazione supporta un massimo di 12 e almeno 4. 
8. Il **periodo di conservazione in giorni** determina il numero di giorni di backup viene conservato in una posizione esterna. Il valore predefinito è 7. Utilità di pianificazione supporta un massimo di 14 e un minimo di 2. I backup precedenti al periodo di conservazione vengono eliminati automaticamente dalla posizione esterna.

    > [!Note]  
    > Se si desidera archiviare i backup precedenti al periodo di conservazione, assicurarsi di eseguire il backup di file prima che l'utilità di pianificazione consente di eliminare i backup. Se si riduce il periodo di conservazione dei backup (ad esempio, da 7 giorni per 5 giorni), l'utilità di pianificazione eliminerà tutti i backup meno recente il nuovo periodo di conservazione. Assicurarsi che siano ok con i backup eliminati prima di aggiornare questo valore. 

9. Fornire un certificato nella casella file CER di certificato nelle impostazioni di crittografia. I file di backup vengono crittografati usando questa chiave pubblica nel certificato. È necessario fornire un certificato che contiene solo la parte di chiave pubblica quando si configurano le impostazioni di backup. Dopo aver impostato questo certificato per la prima volta o ruotare il certificato in futuro, è possibile visualizzare solo l'identificazione personale del certificato. Non è possibile scaricare o visualizzare il file di certificato caricato. Per creare il file del certificato, eseguire il comando PowerShell seguente per creare un certificato autofirmato con le chiavi pubbliche e private ed esportare un certificato con solo la parte di chiave pubblica.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 e versioni successive**: Azure Stack accetta un certificato per crittografare i dati di backup dell'infrastruttura. Assicurarsi di archiviare il certificato con la chiave pubblica e privata in un luogo sicuro. Per motivi di sicurezza, non è consigliabile usare il certificato con le chiavi pubbliche e private per configurare le impostazioni di backup. Per altre informazioni su come gestire il ciclo di vita del certificato, vedere [consigliate per il servizio di Backup Infrastructure](azure-stack-backup-best-practices.md).
   > 
   > **1811 o versioni precedenti**: Azure Stack accetta una chiave simmetrica per crittografare i dati di backup dell'infrastruttura. Usare la [cmdlet New-AzsEncryptionKey64 per creare una chiave](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Dopo l'aggiornamento da 1811 a 1901, le impostazioni di backup verranno mantenute la chiave di crittografia. È consigliabile aggiornare le impostazioni di backup per utilizzare un certificato. Supporto di chiavi di crittografia è ora deprecato. È necessario almeno 3 versioni per aggiornare le impostazioni per usare un certificato. 

10. Selezionare **OK** per salvare le impostazioni di backup controller.

![Azure Stack - impostazioni Backup del controller](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Avviare il backup
Per avviare un backup, fare clic su **Esegui Backup ora** per avviare un backup su richiesta. Un backup su richiesta non modificherà il tempo per il successivo backup pianificato. Al termine dell'attività, è possibile confermare le impostazioni in **Essentials**:

![Azure Stack - backup su richiesta](media/azure-stack-backup/scheduled-backup.png)

È anche possibile eseguire il cmdlet di PowerShell **Start-AzsBackup** nel computer di amministrazione di Azure Stack. Per altre informazioni, vedere [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Abilitare o disabilitare i backup automatici
I backup vengono pianificati automaticamente quando si abilita il backup. È possibile archiviare il successivo file di backup pianificato **Essentials**. 

![Azure Stack - backup su richiesta](media/azure-stack-backup/on-demand-backup.png)

Se è necessario disabilitare i backup futuri pianificati, fare clic su **disabilitare backup automatico**. Disabilitare i backup automatici manterranno le impostazioni di backup configurate e manterranno la pianificazione del backup. Questa azione richiede semplicemente l'utilità di pianificazione per ignorare i backup futuri. 

![Azure Stack - disable pianificati backup](media/azure-stack-backup/disable-auto-backup.png)

Verificare che i backup futuri pianificati sono stati disabilitati in **Essentials**:

![Azure Stack - verificare che i backup sono stati disabilitati](media/azure-stack-backup/confirm-disable.png)

Fare clic su **abilitare i backup automatici** per informare l'utilità di pianificazione per avviare i backup successivi all'ora pianificata. 

![Azure Stack - enable pianificati backup](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Se è stato configurato prima di aggiornare a 1807 backup dell'infrastruttura, i backup automatici verranno disabilitati. In questo modo i backup avviati da Azure Stack non sono in conflitto con i backup avviati da una motore di pianificazione delle attività esterna. Quando si disabilita qualsiasi utilità di pianificazione esterna, fare clic su **abilitare i backup automatici**.

## <a name="update-backup-settings"></a>Aggiornare le impostazioni di backup
A partire da 1901, il supporto per chiave di crittografia è deprecata. Se si sta configurando i backup per la prima volta in 1901, è necessario usare un certificato. Azure Stack supporta la chiave di crittografia solo se la chiave viene configurata prima dell'aggiornamento al 1901. Modalità di compatibilità con le versioni precedenti continueranno per tre versioni. Successivamente, le chiavi di crittografia non saranno più supportate. 

### <a name="default-mode"></a>Modalità predefinita
Nelle impostazioni di crittografia, se si sta configurando backup dell'infrastruttura per la prima volta dopo l'installazione o l'aggiornamento a 1901, è necessario configurare il backup con un certificato. Usando una chiave di crittografia non è più supportata. 

Per aggiornare il certificato usato per crittografare i dati di backup, è possibile caricare una nuova. Area a esecuzione vincolata con la parte pubblica del file e fare clic su OK per salvare le impostazioni. 

I nuovi backup inizieranno a usare la chiave pubblica del nuovo certificato. Non ha alcun impatto per tutti i backup esistenti creati con il certificato precedente. Assicurarsi di mantenere il certificato precedente tutto in una posizione sicura nel caso in cui è necessario per il ripristino di cloud.

![Azure Stack - identificazione personale del certificato di visualizzazione](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Con le versioni precedenti la modalità di compatibilità
Se è stato configurato il backup prima dell'aggiornamento al 1901, le impostazioni vengono trasferite senza apportare alcuna modifica nel comportamento. In questo caso, chiave di crittografia è supportata per la compatibilità. È possibile aggiornare la chiave di crittografia o il passaggio per usare un certificato. È necessario almeno tre versioni per continuare ad aggiornare la chiave di crittografia. Utilizzare questa volta per la transizione a un certificato. Per creare una nuova crittografia chiave, usare il [cmdlet New-AzsEncryptionKeyBase64](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Azure Stack - utilizzo chiave di crittografia in modalità di compatibilità con le versioni precedenti](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> L'aggiornamento dalla chiave di crittografia al certificato è un'operazione unidirezionale. Dopo aver apportato questa modifica, è possibile passare nuovamente alla chiave di crittografia. Tutti i backup esistenti rimarranno crittografati con la chiave di crittografia precedenti. 

![Azure Stack - certificato di crittografia di utilizzo in modalità di compatibilità con le versioni precedenti](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire un backup. Vedere [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Informazioni su come verificare che sia stato eseguito il backup. Vedere [conferma backup completato nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md)
