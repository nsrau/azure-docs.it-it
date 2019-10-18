---
title: accesso azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 058506110a8ac4b11f272406a854f72062a1c90d
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514714"
---
# <a name="azcopy-login"></a>azcopy login

Accede a Azure Active Directory per accedere alle risorse di archiviazione di Azure.

## <a name="synopsis"></a>Sinossi

Accedere a Azure Active Directory per accedere alle risorse di archiviazione di Azure.

Per essere autorizzati all'account di archiviazione di Azure, è necessario assegnare il ruolo di **collaboratore dati BLOB di archiviazione** all'account utente nel contesto dell'account di archiviazione, del gruppo di risorse padre o della sottoscrizione padre.

Questo comando memorizza nella cache le informazioni di accesso crittografate per l'utente corrente usando i meccanismi predefiniti del sistema operativo.

Per ulteriori informazioni, fare riferimento agli esempi.

> [!IMPORTANT]
> Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Si consiglia di cancellare le variabili che contengono credenziali dalla cronologia della riga di comando. Per evitare che le variabili vengano visualizzate nella cronologia, è possibile utilizzare uno script per richiedere le credenziali dell'utente e per impostare la variabile di ambiente.

```azcopy
azcopy login [flags]
```

## <a name="examples"></a>esempi

Accedere in modo interattivo con l'ID tenant di AAD predefinito impostato su comune:

```azcopy
azcopy login
```

Accedere in modo interattivo con un ID tenant specificato:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Accedere usando l'identità assegnata dal sistema di una macchina virtuale (VM):

```azcopy
azcopy login --identity
```

Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID client dell'identità del servizio:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID oggetto dell'identità del servizio:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID risorsa dell'identità del servizio:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Accedere come entità servizio usando un segreto client. Impostare la variabile di ambiente AZCOPY_SPA_CLIENT_SECRET sul segreto client per l'autenticazione dell'entità servizio basata su Secret.

```azcopy
azcopy login --service-principal
```

Accedere come entità servizio usando un certificato e una password. Impostare la variabile di ambiente AZCOPY_SPA_CERT_PASSWORD sulla password del certificato per l'autorizzazione dell'entità servizio basata su certificato.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Assicurarsi di considerare/Path/to/My/CERT come percorso di un file PEM o PKCS12. AzCopy non raggiunge l'archivio certificati di sistema per ottenere il certificato.

--Certificate-Path è obbligatorio quando si esegue l'autenticazione dell'entità servizio basata su certificati.

## <a name="options"></a>Opzioni

|Opzione|Description|
|--|--|
|--Application-ID stringa|ID applicazione dell'identità assegnata dall'utente. Obbligatorio per l'autenticazione dell'entità servizio.|
|--certificate-percorso stringa|Percorso del certificato per l'autenticazione del nome SPN. Obbligatorio per l'autenticazione dell'entità servizio basata su certificato.|
|-h, --help|Mostra il contenuto della Guida per il comando login.|
|--Identity|eseguire l'accesso con l'identità della macchina virtuale, nota anche come identità del servizio gestito (MSI).|
|--Identity-client-ID stringa|ID client dell'identità assegnata dall'utente.|
|--Identity-Object-ID stringa|ID oggetto dell'identità assegnata dall'utente.|
|--Identity-Resource-ID stringa|ID risorsa dell'identità assegnata dall'utente.|
|--Service-Principal|Accedere tramite SPN (nome dell'entità servizio) usando un certificato o un segreto. Il segreto client o la password del certificato deve essere inserita nella variabile di ambiente appropriata. Digitare `AzCopy env` per visualizzare i nomi e le descrizioni delle variabili di ambiente.|
|--Tenant-ID stringa| ID del tenant di Azure Active Directory da usare per l'accesso interattivo al dispositivo OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Description|
|---|---|
|--Cap-Mbps UInt32|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
