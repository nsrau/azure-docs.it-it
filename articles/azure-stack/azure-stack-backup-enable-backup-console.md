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
ms.date: 07/11/2018
ms.author: jeffgilb
ms.openlocfilehash: fba04490aca4c7123ca478ae07a5f0c865d9a826
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968698"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Abilitare il backup per Azure Stack dal portale di amministrazione
Abilitare il servizio di infrastruttura di Backup tramite il portale di amministrazione in modo che Azure Stack può generare backup. È possibile usare questi backup per ripristinare l'ambiente cloud di ripristino in caso di utilizzo [un errore irreversibile](.\azure-stack-backup-recover-data.md). Lo scopo di ripristino di cloud è garantire che gli operatori e gli utenti possono accedere di nuovo il portale al termine del ripristino completo. Gli utenti avranno gli abbonamenti ripristinati incluse le autorizzazioni di accesso basato sui ruoli e i ruoli, originali piani, offerte e calcolo definito in precedenza, archiviazione e le quote di rete.

Tuttavia, il servizio Backup di infrastruttura non esegue il backup delle macchine virtuali IaaS, le configurazioni di rete e le risorse di archiviazione, ad esempio gli account di archiviazione, BLOB, tabelle e così via, in modo che gli utenti che accedono dopo il ripristino di cloud completa non verrà visualizzato uno dei relativi già esistente risorse. Piattaforma come servizio (PaaS), risorse e i dati anche senza il backup dal servizio. 

Gli amministratori e gli utenti sono tenuti a backup e ripristino delle risorse IaaS e PaaS separatamente dai processi di backup dell'infrastruttura. Per informazioni sul backup delle risorse IaaS e PaaS, vedere i collegamenti seguenti:

- [Macchine virtuali](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [Servizio app](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Prima di abilitare il backup tramite la console, è necessario configurare il servizio di backup. È possibile configurare il servizio di backup tramite PowerShell. Per altre informazioni, vedere [abilitare il Backup per Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Abilitare il backup

1. Aprire il portale di amministrazione di Azure Stack al [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Selezionare **altri servizi** > **backup Infrastructure**. Scegli **Configuration** nel **backup dell'infrastruttura** pannello.

    ![Azure Stack - impostazioni Backup del controller](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Digitare il percorso per il **percorso di archiviazione di Backup**. Usare una stringa di Universal Naming Convention (UNC) per il percorso a una condivisione file ospitata su un dispositivo separato. Una stringa UNC specifica il percorso delle risorse, ad esempio i file condivisi o i dispositivi. Per il servizio, è possibile usare un indirizzo IP. Per garantire la disponibilità dei dati di backup dopo un'emergenza, il dispositivo deve trovarsi in una posizione separata.
    > [!Note]  
    > Se l'ambiente supporta la risoluzione dei nomi dalla rete dell'infrastruttura di Azure Stack per l'ambiente dell'organizzazione, è possibile usare un nome di dominio completo anziché l'indirizzo IP.
4. Tipo di **Username** usando il nome utente e il dominio con diritti di accesso sufficienti per leggere e scrivere file. Ad esempio, `Contoso\backupshareuser`.
5. Tipo di **Password** per l'utente.
5. Digitare di nuovo la password **Conferma Password**.
6. Fornire una chiave precondivisa nel **chiave di crittografia** casella. I file di backup vengono crittografati usando questa chiave. Assicurarsi di conservare la chiave in un luogo sicuro. Dopo aver impostato questa chiave per la prima volta o ruotare la chiave in futuro, non è possibile visualizzare questa chiave da questa interfaccia. Per altre istruzioni generare una chiave precondivisa, seguire gli script in [abilitare il Backup per Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).
7. Selezionare **OK** per salvare le impostazioni di backup controller.

Per eseguire un backup, è necessario scaricare gli strumenti di Azure Stack e quindi eseguire il cmdlet di PowerShell **Start-AzSBackup** nel nodo di amministrazione di Azure Stack. Per altre informazioni, vedere [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire un backup. Visualizzare [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Informazioni su come verificare che sia stato eseguito il backup. Visualizzare [conferma backup completato nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md).
