---
title: Risolvere i problemi relativi a Condivisione dati di Azure
description: Informazioni su come risolvere i problemi relativi a inviti ed errori durante la creazione o la ricezione di condivisioni dati con Condivisione dati di Azure.Learn how to troubleshoot issues with invitations and errors when creating or receiving data shares with Azure Data Share.
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
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Risolvere i problemi comuni nella condivisione dati di AzureTroubleshoot common issues in Azure Data Share 

Questo articolo illustra come risolvere i problemi comuni per la condivisione dati di Azure.This article shows how to troubleshoot common issues for Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Inviti a Condivisione dati di Azure 

In alcuni casi quando un nuovo utente fa clic su **Accept Invitation** (Accetta invito) nel messaggio di posta elettronica di invito ricevuto, viene visualizzato un elenco di inviti vuoto. 

![Nessun invito](media/no-invites.png)

Ciò potrebbe essere dovuto ai seguenti motivi:

* **Il servizio Condivisione dati di Azure non è registrato come provider di risorse di qualsiasi sottoscrizione di Azure nel tenant di Azure.Azure Data Share service is not registered as a resource provider of any Azure subscription in the Azure tenant.** Questo problema si verificherà se nel tenant di Azure non è presente alcuna risorsa condivisione dati. Quando si crea una risorsa condivisione dati di Azure, registra automaticamente il provider di risorse nella sottoscrizione di Azure.When you create an Azure Data Share resource, it automatically registers the resource provider in your Azure subscription. È inoltre possibile registrare manualmente il servizio di condivisione dati seguendo questi passaggi. Per completare questi passaggi, è necessario disporre del ruolo Collaboratore di Azure.You'll need to have the Azure Contributor role to complete these steps.

    1. Nel portale di Azure passare a **Sottoscrizioni**
    1. Selezionare la sottoscrizione da usare per creare la risorsa Condivisione dati di AzureSelect the subscription you want to use to create Azure Data Share resource
    1. Fare clic su **Provider di risorse**
    1. Cerca **Microsoft.DataShare**
    1. Fare clic su **Registra** 

    Per completare questa procedura, è necessario il [ruolo Collaboratore di Controllo degli accessi in base al ruolo in Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor). 

* **L'invito viene inviato all'alias di posta elettronica anziché all'indirizzo di posta elettronica di accesso di Azure.Invitation is sent to your email alias instead of your Azure login email.** Se è stato registrato il servizio Condivisione dati di Azure o è già stata creata una risorsa condivisione dati nel tenant di Azure, ma non è ancora possibile visualizzare l'invito, è possibile che il provider abbia immesso l'alias di posta elettronica come destinatario anziché l'indirizzo di posta elettronica di accesso di Azure.If you have registered the Azure Data Share service or have already created a Data Share resource in the Azure tenant, but still cannot see the invitation, it maybe because the provider has entered your email alias as recipient instead of your Azure login email address. Contattare il provider di dati e assicurarsi che abbia inviato l'invito all'indirizzo di posta elettronica di accesso di Azure e non all'alias di posta elettronica.

* **L'invito è già stato accettato.** Il collegamento nel messaggio di posta elettronica consente di passare alla pagina Invito alla condivisione dati nel portale di Azure, in cui sono elencati solo gli inviti in sospeso. Se hai già accettato l'invito, non verrà più visualizzato nella pagina Invito alla condivisione dati. Passare alla risorsa condivisione dati in cui è stato usato per accettare l'invito per visualizzare le condivisioni ricevute e configurare l'impostazione del cluster di Azure Data Explorer di destinazione.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Errore durante la creazione o la ricezione di una nuova condivisione

"Impossibile aggiungere i set di dati"

"Impossibile eseguire il mapping dei set di dati"

"Impossibile concedere l'accesso alla risorsa Condivisione dati x a y"

"Non si dispone delle autorizzazioni appropriate per x"

"Non è stato possibile aggiungere autorizzazioni di scrittura per l'account di condivisione dati di Azure a una o più risorse selezionate"

Se si riceve uno degli errori sopra indicati durante la creazione di un nuovo set di dati di condivisione o di mapping, potrebbe essere dovuto a autorizzazioni insufficienti per l'archivio dati di Azure.If you receive any of the above errors when creating a new share or mapping datasets, it could be due to insufficient permissions to the Azure data store. Vedere [Ruoli e requisiti](concepts-roles-permissions.md) per le autorizzazioni necessarie. 

È necessaria l'autorizzazione di scrittura per condividere o ricevere dati da un archivio dati di Azure, che in genere esiste nel ruolo Collaboratore.You need write permission to share or receive data from an Azure data store, which typically exists in the Contributor role. 

Se è la prima volta che si condividono o si ricevono dati dall'archivio dati di Azure, è necessaria anche *l'autorizzazione Microsoft.Authorization/Role assignments/write,* che in genere esiste nel ruolo Proprietario.If this is the first time you are sharing or receiving data from the Azure data store, you also need Microsoft.Authorization/role assignments/write permission, which typically exists in the Owner role. Anche se è stata creata la risorsa dell'archivio dati di Azure, NON rende automaticamente il proprietario della risorsa. Con l'autorizzazione appropriata, il servizio condivisione dati di Azure concede automaticamente all'archivio dati l'identità gestita della risorsa condivisione dati. L'applicazione di questo processo potrebbe richiedere alcuni minuti. Se si verifica un errore a causa di questo ritardo, riprovare tra qualche minuto.

La condivisione basata su SQL richiede autorizzazioni aggiuntive. Per informazioni dettagliate, vedere Risoluzione dei problemi di condivisione basata su SQL.

## <a name="troubleshooting-sql-based-sharing"></a>Risoluzione dei problemi di condivisione basata su SQL

"L'utente x non esiste nel database SQL"

Se viene visualizzato questo errore quando si aggiunge un set di dati da un'origine basata su SQL, è possibile che non sia stato creato un utente per l'identità gestita di Azure Data Share in SQL Server.If you receive this error when adding a dataset from a SQL-based source, it may be because you did not create a user for the Azure Data Share managed identity on your SQL Server.  Per risolvere questo problema, eseguire lo script seguente:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Se viene visualizzato questo errore durante il mapping del set di dati a una destinazione basata su SQL, è possibile che non sia stato creato un utente per l'identità gestita di Azure Data Share in SQL Server.If you receive this error when mapping dataset to a SQL-based target, it may be because you did not create a user for the Azure Data Share managed identity on your SQL Server.  Per risolvere questo problema, eseguire lo script seguente:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati.      

Assicurarsi di aver seguito tutti i prerequisiti elencati nell'esercitazione [Condividi i dati](share-your-data.md) e di accettare e ricevere [dati.](subscribe-to-data-share.md)

## <a name="snapshot-failed"></a>Snapshot non riuscito
L'istantanea potrebbe non riuscire a causa di una serie di motivi. È possibile trovare un messaggio di errore dettagliato facendo clic sull'ora di inizio dello snapshot e quindi sullo stato di ogni set di dati. 

Se il messaggio di errore è correlato all'autorizzazione, verificare che il servizio Condivisione dati disponga dell'autorizzazione necessaria. Per informazioni dettagliate, vedere [Ruoli e requisiti.](concepts-roles-permissions.md) Se è la prima volta che si crea uno snapshot, è possibile che venga concesso l'accesso alla risorsa condivisione dati all'archivio dati di Azure.If this is the first time you are taking a snapshot, it could take a few minutes for Data Share resource to get granted access to the Azure data store. Attendere alcuni minuti e riprovare.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md). 

Per informazioni su come ricevere i dati, continuare con l'esercitazione [sull'accettazione e](subscribe-to-data-share.md) la ricezione dei dati.

