---
title: Gestire i backup con il controllo degli accessi in base al ruol | Documentazione Microsoft
description: Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle operazioni di gestione di backup in un insieme di credenziali di Servizi di ripristino.
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 3bd46b97-4b29-47a5-b5ac-ac174dd36760
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/22/2017
ms.author: trinadhk;markgal
ms.openlocfilehash: b6e4c6761e1bd5c17c9c3428491113042d3b1d31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di Backup di Azure
Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando il Controllo degli accessi in base al ruolo, è possibile separare i compiti all'interno del team e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro.

> [!IMPORTANT]
> I ruoli previsti da Backup di Azure sono limitati alle azioni eseguibili nel portale di Azure o a i cmdlet di PowerShell per l'insieme di credenziali di Servizi di ripristino. Non rientrano sotto il controllo di questi ruoli le azioni eseguite nell'interfaccia utente client di Azure Backup Agent, nell'interfaccia utente di System Center Data Protection Manager o nell'interfaccia utente del server di Backup di Azure.

Backup di Azure offre 3 ruoli predefiniti per controllare le operazioni di gestione di backup. Maggiori informazioni sui [ruoli predefiniti del Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md)

* [Collaboratore di backup](../active-directory/role-based-access-built-in-roles.md#backup-contributor): questo ruolo dispone di tutte le autorizzazioni per creare e gestire il backup, ad eccezione delle autorizzazioni per la creazione dell'insieme di credenziali di Servizi di ripristino e della possibilità di fornire l'accesso ad altri utenti. Si immagini questo ruolo come amministratore della gestione di backup autorizzato a eseguire ogni operazione in tale ambito.
* [Operatore di backup](../active-directory/role-based-access-built-in-roles.md#backup-operator): questo ruolo dispone delle autorizzazioni per tutte le operazioni svolte da un collaboratore, tranne per la rimozione di backup e la gestione dei criteri di backup. Questo ruolo è equivalente al collaboratore, ma non può eseguire operazioni distruttive, ad esempio interrompere il backup con eliminazione dei dati o rimuovere la registrazione di risorse locali.
* [Lettore di backup](../active-directory/role-based-access-built-in-roles.md#backup-reader): questo ruolo dispone delle autorizzazioni per visualizzare tutte le operazioni di gestione di backup. Si immagini questo ruolo come una persona addetta al monitoraggio.

Per definire ruoli personalizzati per un maggiore controllo, vedere come creare [ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md) nel Controllo degli accessi in base al ruolo di Azure.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapping dei ruoli predefiniti di Backup per azioni di gestione di backup
La tabella seguente riporta le azioni di gestione di Backup e il ruolo Controllo degli accessi in base al ruolo (RBAC) minimo richiesto per eseguire tale operazione.

| Operazione di gestione | Ruolo RBAC minimo richiesto |
| --- | --- |
| Creare un insieme di credenziali di Servizi di ripristino | Collaboratore per il gruppo di risorse dell'insieme di credenziali |
| Abilitare il backup di VM di Azure | Operatore di backup per l'insieme di credenziali, collaboratore Macchina virtuale in VM |
| Backup su richiesta della VM | Operatore di backup |
| Ripristino della VM | Operatore di backup, collaboratore di gruppo di risorse in cui le VM e le reti virtuali verranno distribuite |
| Ripristinare dischi e singoli file dal backup delle VM | Operatore di backup, collaboratore Macchina Virtuale in VM |
| Creare criteri di backup per il backup di VM di Azure | Collaboratore di backup |
| Modificare criteri di backup per il backup di VM di Azure | Collaboratore di backup |
| Eliminare criteri di backup per il backup di VM di Azure | Collaboratore di backup |
| Interrompere il backup (con o senza conservazione dei dati) in operazioni di backup di VM | Collaboratore di backup |
| Registrare Windows Server/client/SCDPM locale o server di Backup di Azure | Operatore di backup |
| Eliminare Windows Server/client/SCDPM locale o server di Backup di Azure registrato | Collaboratore di backup |

## <a name="next-steps"></a>Passaggi successivi
* [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* Informazioni su come gestire l'accesso con:
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Interfaccia della riga di comando di Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [API REST](../active-directory/role-based-access-control-manage-access-rest.md)
* [Risoluzione dei problemi del controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-troubleshooting.md): suggerimenti per la risoluzione di problemi comuni.
