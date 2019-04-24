---
title: Gestire i backup con il controllo degli accessi in base al ruolo di Azure
description: Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle operazioni di gestione di backup in un insieme di credenziali di Servizi di ripristino.
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: trinadhk
ms.openlocfilehash: ed3797183e13a00d2c5381fa6449c111c3bc9ab9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253718"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di Backup di Azure
Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando il Controllo degli accessi in base al ruolo, è possibile separare i compiti all'interno del team e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro.

> [!IMPORTANT]
> Ruoli predefiniti forniti da Backup di Azure sono limitati alle azioni che possono essere eseguite nel portale di Azure o tramite l'API REST o i cmdlet di PowerShell o CLI di credenziali di servizi di ripristino. Non rientrano sotto il controllo di questi ruoli le azioni eseguite nell'interfaccia utente client di Azure Backup Agent, nell'interfaccia utente di System Center Data Protection Manager o nell'interfaccia utente del server di Backup di Azure.

Backup di Azure offre tre ruoli predefiniti per controllare le operazioni di gestione di backup. Maggiori informazioni sui [ruoli predefiniti del Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md)

* [Collaboratore di backup](../role-based-access-control/built-in-roles.md#backup-contributor): questo ruolo dispone di tutte le autorizzazioni per creare e gestire il backup, ad eccezione delle autorizzazioni per la creazione dell'insieme di credenziali di Servizi di ripristino e della possibilità di fornire l'accesso ad altri utenti. Si immagini questo ruolo come amministratore della gestione di backup autorizzato a eseguire ogni operazione in tale ambito.
* [Operatore di backup](../role-based-access-control/built-in-roles.md#backup-operator): questo ruolo dispone delle autorizzazioni per tutte le operazioni svolte da un collaboratore, tranne per la rimozione di backup e la gestione dei criteri di backup. Questo ruolo è equivalente al collaboratore, ma non può eseguire operazioni distruttive, ad esempio interrompere il backup con eliminazione dei dati o rimuovere la registrazione di risorse locali.
* [Lettore di backup](../role-based-access-control/built-in-roles.md#backup-reader): questo ruolo dispone delle autorizzazioni per visualizzare tutte le operazioni di gestione di backup. Si immagini questo ruolo come una persona addetta al monitoraggio.

Per definire ruoli personalizzati per un maggiore controllo, vedere come creare [ruoli personalizzati](../role-based-access-control/custom-roles.md) nel Controllo degli accessi in base al ruolo di Azure.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapping dei ruoli predefiniti di Backup per azioni di gestione di backup
La tabella seguente riporta le azioni di gestione di Backup e il ruolo Controllo degli accessi in base al ruolo (RBAC) minimo richiesto per eseguire tale operazione.

| Operazione di gestione | Ruolo RBAC minimo richiesto | Ambito necessario |
| --- | --- | --- |
| Creare un insieme di credenziali di Servizi di ripristino | Collaboratore | Gruppo di risorse contenente l'insieme di credenziali |
| Abilitare il backup di VM di Azure | Operatore di backup | Gruppo di risorse contenente l'insieme di credenziali |
| | Collaboratore macchine virtuali | Risorsa della VM |
| Backup su richiesta della VM | Operatore di backup | Risorsa dell'insieme di credenziali di ripristino |
| Ripristino della VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore | Gruppo di risorse in cui verrà distribuita la VM |
| | Collaboratore macchine virtuali | Macchina virtuale di origine di cui è stato eseguito il backup |
| Ripristinare dischi non gestiti dal backup delle VM | Operatore di backup | Risorsa dell'insieme di credenziali di ripristino |
| | Collaboratore macchine virtuali | Macchina virtuale di origine di cui è stato eseguito il backup |
| | Collaboratore account di archiviazione | Risorsa account di archiviazione in cui i dischi saranno ripristinati |
| Ripristinare dischi gestiti dal backup delle VM | Operatore di backup | Risorsa dell'insieme di credenziali di ripristino |
| | Collaboratore macchine virtuali | Macchina virtuale di origine di cui è stato eseguito il backup |
| | Collaboratore account di archiviazione | Account di archiviazione temporaneo selezionato come parte del ripristino per contenere i dati dall'insieme di credenziali prima di convertirli in dischi gestiti |
| | Collaboratore | Gruppo di risorse in cui verranno ripristinati i dischi gestiti |
| Ripristinare singoli file dal backup delle VM | Operatore di backup | Risorsa dell'insieme di credenziali di ripristino |
| | Collaboratore macchine virtuali | Macchina virtuale di origine di cui è stato eseguito il backup |
| Creare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Risorsa dell'insieme di credenziali di ripristino |
| Modificare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Risorsa dell'insieme di credenziali di ripristino |
| Eliminare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Risorsa dell'insieme di credenziali di ripristino |
| Interrompere il backup (con o senza conservazione dei dati) in operazioni di backup di VM | Collaboratore di backup | Risorsa dell'insieme di credenziali di ripristino |
| Registrare Windows Server/client/SCDPM locale o server di Backup di Azure | Operatore di backup | Risorsa dell'insieme di credenziali di ripristino |
| Eliminare Windows Server/client/SCDPM locale o server di Backup di Azure registrato | Collaboratore di backup | Risorsa dell'insieme di credenziali di ripristino |

> [!IMPORTANT]
> Se si specifica Collaboratore di VM in un ambito di risorsa di macchina virtuale e si fa clic su Backup come parte delle impostazioni della macchina virtuale, si aprirà la schermata "Abilita backup" anche se il backup della macchina virtuale viene già eseguito, in quanto la chiamata per verificare lo stato del backup funziona solo a livello di sottoscrizione. Per evitare questo problema, accedere all'insieme di credenziali e aprire la vista degli elementi di backup della macchina virtuale o specificare il ruolo Collaboratore di VM a livello di sottoscrizione.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisiti del ruolo minimo per il backup di condivisioni File di Azure
Nella tabella seguente consente di acquisire le azioni di gestione di Backup e ruolo corrispondente necessario per eseguire l'operazione di condivisione File di Azure.

| Operazione di gestione | Ruolo richiesto | Risorse |
| --- | --- | --- |
| Abilitare il backup di condivisioni File di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| | Account di archiviazione | Risorsa dell'account di archiviazione per i collaboratori |
| Backup su richiesta della VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| Ripristinare la condivisione File | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore account di archiviazione | Risorse dell'account di archiviazione in cui sono presenti condivisioni di file di destinazione e origine ripristino |
| Ripristinare singoli file | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore account di archiviazione |   Risorse dell'account di archiviazione in cui sono presenti condivisioni di file di destinazione e origine ripristino |
| Arresta protezione | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |      
| Annullare la registrazione di account di archiviazione dall'insieme di credenziali |   Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore account di archiviazione | Risorsa dell'account di archiviazione|


## <a name="next-steps"></a>Passaggi successivi
* [Controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* Informazioni su come gestire l'accesso con:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Risoluzione dei problemi del controllo degli accessi in base al ruolo](../role-based-access-control/troubleshooting.md): suggerimenti per la risoluzione dei problemi comuni.
