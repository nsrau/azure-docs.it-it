---
title: Abilitare il Backup per lo Stack di Azure dal portale di amministrazione | Documenti Microsoft
description: "Attivare il nuovo servizio di infrastruttura tramite il portale di amministrazione, in modo che Azure Stack può essere ripristinato se si è verificato un errore."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: a5a9757d871c343ba663862de7b6d75b9dd21c31
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Abilitare il Backup per lo Stack di Azure dal portale di amministrazione

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Attivare il nuovo servizio di infrastruttura tramite il portale di amministrazione, in modo che Azure Stack può generare backup. È possibile utilizzare questi esegue il gruppo di continuità per ripristinare l'ambiente, se si è verificato un errore.

## <a name="enable-backup"></a>Abilitare il backup

1. Aprire il portale di amministrazione di Azure Stack in [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Selezionare **più servizi** > **backup infrastruttura**. Scegliere **configurazione** nel **backup infrastruttura** blade.

    ![Stack di Azure - impostazioni Backup del controller](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Digitare il percorso di **il percorso di archiviazione di Backup**. È necessario utilizzare una stringa di Universal Naming Convention (UNC) per il percorso di una condivisione file ospitata su un dispositivo distinto. Una stringa UNC specifica il percorso delle risorse, ad esempio i dispositivi o file condivisi. Per il servizio, è possibile utilizzare un indirizzo IP. Per garantire la disponibilità dei dati di backup in caso di emergenza, il dispositivo deve essere in una posizione separata.
    > [!Note]  
    > Se l'ambiente supporta la risoluzione dei nomi di rete dell'infrastruttura di Azure Stack per l'ambiente aziendale, è possibile utilizzare un nome di dominio completo anziché l'indirizzo IP.
4. Tipo di **Username** utilizzando il dominio e il nome utente. Ad esempio, `Contoso\administrator`.
5. Tipo di **Password** per l'utente.
5. Digitare di nuovo la password **Conferma Password**.
6. Fornire una chiave precondivisa nel **chiave di crittografia** casella. I file di backup vengono crittografati tramite la chiave. Assicurarsi di archiviare la chiave in un luogo sicuro. Dopo aver impostato questa chiave per la prima volta o ruotare la chiave in futuro, è possibile visualizzare la chiave da questa interfaccia. Per ulteriori istruzioni generare una chiave precondivisa, seguire gli script in [abilitare il Backup per lo Stack di Azure con PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Selezionare **OK** per salvare le impostazioni del controller di backup.

Per eseguire un backup, è necessario scaricare gli strumenti di Azure Stack e quindi eseguire il cmdlet PowerShell **inizio AzSBackup** sul nodo Amministrazione dello Stack di Azure. Per ulteriori informazioni, vedere [backup Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Passaggi successivi

 - Informazioni su come eseguire un backup, vedere [backup Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Informazioni su come verificare che sia stato eseguito il backup, vedere [backup conferma completata nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md ).
