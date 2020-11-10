---
title: Copiare o spostare dati in archiviazione di Azure tramite AzCopy V10 | Microsoft Docs
description: AzCopy è un'utilità da riga di comando che è possibile usare per copiare i dati in, da o tra account di archiviazione. Questo articolo consente di scaricare AzCopy, connettersi all'account di archiviazione e di trasferire i file.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/09/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperfq2
ms.openlocfilehash: ad9b40b448b48500cd6882ac614611f91370ec9e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410268"
---
# <a name="get-started-with-azcopy"></a>Introduzione ad AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di scaricare AzCopy, connettersi all'account di archiviazione e di trasferire i file.

> [!NOTE]
> AzCopy **V10** è la versione attualmente supportata di AzCopy.
>
> Se è necessario usare una versione precedente di AzCopy, vedere la sezione [usare la versione precedente di AzCopy](#previous-version) di questo articolo.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Scaricare AzCopy

Prima di tutto, scaricare il file eseguibile AzCopy V10 in qualsiasi directory del computer. AzCopy V10 è semplicemente un file eseguibile, quindi non c'è niente da installare.

- [Windows a 64 bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows a 32 bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Questi file vengono compressi come file zip (Windows e Mac) o file con estensione tar (Linux). Per scaricare e decomprimere il file tar in Linux, vedere la documentazione per la distribuzione di Linux.

> [!NOTE]
> Se si vogliono copiare dati da e verso il servizio di [archiviazione tabelle di Azure](../tables/table-storage-overview.md) , installare [AzCopy versione 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Eseguire Azcopy

Per praticità, è consigliabile aggiungere il percorso della directory del file eseguibile di AzCopy al percorso di sistema per facilitarne l'uso. In questo modo è possibile digitare `azcopy` da qualsiasi directory nel sistema.

Se si sceglie di non aggiungere la directory AzCopy al percorso, sarà necessario modificare le directory nel percorso del file eseguibile di AzCopy e digitare `azcopy` o `.\azcopy` nei prompt dei comandi di Windows PowerShell.

Per visualizzare un elenco di comandi, digitare `azcopy -h` e premere il tasto INVIO.

Per informazioni su un comando specifico, è sufficiente includere il nome del comando (ad esempio: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Guida inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)


Per informazioni dettagliate sulla documentazione di riferimento per ogni comando e parametro del comando, vedere [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> In qualità di proprietario dell'account di archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati. Prima di poter eseguire operazioni significative con AzCopy, è necessario decidere come fornire le credenziali di autorizzazione al servizio di archiviazione. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Autorizzare AzCopy

È possibile fornire le credenziali di autorizzazione usando Azure Active Directory (AD) o usando un token di firma di accesso condiviso (SAS).

Usare questa tabella come guida:

| Tipo di archiviazione | Metodo di autorizzazione attualmente supportato |
|--|--|
|**Archiviazione BLOB** | Azure AD e firma di accesso condiviso |
|**Archiviazione BLOB (spazio dei nomi gerarchico)** | Azure AD e firma di accesso condiviso |
|**Archiviazione file** | Solo SAS |

#### <a name="option-1-use-azure-active-directory"></a>Opzione 1: usare Azure Active Directory

Questa opzione è disponibile solo per l'archiviazione BLOB. Con Azure Active Directory è possibile fornire le credenziali una sola volta anziché dover aggiungere un token SAS a ogni comando.  

> [!NOTE]
> Nella versione corrente, se si prevede di copiare i BLOB tra gli account di archiviazione, è necessario aggiungere un token di firma di accesso condiviso a ogni URL di origine. È possibile omettere il token SAS solo dall'URL di destinazione. Per esempi, vedere [copiare i BLOB tra account di archiviazione](storage-use-azcopy-blobs.md).

Per autorizzare l'accesso tramite Azure AD, vedere [autorizzare l'accesso ai BLOB con AzCopy e Azure Active Directory (Azure ad)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Opzione 2: usare un token di firma di accesso condiviso

È possibile aggiungere un token di firma di accesso condiviso a ogni URL di origine o di destinazione usato nei comandi di AzCopy.

Questo comando di esempio copia in modo ricorsivo i dati da una directory locale a un contenitore BLOB. Un token di firma di accesso condiviso fittizio viene aggiunto alla fine dell'URL del contenitore.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Per altre informazioni sui token SAS e su come ottenerne uno, vedere [uso delle firme di accesso condiviso (SAS)](./storage-sas-overview.md).

## <a name="transfer-data"></a>Trasferire i dati

Dopo aver autorizzato la propria identità o ottenuto un token di firma di accesso condiviso, è possibile iniziare a trasferire i dati.

Per trovare i comandi di esempio, vedere uno di questi articoli.

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Trasferire dati con AzCopy e archiviazione Azure Stack](/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-in-a-script"></a>Usare in uno script

#### <a name="obtain-a-static-download-link"></a>Ottenere un collegamento di download statico

Nel corso del tempo, il collegamento per il [download](#download-and-install-azcopy) di AzCopy punterà alle nuove versioni di AzCopy. Se lo script Scarica AzCopy, lo script potrebbe smettere di funzionare se una versione più recente di AzCopy modifica le funzionalità da cui dipende lo script.

Per evitare questi problemi, ottenere un collegamento statico (non modificabile) alla versione corrente di AzCopy. In questo modo, lo script Scarica la stessa versione esatta di AzCopy ogni volta che viene eseguito.

Per ottenere il collegamento, eseguire il comando seguente:

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Per Linux, il `--strip-components=1` `tar` comando rimuove la cartella di primo livello che contiene il nome della versione e estrae il file binario direttamente nella cartella corrente. Questo consente di aggiornare lo script con una nuova versione di `azcopy` aggiornando solo l' `wget` URL.

L'URL viene visualizzato nell'output di questo comando. Lo script può quindi scaricare AzCopy usando tale URL.

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Escape di caratteri speciali nei token SAS

Nei file batch con `.cmd` estensione è necessario eseguire l'escape dei `%` caratteri visualizzati nei token SAS. È possibile eseguire questa operazione aggiungendo un `%` carattere aggiuntivo accanto ai `%` caratteri esistenti nella stringa del token SAS.

#### <a name="run-scripts-by-using-jenkins"></a>Eseguire script con Jenkins

Se si prevede di usare [Jenkins](https://jenkins.io/) per eseguire gli script, assicurarsi di inserire il comando seguente all'inizio dello script.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Usare in Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) USA AzCopy per eseguire tutte le operazioni di trasferimento dei dati. È possibile utilizzare [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se si desidera sfruttare i vantaggi delle prestazioni di AzCopy, ma si preferisce utilizzare un'interfaccia utente grafica piuttosto che la riga di comando per interagire con i file.

Storage Explorer usa la chiave dell'account per eseguire operazioni, quindi, dopo l'accesso Storage Explorer, non è necessario fornire credenziali di autorizzazione aggiuntive.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Configurazione, ottimizzazione e correzione

Vedere [configurare, ottimizzare e risolvere i problemi relativi a AzCopy](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Usa una versione precedente

Se è necessario usare la versione precedente di AzCopy, vedere uno dei seguenti collegamenti:

- [AzCopy in Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy in Linux (V7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Passaggi successivi

In caso di domande, problemi o commenti e suggerimenti generali, inviarli [nella pagina di GitHub](https://github.com/Azure/azure-storage-azcopy) .