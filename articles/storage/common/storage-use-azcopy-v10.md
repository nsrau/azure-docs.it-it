---
title: Copiare o spostare dati in archiviazione di Azure tramite AzCopy V10 | Microsoft Docs
description: AzCopy è un'utilità da riga di comando che è possibile usare per copiare i dati in, da o tra account di archiviazione. Questo articolo consente di scaricare AzCopy, connettersi all'account di archiviazione e quindi trasferire i file.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0572a18e530eaff2b5a2d8aa8ced5af26f762aa8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873931"
---
# <a name="get-started-with-azcopy"></a>Introduzione ad AzCopy

AzCopy è un'utilità da riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di scaricare AzCopy, connettersi all'account di archiviazione e quindi trasferire i file.

> [!NOTE]
> AzCopy **V10** è la versione attualmente supportata di AzCopy.
>
> Se è necessario usare AzCopy **v 8.1**, vedere la sezione [usare la versione precedente di AzCopy](#previous-version) di questo articolo.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Scaricare AzCopy

Prima di tutto, scaricare il file eseguibile AzCopy V10 in qualsiasi directory del computer. AzCopy V10 è semplicemente un file eseguibile, quindi non c'è niente da installare.

- [Windows a 64 bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows a 32 bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Questi file vengono compressi come file zip (Windows e Mac) o file con estensione tar (Linux).

È possibile usare questi comandi per scaricare e decomprimere il file tar in Linux.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopy-v10-linux
tar -xf azcopy.tar.gz
```

> [!NOTE]
> Se si vogliono copiare dati da e verso il servizio di [archiviazione tabelle di Azure](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) , installare [AzCopy versione 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Esegui AzCopy

Per praticità, è consigliabile aggiungere il percorso della directory del file eseguibile AzCopy al percorso di sistema per facilitarne l'uso. In questo modo è possibile digitare `azcopy` da qualsiasi directory nel sistema.

Se si sceglie di non aggiungere la directory AzCopy al percorso, sarà necessario modificare le directory nel percorso del file eseguibile di AzCopy e digitare `azcopy` o `.\azcopy` in prompt dei comandi di Windows PowerShell.

Per visualizzare un elenco di comandi, digitare `azcopy -h` e premere il tasto INVIO.

Per informazioni su un comando specifico, è sufficiente includere il nome del comando (ad esempio: `azcopy list -h`).

![Guida inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Per informazioni dettagliate sulla documentazione di riferimento per ogni comando e parametro del comando, vedere [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> In qualità di proprietario dell'account di archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati. Prima di poter eseguire operazioni significative con AzCopy, è necessario decidere come fornire le credenziali di autorizzazione al servizio di archiviazione. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere il modo in cui fornire le credenziali di autorizzazione

È possibile fornire le credenziali di autorizzazione usando Azure Active Directory (AD) o usando un token di firma di accesso condiviso (SAS).

Usare questa tabella come guida:

| Tipo di risorse di archiviazione | Metodo di autorizzazione attualmente supportato |
|--|--|
|**Archiviazione BLOB** | Firma di accesso condiviso Azure AD & |
|**Archiviazione BLOB (spazio dei nomi gerarchico)** | Firma di accesso condiviso Azure AD & |
|**Archiviazione file** | Solo SAS |

### <a name="option-1-use-azure-active-directory"></a>Opzione 1: usare Azure Active Directory

Con Azure Active Directory è possibile fornire le credenziali una sola volta anziché dover aggiungere un token SAS a ogni comando.  

> [!NOTE]
> Nella versione corrente, se si prevede di copiare i BLOB tra gli account di archiviazione, è necessario aggiungere un token di firma di accesso condiviso a ogni URL di origine. È possibile omettere il token SAS solo dall'URL di destinazione. Per esempi, vedere [copiare i BLOB tra account di archiviazione](storage-use-azcopy-blobs.md).

Il livello di autorizzazione necessario si basa sul fatto che si preveda di caricare i file o semplicemente scaricarli.

Se si vuole solo scaricare i file, verificare che il [lettore di dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) sia stato assegnato all'identità utente, all'identità gestita o all'entità servizio.

> Le identità utente, le identità gestite e le entità servizio sono ognuna un tipo di *entità di sicurezza*, quindi si userà il termine *entità di sicurezza* per la parte restante di questo articolo.

Se si vuole caricare i file, verificare che uno di questi ruoli sia stato assegnato all'entità di sicurezza:

- [Collaboratore ai dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietario dei dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Questi ruoli possono essere assegnati all'entità di sicurezza in uno di questi ambiti:

- Contenitore (file system)
- Account di archiviazione
- Gruppo di risorse
- Sottoscrizione

Per informazioni su come verificare e assegnare i ruoli, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nel portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Tenere presente che le assegnazioni di ruolo RBAC possono richiedere fino a cinque minuti per la propagazione.

Non è necessario che uno di questi ruoli sia assegnato all'entità di sicurezza se l'entità di sicurezza viene aggiunta all'elenco di controllo di accesso (ACL) del contenitore o della directory di destinazione. Nell'ACL l'entità di sicurezza necessita dell'autorizzazione di scrittura per la directory di destinazione e dell'autorizzazione Execute per il contenitore e ogni directory padre.

Per altre informazioni, vedere [controllo di accesso in Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autenticare un'identità utente

Dopo aver verificato che all'identità utente è stato assegnato il livello di autorizzazione necessario, aprire un prompt dei comandi, digitare il comando seguente, quindi premere il tasto INVIO.

```azcopy
azcopy login
```

Se si appartiene a più di un'organizzazione, includere l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Sostituire il segnaposto `<tenant-id>` con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare **Azure Active Directory > proprietà > ID directory** nella portale di Azure.

Questo comando restituisce un codice di autenticazione e l'URL di un sito Web. Aprire il sito Web, immettere il codice e quindi scegliere il pulsante **Avanti**.

![Creare un contenitore](media/storage-use-azcopy-v10/azcopy-login.png)

Verrà visualizzata una finestra di accesso. In tale finestra accedere al proprio account Azure usando le relative credenziali. Dopo aver completato l'accesso, è possibile chiudere la finestra del browser e iniziare a usare AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autenticare un'entità servizio

Si tratta di un'ottima opzione se si prevede di usare AzCopy all'interno di uno script che viene eseguito senza interazione dell'utente, in particolare in caso di esecuzione in locale. Se si prevede di eseguire AzCopy in macchine virtuali eseguite in Azure, un'identità del servizio gestito è più semplice da amministrare. Per altre informazioni, vedere la sezione [autenticare un'identità gestita](#managed-identity) di questo articolo.

Prima di eseguire uno script, è necessario eseguire l'accesso in modo interattivo almeno una volta, in modo da poter fornire a AzCopy le credenziali dell'entità servizio.  Queste credenziali vengono archiviate in un file protetto e crittografato, in modo che lo script non debba fornire tali informazioni riservate.

È possibile accedere all'account usando un segreto client o usando la password di un certificato associato alla registrazione dell'app dell'entità servizio.

Per altre informazioni sulla creazione di un'entità servizio, vedere [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Per ulteriori informazioni sulle entità servizio in generale, vedere [oggetti applicazione e oggetti entità servizio in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Uso di un segreto client

Per iniziare, impostare la variabile di ambiente `AZCOPY_SPA_CLIENT_SECRET` sul segreto client della registrazione dell'app dell'entità servizio.

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
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Sostituire il segnaposto `<application-id>` con l'ID applicazione della registrazione dell'app dell'entità servizio. Sostituire il segnaposto `<tenant-id>` con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare **Azure Active Directory > proprietà > ID directory** nella portale di Azure. 

##### <a name="using-a-certificate"></a>Utilizzo di un certificato

Se si preferisce usare le proprie credenziali per l'autorizzazione, è possibile caricare un certificato nella registrazione dell'app e quindi usare tale certificato per l'accesso.

Oltre a caricare il certificato nella registrazione dell'app, è anche necessario disporre di una copia del certificato salvato nel computer o nella macchina virtuale in cui verrà eseguito AzCopy. Questa copia del certificato deve essere in. PFX o. Formato PEM e deve includere la chiave privata. La chiave privata deve essere protetta da password. Se si usa Windows e il certificato esiste solo in un archivio certificati, assicurarsi di esportare il certificato in un file PFX (inclusa la chiave privata). Per istruzioni, vedere [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Impostare quindi la variabile di ambiente `AZCOPY_SPA_CERT_PASSWORD` sulla password del certificato.

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

Sostituire il segnaposto `<path-to-certificate-file>` con il percorso relativo o il percorso completo del file di certificato. AzCopy Salva il percorso del certificato, ma non salva una copia del certificato, quindi assicurarsi di conservare il certificato. Sostituire il segnaposto `<tenant-id>` con l'ID tenant dell'organizzazione a cui appartiene l'account di archiviazione. Per trovare l'ID tenant, selezionare **Azure Active Directory > proprietà > ID directory** nella portale di Azure.

> [!NOTE]
> Prendere in considerazione l'uso di un prompt, come illustrato in questo esempio. In questo modo, la password non verrà visualizzata nella cronologia dei comandi della console. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Autenticare un'identità gestita

Si tratta di un'ottima opzione se si prevede di usare AzCopy all'interno di uno script che viene eseguito senza interazione dell'utente e lo script viene eseguito da una macchina virtuale (VM) di Azure. Quando si usa questa opzione, non è necessario archiviare le credenziali nella macchina virtuale.

È possibile accedere all'account usando un'identità gestita a livello di sistema abilitata nella VM oppure usando l'ID client, l'ID oggetto o l'ID di risorsa di un'identità gestita assegnata dall'utente che è stata assegnata alla macchina virtuale.

Per altre informazioni su come abilitare un'identità gestita a livello di sistema o creare un'identità gestita assegnata dall'utente, vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale usando il portale di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Uso di un'identità gestita a livello di sistema

Assicurarsi prima di tutto che sia stata abilitata un'identità gestita a livello di sistema nella macchina virtuale. Vedere [identità gestita assegnata dal sistema](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Quindi, nella console dei comandi, digitare il comando seguente, quindi premere il tasto INVIO.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Uso di un'identità gestita assegnata dall'utente

Assicurarsi prima di tutto che sia stata abilitata un'identità gestita assegnata dall'utente nella macchina virtuale. Vedere [identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Quindi, nella console dei comandi, digitare uno dei comandi seguenti, quindi premere il tasto INVIO.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Sostituire il segnaposto `<client-id>` con l'ID client dell'identità gestita assegnata dall'utente.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Sostituire il segnaposto `<object-id>` con l'ID oggetto dell'identità gestita assegnata dall'utente.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Sostituire il segnaposto `<resource-id>` con l'ID risorsa dell'identità gestita assegnata dall'utente.

### <a name="option-2-use-a-sas-token"></a>Opzione 2: usare un token di firma di accesso condiviso

È possibile aggiungere un token di firma di accesso condiviso a ogni URL di origine o di destinazione usato nei comandi di AzCopy.

Questo comando di esempio copia in modo ricorsivo i dati da una directory locale a un contenitore BLOB. Un token di firma di accesso condiviso fittizio viene aggiunto alla fine dell'URL del contenitore.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Per altre informazioni sui token SAS e su come ottenerne uno, vedere [uso delle firme di accesso condiviso (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Trasferire i file

Dopo aver autenticato l'identità o ottenuto un token di firma di accesso condiviso, è possibile iniziare a trasferire i file.

Per trovare i comandi di esempio, vedere uno di questi articoli.

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Trasferire dati con AzCopy e archiviazione Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Usare AzCopy in uno script

### <a name="obtain-a-static-download-link"></a>Ottenere un collegamento di download statico

Nel corso del tempo, il collegamento per il [download](#download-and-install-azcopy) di AzCopy punterà alle nuove versioni di AzCopy. Se lo script Scarica AzCopy, lo script potrebbe smettere di funzionare se una versione più recente di AzCopy modifica le funzionalità da cui dipende lo script.

Per evitare questi problemi, ottenere un collegamento statico (non modificabile) alla versione corrente di AzCopy. In questo modo, lo script Scarica la stessa versione esatta di AzCopy ogni volta che viene eseguito.

Per ottenere il collegamento, eseguire il comando seguente:

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Per Linux, `--strip-components=1` nel comando `tar` rimuove la cartella di primo livello che contiene il nome della versione, estrae invece il file binario direttamente nella cartella corrente. Questo consente di aggiornare lo script con una nuova versione di `azcopy` aggiornando solo l'URL del `wget`.

L'URL viene visualizzato nell'output di questo comando. Lo script può quindi scaricare AzCopy usando tale URL.

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Escape di caratteri speciali nei token SAS

Nei file batch con estensione `.cmd` sarà necessario eseguire l'escape dei caratteri `%` visualizzati nei token SAS. È possibile eseguire questa operazione aggiungendo un carattere di aggiunta `%` accanto ai caratteri `%` esistenti nella stringa del token SAS.

### <a name="run-scripts-by-using-jenkins"></a>Eseguire script con Jenkins

Se si prevede di usare [Jenkins](https://jenkins.io/) per eseguire gli script, assicurarsi di inserire il comando seguente all'inizio dello script.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Usare AzCopy in Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) USA AzCopy per eseguire tutte le operazioni di trasferimento dei dati. È possibile utilizzare [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se si desidera sfruttare i vantaggi delle prestazioni di AzCopy, ma si preferisce utilizzare un'interfaccia utente grafica piuttosto che la riga di comando per interagire con i file.

Storage Explorer usa la chiave dell'account per eseguire operazioni, quindi, dopo l'accesso Storage Explorer, non è necessario fornire credenziali di autorizzazione aggiuntive.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Usare la versione precedente di AzCopy

Se è necessario usare la versione precedente di AzCopy (AzCopy v 8.1), vedere uno dei seguenti collegamenti:

- [AzCopy in Windows (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy on Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurare, ottimizzare e risolvere i problemi relativi a AzCopy

Vedere [configurare, ottimizzare e risolvere i problemi relativi a AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Passaggi successivi

In caso di domande, problemi o commenti e suggerimenti generali, inviarli [nella pagina di GitHub](https://github.com/Azure/azure-storage-azcopy) .
