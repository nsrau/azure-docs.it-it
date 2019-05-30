---
title: Copiare o spostare i dati in archiviazione di Azure tramite AzCopy v10 | Microsoft Docs
description: AzCopy è un'utilità della riga di comando che è possibile usare per copiare i dati a, da o tra account di archiviazione. Questo articolo consente di scaricare AzCopy, connettersi all'account di archiviazione e quindi trasferire i file.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: cc65d6d3f7e7dcc08ea29ecc8a299b556563135b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236344"
---
# <a name="get-started-with-azcopy"></a>Introduzione ad AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB o file da o verso un account di archiviazione. Questo articolo consente di scaricare AzCopy, connettersi all'account di archiviazione e quindi trasferire i file.

> [!NOTE]
> AzCopy **V10** è la versione attualmente supportata di AzCopy.
>
> Se è necessario usare AzCopy **v8.1**, vedere la [utilizzare la versione precedente di AzCopy](#previous-version) sezione di questo articolo.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Download di AzCopy

Prima di tutto, scaricare il file eseguibile AzCopy V10 per qualsiasi cartella nel computer. Per praticità, è consigliabile aggiungere il percorso di cartella di AzCopy al percorso di sistema per la semplicità d'uso.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Se si vogliono copiare i dati da e verso i [archiviazione tabelle di Azure](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) del servizio, quindi installare [AzCopy versione 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Eseguire AzCopy

Da un prompt dei comandi, passare alla directory in cui è stato scaricato il file.

Per visualizzare un elenco di comandi AzCopy, digitare `azCopy`, quindi premere il tasto INVIO.

Per altre informazioni su un comando specifico, digitare `azCopy` seguito dal nome del comando.

Ad esempio, per scoprire le `copy` comando, digitare `azcopy copy`e quindi premere INVIO.

Prima di effettuare altri elementi significativi con AzCopy, è necessario decidere come fornire le credenziali di autorizzazione al servizio di archiviazione.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere come fornire le credenziali di autorizzazione

È possibile fornire credenziali di autorizzazione con Azure Active Directory (AD) o con un token di firma di accesso condiviso (SAS).

Usare questa tabella come guida:

| Tipo di archiviazione | Metodo attualmente supportato di autorizzazione |
|--|--|
|**Archiviazione BLOB** | Azure AD & firma di accesso condiviso |
|**Archiviazione BLOB (spazio dei nomi gerarchico)** | Solo Azure AD |
|**Archiviazione file** | Firma di accesso condiviso solo |

### <a name="option-1-use-azure-ad"></a>Opzione 1: Usare Azure AD

Il livello di autorizzazione che è necessario dipende se si prevede di caricare i file o semplicemente eseguirne il download.

#### <a name="authorization-to-upload-files"></a>Autorizzazione per caricare i file

Verificare che uno di questi ruoli è stato assegnato alla propria identità:

- [Collaboratore ai dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietario dei dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Questi ruoli possono essere assegnati alla propria identità in uno di questi ambiti:

- Contenitore (file system di)
- Account di archiviazione
- Gruppo di risorse
- Sottoscrizione

Per informazioni su come verificare e assegnare ruoli, vedere [concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Non è necessario avere uno di questi ruoli assegnati alla propria identità se l'identità viene aggiunto all'elenco di controllo di accesso (ACL) del contenitore di destinazione o della cartella. Nell'elenco ACL, la tua identità richiede autorizzazione di scrittura nella cartella di destinazione e l'autorizzazione di esecuzione nel contenitore e tutte le cartelle padre.

Per altre informazioni, vedere [controllo degli accessi in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authorization-to-download-files"></a>Autorizzazione per scaricare i file

Verificare che uno di questi ruoli è stato assegnato alla propria identità:

- [Lettore dei dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [Collaboratore ai dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietario dei dati del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Questi ruoli possono essere assegnati alla propria identità in uno di questi ambiti:

- Contenitore (file system di)
- Account di archiviazione
- Gruppo di risorse
- Sottoscrizione

Per informazioni su come verificare e assegnare ruoli, vedere [concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Non è necessario avere uno di questi ruoli assegnati alla propria identità se l'identità viene aggiunto all'elenco di controllo di accesso (ACL) del contenitore di destinazione o della cartella. Nell'elenco ACL, la tua identità richiede l'autorizzazione per la cartella di destinazione, autorizzazione read ed execute sul contenitore e tutte le cartelle padre.

Per altre informazioni, vedere [controllo degli accessi in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Eseguire l'autenticazione dell'identità

Dopo aver verificato che l'identità è stata assegnata il livello di autorizzazione necessari, aprire un prompt dei comandi, digitare il comando seguente e quindi premere INVIO.

```azcopy
azcopy login
```

Questo comando restituisce un codice di autenticazione e l'URL di un sito Web. Aprire il sito Web, specificare il codice e quindi scegliere il **successivo** pulsante.

![Creare un contenitore](media/storage-use-azcopy-v10/azcopy-login.png)

Verrà visualizzata una finestra di accesso. In tale finestra, accedere all'account Azure usando le credenziali dell'account Azure. Dopo aver correttamente eseguito l'accesso, è possibile chiudere la finestra del browser e iniziare a usare AzCopy.

### <a name="option-2-use-a-sas-token"></a>Opzione 2: Usare un token di firma di accesso condiviso

È possibile aggiungere un token di firma di accesso condiviso per ogni URL di origine o di destinazione usata nei comandi di AzCopy.

Questo comando di esempio in modo ricorsivo copia i dati da una directory locale in un contenitore blob. Un token di firma di accesso condiviso fittizio viene aggiunto alla fine della dell'URL del contenitore.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Per altre informazioni sui token di firma di accesso condiviso e su come ottenerne uno, vedere [uso delle firme di accesso (SAS) condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Trasferimento di file

Dopo aver autenticato la tua identità o ottenuto un token di firma di accesso condiviso, è possibile iniziare il trasferimento di file.

Per trovare i comandi di esempio, vedere questi articoli.

- [Trasferire dati con AzCopy e l'archiviazione blob](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e archiviazione file](storage-use-azcopy-files.md)

- [Trasferire dati con AzCopy e Amazon S3 bucket](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurare, ottimizzare e risolvere i problemi di AzCopy

Vedere [configura, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>Usare AzCopy in Storage Explorer

Se si desidera sfruttare i vantaggi delle prestazioni di AzCopy, ma si preferisce usare Storage Explorer piuttosto che la riga di comando per interagire con i file, quindi abilitare AzCopy in Storage Explorer.

In Storage Explorer, scegliere **Preview**->**usare AzCopy per migliorata del caricamento di Blob e il Download**.

![Abilitare AzCopy come motore di trasferimento in Azure Storage Explorer](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Non è necessario abilitare questa impostazione se è stato abilitato uno spazio dei nomi gerarchico nell'account di archiviazione. Ciò avviene perché Storage Explorer usa automaticamente AzCopy sugli account di archiviazione con uno spazio dei nomi gerarchico.  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Utilizzare la versione precedente di AzCopy

Se è necessario utilizzare la versione precedente di AzCopy (AzCopy v8.1), vedere uno dei collegamenti seguenti:

- [AzCopy on Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy on Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Passaggi successivi

Se hai domande, problemi o commenti e suggerimenti generali, inviarli [su GitHub](https://github.com/Azure/azure-storage-azcopy) pagina.
