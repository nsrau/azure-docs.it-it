---
title: Gestire i backup con il controllo degli accessi in base al ruoloManage Backups with Role-Based Access Control
description: Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle operazioni di gestione di backup in un insieme di credenziali di Servizi di ripristino.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e2e32ac6981635e3b9885119fdf397783ac32cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273202"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di Backup di Azure

Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando il Controllo degli accessi in base al ruolo, è possibile separare i compiti all'interno del team e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro.

> [!IMPORTANT]
> I ruoli forniti da Backup di Azure sono limitati alle azioni che possono essere eseguite nel portale di Azure o tramite l'API REST o i cmdlet dell'insieme di credenziali di Servizi di ripristino PowerShell o dell'interfaccia della riga di comando. Non rientrano sotto il controllo di questi ruoli le azioni eseguite nell'interfaccia utente client di Azure Backup Agent, nell'interfaccia utente di System Center Data Protection Manager o nell'interfaccia utente del server di Backup di Azure.

Backup di Azure offre tre ruoli predefiniti per controllare le operazioni di gestione dei backup. Maggiori informazioni sui [ruoli predefiniti del Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md)

* [Collaboratore backup:](../role-based-access-control/built-in-roles.md#backup-contributor) questo ruolo dispone di tutte le autorizzazioni per creare e gestire il backup, ad eccezione dell'eliminazione dell'insieme di credenziali dei servizi di ripristino e dell'accesso ad altri utenti. Si immagini questo ruolo come amministratore della gestione di backup autorizzato a eseguire ogni operazione in tale ambito.
* [Operatore di backup](../role-based-access-control/built-in-roles.md#backup-operator): questo ruolo dispone delle autorizzazioni per tutte le operazioni svolte da un collaboratore, tranne per la rimozione di backup e la gestione dei criteri di backup. Questo ruolo è equivalente al collaboratore, ma non può eseguire operazioni distruttive, ad esempio interrompere il backup con eliminazione dei dati o rimuovere la registrazione di risorse locali.
* [Lettore di backup](../role-based-access-control/built-in-roles.md#backup-reader): questo ruolo dispone delle autorizzazioni per visualizzare tutte le operazioni di gestione di backup. Si immagini questo ruolo come una persona addetta al monitoraggio.

Se si vuole definire ruoli personalizzati per un controllo ancora maggiore, vedere come creare ruoli personalizzati in Controllo degli accessi in base al ruolo di Azure.If you're looking to define your own roles for even more control, see how to [build Custom roles](../role-based-access-control/custom-roles.md) in Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapping dei ruoli predefiniti di Backup per azioni di gestione di backup

La tabella seguente riporta le azioni di gestione di Backup e il ruolo Controllo degli accessi in base al ruolo (RBAC) minimo richiesto per eseguire tale operazione.

| Operazione di gestione | Ruolo RBAC minimo richiesto | Ambito necessario |
| --- | --- | --- |
| Creare un insieme di credenziali di Servizi di ripristino | Collaboratore di backup | Gruppo di risorse contenente l'insieme di credenziali |
| Abilitare il backup di VM di Azure | Operatore di backup | Gruppo di risorse contenente l'insieme di credenziali |
| | Collaboratore macchine virtuali | Risorsa della VM |
| Backup su richiesta della VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| Ripristino della VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore | Gruppo di risorse in cui verrà distribuita la VM |
| | Collaboratore macchine virtuali | Macchina virtuale di origine di cui è stato eseguito il backup |
| Ripristinare dischi non gestiti dal backup delle VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore macchine virtuali | Macchina virtuale di origine di cui è stato eseguito il backup |
| | Collaboratore account di archiviazione | Risorsa account di archiviazione in cui i dischi saranno ripristinati |
| Ripristinare dischi gestiti dal backup delle VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore macchine virtuali | Macchina virtuale di origine di cui è stato eseguito il backup |
| | Collaboratore account di archiviazione | Account di archiviazione temporaneo selezionato come parte del ripristino per contenere i dati dall'insieme di credenziali prima di convertirli in dischi gestiti |
| | Collaboratore | Gruppo di risorse in cui verranno ripristinati i dischi gestiti |
| Ripristinare singoli file dal backup delle VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore macchine virtuali | Macchina virtuale di origine di cui è stato eseguito il backup |
| Creare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Modificare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Eliminare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Interrompere il backup (con o senza conservazione dei dati) in operazioni di backup di VM | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Registrare Windows Server/client/SCDPM locale o server di Backup di Azure | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| Eliminare Windows Server/client/SCDPM locale o server di Backup di Azure registrato | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |

> [!IMPORTANT]
> Se si specifica Collaboratore di VM in un ambito di risorsa di macchina virtuale e si fa clic su Backup come parte delle impostazioni della macchina virtuale, si aprirà la schermata "Abilita backup" anche se il backup della macchina virtuale viene già eseguito, in quanto la chiamata per verificare lo stato del backup funziona solo a livello di sottoscrizione. Per evitare questo problema, accedere all'insieme di credenziali e aprire la vista degli elementi di backup della macchina virtuale o specificare il ruolo Collaboratore di VM a livello di sottoscrizione.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisiti minimi del ruolo per il backup della condivisione file di AzureMinimum role requirements for the Azure File share backup

Nella tabella seguente vengono acquisite le azioni di gestione Backup e il ruolo corrispondente necessario per eseguire l'operazione di condivisione file di Azure.The following table captures the Backup management actions and corresponding role required to perform Azure File share operation.

| Operazione di gestione | Ruolo obbligatorio | Risorse |
| --- | --- | --- |
| Abilitare il backup delle condivisioni file di AzureEnable backup of Azure File shares | Collaboratore di backup |Insieme di credenziali dei servizi di ripristino |
| |Account di archiviazione | Risorsa account di archiviazione collaboratore |
| Backup su richiesta della VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| Ripristina condivisione file | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore account di archiviazione | Risorse dell'account di archiviazione in cui sono presenti le condivisioni file di origine e di destinazione del ripristinoStorage account resources where restore source and Target file shares are present |
| Ripristino di singoli file | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| |Collaboratore account di archiviazione|Risorse dell'account di archiviazione in cui sono presenti le condivisioni file di origine e di destinazione del ripristinoStorage account resources where restore source and Target file shares are present |
| Arresta protezione |Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Annullare la registrazione dell'account di archiviazione dall'insieme di credenzialiUnregister storage account from vault |Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| |Collaboratore account di archiviazione | Risorsa dell'account di archiviazione|

## <a name="next-steps"></a>Passaggi successivi

* [Controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* Informazioni su come gestire l'accesso con:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfaccia della riga di comando di AzureAzure](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Risoluzione dei problemi del controllo degli accessi in base al ruolo](../role-based-access-control/troubleshooting.md): suggerimenti per la risoluzione di problemi comuni.
