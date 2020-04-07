---
title: Copiare o spostare dati in Archiviazione di Azure usando AzCopy v10. Documenti Microsoft
description: AzCopy è un'utilità della riga di comando che è possibile usare per copiare dati in, da o tra account di archiviazione. Questo articolo illustra come scaricare AzCopy, connettersi all'account di archiviazione e quindi trasferire i file.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8701fe6857e95334a5e1d24bfe70feb130d5512c
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756020"
---
# <a name="get-started-with-azcopy"></a>Introduzione ad AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB o file in o da un account di archiviazione. Questo articolo illustra come scaricare AzCopy, connettersi all'account di archiviazione e quindi trasferire i file.

> [!NOTE]
> AzCopy V10 è la versione attualmente supportata di AzCopy.AzCopy **V10** is the currently supported version of AzCopy.
>
> Se è necessario usare una versione precedente di AzCopy, vedere la sezione [Usare la versione precedente di AzCopy](#previous-version) di questo articolo.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Scaricare AzCopy

Scaricare innanzitutto il file eseguibile AzCopy V10 in qualsiasi directory del computer. AzCopy V10 è solo un file eseguibile, quindi non c'è niente da installare.

- [Windows a 64 bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows a 32 bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Questi file sono compressi come un file zip (Windows e Mac) o un file tar (Linux). Per scaricare e decomprimere il file tar su Linux, vedere la documentazione relativa alla distribuzione Linux.

> [!NOTE]
> Se si desidera copiare i dati da e verso il servizio di archiviazione tabelle di [Azure,](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) installare [AzCopy versione 7.3.](https://aka.ms/downloadazcopynet)


## <a name="run-azcopy"></a>Eseguire AzCopy

Per comodità, è consigliabile aggiungere il percorso di directory dell'eseguibile AzCopy al percorso di sistema per semplificarne l'utilizzo. In questo modo `azcopy` è possibile digitare da qualsiasi directory del sistema.

Se si sceglie di non aggiungere la directory AzCopy al percorso, sarà necessario modificare le `azcopy` `.\azcopy` directory in base al percorso dell'eseguibile AzCopy e al tipo o nei prompt dei comandi di Windows PowerShell.

Per visualizzare un elenco `azcopy -h` di comandi, digitare e premere INVIO.

Per informazioni su un comando specifico, è sufficiente includere `azcopy list -h`il nome del comando (ad esempio: ).

![Guida in linea](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Per trovare la documentazione di riferimento dettagliata per ogni comando e parametro di comando, vedere [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> In qualità di proprietario dell'account di archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati. Prima di poter eseguire qualsiasi operazione significativa con AzCopy, è necessario decidere come fornire le credenziali di autorizzazione al servizio di archiviazione. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere la modalità di assegnazione delle credenziali di autorizzazione

È possibile fornire le credenziali di autorizzazione usando Azure Active Directory (AD) o un token firma di accesso condiviso.

Utilizzare questa tabella come guida:Use this table as a guide:

| Tipo di archiviazione | Metodo di autorizzazione attualmente supportato |
|--|--|
|**Archiviazione BLOB** | Azure AD & SAS |
|**Archiviazione BLOB (spazio dei nomi gerarchico)Blob storage (hierarchical namespace)** | Azure AD & SAS |
|**Archiviazione dei file** | Solo sAS |

### <a name="option-1-use-azure-active-directory"></a>Opzione 1: Usare Azure Active DirectoryOption 1: Use Azure Active Directory

Usando Azure Active Directory, è possibile fornire le credenziali una sola volta anziché dover aggiungere un token di firma di accesso condiviso a ogni comando.  

> [!NOTE]
> Nella versione corrente, se si prevede di copiare BLOB tra account di archiviazione, è necessario aggiungere un token di firma di accesso condiviso a ogni URL di origine. È possibile omettere il token di firma di accesso condiviso solo dall'URL di destinazione. Per alcuni esempi, vedere [Copiare BLOB tra account di archiviazione.](storage-use-azcopy-blobs.md)

Il livello di autorizzazione necessario si basa sul fatto che si prevede di caricare i file o semplicemente scaricarli.

Se si desidera scaricare solo i file, verificare che il lettore di [dati BLOB](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) di archiviazione sia stato assegnato all'identità utente, all'identità gestita o all'entità servizio.

> Le identità utente, le identità gestite e le entità servizio sono un tipo di *entità di sicurezza,* pertanto verrà usato il termine *entità di sicurezza* per il resto di questo articolo.

Se si desidera caricare file, verificare che uno di questi ruoli sia stato assegnato all'entità di sicurezza:

- [Collaboratore ai dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietario dei dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Questi ruoli possono essere assegnati all'entità di sicurezza in uno di questi ambiti:These roles can be assigned to your security principal in any of these scopes:

- Contenitore (file system)
- Account di archiviazione
- Resource group
- Subscription

Per informazioni su come verificare e assegnare ruoli, vedere [Concedere l'accesso ai dati BLOB e coda](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)di Azure con RBAC nel portale di Azure.To learn how to verify and assign roles, see Grant access to Azure blob and queue data with RBAC in the Azure portal.

> [!NOTE]
> Tenere presente che le assegnazioni di ruolo RBAC possono richiedere fino a cinque minuti per la propagazione.

Non è necessario disporre di uno di questi ruoli assegnati all'entità di sicurezza se l'entità di sicurezza viene aggiunta all'elenco di controllo di accesso (ACL) del contenitore o della directory di destinazione. Nell'elenco di controllo di accesso, l'entità di sicurezza deve disporre dell'autorizzazione di scrittura per la directory di destinazione ed eseguire l'autorizzazione sul contenitore e su ogni directory padre.

Per altre informazioni, vedere [Controllo di accesso in Azure Data Lake Storage Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

#### <a name="authenticate-a-user-identity"></a>Autenticare un'identità utenteAuthenticate a user identity

Dopo aver verificato che all'identità utente sia stato assegnato il livello di autorizzazione necessario, aprire un prompt dei comandi, digitare il comando seguente e quindi premere INVIO.

```azcopy
azcopy login
```

Se si appartiene a più di un'organizzazione, includere l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Sostituire `<tenant-id>` il segnaposto con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare Azure Active Directory > Proprietà > ID directory nel portale di Azure.To find the tenant ID, select **Azure Active Directory > Properties > Directory ID** in the Azure portal.

Questo comando restituisce un codice di autenticazione e l'URL di un sito Web. Aprire il sito Web, immettere il codice e quindi scegliere il pulsante **Avanti**.

![Creare un contenitore](media/storage-use-azcopy-v10/azcopy-login.png)

Verrà visualizzata una finestra di accesso. In tale finestra accedere al proprio account Azure usando le relative credenziali. Dopo aver completato l'accesso, è possibile chiudere la finestra del browser e iniziare a usare AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autenticare un'entità servizioAuthenticate a service principal

Si tratta di un'ottima opzione se si prevede di usare AzCopy all'interno di uno script che viene eseguito senza l'interazione dell'utente, in particolare quando si esegue in locale. Se si prevede di eseguire AzCopy in macchine virtuali eseguite in Azure, un'identità del servizio gestito è più facile da amministrare. Per altre informazioni, vedere la sezione [Autenticare un'identità gestita](#managed-identity) di questo articolo.

Prima di eseguire uno script, è necessario accedere in modo interattivo almeno una volta in modo da poter fornire AzCopy con le credenziali dell'entità servizio.  Tali credenziali vengono archiviate in un file protetto e crittografato in modo che lo script non debba fornire tali informazioni riservate.

È possibile accedere all'account usando un segreto client o la password di un certificato associato alla registrazione dell'app dell'entità servizio.

Per altre informazioni sulla creazione dell'entità servizio, vedere [Procedura: Usare il portale per creare un'applicazione e un'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)di Azure AD in grado di accedere alle risorse.

Per altre informazioni sulle entità servizio in generale, vedere [Oggetti applicazione e entità servizio in Azure Active DirectoryTo](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) learn more about service principals in general, see Application and service principal objects in Azure Active Directory

##### <a name="using-a-client-secret"></a>Utilizzo di un segreto clientUsing a client secret

Iniziare impostando `AZCOPY_SPA_CLIENT_SECRET` la variabile di ambiente sul segreto client della registrazione dell'app dell'entità servizio.

> [!NOTE]
> Assicurarsi di impostare questo valore dal prompt dei comandi e non nelle impostazioni delle variabili di ambiente del sistema operativo. In questo modo, il valore è disponibile solo per la sessione corrente.

This example shows how you could do this in PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Si consiglia di utilizzare un prompt come illustrato in questo esempio. In questo modo, la password non verrà visualizzata nella cronologia dei comandi della console.  

Successivamente, digitare il comando seguente e quindi premere INVIO.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Sostituire `<application-id>` il segnaposto con l'ID applicazione della registrazione dell'app dell'entità servizio. Sostituire `<tenant-id>` il segnaposto con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare Azure Active Directory > Proprietà > ID directory nel portale di Azure.To find the tenant ID, select **Azure Active Directory > Properties > Directory ID** in the Azure portal. 

##### <a name="using-a-certificate"></a>Utilizzo di un certificato

Se si preferisce usare le proprie credenziali per l'autorizzazione, è possibile caricare un certificato per la registrazione dell'app e quindi usare tale certificato per l'accesso.

Oltre a caricare il certificato nella registrazione dell'app, è necessario disporre anche di una copia del certificato salvato nel computer o nella macchina virtuale in cui verrà eseguito AzCopy. Questa copia del certificato deve essere in formato . PFX o . pEM e deve includere la chiave privata. La chiave privata deve essere protetta da password. Se si usa Windows e il certificato esiste solo in un archivio certificati, assicurarsi di esportare il certificato in un file PFX (inclusa la chiave privata). Per istruzioni, vedere [Export-PfxCertificateFor](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) guidance, see Export-PfxCertificate

Impostare quindi `AZCOPY_SPA_CERT_PASSWORD` la variabile di ambiente sulla password del certificato.

> [!NOTE]
> Assicurarsi di impostare questo valore dal prompt dei comandi e non nelle impostazioni delle variabili di ambiente del sistema operativo. In questo modo, il valore è disponibile solo per la sessione corrente.

This example shows how you could do this task in PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Successivamente, digitare il comando seguente e quindi premere INVIO.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Sostituire `<path-to-certificate-file>` il segnaposto con il percorso relativo o completo del file del certificato. AzCopy salva il percorso di questo certificato ma non salva una copia del certificato, quindi assicurati di mantenere il certificato sul posto. Sostituire `<tenant-id>` il segnaposto con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare Azure Active Directory > Proprietà > ID directory nel portale di Azure.To find the tenant ID, select **Azure Active Directory > Properties > Directory ID** in the Azure portal.

> [!NOTE]
> Si consiglia di utilizzare un prompt come illustrato in questo esempio. In questo modo, la password non verrà visualizzata nella cronologia dei comandi della console. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Autenticare un'identità gestitaAuthenticate a managed identity

Si tratta di un'ottima opzione se si prevede di usare AzCopy all'interno di uno script che viene eseguito senza l'interazione dell'utente e lo script viene eseguito da una macchina virtuale di Azure (VM). Quando si usa questa opzione, non è necessario archiviare le credenziali nella macchina virtuale.

È possibile accedere all'account usando un'identità gestita a livello di sistema abilitata nella macchina virtuale oppure l'ID client, l'ID oggetto o l'ID risorsa di un'identità gestita assegnata dall'utente assegnata alla macchina virtuale.

Per altre informazioni su come abilitare un'identità gestita a livello di sistema o creare un'identità gestita assegnata dall'utente, vedere [Configurare le identità gestite per le risorse](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)di Azure in una macchina virtuale usando il portale di Azure.To learn more about how to enable a system-wide managed identity or create a user-assigned managed identity, see Configure managed identities for Azure resources on a VM using the Azure portal .

##### <a name="using-a-system-wide-managed-identity"></a>Utilizzo di un'identità gestita a livello di sistemaUsing a system-wide managed identity

Verificare innanzitutto di aver abilitato un'identità gestita a livello di sistema nella macchina virtuale. Vedere [Identità gestita assegnata dal sistema](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Quindi, nella console dei comandi, digitare il comando seguente e quindi premere INVIO.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Utilizzo di un'identità gestita assegnata dall'utenteUsing a user-assigned managed identity

Verificare innanzitutto di aver abilitato un'identità gestita assegnata dall'utente nella macchina virtuale. Vedere [Identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Quindi, nella console dei comandi, digitare uno dei seguenti comandi e quindi premere il tasto INVIO.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Sostituire `<client-id>` il segnaposto con l'ID client dell'identità gestita assegnata dall'utente.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Sostituire `<object-id>` il segnaposto con l'ID oggetto dell'identità gestita assegnata dall'utente.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Sostituire `<resource-id>` il segnaposto con l'ID risorsa dell'identità gestita assegnata dall'utente.

### <a name="option-2-use-a-sas-token"></a>Opzione 2: Usare un token di firma di accesso condivisoOption 2: Use a SAS token

È possibile aggiungere un token di firma di accesso condiviso a ogni URL di origine o di destinazione usato nei comandi AzCopy.You can append a SAS token to each source or destination URL that use in your AzCopy commands.

Questo comando di esempio copia in modo ricorsivo i dati da una directory locale a un contenitore BLOB. Un token di firma di accesso condiviso fittizio viene aggiunto alla fine dell'URL del contenitore.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Per altre informazioni sui token di firma di accesso condiviso e su come ottenerne uno, vedere [Uso delle firme](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)di accesso condiviso.

## <a name="transfer-files"></a>Trasferire i file

Dopo aver autenticato l'identità o ottenuto un token di firma di accesso condiviso, è possibile iniziare a trasferire i file.

Per trovare i comandi di esempio, vedere uno di questi articoli.

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Trasferire i dati con AzCopy e l'archiviazione di Azure StackTransfer data with AzCopy and Azure Stack storage](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Usare AzCopy in uno scriptUse AzCopy in a script

### <a name="obtain-a-static-download-link"></a>Ottenere un collegamento di download statico

Nel corso del tempo, il collegamento di download AzCopy punterà a nuove versioni di AzCopy.Over time, the AzCopy [download link](#download-and-install-azcopy) will point to new versions of AzCopy. Se lo script scarica AzCopy, lo script potrebbe smettere di funzionare se una versione più recente di AzCopy modifica le funzionalità da cui dipende lo script.

To avoid these issues, obtain a static (un-changing) link to the current version of AzCopy. In questo modo, lo script scarica la stessa versione esatta di AzCopy ogni volta che viene eseguito.

Per ottenere il collegamento, eseguire questo comando:

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Per Linux, `--strip-components=1` `tar` sul comando rimuove la cartella di primo livello che contiene il nome della versione e invece estrae il file binario direttamente nella cartella corrente. In questo modo lo script può `azcopy` essere aggiornato `wget` con una nuova versione di aggiornando solo l'URL.

L'URL viene visualizzato nell'output di questo comando. Lo script può quindi scaricare AzCopy usando tale URL.

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Eseguire l'escape di caratteri speciali nei token di firma di accesso condiviso

Nei file batch `.cmd` con estensione, è necessario `%` eseguire l'escape dei caratteri visualizzati nei token di firma di accesso condiviso. È possibile farlo aggiungendo `%` un carattere `%` aggiuntivo accanto ai caratteri esistenti nella stringa del token di firma di accesso condiviso.

### <a name="run-scripts-by-using-jenkins"></a>Eseguire script utilizzando Jenkins

Se si prevede di utilizzare [Jenkins](https://jenkins.io/) per eseguire gli script, assicurarsi di inserire il comando seguente all'inizio dello script.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Usare AzCopy in Esplora archivi di AzureUse AzCopy in Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) usa AzCopy per eseguire tutte le operazioni di trasferimento dei dati. È possibile usare [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se si desidera sfruttare i vantaggi in termini di prestazioni di AzCopy, ma si preferisce usare un'interfaccia utente grafica anziché la riga di comando per interagire con i file.

Storage Explorer usa la chiave dell'account per eseguire le operazioni, pertanto, dopo l'accesso a Storage Explorer, non sarà necessario fornire credenziali di autorizzazione aggiuntive.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Usare la versione precedente di AzCopyUse the previous version of AzCopy

Se è necessario utilizzare la versione precedente di AzCopy, vedere uno dei collegamenti seguenti:

- [AzCopy in Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy su Linux (v7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurare, ottimizzare e risolvere i problemi di AzCopy

Vedere [Configurare, ottimizzare e risolvere i problemi relativi a AzCopySee Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Passaggi successivi

In caso di domande, problemi o feedback generale, inviarli nella pagina [GitHub.If](https://github.com/Azure/azure-storage-azcopy) you have questions, issues, or general feedback, submit them on GitHub page.
