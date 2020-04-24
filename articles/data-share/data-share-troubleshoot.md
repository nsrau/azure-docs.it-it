---
title: Risolvere i problemi relativi a Condivisione dati di Azure
description: Informazioni su come risolvere i problemi relativi agli inviti e agli errori durante la creazione o la ricezione di condivisioni dati con la condivisione di dati di Azure.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964227"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Risolvere i problemi comuni della condivisione dati di Azure 

Questo articolo illustra come risolvere i problemi comuni per la condivisione di dati di Azure. 

## <a name="azure-data-share-invitations"></a>Inviti a Condivisione dati di Azure 

In alcuni casi quando un nuovo utente fa clic su **Accept Invitation** (Accetta invito) nel messaggio di posta elettronica di invito ricevuto, viene visualizzato un elenco di inviti vuoto. 

![Nessun invito](media/no-invites.png)

Questo problema può essere dovuto ai motivi seguenti:

* **Il servizio di condivisione dati di Azure non è registrato come provider di risorse di una sottoscrizione di Azure nel tenant di Azure.** Questo problema si verificherà se non è presente alcuna risorsa di condivisione dati nel tenant di Azure. Quando si crea una risorsa di condivisione dati di Azure, il provider di risorse viene registrato automaticamente nella sottoscrizione di Azure. È anche possibile registrare manualmente il servizio di condivisione dati seguendo questa procedura. Per completare questa procedura, è necessario disporre del ruolo Collaboratore di Azure.

    1. Nel portale di Azure passare a **Sottoscrizioni**
    1. Selezionare la sottoscrizione che si vuole usare per creare una risorsa di condivisione dati di Azure
    1. Fare clic su **Provider di risorse**
    1. Cerca **Microsoft. DataShare**
    1. Fare clic su **Registra** 

    Per completare questa procedura, è necessario il [ruolo Collaboratore di Controllo degli accessi in base al ruolo in Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor). 

* **L'invito viene inviato all'alias di posta elettronica anziché al messaggio di posta elettronica di accesso di Azure.** Se il servizio Condivisione dati di Azure è stato registrato o è già stata creata una risorsa di condivisione dati nel tenant di Azure, ma non è ancora possibile visualizzare l'invito, è possibile che il provider abbia immesso l'alias di posta elettronica come destinatario anziché l'indirizzo di posta elettronica di accesso di Azure. Contattare il provider di dati e assicurarsi che abbia inviato l'invito all'indirizzo di posta elettronica dell'account di accesso di Azure e non all'alias di posta elettronica.

* **L'invito è già stato accettato.** Il collegamento nel messaggio di posta elettronica consente di visualizzare la pagina di invito alla condivisione dati in portale di Azure, che elenca solo gli inviti in sospeso. Se l'invito è già stato accettato, non verrà più visualizzato nella pagina di invito alla condivisione dei dati. Passare alla risorsa di condivisione dati usata per accettare l'invito per visualizzare le condivisioni ricevute e configurare l'impostazione del cluster di Esplora dati di Azure di destinazione.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Errore durante la creazione o la ricezione di una nuova condivisione

"Impossibile aggiungere set di impostazioni"

"Impossibile eseguire il mapping di DataSets"

"Impossibile concedere la risorsa condivisione dati x accesso a y"

"Non si dispone delle autorizzazioni appropriate per x"

"Non è stato possibile aggiungere le autorizzazioni di scrittura per l'account di condivisione dati di Azure a una o più risorse selezionate"

Se durante la creazione di un nuovo set di dati di condivisione o di mapping si riceve uno degli errori precedenti, le autorizzazioni potrebbero essere insufficienti per l'archivio dati di Azure. Vedere [ruoli e requisiti per le](concepts-roles-permissions.md) autorizzazioni necessarie. 

È necessario disporre dell'autorizzazione di scrittura per condividere o ricevere dati da un archivio dati di Azure, che in genere esiste nel ruolo Collaboratore. 

Se è la prima volta che si condividono o si ricevono dati dall'archivio dati di Azure, è necessaria anche l'autorizzazione *Microsoft. Authorization/Role Attributes/Write* , che in genere esiste nel ruolo proprietario. Anche se è stata creata la risorsa Archivio dati di Azure, non viene automaticamente creato il proprietario della risorsa. Con l'autorizzazione appropriata, il servizio di condivisione dati di Azure concede automaticamente all'archivio dati l'accesso a identità gestite della risorsa di condivisione dati. Questo processo potrebbe richiedere alcuni minuti per essere applicato. Se si verifica un errore a causa di questo ritardo, riprovare tra qualche minuto.

Per la condivisione basata su SQL sono necessarie autorizzazioni aggiuntive. Per informazioni dettagliate, vedere Risoluzione dei problemi di condivisione basata su SQL.

## <a name="troubleshooting-sql-based-sharing"></a>Risoluzione dei problemi di condivisione basata su SQL

"L'utente x non esiste nel database SQL"

Se questo errore viene visualizzato quando si aggiunge un set di dati da un'origine basata su SQL, è possibile che non sia stato creato un utente per l'identità gestita di condivisione dati di Azure nel SQL Server.  Per risolvere il problema, eseguire lo script seguente:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Se questo errore viene visualizzato durante il mapping del set di dati a una destinazione basata su SQL, è possibile che non sia stato creato un utente per l'identità gestita di condivisione dati di Azure nel SQL Server.  Per risolvere il problema, eseguire lo script seguente:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati.      

Assicurarsi di aver seguito tutti i prerequisiti elencati nell'esercitazione [condividere i dati](share-your-data.md) e [accettare e ricevere dati](subscribe-to-data-share.md) .

## <a name="snapshot-failed"></a>Snapshot non riuscito
Lo snapshot potrebbe non riuscire a causa di diversi motivi. È possibile trovare un messaggio di errore dettagliato facendo clic sull'ora di inizio dello snapshot e quindi sullo stato di ogni set di dati. 

Se il messaggio di errore è correlato all'autorizzazione, verificare che il servizio Condivisione dati disponga dell'autorizzazione necessaria. Per informazioni dettagliate, vedere [ruoli e requisiti](concepts-roles-permissions.md) . Se è la prima volta che si acquisisce uno snapshot, potrebbero essere necessari alcuni minuti prima che alla risorsa di condivisione dati venga concesso l'accesso all'archivio dati di Azure. Attendere alcuni minuti e riprovare.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md). 

Per informazioni su come ricevere dati, continuare con l'esercitazione [accettare e ricevere dati](subscribe-to-data-share.md) .

