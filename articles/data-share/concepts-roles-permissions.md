---
title: Ruoli e requisiti per Condivisione dati di Azure
description: Informazioni sui ruoli e i requisiti di controllo di accesso per i provider di dati e i consumer di dati per condividere i dati nella condivisione dati di Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 34c73a6bd400da076c68f308a2100a0f4569bd04
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490583"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Ruoli e requisiti per Condivisione dati di Azure 

Questo articolo descrive i ruoli necessari per condividere i dati usando la condivisione di dati di Azure, nonché per accettare e ricevere dati tramite la condivisione di dati di Azure. 

## <a name="roles-and-requirements"></a>Ruoli e requisiti

La condivisione di dati di Azure Usa identità gestite per i servizi di Azure (precedentemente noti come MSI) per eseguire l'autenticazione agli account di archiviazione sottostanti per poter leggere i dati da condividere con un provider di dati, nonché ricevere dati condivisi come consumer di dati. Di conseguenza, non esiste alcuno scambio di credenziali tra il provider di dati e il consumer di dati. 

È necessario concedere all'identità del servizio gestita l'accesso all'account di archiviazione sottostante o al database SQL. Il servizio di condivisione dati di Azure usa la identità del servizio gestita della risorsa di condivisione dati di Azure per leggere e scrivere i dati. L'utente della condivisione dati di Azure deve avere la possibilità di creare un'assegnazione di ruolo per la identità del servizio gestita all'account di archiviazione o al database SQL da cui condividono i dati. 

Nel caso di archiviazione, l'autorizzazione per la creazione di assegnazioni di ruolo è disponibile nel ruolo **proprietario** , ruolo amministratore accesso utenti o un ruolo personalizzato con Microsoft. autorizzazione/assegnazioni di ruolo/autorizzazione di scrittura assegnata. 

Se non si è proprietari dell'account di archiviazione in questione e non si è in grado di creare un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati di Azure, è possibile richiedere a un amministratore di Azure di creare un'assegnazione di ruolo per conto dell'utente. 

Di seguito è riportato un riepilogo dei ruoli assegnati alla condivisione dati identità gestita da risorse:

| |  |  |
|---|---|---|
|**Tipo di archiviazione**|**Archivio provider di dati**|**Archivio di destinazione del consumer di dati**|
|Archiviazione BLOB di Azure| Lettore dati BLOB di archiviazione | Collaboratore dati BLOB di archiviazione
|Azure Data Lake Gen1 | Proprietario | Non supportato
|Azure Data Lake Gen2 | Lettore dati BLOB di archiviazione | Collaboratore dati BLOB di archiviazione
|SQL di Azure | dbo | dbo 
|

### <a name="data-providers"></a>Provider di dati 
Per aggiungere un set di dati a una condivisione dati di Azure, è necessario aggiungere i provider di dati Identity gestito dalla risorsa al ruolo di lettore dati BLOB di archiviazione. Questa operazione viene eseguita automaticamente dal servizio di condivisione dati di Azure se l'utente aggiunge set di dati tramite Azure ed è un proprietario dell'account di archiviazione oppure è membro di un ruolo personalizzato con l'autorizzazione Microsoft. Authorization/Role assigns/Write assegnata. 

In alternativa, l'utente può avere un amministratore di Azure aggiungere manualmente la condivisione dati identità gestita al ruolo lettore dati BLOB di archiviazione. La creazione manuale di questa assegnazione di ruolo da parte dell'amministratore non sarà più un proprietario dell'account di archiviazione o avrà un'assegnazione di ruolo personalizzata. Questo vale per i dati condivisi da archiviazione di Azure o Azure Data Lake Gen2. 

Se si condividono dati da Azure Data Lake Gen1, l'assegnazione di ruolo deve essere eseguita al ruolo proprietario. 

Per creare un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati, seguire questa procedura:

1. Passare all'account di archiviazione.
1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi un'assegnazione di ruolo**.
1. In *ruolo*selezionare *lettore dati BLOB di archiviazione*.
1. In *Seleziona*Digitare il nome dell'account di condivisione dati di Azure.
1. Fare clic su *Save*.

Per le origini basate su SQL, è necessario creare un utente da un provider esterno nel database SQL da cui vengono condivisi i dati con lo stesso nome dell'account di condivisione dati di Azure. Uno script di esempio insieme ad altri prerequisiti per la condivisione basata su SQL è disponibile nell'esercitazione [condividere i dati](share-your-data.md) . 

### <a name="data-consumers"></a>Consumer di dati
Per ricevere i dati, è necessario aggiungere la condivisione di dati dei consumer di dati alle identità gestite dalla risorsa al ruolo di collaboratore dei dati BLOB di archiviazione e/o al ruolo dbo di un database SQL se si ricevono dati in un database SQL. 

Nel caso dell'archiviazione, questa operazione viene eseguita automaticamente dal servizio di condivisione dati di Azure se l'utente aggiunge set di dati tramite Azure ed è un proprietario dell'account di archiviazione oppure è un membro di un ruolo personalizzato che ha l'autorizzazione Microsoft. Authorization/Role assigns/Write assegnato. 

In alternativa, l'utente può avere un amministratore di Azure aggiungere manualmente la condivisione dati identità gestita al ruolo Collaboratore dati BLOB di archiviazione. La creazione manuale di questa assegnazione di ruolo da parte dell'amministratore non sarà più un proprietario dell'account di archiviazione o avrà un'assegnazione di ruolo personalizzata. Si noti che questo vale per i dati condivisi in archiviazione di Azure o Azure Data Lake Gen2. La ricezione di dati per Azure Data Lake Gen1 non è supportata. 

Per creare manualmente un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati, seguire questa procedura:

1. Passare all'account di archiviazione.
1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi un'assegnazione di ruolo**.
1. In *ruolo*selezionare *collaboratore dati BLOB di archiviazione*. 
1. In *Seleziona*Digitare il nome dell'account di condivisione dati di Azure.
1. Fare clic su *Save*.

Se si condividono dati usando le API REST, sarà necessario creare manualmente queste assegnazioni di ruolo aggiungendo l'account di condivisione dati in ai ruoli appropriati. 

Se si ricevono dati in un'origine basata su SQL, assicurarsi che venga creato un nuovo utente da un provider esterno con lo stesso nome dell'account di condivisione dati di Azure. Vedere l'esercitazione sui prerequisiti in [accettare e ricevere dati](subscribe-to-data-share.md) . 

Per altre informazioni su come aggiungere un'assegnazione di ruolo, fare riferimento a [questa documentazione,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) che illustra come aggiungere un'assegnazione di ruolo a una risorsa di Azure. 

## <a name="resource-provider-registration"></a>Registrazione del provider di risorse 

Quando si accetta un invito di condivisione dati di Azure, è necessario registrare manualmente il provider di risorse Microsoft. DataShare in nella sottoscrizione. Seguire questa procedura per registrare il provider di risorse Microsoft. DataShare nella sottoscrizione di Azure. 

1. Nella portale di Azure passare a **sottoscrizioni**.
1. Selezionare la sottoscrizione che si sta usando per la condivisione di dati di Azure.
1. Fare clic su **provider di risorse**.
1. Cercare Microsoft. DataShare.
1. Fare clic su **Register**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui ruoli in Azure - [Comprendere le definizioni dei ruoli](../role-based-access-control/role-definitions.md)

