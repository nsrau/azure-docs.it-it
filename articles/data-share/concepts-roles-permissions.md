---
title: Ruoli e requisiti per l'anteprima della condivisione dati di Azure
description: Ruoli e requisiti per l'anteprima della condivisione dati di Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7bf98f8774551292574d4f1951eba44657fa7de0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307349"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Ruoli e requisiti per l'anteprima della condivisione dati di Azure

Questo articolo descrive i ruoli necessari per condividere i dati con l'anteprima della condivisione dati di Azure, nonché per accettare e ricevere dati tramite l'anteprima di condivisione dati di Azure. 

## <a name="roles-and-requirements"></a>Ruoli e requisiti

La condivisione di dati di Azure Usa identità gestite per i servizi di Azure (precedentemente noti come MSI) per eseguire l'autenticazione agli account di archiviazione sottostanti per poter leggere i dati da condividere con un provider di dati, nonché ricevere dati condivisi come consumer di dati. Di conseguenza, non esiste alcuno scambio di credenziali tra il provider di dati e il consumer di dati. 

È necessario concedere all'identità del servizio gestita l'accesso agli account di archiviazione sottostanti. Il servizio di condivisione dati di Azure usa la identità del servizio gestita della risorsa di condivisione dati di Azure per leggere e scrivere i dati. L'utente della condivisione dati di Azure deve avere la possibilità di creare un'assegnazione di ruolo per la identità del servizio gestita all'account di archiviazione da cui condividono i dati. L'autorizzazione per la creazione di assegnazioni di ruolo è disponibile nel ruolo **proprietario** , ruolo amministratore accesso utenti o ruolo personalizzato con Microsoft. autorizzazione/assegnazione ruolo/autorizzazione di scrittura assegnata. 

Se non si è proprietari dell'account di archiviazione in questione e non si è in grado di creare un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati di Azure, è possibile richiedere a un amministratore di Azure di creare un'assegnazione di ruolo per conto dell'utente. 

Di seguito è riportato un riepilogo dei ruoli assegnati alla condivisione dati identità gestita da risorse:

| |  |  |
|---|---|---|
|**Tipo di archiviazione**|**provider di dati account di archiviazione di origine**|**Account di archiviazione di destinazione del consumer di dati**|
|Archivio BLOB di Azure| Ruolo con autorizzazioni di lettura per i dati dei BLOB di archiviazione | Collaboratore ai dati dei BLOB di archiviazione
|Azure Data Lake Gen1 | Proprietario | Non supportata
|Azure Data Lake Gen2 | Ruolo con autorizzazioni di lettura per i dati dei BLOB di archiviazione | Collaboratore ai dati dei BLOB di archiviazione
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

### <a name="data-consumers"></a>Consumer di dati
Per ricevere i dati, è necessario aggiungere l'identità gestita di risorse di condivisione dei dati dei consumer di dati al ruolo di collaboratore dati BLOB di archiviazione. Questo ruolo è necessario per abilitare il servizio di condivisione dati di Azure per poter scrivere nell'account di archiviazione. Questa operazione viene eseguita automaticamente dal servizio di condivisione dati di Azure se l'utente aggiunge set di dati tramite Azure ed è un proprietario dell'account di archiviazione oppure è membro di un ruolo personalizzato con l'autorizzazione Microsoft. Authorization/Role assigns/Write assegnata. 

In alternativa, l'utente può avere un amministratore di Azure aggiungere manualmente la condivisione dati identità gestita al ruolo Collaboratore dati BLOB di archiviazione. La creazione manuale di questa assegnazione di ruolo da parte dell'amministratore non sarà più un proprietario dell'account di archiviazione o avrà un'assegnazione di ruolo personalizzata. Si noti che questo vale per i dati condivisi in archiviazione di Azure o Azure Data Lake Gen2. La ricezione di dati per Azure Data Lake Gen1 non è supportata. 

Per creare manualmente un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati, seguire questa procedura:

1. Passare all'account di archiviazione.
1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi un'assegnazione di ruolo**.
1. In *ruolo*selezionare *collaboratore dati BLOB di archiviazione*. 
1. In *Seleziona*Digitare il nome dell'account di condivisione dati di Azure.
1. Fare clic su *Save*.

Se si condividono dati usando le API REST, sarà necessario creare manualmente queste assegnazioni di ruolo aggiungendo l'account di condivisione dati in ai ruoli appropriati. 

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

