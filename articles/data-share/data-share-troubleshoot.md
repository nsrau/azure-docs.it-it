---
title: Risolvere i problemi relativi a Condivisione dati di Azure
description: Informazioni su come risolvere i problemi relativi agli inviti e agli errori durante la creazione o la ricezione di condivisioni dati con la condivisione di dati di Azure.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.openlocfilehash: a323dec66a3077784ff85deadd4f12086648fb3a
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220459"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Risolvere i problemi comuni in Condivisione dati di Azure 

Questo articolo illustra come risolvere i problemi comuni per la condivisione di dati di Azure. 

## <a name="azure-data-share-invitations"></a>Inviti a Condivisione dati di Azure 

In alcuni casi, quando un nuovo utente fa clic su **accetta invito** dall'invito tramite posta elettronica inviato, è possibile che venga visualizzato un elenco vuoto di inviti. 

![Nessun invito](media/no-invites.png)

Il problema può dipendere dalle cause seguenti:

* **Il servizio Condivisione dati di Azure non è registrato come provider di risorse di una sottoscrizione di Azure nel tenant di Azure.** Questo problema si verificherà se non è presente alcuna risorsa di Condivisione dati nel tenant di Azure. Quando si crea una risorsa di Condivisione dati di Azure, il provider di risorse viene registrato automaticamente nella sottoscrizione di Azure. È anche possibile registrare manualmente il servizio di Condivisione dati seguendo questa procedura. Per completarla, è necessario il ruolo di Collaboratore di Azure.

    1. Nel portale di Azure passare a **Sottoscrizioni**
    1. Selezionare la sottoscrizione che si desidera usare per creare una risorsa di Condivisione dati di Azure
    1. Fare clic su **Provider di risorse**
    1. Cercare **Microsoft.DataShare**
    1. Fare clic su **Registra** 

    Per completare questi passaggi, è necessario avere il [ruolo di collaboratore di Azure](../role-based-access-control/built-in-roles.md#contributor) per la sottoscrizione di Azure. 

* **L'invito viene inviato all'alias di posta elettronica anziché all'indirizzo di posta elettronica di accesso di Azure.** Se il servizio Condivisione dati di Azure è stato registrato o è già stata creata una risorsa di Condivisione dati nel tenant di Azure, ma non è ancora possibile visualizzare l'invito, il problema potrebbe essere dovuto al fatto che il provider ha immesso come destinatario l'alias di posta elettronica anziché l'indirizzo di posta elettronica di accesso di Azure. Contattare il provider di dati e assicurarsi che abbia inviato l'invito all'indirizzo di posta elettronica di accesso di Azure e non all'alias di posta elettronica personale.

* **L'invito è già stato accettato.** Il collegamento nel messaggio di posta elettronica consente di visualizzare la pagina di invito di Condivisione dati nel portale di Azure, che elenca solo gli inviti in sospeso. Se l'invito è già stato accettato, non verrà più visualizzato nella pagina di invito di Condivisione dati. Passare alla risorsa di Condivisione dati usata per accettare l'invito per visualizzare le condivisioni ricevute e configurare l'impostazione del cluster di Esplora dati di Azure di destinazione.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Errore durante la creazione o la ricezione di una nuova condivisione

"Impossibile aggiungere set di impostazioni"

"Impossibile eseguire il mapping di DataSets"

"Impossibile concedere la risorsa condivisione dati x accesso a y"

"Non si dispone delle autorizzazioni appropriate per x"

"Non è stato possibile aggiungere le autorizzazioni di scrittura per l'account di condivisione dati di Azure a una o più risorse selezionate"

Se durante la creazione di un nuovo set di dati di condivisione o di mapping si riceve uno degli errori precedenti, le autorizzazioni potrebbero essere insufficienti per l'archivio dati di Azure. Vedere [ruoli e requisiti per le](concepts-roles-permissions.md) autorizzazioni necessarie. 

È necessario disporre dell'autorizzazione di scrittura per condividere o ricevere dati da un archivio dati di Azure, che in genere esiste nel ruolo Collaboratore. 

Se è la prima volta che si condividono o si ricevono dati dall'archivio dati di Azure, è necessaria anche l'autorizzazione *Microsoft. Authorization/Role Attributes/Write* , che in genere esiste nel ruolo proprietario. Anche se è stata creata la risorsa Archivio dati di Azure, non viene automaticamente creato il proprietario della risorsa. Con l'autorizzazione appropriata, il servizio di condivisione dati di Azure concede automaticamente all'archivio dati l'accesso a identità gestite della risorsa di condivisione dati. Questo processo potrebbe richiedere alcuni minuti per essere applicato. Se si verifica un errore a causa di questo ritardo, riprovare tra qualche minuto.

Per la condivisione basata su SQL sono necessarie autorizzazioni aggiuntive. Per un elenco dettagliato dei prerequisiti, vedere [condividere da origini SQL](how-to-share-from-sql.md) .

## <a name="snapshot-failed"></a>Snapshot non riuscito
Lo snapshot potrebbe non riuscire a causa di diversi motivi. È possibile trovare un messaggio di errore dettagliato facendo clic sull'ora di inizio dello snapshot e quindi sullo stato di ogni set di dati. Di seguito sono riportati i motivi comuni per cui lo snapshot non riesce:

* La condivisione dati non è autorizzata a leggere dall'archivio dati di origine o a scrivere nell'archivio dati di destinazione. Per informazioni dettagliate sui requisiti di autorizzazione, vedere [ruoli e requisiti](concepts-roles-permissions.md) . Se è la prima volta che si acquisisce uno snapshot, potrebbero essere necessari alcuni minuti prima che alla risorsa di condivisione dati venga concesso l'accesso all'archivio dati di Azure. Attendere alcuni minuti e riprovare.
* La connessione della condivisione dati all'archivio dati di origine o di destinazione è bloccata dal firewall.
* Il set di dati condiviso o l'archivio dati di origine o di destinazione viene eliminato.

Per le origini SQL, di seguito sono riportate altre cause degli errori di snapshot. 

* Lo script SQL di origine o di destinazione per concedere l'autorizzazione per la condivisione dati non viene eseguito o viene eseguito utilizzando l'autenticazione SQL anziché l'autenticazione Azure Active Directory.  
* L'archivio dati SQL di origine o di destinazione è sospeso.
* I tipi di dati SQL non sono supportati dal processo snapshot o dall'archivio dati di destinazione. Per informazioni dettagliate, fare riferimento alla [condivisione da origini SQL](how-to-share-from-sql.md#supported-data-types) .
* L'archivio dati SQL di origine o di destinazione è bloccato da altri processi. La condivisione di dati di Azure non applica blocchi all'archivio dati SQL di origine e di destinazione. Tuttavia, i blocchi esistenti nell'archivio dati SQL di origine e di destinazione provocheranno un errore di snapshot.
* Un vincolo FOREIGN KEY fa riferimento alla tabella SQL di destinazione. Durante lo snapshot, se esiste una tabella di destinazione con lo stesso nome, la condivisione di dati di Azure Elimina la tabella e crea una nuova tabella. Se un vincolo FOREIGN KEY fa riferimento alla tabella SQL di destinazione, la tabella non può essere eliminata.
* Viene generato un file CSV di destinazione, ma non è possibile leggere i dati in Excel. Questo problema può verificarsi quando la tabella SQL di origine contiene dati con caratteri non inglesi. In Excel selezionare la scheda "Get data" e scegliere il file CSV, selezionare origine file come 65001: Unicode (UTF-8) e caricare i dati.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md). 

Per informazioni su come ricevere dati, continuare con l'esercitazione [accettare e ricevere dati](subscribe-to-data-share.md) .