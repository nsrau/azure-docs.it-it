---
title: Abilitare il backup per lo Stack di Azure dal portale di amministrazione | Documenti Microsoft
description: Abilitare il servizio di Backup di infrastruttura tramite il portale di amministrazione in modo che Azure Stack può essere ripristinato se si è verificato un errore.
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
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: 0ef8247eba4605d3c8e5ef0992ce97bce989002e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Abilitare il backup per lo Stack di Azure dal portale di amministrazione
Abilitare il servizio di Backup di infrastruttura tramite il portale di amministrazione in modo che Azure Stack può generare backup. È possibile utilizzare queste copie di backup per ripristinare l'ambiente cloud di ripristino in caso di utilizzo [un errore irreversibile](.\azure-stack-backup-recover-data.md). Lo scopo di ripristino cloud è garantire che gli operatori e gli utenti possono accedere di nuovo il portale al termine del ripristino. Gli utenti avranno le relative sottoscrizioni ripristinati incluse le autorizzazioni di accesso basata su ruoli e ruoli, piani originali, offerte e definito in precedenza calcolo, archiviazione e le quote di rete.

Tuttavia, il servizio di Backup dell'infrastruttura non esegue il backup le macchine virtuali IaaS, configurazioni di rete e le risorse di archiviazione, ad esempio gli account di archiviazione BLOB, tabelle e così via, in modo che gli utenti che accedono al termine del ripristino di cloud completa non verrà visualizzato uno dei relativi già esistente risorse. Piattaforma distribuita come servizio (PaaS) delle risorse e i dati inoltre non sottoposti a backup dal servizio. 

Gli amministratori e gli utenti sono responsabili per backup e ripristino risorse IaaS e PaaS separatamente dai processi di backup dell'infrastruttura. Per informazioni sul backup risorse IaaS e PaaS, vedere i collegamenti seguenti:

- [Macchine virtuali](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [Servizio app](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Prima di abilitare il backup tramite la console, è necessario configurare il servizio di backup. È possibile configurare il servizio di backup tramite PowerShell. Per ulteriori informazioni, vedere [abilitare il Backup per lo Stack di Azure con PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Abilitare il backup

1. Aprire il portale di amministrazione dello Stack di Azure all'indirizzo [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Selezionare **più servizi** > **backup infrastruttura**. Scegliere **configurazione** nel **backup infrastruttura** blade.

    ![Stack di Azure - impostazioni Backup del controller](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Digitare il percorso di **il percorso di archiviazione di Backup**. Utilizzare una stringa di Universal Naming Convention (UNC) per il percorso di una condivisione file ospitata su un dispositivo distinto. Una stringa UNC specifica il percorso delle risorse, ad esempio i dispositivi o file condivisi. Per il servizio, è possibile utilizzare un indirizzo IP. Per garantire la disponibilità dei dati di backup dopo un'emergenza, il dispositivo deve essere in una posizione separata.
    > [!Note]  
    > Se l'ambiente supporta la risoluzione dei nomi di rete dell'infrastruttura di Azure Stack per l'ambiente aziendale, è possibile utilizzare un nome di dominio completo anziché l'indirizzo IP.
4. Tipo di **Username** utilizzando il dominio e il nome utente. Ad esempio, `Contoso\administrator`.
5. Tipo di **Password** per l'utente.
5. Digitare di nuovo la password **Conferma Password**.
6. Fornire una chiave precondivisa nel **chiave di crittografia** casella. I file di backup vengono crittografati tramite la chiave. Assicurarsi di archiviare la chiave in un luogo sicuro. Dopo aver impostato questa chiave per la prima volta o ruotare la chiave in futuro, è possibile visualizzare la chiave da questa interfaccia. Per ulteriori istruzioni generare una chiave precondivisa, seguire gli script in [abilitare il Backup per lo Stack di Azure con PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Selezionare **OK** per salvare le impostazioni del controller di backup.

Per eseguire un backup, è necessario scaricare gli strumenti di Azure Stack e quindi eseguire il cmdlet PowerShell **inizio AzSBackup** sul nodo Amministrazione dello Stack di Azure. Per ulteriori informazioni, vedere [backup Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire un backup. Vedere [backup Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Informazioni su come verificare che sia stato eseguito il backup. Vedere [backup conferma completata nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md).
