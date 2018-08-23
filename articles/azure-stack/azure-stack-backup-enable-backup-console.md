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
ms.date: 08/16/2018
ms.author: jeffgilb
ms.openlocfilehash: 6231ee760902618afedf64443690be0b02c4d0eb
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139450"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Abilitare il backup per Azure Stack dal portale di amministrazione
Abilitare il servizio di infrastruttura di Backup tramite il portale di amministrazione in modo che Azure Stack può generare backup. È possibile usare questi backup per ripristinare l'ambiente cloud di ripristino in caso di utilizzo [un errore irreversibile](.\azure-stack-backup-recover-data.md). Lo scopo di ripristino di cloud è garantire che gli operatori e gli utenti possono accedere di nuovo il portale al termine del ripristino completo. Gli utenti avranno gli abbonamenti ripristinati incluse le autorizzazioni di accesso basato sui ruoli e i ruoli, originali piani, offerte e calcolo definito in precedenza, archiviazione e le quote di rete.

Tuttavia, il servizio Backup di infrastruttura non esegue il backup delle macchine virtuali IaaS, le configurazioni di rete e le risorse di archiviazione, ad esempio gli account di archiviazione, BLOB, tabelle e così via, in modo che gli utenti che accedono dopo il ripristino di cloud completa non verrà visualizzato uno dei relativi già esistente risorse. Piattaforma come servizio (PaaS), risorse e i dati anche senza il backup dal servizio. 

Gli amministratori e gli utenti sono tenuti a backup e ripristino delle risorse IaaS e PaaS separatamente dai processi di backup dell'infrastruttura. Per informazioni sul backup delle risorse IaaS e PaaS, vedere i collegamenti seguenti:

- [Macchine virtuali](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [Servizio app](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Abilitare o configurare nuovamente il backup

1. Aprire il [portale di amministrazione di Azure Stack](azure-stack-manage-portals.md).
2. Selezionare **altri servizi** > **backup Infrastructure**. Scegli **Configuration** nel **backup dell'infrastruttura** pannello.
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

9. Fornire una chiave precondivisa nel **chiave di crittografia** casella. I file di backup vengono crittografati usando questa chiave. Assicurarsi di conservare la chiave in un luogo sicuro. Dopo aver impostato questa chiave per la prima volta o ruotare la chiave in futuro, non è possibile visualizzare la chiave da questa interfaccia. Per creare la chiave, eseguire i seguenti comandi di PowerShell per Azure Stack:
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. Selezionare **OK** per salvare le impostazioni di backup controller.

    ![Azure Stack - impostazioni Backup del controller](media\azure-stack-backup\backup-controller-settings.png)

## <a name="start-backup"></a>Avviare il backup
Per avviare un backup, fare clic su **Esegui Backup ora** per avviare un backup su richiesta. Un backup su richiesta non modificherà il tempo per il successivo backup pianificato. Al termine dell'attività, è possibile confermare le impostazioni in **Essentials**:

![Azure Stack - backup su richiesta](media\azure-stack-backup\scheduled-backup.png).

È anche possibile eseguire il cmdlet di PowerShell **Start-AzsBackup** nel computer di amministrazione di Azure Stack. Per altre informazioni, vedere [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Abilitare o disabilitare i backup automatici
I backup vengono pianificati automaticamente quando si abilita il backup. È possibile archiviare il successivo file di backup pianificato **Essentials**. 

![Azure Stack - backup su richiesta](media\azure-stack-backup\on-demand-backup.png)

Se è necessario disabilitare i backup futuri pianificati, fare clic su **disabilitare backup automatico**. Disabilitare i backup automatici manterranno le impostazioni di backup configurate e manterranno la pianificazione del backup. Questa azione richiede semplicemente l'utilità di pianificazione per ignorare i backup futuri. 

![Azure Stack - disable pianificati backup](media\azure-stack-backup\disable-auto-backup.png)

Verificare che i backup futuri pianificati sono stati disabilitati in **Essentials**:

![Azure Stack - verificare che i backup sono stati disabilitati](media\azure-stack-backup\confirm-disable.png)

Fare clic su **abilitare i backup automatici** per informare l'utilità di pianificazione per avviare i backup successivi all'ora pianificata. 

![Azure Stack - enable pianificati backup](media\azure-stack-backup\enable-auto-backup.png)


> [!Note]  
> Se è stato configurato prima di aggiornare a 1807 backup dell'infrastruttura, i backup automatici verranno disabilitati. In questo modo i backup avviati da Azure Stack non sono in conflitto con i backup avviati da una motore di pianificazione delle attività esterna. Quando si disabilita qualsiasi utilità di pianificazione esterna, fare clic su **abilitare i backup automatici**.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire un backup. Visualizzare [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Informazioni su come verificare che sia stato eseguito il backup. Visualizzare [conferma backup completato nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md).
