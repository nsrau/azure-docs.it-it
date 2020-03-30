---
title: Azcopy login Documenti Microsoft
description: In questo articolo vengono fornite informazioni di riferimento per il comando azcopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295414"
---
# <a name="azcopy-login"></a>azcopy login

Esegue l'accesso ad Azure Active Directory per accedere alle risorse di Archiviazione di Azure.Logs in to Azure Active Directory to access Azure Storage resources.

## <a name="synopsis"></a>Riepilogo

Accedere ad Azure Active Directory per accedere alle risorse di Archiviazione di Azure.Log in to Azure Active Directory to access Azure Storage resources.

Per essere autorizzati all'account di archiviazione di Azure, è necessario assegnare il ruolo **Collaboratore dati BLOB** di archiviazione all'account utente nel contesto dell'account di archiviazione, del gruppo di risorse padre o della sottoscrizione padre.

Questo comando memorizzerà nella cache le informazioni di accesso crittografate per l'utente corrente utilizzando i meccanismi incorporati del sistema operativo.

Si prega di fare riferimento agli esempi per ulteriori informazioni.

> [!IMPORTANT]
> Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Prendere in considerazione la cancellazione delle variabili che contengono le credenziali dalla cronologia della riga di comando. Per impedire la visualizzazione delle variabili nella cronologia, è possibile utilizzare uno script per richiedere all'utente le credenziali e impostare la variabile di ambiente.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Esempi

Accedere in modo interattivo con l'ID tenant AAD predefinito impostato su common:Log in interactively with default AAD tenant ID set to common:

```azcopy
azcopy login
```

Accedere in modo interattivo con un ID tenant specificato:Log in interactively with a specified tenant ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Accedere utilizzando l'identità assegnata dal sistema di una macchina virtuale (VM):

```azcopy
azcopy login --identity
```

Accedere utilizzando l'identità assegnata dall'utente di una macchina virtuale e un ID client dell'identità del servizio:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Accedere utilizzando l'identità assegnata dall'utente di una macchina virtuale e un ID oggetto dell'identità del servizio:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Accedere utilizzando l'identità assegnata dall'utente di una macchina virtuale e un ID risorsa dell'identità del servizio:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Accedere come entità servizio usando un segreto client. Impostare la variabile di ambiente AZCOPY_SPA_CLIENT_SECRET al segreto client per l'autenticazione dell'entità servizio basata su segreto.

```azcopy
azcopy login --service-principal
```

Accedere come entità servizio usando un certificato e una password. Impostare la variabile di ambiente AZCOPY_SPA_CERT_PASSWORD alla password del certificato per l'autorizzazione dell'entità servizio basata su certificato.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Assicurarsi di considerare /path/to/my/cert come un percorso a un file PEM o PKCS12. AzCopy non entra nell'archivio dei certificati di sistema per ottenere il certificato.

--certificate-path è obbligatorio quando si esegue l'autenticazione dell'entità servizio basata su certificato.

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|--aad-endpoint|Endpoint di Azure Active Directory da usare. Il valore`https://login.microsoftonline.com`predefinito ( ) è corretto per il cloud di Azure pubblico. Impostare questo parametro durante l'autenticazione in un cloud nazionale. Vedere Endpoint di autenticazione di [Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)
Questo flag non è necessario per l'identità del servizio gestito.|
|--application-id (stringa)|ID applicazione dell'identità assegnata dall'utente. Obbligatorio per l'autenticazione dell'entità servizio.|
|--certificate-path (stringa del percorso del certificato)|Percorso del certificato per l'autenticazione SPN. Obbligatorio per l'autenticazione dell'entità servizio basata su certificati.|
|-h, --help|Visualizzare il contenuto della Guida per il comando di accesso.|
|--identità|accedere utilizzando l'identità della macchina virtuale, nota anche come identità del servizio gestito (MSI).|
|--identity-client-id stringa|ID client dell'identità assegnata dall'utente.|
|--identity-object-id (stringa)|ID oggetto dell'identità assegnata dall'utente.|
|--identity-resource-id stringa|ID risorsa dell'identità assegnata dall'utente.|
|--service-principal|Accedere tramite SPN (Nome dell'entità servizio) utilizzando un certificato o un segreto. Il segreto client o la password del certificato deve essere inserito nella variabile di ambiente appropriata. Digitare `AzCopy env` per visualizzare i nomi e le descrizioni delle variabili di ambiente.|
|--tenant-id (stringa)| l'ID tenant di Azure Active Directory da usare per l'accesso interattivo del dispositivo OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps uint32|Limita la velocità di trasferimento, in megabit al secondo. La velocità effettiva momento per momento potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|

## <a name="see-also"></a>Vedere anche

- [azcopy](storage-ref-azcopy.md)
