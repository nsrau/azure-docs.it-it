---
title: Autorizzare l'accesso ai BLOB con AzCopy & Azure Active Directory | Microsoft Docs
description: È possibile fornire le credenziali di autorizzazione per le operazioni AzCopy usando Azure Active Directory (Azure AD).
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 89aab37b750a61bd21ba9af23875536a8cfbff4a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414829"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>Autorizzare l'accesso ai BLOB con AzCopy e Azure Active Directory (Azure AD)

È possibile specificare AzCopy con le credenziali di autorizzazione usando Azure AD. In questo modo, non sarà necessario aggiungere un token di firma di accesso condiviso a ogni comando. 

Per iniziare, verificare le assegnazioni di ruolo. Scegliere quindi il tipo di _entità di sicurezza_ che si vuole autorizzare. Un' [identità utente](../../active-directory/fundamentals/add-users-azure-active-directory.md), un' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md)e un' [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md) sono ognuna un tipo di entità di sicurezza.

Un'identità utente è qualsiasi utente che dispone di un'identità in Azure AD. Si tratta dell'entità di sicurezza più semplice da autorizzare. Le identità gestite e le entità servizio sono ottime opzioni se si prevede di usare AzCopy all'interno di uno script che viene eseguito senza interazione dell'utente. Un'identità gestita è più adatta per gli script eseguiti da una macchina virtuale (VM) di Azure e un'entità servizio è più adatta per gli script eseguiti in locale. 

Per altre informazioni su AzCopy, iniziare [a usare AzCopy](storage-use-azcopy-v10.md).

## <a name="verify-role-assignments"></a>Verificare le assegnazioni di ruolo

Il livello di autorizzazione necessario si basa sul fatto che si preveda di caricare i file o semplicemente scaricarli.

Se si vuole solo scaricare i file, verificare che il ruolo [lettore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) sia stato assegnato all'identità utente, all'identità gestita o all'entità servizio.

Se si vuole caricare i file, verificare che uno di questi ruoli sia stato assegnato all'entità di sicurezza:

- [Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Questi ruoli possono essere assegnati all'entità di sicurezza in uno di questi ambiti:

- Contenitore (file system)
- Account di archiviazione
- Resource group
- Subscription

Per informazioni su come verificare e assegnare i ruoli, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](./storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json).

> [!NOTE]
> Tenere presente che le assegnazioni di ruolo di Azure possono richiedere fino a cinque minuti per la propagazione.

Non è necessario che uno di questi ruoli sia assegnato all'entità di sicurezza se l'entità di sicurezza viene aggiunta all'elenco di controllo di accesso (ACL) del contenitore o della directory di destinazione. Nell'ACL l'entità di sicurezza necessita dell'autorizzazione di scrittura per la directory di destinazione e dell'autorizzazione Execute per il contenitore e ogni directory padre.

Per altre informazioni, vedere [modello di controllo di accesso in Azure Data Lake storage Gen2](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Autorizzare un'identità utente

Dopo aver verificato che all'identità utente è stato assegnato il livello di autorizzazione necessario, aprire un prompt dei comandi, digitare il comando seguente, quindi premere il tasto INVIO.

```azcopy
azcopy login
```

Se viene visualizzato un errore, provare ad includere l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Sostituire il `<tenant-id>` segnaposto con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare **Azure Active Directory > proprietà > ID directory** nella portale di Azure.

Questo comando restituisce un codice di autenticazione e l'URL di un sito Web. Aprire il sito Web, immettere il codice e quindi scegliere il pulsante **Avanti**.

![Creare un contenitore](media/storage-use-azcopy-v10/azcopy-login.png)

Verrà visualizzata una finestra di accesso. In tale finestra accedere al proprio account Azure usando le relative credenziali. Dopo aver completato l'accesso, è possibile chiudere la finestra del browser e iniziare a usare AzCopy.

<a id="service-principal"></a>

## <a name="authorize-a-managed-identity"></a>Autorizzare un'identità gestita

Si tratta di un'ottima opzione se si prevede di usare AzCopy all'interno di uno script che viene eseguito senza interazione dell'utente e lo script viene eseguito da una macchina virtuale (VM) di Azure. Quando si usa questa opzione, non è necessario archiviare le credenziali nella macchina virtuale.

È possibile accedere all'account usando un'identità gestita a livello di sistema abilitata nella VM oppure usando l'ID client, l'ID oggetto o l'ID di risorsa di un'identità gestita assegnata dall'utente che è stata assegnata alla macchina virtuale.

Per altre informazioni su come abilitare un'identità gestita a livello di sistema o creare un'identità gestita assegnata dall'utente, vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale usando il portale di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autorizzare usando un'identità gestita a livello di sistema

Assicurarsi prima di tutto che sia stata abilitata un'identità gestita a livello di sistema nella macchina virtuale. Vedere [identità gestita assegnata dal sistema](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Quindi, nella console dei comandi, digitare il comando seguente, quindi premere il tasto INVIO.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autorizzare usando un'identità gestita assegnata dall'utente

Assicurarsi prima di tutto che sia stata abilitata un'identità gestita assegnata dall'utente nella macchina virtuale. Vedere [identità gestita assegnata dall'utente](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Quindi, nella console dei comandi, digitare uno dei comandi seguenti, quindi premere il tasto INVIO.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Sostituire il `<client-id>` segnaposto con l'ID client dell'identità gestita assegnata dall'utente.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Sostituire il `<object-id>` segnaposto con l'ID oggetto dell'identità gestita assegnata dall'utente.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Sostituire il `<resource-id>` segnaposto con l'ID risorsa dell'identità gestita assegnata dall'utente.

## <a name="authorize-a-service-principal"></a>Autorizzare un'entità servizio

Si tratta di un'ottima opzione se si prevede di usare AzCopy all'interno di uno script che viene eseguito senza interazione dell'utente, in particolare in caso di esecuzione in locale. Se si prevede di eseguire AzCopy in macchine virtuali eseguite in Azure, un'identità del servizio gestito è più semplice da amministrare. Per altre informazioni, vedere la sezione [autorizzare un'identità gestita](#authorize-a-managed-identity) di questo articolo.

Prima di eseguire uno script, è necessario eseguire l'accesso in modo interattivo almeno una volta, in modo da poter fornire a AzCopy le credenziali dell'entità servizio.  Queste credenziali vengono archiviate in un file protetto e crittografato, in modo che lo script non debba fornire tali informazioni riservate.

È possibile accedere all'account usando un segreto client o usando la password di un certificato associato alla registrazione dell'app dell'entità servizio.

Per altre informazioni sulla creazione di un'entità servizio, vedere [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md).

Per ulteriori informazioni sulle entità servizio in generale, vedere [oggetti applicazione e oggetti entità servizio in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autorizzare un'entità servizio usando un segreto client

Per iniziare, impostare la `AZCOPY_SPA_CLIENT_SECRET` variabile di ambiente sul segreto client della registrazione dell'app dell'entità servizio.

> [!NOTE]
> Assicurarsi di impostare questo valore dal prompt dei comandi e non nelle impostazioni delle variabili di ambiente del sistema operativo. In questo modo, il valore è disponibile solo per la sessione corrente.

Questo esempio illustra come eseguire questa operazione in PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Prendere in considerazione l'uso di un prompt, come illustrato in questo esempio. In questo modo, la password non verrà visualizzata nella cronologia dei comandi della console.  

Digitare quindi il comando seguente e premere il tasto INVIO.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Sostituire il `<application-id>` segnaposto con l'ID applicazione della registrazione dell'app dell'entità servizio. Sostituire il `<tenant-id>` segnaposto con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare **Azure Active Directory > proprietà > ID directory** nella portale di Azure. 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autorizzare un'entità servizio tramite un certificato

Se si preferisce usare le proprie credenziali per l'autorizzazione, è possibile caricare un certificato nella registrazione dell'app e quindi usare tale certificato per l'accesso.

Oltre a caricare il certificato nella registrazione dell'app, è anche necessario disporre di una copia del certificato salvato nel computer o nella macchina virtuale in cui verrà eseguito AzCopy. Questa copia del certificato deve essere in. PFX o. Formato PEM e deve includere la chiave privata. La chiave privata deve essere protetta da password. Se si usa Windows e il certificato esiste solo in un archivio certificati, assicurarsi di esportare il certificato in un file PFX (inclusa la chiave privata). Per istruzioni, vedere [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)

Impostare quindi la `AZCOPY_SPA_CERT_PASSWORD` variabile di ambiente sulla password del certificato.

> [!NOTE]
> Assicurarsi di impostare questo valore dal prompt dei comandi e non nelle impostazioni delle variabili di ambiente del sistema operativo. In questo modo, il valore è disponibile solo per la sessione corrente.

Questo esempio illustra come eseguire questa attività in PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Digitare quindi il comando seguente e premere il tasto INVIO.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Sostituire il `<path-to-certificate-file>` segnaposto con il percorso relativo o il percorso completo del file di certificato. AzCopy Salva il percorso del certificato, ma non salva una copia del certificato, quindi assicurarsi di conservare il certificato. Sostituire il `<tenant-id>` segnaposto con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare **Azure Active Directory > proprietà > ID directory** nella portale di Azure.

> [!NOTE]
> Prendere in considerazione l'uso di un prompt, come illustrato in questo esempio. In questo modo, la password non verrà visualizzata nella cronologia dei comandi della console. 

<a id="managed-identity"></a>


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su AzCopy, iniziare [a usare AzCopy](storage-use-azcopy-v10.md)

- In caso di domande, problemi o commenti e suggerimenti generali, inviarli [nella pagina di GitHub](https://github.com/Azure/azure-storage-azcopy) .